---
title: 2강. 변수와 타입
description: var, final, const의 차이와 기본 타입, 그리고 Dart의 null safety.
date: 2026-10-12
tags:
  - dart
  - 강의
---

## 변수 만들기

변수는 값을 담아 두는 상자예요. 두 가지 방법으로 만들 수 있어요.

```dart
void main() {
  var name = '해리슨';   // Dart가 알아서 String이라고 판단해요
  String school = '한빛고'; // 타입을 직접 적을 수도 있어요

  name = '민수';          // 값은 바꿀 수 있어요
  // name = 17;           // ❌ 에러! 처음에 String이었으니 숫자는 못 넣어요

  print(name);
  print(school);
}
```

`var`로 만들어도 **처음 넣은 값으로 타입이 정해지고, 그 뒤로는 안 바뀌어요.** 이걸 *타입 추론*이라고 해요.

## 기본 타입

| 타입     | 뜻          | 예시                 |
| -------- | ----------- | -------------------- |
| `int`    | 정수        | `17`, `-3`, `0`      |
| `double` | 소수        | `172.5`, `3.14`      |
| `String` | 문자열      | `'안녕'`, `"Dart"`   |
| `bool`   | 참/거짓     | `true`, `false`      |

```dart
void main() {
  int age = 17;
  double height = 172.5;
  String name = '민수';
  bool isStudent = true;

  print('$name / $age / $height / $isStudent');
}
```

### 나눗셈은 조심!

```dart
void main() {
  print(7 / 2);   // 3.5  → /는 항상 double
  print(7 ~/ 2);  // 3    → ~/는 몫(정수)
  print(7 % 2);   // 1    → %는 나머지
}
```

## `final`과 `const`: 바꿀 수 없는 변수

한 번 정하면 다시는 안 바꿀 값이라면 `var` 대신 `final`이나 `const`를 써요.

```dart
void main() {
  final birthYear = 2009;
  // birthYear = 2010; // ❌ 에러! final은 다시 못 바꿔요

  const pi = 3.14159;
  print(birthYear);
  print(pi);
}
```

|           | 언제 값이 정해져요?                     | 예시                          |
| --------- | --------------------------------------- | ----------------------------- |
| `final`   | 프로그램이 **실행될 때**                | `final now = DateTime.now();` |
| `const`   | 코드를 **쓸 때** 이미 정해져 있어야 함  | `const maxScore = 100;`       |

`DateTime.now()`는 실행해 봐야 알 수 있으니까 `const`로는 못 만들어요. 헷갈리면 **일단 `final`을 쓰세요.** 실제로 Flutter 코드에서는 `final`을 제일 많이 써요.

## 타입 바꾸기

```dart
void main() {
  int a = int.parse('42');        // 문자열 → 정수
  double b = double.parse('3.5'); // 문자열 → 소수
  String c = 42.toString();       // 숫자 → 문자열

  int? d = int.tryParse('abc');   // 실패하면 에러 대신 null
  print('$a $b $c $d');           // 42 3.5 42 null
}
```

## Null safety: "값이 없음"을 다루는 법

`null`은 "아무 값도 없음"이라는 뜻이에요. Dart는 기본적으로 **변수에 null을 못 넣게** 막아요. 실수로 빈 값을 쓰다가 앱이 터지는 걸 막아 주는 거예요.

null이 들어갈 수도 있는 변수는 타입 뒤에 `?`를 붙여요.

```dart
void main() {
  String name = '민수';
  // name = null;       // ❌ 에러! String에는 null 금지

  String? nickname;     // ?가 붙으면 null 허용. 처음 값은 null
  print(nickname);      // null

  // null일 때 쓸 기본값: ??
  print(nickname ?? '별명 없음');   // 별명 없음

  // null이 아닐 때만 접근: ?.
  print(nickname?.length);          // null (에러 안 남)

  nickname = '민트';
  print(nickname.length);           // 2
}
```

| 기호   | 뜻                                                      |
| ------ | ------------------------------------------------------- |
| `T?`   | null이 들어갈 수 있는 타입                              |
| `??`   | 왼쪽이 null이면 오른쪽 값을 써요                        |
| `?.`   | null이면 그냥 null, 아니면 접근해요                     |
| `!`    | "절대 null 아니야!"라고 우기기. 틀리면 앱이 터져요 ⚠️  |

> [!warning] `!`는 최대한 아껴 쓰세요
> `!`는 컴퓨터한테 "내가 책임질게"라고 말하는 거예요. 정말 null이 아닌 게 확실할 때만 쓰고, 보통은 `??`나 `if (x != null)`으로 확인하는 게 안전해요.

## 연습 문제

사용자가 입력한 나이가 문자열 `'abc'`로 들어왔다고 해 볼게요. 숫자로 바꿀 수 있으면 그 숫자를, 못 바꾸면 `0`을 출력하세요. (힌트: `int.tryParse`와 `??`)

> [!example]- 정답 보기
> ```dart
> void main() {
>   var input = 'abc';
>   var age = int.tryParse(input) ?? 0;
>   print(age); // 0
> }
> ```
> `input`을 `'17'`로 바꾸면 `17`이 출력돼요.

---

[[harrison-lectures/01-getting-started|← 1강]] · 다음 강의: [[harrison-lectures/03-control-flow|3강. 조건문과 반복문 →]]
