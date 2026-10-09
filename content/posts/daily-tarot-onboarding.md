---
title: "Breaking down the tricky parts of my Daily Tarot onboarding screen"
description: A Flutter calendar grid, a draggable clock dial, and staggered animations, explained one piece at a time.
date: 2026-10-09
tags:
  - flutter
  - dart
  - devlog
---

Today I built the onboarding flow for **Daily Tarot**, a Flutter app that asks for your name, gender, birth date, and birth time before it reads your cards. I wrote it as fast as I could, so this post is me slowing down and explaining to myself the parts that were actually tricky.

The flow is a `TabBarView` with swiping turned off. Each step calls `moveToPage(n)` when it's done, so the user moves through the pages like a wizard:

```dart
TabBarView(
  controller: tabController,
  physics: NeverScrollableScrollPhysics(), // only code can change the page
  children: [ /* intro, name, gender, birth date, birth time, confirm */ ],
)
```

Most of it is ordinary UI. Four pieces took real thought.

## 1. Building a calendar grid from scratch

Instead of using a date-picker package, I drew my own month grid: 7 columns, starting on Sunday, with greyed-out days from the previous and next month filling the gaps. The whole grid comes from one function:

```dart
List<calenderCellData> buildCalenderDays(int year, int month) {
  final firstDay = DateTime(year, month, 1);
  final lastDay = DateTime(year, month + 1, 0);       // trick #1

  final startWeekday = firstDay.weekday % 7;          // trick #2
  final totalDays = lastDay.day;
  final prevMonthLastDay = DateTime(year, month, 0).day;

  final days = <calenderCellData>[];

  // Leading days from the previous month
  for (int i = 0; i < startWeekday; i++) {
    final day = prevMonthLastDay - startWeekday + i + 1;
    days.add(calenderCellData(isCurrentMonth: false, date: DateTime(year, month - 1, day)));
  }

  // The actual month
  for (int i = 1; i <= totalDays; i++) {
    days.add(calenderCellData(isCurrentMonth: true, date: DateTime(year, month, i)));
  }

  // Trailing days until the last row is full
  int nextDay = 1;
  while (days.length % 7 != 0) {
    days.add(calenderCellData(isCurrentMonth: false, date: DateTime(year, month + 1, nextDay)));
    nextDay++;
  }
  return days;
}
```

**Trick #1: day 0 means "the last day of the previous month."** Dart's `DateTime` normalizes values that are out of range. `DateTime(2026, 3, 0)` doesn't crash. It rolls back to February 28. So `DateTime(year, month + 1, 0).day` tells you how many days the current month has, and you never need a "30 days hath September" lookup table or a leap-year check. The same rule is why `month - 1` in January (month 0) and `month + 1` in December (month 13) still produce the right dates.

**Trick #2: moving Sunday to the front.** Dart numbers weekdays Monday = 1 through Sunday = 7, but my grid starts on Sunday. Taking `weekday % 7` turns Sunday into 0 and leaves Monday through Saturday as 1 to 6. That result is also the number of blank cells needed before day 1.

**Filling the gaps.** If the month starts on a Wednesday (`startWeekday = 3`) and February had 28 days, the leading cells are `28 - 3 + 0 + 1 = 26`, then 27, then 28. The trailing loop keeps adding days until the total is a multiple of 7, which always leaves a complete final row.

Each cell remembers whether it belongs to the current month. The grid uses that flag to dim the outside days and turn off taps on them.

## 2. Turning a finger position into a clock time

The birth-time step uses a round clock face, like Android's time picker. You drag around it to pick the hour, it switches to minutes on its own, and you drag again. The hardest part is converting a touch point into a number on the clock.

```dart
void _handleDialTouch(Offset localPosition, double dialSize) {
  final center = Offset(dialSize / 2, dialSize / 2);
  final dx = localPosition.dx - center.dx;
  final dy = localPosition.dy - center.dy;
  if (dx * dx + dy * dy < 20 * 20) return;   // dead zone in the middle

  double angle = atan2(dx, -dy);
  if (angle < 0) angle += 2 * pi;

  if (_isSelectingHour) {
    int hour = (angle / (2 * pi) * 12).round();
    if (hour == 0) hour = 12;
    setState(() => _tempHour = hour);
  } else {
    final minute = (angle / (2 * pi) * 60).round() % 60;
    setState(() => _tempMinute = minute);
  }
}
```

Going through it line by line:

1. **Measure from the center.** `dx` and `dy` give the touch position relative to the middle of the dial.
2. **Dead zone.** If the finger is within 20px of the center, the angle jumps around wildly with tiny movements, so we ignore it. Comparing `dx² + dy²` with `20²` avoids calling `sqrt`.
3. **`atan2(dx, -dy)` is the key line.** The usual form is `atan2(y, x)`, which measures counter-clockwise from 3 o'clock. A clock measures **clockwise from 12 o'clock**, so the arguments change. Screen coordinates have y pointing *down*, which means "up" is `-dy`. Passing `(dx, -dy)` gives an angle that is 0 at 12 o'clock and grows clockwise. That's exactly a clock.
4. **Normalize.** `atan2` returns values from $-\pi$ to $\pi$. Adding $2\pi$ to negative values maps everything into $[0, 2\pi)$.
5. **Scale to clock units.** $\frac{\theta}{2\pi}$ is "how far around the circle" from 0 to 1. Multiply by 12 for hours or by 60 for minutes, then round to the nearest one. Near the top, rounding gives 12 hours (or 60 minutes), so we wrap it: hour 0 becomes **12**, and `% 60` turns 60 minutes into **0**.

Drawing the dial is the same math in reverse. To place the number for position `i` around a circle of radius `r`:

```dart
final angle = i / 12 * 2 * pi;
final x = center.dx + r * sin(angle);
final y = center.dy - r * cos(angle);   // minus because screen y points down
```

Using `sin` for x and `cos` for y (instead of the usual opposite) is what starts the circle at 12 o'clock and makes it go clockwise. It's the same coordinate change as the `atan2(dx, -dy)` above.

### Automatically moving from hours to minutes

When you lift your finger after picking the hour, the dial switches to minutes. When you lift it after picking the minutes, the time is saved and the app moves to the next page:

```dart
void _handleDialTouchEnd() {
  if (_isSelectingHour) {
    Future.delayed(Duration(milliseconds: 250), () {
      if (mounted) setState(() => _isSelectingHour = false);
    });
  } else {
    setState(() {
      unknownTime = false;
      selectedHour = _tempHour;
      selectedMinute = _tempMinute;
    });
    Future.delayed(Duration(milliseconds: 300), () {
      if (mounted) moveToPage(6);
    });
  }
}
```

The short delays let you *see* your choice land before the screen changes. The `if (mounted)` check matters: by the time the delay finishes, the widget may already be gone (for example, if the user hit back), and calling `setState` on a disposed widget throws.

Picked values live in **temporary** variables (`_tempHour`, `_tempMinute`) until the minute is confirmed, and only then are they copied into the real `selectedHour` and `selectedMinute`. When you come back to this page, a listener on the tab controller resets the dial to hour mode and loads your previous answer:

```dart
tabController.addListener(() {
  if (!tabController.indexIsChanging && tabController.index == 5) {
    _isSelectingHour = true;
    if (selectedHour != null) _tempHour = selectedHour!;
    if (selectedMinute != null) _tempMinute = selectedMinute!;
  }
});
```

`indexIsChanging` is true *during* the page-slide animation. Checking for `!indexIsChanging` means the reset runs once, after the slide finishes.

### 12-hour to 24-hour

The dial shows 1 to 12 with an AM/PM toggle, but I store the time in 24-hour form. The edge cases are all about 12:

```dart
int to24Hour(int hour12, bool isAm) {
  if (isAm) return hour12 == 12 ? 0 : hour12;      // 12 AM → 00
  return hour12 == 12 ? 12 : hour12 + 12;          // 12 PM → 12, 1 PM → 13
}
```

| 12-hour | 24-hour |
| ------- | ------- |
| 12 AM   | 00      |
| 1 AM    | 01      |
| 12 PM   | 12      |
| 1 PM    | 13      |

## 3. Staggered animations from one controller

When the app opens, the moon drifts in, then the clouds slide toward the center, then the logo, title, and button fade up one after another. All of that is driven by **one** 4-second `AnimationController`, not one per element.

The trick is a small helper that cuts a slice out of the controller's 0 to 1 timeline:

```dart
double interValValue(double start, double end) {
  final t = ((ca.value - start) / (end - start)).clamp(0.0, 1.0);
  return Curves.easeOut.transform(t);
}
```

If the controller is at `0.5` and I ask for `interValValue(0.35, 0.7)`, I get `(0.5 - 0.35) / 0.35 ≈ 0.43`. That means this element is 43% of the way through *its own* animation. Before 0.35 it's clamped to 0 (not started yet), and after 0.7 it's clamped to 1 (finished). Each element gets its own window:

| Element | Window      |
| ------- | ----------- |
| Moon    | 0.10 – 0.35 |
| Clouds  | 0.20 – 0.45 |
| Logo    | 0.10 – 0.35 |
| Title   | 0.35 – 0.70 |
| Button  | 0.50 – 1.00 |

The windows overlap, so each element starts moving before the previous one has finished, which makes the sequence feel smooth instead of robotic. (This is essentially what Flutter's built-in `Interval` curve does. I just wrote it by hand.)

The clouds use one more small trick. Each cloud starts at its own offset and slides 90px *toward the center*:

```dart
offset: Offset(e.dx - (e.dx.sign * 90 * value), e.dy)
```

`e.dx.sign` is `+1` for clouds on the right and `-1` for clouds on the left, so a single expression moves every cloud inward, whichever side it starts on.

## 4. A clock face drawn with `CustomPainter`

The dial is drawn by hand on a `Canvas`: an outer ring, a hand from the center to the selected value, a dot in the middle, and 12 labels placed with the `sin`/`cos` formula above. `shouldRepaint` returns true only when the mode or the selected value changes, so Flutter doesn't redraw the dial on every frame:

```dart
@override
bool shouldRepaint(covariant _ClockDialPainter old) =>
    old.isHourMode != isHourMode || old.selectedValue != selectedValue;
```

## What I'd clean up next

Going back over it slowly turned up a few rough edges from writing it in a hurry:

- **The page count doesn't match.** The `TabController` has `length: 7`, but the `TabBarView` has 6 children (the age page was built but never added). Flutter will complain about that, and it also explains why some `moveToPage(n)` calls jump to the wrong step.
- **The clock hand is mirrored.** The hand uses `center.dx - r * sin(angle)` while the numbers use `+`, so at 3 o'clock the hand points to 9. The fix is a single `+`.
- **The selected number never gets its circle.** `isSelected = radius == selectedValue` compares the dial's size with the time. It should be `i == selectedValue` for hours and `minuteVal == selectedValue` for minutes.
- **Validators that navigate.** The name and age validators call `moveToPage`, so validating the form also changes the page as a side effect. That should move into an `onFieldSubmitted`.
- **Missing `setState`.** Tapping AM/PM or the hour/minute boxes changes state without calling `setState`, so the UI doesn't update until something else triggers a rebuild.
- **The year/month picker doesn't close** after you tap 확인 ("Confirm"). It needs a `Navigator.pop(context)`.
- **`super.dispose()` should come last**, after disposing my own controllers.
- **The entrance slide barely moves.** `Offset(0, 20 - (1 * value))` only travels 1px. I meant `20 * (1 - value)`.

Writing fast was fun. Explaining it afterwards is how I find out what I actually wrote.
