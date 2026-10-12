---
title: 6강. 클래스
description: 나만의 타입을 만드는 클래스. 생성자, getter, 상속까지.
date: 2026-10-12
tags:
  - dart
  - 강의
---

## 클래스가 뭐예요?

지금까지 `int`, `String` 같은 타입을 썼어요. **클래스는 내가 직접 만드는 타입**이에요. "학생"이라는 타입을 만들어서 이름, 학년, 자기소개 기능을 한 덩어리로 묶을 수 있어요.

- **클래스**: 설계도 (붕어빵 틀)
- **객체(인스턴스)**: 설계도로 만든 실제 물건 (붕어빵)

```dart
class Student {
  // 필드: 객체가 가지는 값
  String name;
  int grade;

  // 생성자: 객체를 만들 때 실행돼요
  Student(this.name, this.grade);

  // 메서드: 객체가 할 수 있는 일 (클래스 안의 함수)
  void introduce() {
    print('안녕하세요, ${grade}학년 ${name}입니다.');
  }
}

void main() {
  var minsu = Student('민수', 2);
  var jiyeon = Student('지연', 1);

  minsu.introduce();   // 안녕하세요, 2학년 민수입니다.
  jiyeon.introduce();  // 안녕하세요, 1학년 지연입니다.

  print(minsu.name);   // 민수  ← 점(.)으로 필드에 접근
}
```

`Student(this.name, this.grade)`는 "받은 값을 바로 `name`과 `grade` 필드에 넣어라"라는 뜻의 줄임말이에요.

## 이름 붙인 매개변수로 생성자 만들기

4강에서 배운 `{ }`와 `required`를 생성자에도 쓸 수 있어요. Flutter 위젯이 전부 이렇게 생겼어요.

```dart
class TarotCard {
  final String name;
  final bool isReversed;

  const TarotCard({required this.name, this.isReversed = false});
}

void main() {
  var card = TarotCard(name: '태양', isReversed: true);
  var card2 = TarotCard(name: '별');   // isReversed는 기본값 false

  print(card.name);       // 태양
  print(card2.isReversed); // false
}
```

필드를 `final`로 만들면 한 번 만든 뒤에는 값을 못 바꿔요. 실수로 값이 바뀌는 걸 막아 줘서 안전해요.

## getter: 필드처럼 쓰는 계산값

```dart
class TarotCard {
  final String name;
  final bool isReversed;

  const TarotCard({required this.name, this.isReversed = false});

  String get label => isReversed ? '$name (역방향)' : name;
}

void main() {
  var card = TarotCard(name: '태양', isReversed: true);
  print(card.label); // 태양 (역방향)  ← 괄호 없이 필드처럼 써요
}
```

## 상속: 기존 클래스 물려받기

`extends`를 쓰면 다른 클래스의 필드와 메서드를 그대로 물려받아요. 필요한 부분만 `@override`로 바꿔 쓰면 돼요.

```dart
class Animal {
  String name;
  Animal(this.name);

  void speak() {
    print('$name: ...');
  }
}

class Dog extends Animal {
  Dog(super.name);   // 받은 name을 부모(Animal)에게 넘겨요

  @override
  void speak() {
    print('$name: 멍멍!');
  }
}

class Cat extends Animal {
  Cat(super.name);

  @override
  void speak() {
    print('$name: 야옹~');
  }
}

void main() {
  var animals = [Dog('바둑이'), Cat('나비')];
  for (var a in animals) {
    a.speak();
  }
  // 바둑이: 멍멍!
  // 나비: 야옹~
}
```

> [!tip] Flutter의 `extends StatelessWidget`
> Flutter에서 화면을 만들 때 항상 보는 코드예요.
> ```dart
> class MyScreen extends StatelessWidget {
>   const MyScreen({super.key});
>
>   @override
>   Widget build(BuildContext context) {
>     return Text('안녕');
>   }
> }
> ```
> `StatelessWidget`을 **상속**받고, `build` 메서드를 **override**해서 "내 화면은 이렇게 생겼어"라고 알려 주는 거예요. 이제 이 코드가 읽히죠?

## 이름 앞의 `_`는 "비공개"

```dart
class Counter {
  int _count = 0;          // _로 시작하면 이 파일 밖에서는 못 써요

  void increase() => _count++;
  int get count => _count;  // 읽는 건 getter로만 허락
}
```

`_`로 시작하는 이름은 **같은 파일 안에서만** 쓸 수 있어요. 바깥에서 값을 마음대로 못 바꾸게 막을 때 써요. Flutter의 `_MyScreenState` 같은 이름도 같은 이유예요.

## 연습 문제

`BankAccount` 클래스를 만드세요.

- `owner`(String)와 `balance`(int) 필드
- `deposit(int amount)`: 잔액을 늘려요
- `withdraw(int amount)`: 잔액이 충분하면 줄이고, 부족하면 `잔액 부족!`을 출력해요

> [!example]- 정답 보기
> ```dart
> class BankAccount {
>   final String owner;
>   int balance;
>
>   BankAccount({required this.owner, this.balance = 0});
>
>   void deposit(int amount) {
>     balance += amount;
>   }
>
>   void withdraw(int amount) {
>     if (amount > balance) {
>       print('잔액 부족!');
>       return;
>     }
>     balance -= amount;
>   }
> }
>
> void main() {
>   var account = BankAccount(owner: '민수');
>   account.deposit(10000);
>   account.withdraw(3000);
>   print(account.balance); // 7000
>   account.withdraw(50000); // 잔액 부족!
> }
> ```

---

[[harrison-lectures/05-collections|← 5강]] · 다음 강의: [[harrison-lectures/07-async|7강. 비동기 →]]
