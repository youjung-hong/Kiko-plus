---
layout: post
title: "this와 객체 프로토타입"
description: "You Don't Know JS: this와 객체 프로토타입의 요약 정리입니다."
date: 2017-10-04
tags: [You Don't Know JS, this, 프로토타입, prototype]
comments: true
share: true
---

## 1. this라나 뭐라나

this는 **실행** 시점에 결정된다.

## 2. this가 이런 거로군

this의 바인딩은 함수의 직접적인 **호출부** 에 의해 달라진다.  
일단 호출부를 식별한 경우, 다음 우선순위에 따라 적용한다.

1. new로 호출했다면 새로 생성된 객체
2. call/apply/bind로 호출됐다면 주어진 객체
3. 콘텍스트 객체로 호출했다면 이 객체
4. 기본 바인딩: strict 모드인 경우 undefined, 아니라면 global 객체(브라우저에서는 window)

## 3. 객체

자바스크립트에서는 리터럴 방식과 생성자 방식으로 객체를 생성한다. 생성자 방식보다는 리터럴 방식을 사용하는 것이 좋다.  
객체는 원시 타입 중 한 가지이다. 키/값의 쌍을 모아놓은 저장소이다. 프로퍼티에 접근하면 [[Get]] 연산, 프로퍼티 설정 시에는 [[Put]] 연산이 수행된다.  
프로퍼티는 프로퍼티 서술자를 통해 제어가능한 writable, configurable 등의 속성을 지정할 수 있다. 객체는 Object.preventExtensions(), Object.seal(), Object.freeze()를 통해 여러 단계의 불변성을 적용할 수 있다.  
프로퍼티가 반드시 값을 가져야 하는 것은 아니다. 게터/세터로 '접근자 프로퍼티' 형태를 취할 수 있다.

for..in 루프는 enumerable한 프로퍼티만 노출한다.  
ES6부터는 @@iterator객체의 next() 메서드를 통해 for..of 구문에서 배열, 객체 등에서 여러 값을 순회할 수 있다.  


## 4. 클래스와 객체의 혼합

클래스 패러다임은 디자인 패턴 중 하나이다.
클래스 패턴에서는 Base Class - Specified Class라는 상속 구조로 객체간의 관계를 표현한다. 오버라이드하여 세분화된 동작을 정의한다. 자식 클래스에서 super 키워드를 통해 부모를 클래스를 참조할 수 있다.

클래스 개념은 자바스크립트의 객체 체계와 맞지 않다. class 키워드, extends 등의 키워드를 제공하기는 한다. 그러나 이는 클래스 디자인 패턴에 익숙한 개발자를 위해 자바스크립트를 억지로 고친 syntactic sugar일 뿐이다.  

클래스는 소프트웨어 디자인패턴 중 한가지 옵션일 뿐이니 자바스크립트에서 클래스를 사용할 지 말지는 결국 개발자가 결정할 문제이다.

- - -
### 자바스크립트에서의 클래스 패턴
- 함수를 new 키워드로 호출할 경우, 객체를 반환한다. 관습적으로 대문자로 시작하도록 명명한다.
- 상속받거나 인스턴스화해도 자동으로 **복사** 작업이 일어나지 않음: 상위 객체가 다른 객체에 복사되는 것이 아니라 서로 **연결** 됨
- mixin 패턴: 자바스크립트에서 누락된 클래스 복사 기능을 흉내낸 것
  - Object.extends(Parent), Parent.call(this) 등을 통해 부모 클래스의 프로퍼티, 메서드를 복사한다. 이 때 복사되는 것은 함수 자체가 아닌 **참조값** 이다.

## 5. 프로토타입

### 프로토타입

객체의 프로퍼티를 참조할 경우 [[Get]]이 호출된다. 이 때 동작은 다음과 같다.  
1) 객체에 해당 프로퍼티가 존재하는가? - 존재하지 않으면 2) 수행  
2) [[Prototype]] 체인 내에 해당 프로퍼티가 존재하는가? - 프로토타입 체인이 끝날 때까지 수행, 없으면 undefined를 반환  

객체의 프로퍼티를 설정하면, 해당 객체에 프로퍼티가 추가된다. [[Prototype]] 체인 내에 동일한 이름의 프로퍼티가 있더라도 해당하는 객체에 새 프로퍼티가 추가된다. 이 때 객체에서 해당하는 프로퍼티를 참조할 경우, [[Prototype]] 체인 내에 있는 프로퍼티가 아닌, 새 프로퍼티를 참조하게 된다. 이 경우, [[Prototype]] 체인 내의 기존 프로퍼티는 **가려진다** 라고 하며, shadowed property라고 한다.  

new 키워드를 통해 객체를 생성하면, 객체 내부에 자동으로 [[Prototype]] 프로퍼티가 생기고, 이 프로퍼티는 생성자 함수의 .prototype 객체를 참조한다.  
생성자 함수의 .prototype 객체는 .constructor 프로퍼티를 갖으며, 이는 생성자 함수를 가리킨다. 그런데 자바스크립트에서 이 값은 변경될 수 있다. .prototype 객체를 변경할 경우, .constructor 값을 설정해주어야 하며, 이 값은 enumerable하지 않다.

- - -
### 상속 흉내 내기: 프로토타입 체인  

자바스크립트는 클래스 패턴과 맞지 않지만, 생성자 함수와 프로토타입 체인을 통해 상속을 흉내낼 수 있다.  
이 때 다음과 같은 방식으로 상속을 구현한다.  

```javascript
function Foo(name) {
  this.name = name;
}
Foo.prototype.myName = function() {
  return this.name;
};

function Bar(name, label) {
  Foo.call(this, name); /* 1 */
  this.label = label;
}
Bar.prototype = Object.create(Foo.prototype); /* 2 */
/* BAD
 * Bar.prototype = Foo.prototype;
 * Bar.prototype = new Foo();
 */
Bar.prototype.myLabel = function() {
  return this.label;
};

var a = new Bar('a', 'obj a');
a.myName();
a.myLabel();
```

- `1*` Foo의 생성자를 Bar의 생성자 내부에서 호출한다. 이 때 this 값은 Bar의 객체이다. Bar 객체 내에 Foo의 프로퍼티들이 생성된다.
- `2*` Object.create()를 통해 Foo.prototype를 [[Prototype]]으로 가지는 객체를 생성한다. 생성된 객체를 Bar.prototype 객체로 설정한다. Bar의 객체들은 [[Prototype]] 체인을 통해 Foo와 연결된다.
- `BAD 1` Foo.prototype 객체와 직접 연결되므로, Bar.prototype에 추가한 메서드들은 Foo.prototype에 추가된다. Bar의 수정이 Foo에 직접적으로 영향이 있다.
- `BAD 2` Foo의 메서드뿐만 아니라 프로퍼티(name)도 Bar.prototype가 갖고 있다. name 값은 객체가 개별적으로 갖는 값이다. 어차피 set 과정에서 객체 내에 새 프로퍼티로 추가되므로 prototype을 통해 공통적으로 관리될 필요가 없다. 오히려 메모리가 낭비되므로 좋지 않다.

- - -
### 클래스의 관계 조사

`{객체} instanceof {생성자 함수}`: 객체의 [[Prototype]] 체인 내에 {생성자 함수}.prototype 객체가 있는 지 탐색  
`{객체1}.isPrototypeOf({객체2})`: 객체1의 프로토타입 체인 내에 객체2가 있는 지 탐색  
내부 동작은 다음과 같다
```javascript
Object.prototype.isPrototypeOf = function(obj) {
  function F();
  F.prototype = obj;
  return this instanceof F;
}
```
`Object.getPrototypeOf({객체})`: 객체의 [[Prototype]]을 조회 (ES5 이상)  
`{객체}.__proto__`: (대부분의) 브라우저에서 제공하는 비표준 접근 방법

## 6. 작동 위임

작동 위임 방식은 클래스 상속 방식보다 코드가 더 간결해진다.
```javascript
Task = {
  setID: function(ID) { this.id = ID; }
  outputID: function() { console.log( this.id; }
} /* 1-1 */

// XYZ가 ’Task’에 위임한다.
XYZ = Object.create(Task); /* 1-2 */
XYZ.prepareTask /* 2 */ = function(ID, Label) {
  this.setID(ID);
  this.label = label;
}
XYZ.outputID = function() {
  this.outputID();
  console.log(this.label);
};
```

- `1` Task와 XYZ는 클래스도 함수도 아닌 객체
- `1-2` XYZ를 Task를 [[Prototype]]으로 갖는 객체로 설정
- `2` 생성된 객체에 메서드를 직접 등록, 오버라이드 개념을 위해 동일한 이름을 쓰기보다는 동작을 더 잘 설명할 수 있는 함수명을 사용하면 된다.
