---
title: 4강. 함수
description: 함수를 만들고, 매개변수를 다양하게 받고, 함수를 값처럼 넘겨요.
date: 2026-10-12
tags:
  - dart
  - 강의
---

## 함수가 뭐예요?

함수는 **자주 쓰는 코드에 이름을 붙여 둔 것**이에요. 한 번 만들어 두면 이름만 불러서 몇 번이고 쓸 수 있어요. 사실 1강부터 쓰던 `main`과 `print`도 함수예요.

```dart
int add(int a, int b) {
  return a + b;
}

void main() {
  var result = add(3, 5);
  print(result); // 8
}
```

```
int    add   (int a, int b)
 │      │          │
 │      │          └─ 매개변수: 함수에 넣어 주는 값
 │      └─ 함수 이름
 └─ 반환 타입: 함수가 돌려주는 값의 타입 (없으면 void)
```

`return`을 만나면 그 값을 돌려주고 함수가 끝나요.

## 화살표 함수

한 줄짜리 함수는 `=>`로 짧게 쓸 수 있어요. 아래 두 함수는 완전히 똑같아요.

```dart
int square(int x) {
  return x * x;
}

int square2(int x) => x * x;
```

## 매개변수 받는 3가지 방법

### 1. 기본: 순서대로

```dart
void introduce(String name, int age) {
  print('이름: $name, 나이: $age');
}

void main() {
  introduce('민수', 17);
}
```

### 2. 선택: `[ ]`로 감싸면 생략 가능

```dart
void greet(String name, [String? greeting]) {
  print('${greeting ?? '안녕'}, $name');
}

void main() {
  greet('민수');          // 안녕, 민수
  greet('민수', '반가워'); // 반가워, 민수
}
```

### 3. 이름 붙이기: `{ }`로 감싸기 ⭐

```dart
void makeProfile({required String name, int age = 0, String? school}) {
  print('이름: $name, 나이: $age, 학교: ${school ?? '비공개'}');
}

void main() {
  makeProfile(name: '민수', age: 17);
  makeProfile(age: 16, name: '지연', school: '한빛고'); // 순서 상관없음!
}
```

- 부를 때 `이름: 값` 형식으로 넣어요. 순서는 상관없어요.
- `required`가 붙으면 꼭 넣어야 해요.
- `int age = 0`처럼 기본값을 줄 수 있어요.

> [!tip] Flutter는 거의 다 이 방식이에요
> Flutter 코드를 보면 이런 게 엄청 많아요.
> ```dart
> Text('안녕', style: TextStyle(fontSize: 18, color: Colors.white))
> ```
> `style:`, `fontSize:`, `color:` 전부 **이름 붙인 매개변수**예요. 이걸 이해하면 Flutter 코드가 훨씬 잘 읽혀요.

## 함수를 값처럼 쓰기

Dart에서는 함수도 변수에 담거나 다른 함수에 넘길 수 있어요. 이름 없는 함수(익명 함수)를 바로 만들어서 넘기는 경우가 많아요.

```dart
void repeat(int times, void Function(int) action) {
  for (var i = 1; i <= times; i++) {
    action(i);
  }
}

void main() {
  // 익명 함수를 그 자리에서 만들어서 넘겨요
  repeat(3, (i) {
    print('${i}번째 실행');
  });

  // 한 줄이면 화살표로
  repeat(2, (i) => print('짧게 ${i}번'));
}
```

`void Function(int)`는 "int 하나를 받고 아무것도 안 돌려주는 함수"라는 타입이에요.

> [!tip] 버튼의 `onTap`이 바로 이거예요
> ```dart
> InkWell(
>   onTap: () {
>     print('눌렸다!');
>   },
>   child: Text('버튼'),
> )
> ```
> `onTap`에 "눌렸을 때 실행할 함수"를 넘겨 주는 거예요. 버튼이 눌리면 Flutter가 그 함수를 불러 줘요.

## 연습 문제

점수(`int`)를 받아서 등급(`String`)을 돌려주는 함수 `getGrade`를 만드세요.

- 90 이상: `A`
- 80 이상: `B`
- 70 이상: `C`
- 나머지: `F`

`main`에서 `getGrade(95)`, `getGrade(72)`, `getGrade(40)`을 출력해 보세요.

> [!example]- 정답 보기
> ```dart
> String getGrade(int score) {
>   if (score >= 90) return 'A';
>   if (score >= 80) return 'B';
>   if (score >= 70) return 'C';
>   return 'F';
> }
>
> void main() {
>   print(getGrade(95)); // A
>   print(getGrade(72)); // C
>   print(getGrade(40)); // F
> }
> ```
> `return`을 만나면 함수가 바로 끝나기 때문에 `else`를 안 써도 돼요.

---

[[harrison-lectures/03-control-flow|← 3강]] · 다음 강의: [[harrison-lectures/05-collections|5강. 컬렉션 →]]
