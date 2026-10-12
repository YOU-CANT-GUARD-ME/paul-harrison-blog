---
title: 7강. 비동기 (Future, async, await)
description: 시간이 걸리는 일을 기다리는 법. 앱 만들 때 꼭 필요해요.
date: 2026-10-12
tags:
  - dart
  - 강의
---

## 왜 필요해요?

앱은 시간이 걸리는 일을 자주 해요. 서버에서 데이터 받아오기, 파일 읽기, "0.3초 뒤에 다음 화면으로 넘어가기" 같은 것들이요. 이런 일을 기다리는 동안 앱이 멈춰 버리면 안 되겠죠? 그래서 **"이건 나중에 끝나"**라고 표시하고 다른 일을 계속하는 방법이 필요해요. 이걸 **비동기**라고 해요.

## 순서가 이상해요!

먼저 이 코드를 실행해 보세요.

```dart
void main() {
  print('1');
  Future.delayed(Duration(seconds: 1), () => print('3'));
  print('2');
}
```

결과는 `1, 2, 3`이에요. `Future.delayed`는 "1초 뒤에 실행해 줘"라고 **예약만** 하고 바로 다음 줄로 넘어가요. 그래서 `2`가 먼저 나와요.

## Future: "나중에 올 값"

`Future<String>`은 "**지금은 없지만 나중에 String이 올 거야**"라는 뜻이에요. 택배 송장번호 같은 거예요. 물건(값)은 아직 없지만, 언젠가 도착해요.

## async와 await: 기다리기

```dart
Future<String> drawCard() async {
  await Future.delayed(Duration(seconds: 2)); // 2초 기다리기
  return '오늘의 카드는 태양 ☀️';
}

Future<void> main() async {
  print('카드를 뽑는 중...');
  var result = await drawCard();
  print(result);
  print('끝!');
}
```

```
카드를 뽑는 중...
(2초 뒤)
오늘의 카드는 태양 ☀️
끝!
```

규칙은 세 가지예요.

1. 시간이 걸리는 함수는 반환 타입을 `Future<타입>`으로 써요.
2. 그 함수 안에서 기다리고 싶으면 함수에 `async`를 붙이고,
3. 기다릴 곳 앞에 `await`를 붙여요.

> [!warning] await를 빼먹으면?
> ```dart
> var result = drawCard();   // await 없음!
> print(result);             // Instance of 'Future<String>'
> ```
> 값이 아니라 "송장번호"(Future)가 출력돼요. 값이 이상하게 나오면 `await`를 빼먹었는지 먼저 확인하세요.

## 에러 처리: try / catch

인터넷이 끊기거나 서버가 고장 나면 에러가 날 수 있어요. `try`로 감싸고 `catch`에서 에러를 잡으면 앱이 터지지 않아요.

```dart
Future<int> loadScore() async {
  await Future.delayed(Duration(milliseconds: 500));
  throw Exception('서버 오류');   // 일부러 에러 내기
}

Future<void> main() async {
  try {
    var score = await loadScore();
    print('점수: $score');
  } catch (e) {
    print('에러 발생: $e');   // 에러 발생: Exception: 서버 오류
  }
}
```

## Flutter에서는 이렇게 써요

제가 만든 앱에서 실제로 쓴 코드예요. 시간을 고르고 0.3초 뒤에 다음 페이지로 넘어가요.

```dart
Future.delayed(Duration(milliseconds: 300), () {
  if (mounted) {
    moveToPage(6);
  }
});
```

`if (mounted)`가 중요해요. 0.3초 기다리는 사이에 사용자가 뒤로 가기를 눌러서 화면이 **이미 사라졌을 수도** 있거든요. 사라진 화면을 건드리면 에러가 나니까, 화면이 아직 있는지(`mounted`) 확인하고 실행하는 거예요.

## 연습 문제

1초마다 숫자를 세는 카운트다운을 만드세요. `3`, `2`, `1`을 1초 간격으로 출력하고, 마지막에 `발사! 🚀`를 출력하세요. (힌트: `for` 반복문 안에서 `await Future.delayed(...)`)

> [!example]- 정답 보기
> ```dart
> Future<void> main() async {
>   for (var i = 3; i >= 1; i--) {
>     print(i);
>     await Future.delayed(Duration(seconds: 1));
>   }
>   print('발사! 🚀');
> }
> ```
> `await`가 있어서 반복문이 한 바퀴마다 1초씩 기다려요. `await`를 빼면 숫자가 한꺼번에 출력돼요.

---

## 🎉 수고했어요!

여기까지 왔으면 Dart 기초는 끝이에요. 이제 Flutter 코드를 열어 보면 `final`, 이름 붙인 매개변수, `children: [...]`, `extends StatelessWidget`, `async`/`await` 같은 게 전부 읽힐 거예요.

다음 단계로는 [Flutter 공식 문서](https://docs.flutter.dev/get-started)를 따라 첫 앱을 만들어 보세요. 막히면 언제든 물어보고요!

[[harrison-lectures/06-classes|← 6강]] · [[harrison-lectures/index|강의 목록으로]]
