---
title: 3강. 조건문과 반복문
description: if, switch로 갈림길을 만들고 for, while로 반복해요.
date: 2026-10-12
tags:
  - dart
  - 강의
---

## 비교 연산자와 논리 연산자

조건문을 쓰려면 먼저 "참인지 거짓인지"를 따질 줄 알아야 해요.

| 연산자 | 뜻                | 예시 (결과)            |
| ------ | ----------------- | ---------------------- |
| `==`   | 같다              | `3 == 3` (`true`)      |
| `!=`   | 다르다            | `3 != 4` (`true`)      |
| `>` `<` `>=` `<=` | 크기 비교 | `5 >= 5` (`true`) |
| `&&`   | 그리고 (둘 다 참) | `true && false` (`false`) |
| `\|\|` | 또는 (하나만 참)  | `true \|\| false` (`true`) |
| `!`    | 반대로            | `!true` (`false`)      |

## if / else

```dart
void main() {
  var score = 82;

  if (score >= 90) {
    print('A');
  } else if (score >= 80) {
    print('B');
  } else if (score >= 70) {
    print('C');
  } else {
    print('다시 공부하자!');
  }
}
```

위에서부터 차례대로 검사하고, **처음으로 참인 곳 하나만** 실행해요. 82점이면 `B`만 출력돼요.

### 한 줄로 쓰기: 삼항 연산자

```dart
void main() {
  var score = 55;
  var result = score >= 60 ? '합격' : '불합격';
  print(result); // 불합격
}
```

`조건 ? 참일 때 값 : 거짓일 때 값`. Flutter에서 정말 많이 써요. 예를 들면 `color: isSelected ? Colors.white : Colors.grey`처럼요.

## switch

값 하나를 여러 경우와 비교할 땐 `switch`가 깔끔해요.

```dart
void main() {
  var day = '토';

  switch (day) {
    case '토':
    case '일':
      print('주말이다!');
    case '금':
      print('거의 다 왔다');
    default:
      print('평일...');
  }
}
```

- 내용이 비어 있는 `case`는 다음 `case`와 같이 처리돼요. 그래서 `'토'`와 `'일'` 둘 다 `주말이다!`가 나와요.
- `default`는 어디에도 해당하지 않을 때 실행돼요.

### switch로 값 만들기

값을 골라서 변수에 넣고 싶을 땐 이렇게 쓸 수 있어요.

```dart
void main() {
  var weather = 'rainy';

  var emoji = switch (weather) {
    'sunny' => '☀️',
    'rainy' => '🌧️',
    'snowy' => '❄️',
    _ => '🤷',   // _는 "나머지 전부"
  };

  print(emoji); // 🌧️
}
```

## for 반복문

정해진 횟수만큼 반복할 때 써요.

```dart
void main() {
  for (var i = 1; i <= 5; i++) {
    print('${i}번째 반복');
  }
}
```

`for (시작; 조건; 매번 끝날 때 할 일)` 순서예요.

1. `var i = 1`: `i`를 1로 시작해요.
2. `i <= 5`: 이게 참인 동안 계속 돌아요.
3. `i++`: 한 바퀴 끝날 때마다 `i`에 1을 더해요.

목록에 있는 걸 하나씩 꺼낼 땐 `for-in`이 더 편해요. (목록은 5강에서 자세히 배워요.)

```dart
void main() {
  var members = ['민수', '지연', '철수'];
  for (var name in members) {
    print('안녕, $name');
  }
}
```

## while 반복문

몇 번 돌지 모르고 "조건이 참인 동안" 반복할 때 써요.

```dart
void main() {
  var money = 10000;
  var days = 0;

  while (money > 0) {
    money -= 3000;  // 매일 3000원씩 씀
    days++;
  }

  print('${days}일 만에 용돈 끝');  // 4일 만에 용돈 끝
}
```

> [!warning] 무한 반복 주의
> 조건이 영원히 참이면 반복이 안 끝나요. 반복 안에서 조건이 언젠가 거짓이 되도록 값을 바꿔 주는지 꼭 확인하세요.

## break와 continue

- `break`: 반복을 **완전히** 멈춰요.
- `continue`: 이번 바퀴만 건너뛰고 다음 바퀴로 가요.

```dart
void main() {
  for (var i = 1; i <= 10; i++) {
    if (i == 7) break;          // 7이 되면 끝
    if (i % 2 == 0) continue;   // 짝수는 건너뛰기
    print(i);                   // 1, 3, 5
  }
}
```

## 연습 문제: FizzBuzz

1부터 20까지 출력하는데,

- 3의 배수면 숫자 대신 `Fizz`
- 5의 배수면 `Buzz`
- 3과 5의 공배수(15의 배수)면 `FizzBuzz`

를 출력하세요. 개발자 면접에도 나오는 유명한 문제예요!

> [!example]- 정답 보기
> ```dart
> void main() {
>   for (var i = 1; i <= 20; i++) {
>     if (i % 15 == 0) {
>       print('FizzBuzz');
>     } else if (i % 3 == 0) {
>       print('Fizz');
>     } else if (i % 5 == 0) {
>       print('Buzz');
>     } else {
>       print(i);
>     }
>   }
> }
> ```
> **15를 제일 먼저 검사하는 게 포인트예요.** `i % 3 == 0`을 먼저 검사하면 15에서 `Fizz`만 나오고 끝나 버려요.

---

[[harrison-lectures/02-variables|← 2강]] · 다음 강의: [[harrison-lectures/04-functions|4강. 함수 →]]
