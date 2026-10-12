---
title: 5강. 컬렉션
description: 여러 값을 한 번에 담는 List, Map, Set과 자주 쓰는 기능들.
date: 2026-10-12
tags:
  - dart
  - 강의
---

값을 하나씩 변수에 담으면 금방 한계가 와요. 반 친구 30명 이름을 변수 30개로 만들 순 없잖아요? 그래서 **여러 값을 한 번에 담는 상자**가 필요해요.

| 종류   | 특징                             | 모양                      |
| ------ | -------------------------------- | ------------------------- |
| `List` | 순서가 있는 목록                 | `['사과', '바나나']`      |
| `Map`  | 이름표(key)와 값(value)의 짝     | `{'민수': 17, '지연': 16}` |
| `Set`  | 중복 없는 모음                   | `{'dart', 'flutter'}`     |

## List

```dart
void main() {
  var fruits = ['사과', '바나나'];

  fruits.add('딸기');        // 끝에 추가
  print(fruits);            // [사과, 바나나, 딸기]
  print(fruits[0]);         // 사과  ← 번호는 0부터!
  print(fruits.length);     // 3
  print(fruits.contains('포도')); // false

  fruits.remove('바나나');
  print(fruits);            // [사과, 딸기]
}
```

> [!warning] 번호는 0부터 시작해요
> 첫 번째는 `[0]`, 마지막은 `[length - 1]`이에요. `fruits[3]`처럼 없는 번호를 쓰면 에러가 나요.

타입을 직접 적을 땐 `List<String>`처럼 `< >` 안에 담을 값의 타입을 써요.

```dart
List<int> scores = [90, 45, 72, 100];
```

### 자주 쓰는 기능: where, map

```dart
void main() {
  var scores = [90, 45, 72, 100];

  // where: 조건에 맞는 것만 골라요
  var passed = scores.where((s) => s >= 60).toList();
  print(passed);   // [90, 72, 100]

  // map: 하나씩 바꿔요
  var bonus = scores.map((s) => s + 5).toList();
  print(bonus);    // [95, 50, 77, 105]
}
```

`(s) => s >= 60`은 4강에서 배운 **익명 함수**예요. 목록의 값을 하나씩 `s`에 넣어서 실행해요. 끝에 붙은 `.toList()`는 결과를 다시 List로 만들어 줘요.

## Map

```dart
void main() {
  var ages = {'민수': 17, '지연': 16};

  ages['철수'] = 18;          // 추가 (이미 있으면 수정)
  print(ages['민수']);        // 17
  print(ages['영희']);        // null ← 없는 key는 null
  print(ages.keys);           // (민수, 지연, 철수)
  print(ages.values);         // (17, 16, 18)

  for (var entry in ages.entries) {
    print('${entry.key}: ${entry.value}');
  }
}
```

없는 key를 찾으면 `null`이 나와요. 그래서 `ages['민수']`의 타입은 `int`가 아니라 `int?`예요. 2강에서 배운 `??`가 여기서 쓸모 있어요: `ages['영희'] ?? 0`.

## Set

```dart
void main() {
  var tags = {'dart', 'flutter'};
  tags.add('dart');          // 이미 있어서 무시돼요
  print(tags);               // {dart, flutter}
  print(tags.length);        // 2

  var empty = <String>{};    // 빈 Set
  var notSet = {};           // ⚠️ 이건 빈 Map이에요!
}
```

## Flutter에서 자주 쓰는 문법

### 펼치기: `...`

```dart
void main() {
  var a = [1, 2];
  var b = [0, ...a, 3];
  print(b); // [0, 1, 2, 3]
}
```

### 목록 안에서 if, for

```dart
void main() {
  var isLoggedIn = true;

  var menu = [
    '홈',
    '검색',
    if (isLoggedIn) '내 정보',   // 조건이 참일 때만 들어가요
  ];
  print(menu); // [홈, 검색, 내 정보]

  var squares = [for (var i = 1; i <= 4; i++) i * i];
  print(squares); // [1, 4, 9, 16]
}
```

> [!tip] 왜 Flutter에서 많이 써요?
> Flutter 화면은 `children: [ ... ]`처럼 위젯 **목록**으로 만들어요. 그래서 "로그인했을 때만 이 버튼 보여 주기" 같은 걸 `if`로, "데이터 개수만큼 카드 만들기" 같은 걸 `for`나 `...list.map(...)`으로 해요.

## 연습 문제

과목별 점수가 Map에 들어 있어요. **평균**을 구해서 출력하세요.

```dart
var scores = {'국어': 85, '영어': 92, '수학': 78};
```

> [!example]- 정답 보기
> ```dart
> void main() {
>   var scores = {'국어': 85, '영어': 92, '수학': 78};
>
>   var total = 0;
>   for (var s in scores.values) {
>     total += s;
>   }
>
>   print('평균: ${total / scores.length}'); // 평균: 85.0
> }
> ```
> `/`는 항상 `double`을 돌려줘서 `85`가 아니라 `85.0`이 나와요. (2강 내용!)

---

[[harrison-lectures/04-functions|← 4강]] · 다음 강의: [[harrison-lectures/06-classes|6강. 클래스 →]]
