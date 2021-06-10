---
layout: post
title: Javascript - 함수(function) 다시보기
tags: [javascript]
author: Hwaryeon Im
comments : True
---


<br><br><br>

## Javascript : 함수(function) 다시보기

<u>자바스크립트에서 함수는 fisrt-class object다.</u>

그렇다면 **first-class object**란 무엇일까?
 - 변수에 저장할 수 있어야 한다
 - 함수의 파라미터로 전달할 수 있어야 한다
 - 함수의 반환값으로 사용할 수 있어야 한다
 - 자료 구조에 저장할 수 있어야 한다

또한, 함수는 변수의 스코프를 결정하고 private 변수 또는 메소드 뿐만 아니라 함수의 특징을 이용하여 public 속성과 메소드를 제공하며 자바스크립트 모듈을 작성하는 좋은 도구이다.

함수에 대한 올바르고 정확한 이해는 자바스크립트를 이해하는 데 있어 핵심 중의 하나이며 
대규모 웹 애플리케이션이나 Single Page Applications을 개발하는데 있어 가장 중요한 개념이 된다.

<br>

### 함수와 익명함수

자바스크립트에서 함수를 정의하는 방법은 Function 객체를 사용하는 방법과 <br>연산자인 function을 사용하는 방법이 있다.<br>일반적으로 Function 객체를 사용한 정의 방법은 많이 사용되지는 않는다.
연산자인 function을 이용한 함수 정의 방식은 **함수선언문**과 **함수표현식**으로 나뉜다.

함수를 정의할 때 <u>"함수는 fisrt-class object이므로 변수에 할당될 수 있다."</u> 라는 전제 하에 <br>아래와 같이 작성할 수 있다.

```
//기명 함수표현식
var company = function company() {
 /* 실행코드 */
};

//익명 함수표현식
var company = function() {
 /* 실행코드 */
};

//기명 즉시실행함수
(function company() {
 /* 실행코드 */
}());

//익명 즉시실행함수 - 더글락스 클락포트의 권장 표기법
(function() {
 /* 실행코드 */
}());

//익명 즉시실행함수
(function() {
 /* 실행코드 */
})();
```
이렇게 정의한 방식을 **함수표현식**이라고 한다. 함수가 변수에 할당되었으므로 <u>"함수는 객체이다."</u>
라는 정의가 가능하다. 함수표현식은 함수선언식과는 달리 스크립트 로딩 시점에 VO에 함수를
저장하지 않고 <u>runtime시에 해석되고 실행되므로</u> 이 두가지를 구분하는 것은 중요하다.

> 함수선언식으로 함수를 정의하면 사용하기에 쉽지만 대규모 애플리케이션을 개발하는 경우 인터프리터가 너무 많은 코드를 VO에 저장하므로 애플리케이션의 응답속도는 현저히 떨어질 수 있으므로 주의해야한다. 참고로, <u>스크립트 파일을 모듈화하고 이를 필요한 시점에 비동기 방식으로 로딩하여 http 요청을 줄이고 응답속도와 사용자 체감속도를 향상시킬 수 있다.</u>

<br>

### 즉시실행함수

자바스크립트에서 가장 큰 문제점 중의 하나는 <u>글로벌 스코프에 정의된 것은 코드 내의 어디서든지</u>
<u>접근이 가능하다는 것.</u> 하지만, 외부에 공유되면 안되거나 공유될 필요가 없는 속성이나 메소드가 있다.<br>
또한, 다른 스크립트 파일 내에서 동일한 이름으로 명명된 변수나 함수가 있을 경우 원치 않는 결과를
가져올 수 있다.

변수의 스코프를 포함하는데 사용되며(변수의 접근 범위가 함수 내부가 아닌 외부에서도 가능해짐) 외부에서 함수 내의 변수에 접근할 경우 이를 통제할 수 있다.
즉시실행함수는 글로벌 네임스페이스에 변수를 추가하지 않아도 되기 때문에 코드 충돌이 없이
구현할 수 있어 플러그인이나 라이브러리 등을 만들때 많이 사용된다.

<br>

### 모듈 패턴(Module Pattern)

자바스크립트는 언어레벨에서 **캡슐화를 위한 접근제어자(private, public 등)**, 모듈 간의 구분을 위한
**package**가 명식적으로 제공되지 않는다. 하지만 명시적으로 제공되지 않는 이런 지원도구들을 자바스크립트 함수의 특징을 이용하여 유사하게 제공할 수 있다.

즉시실행함수는 우리가 작성한 코드들 뿐만 아니라 함께 사용하는 외부 라이브러리와도
충돌없이 구동하는 **샌드박스(sandbox)**를 제공한다. 이 특징과 단위기능별로 작성된 코드를 분리된 개별 파일 형태로 유지한다면 모듈화 조건을 해결할 수 있다.

```
var clerk = (function() {
   var name = 'Teo';
   var sex = '남자'; 
   var position = '엔지니어';
   // salary private
   var salary = 2000;
   var taxSalary = 200;
   var totalBouns = 100;
   var taxBouns = 10;

   var payBouns = function() {
        totalBouns = totalBouns - taxBouns;
        return totalBouns;	
   };
   var paySalary = function() {
        return salary - taxSalary;
   };

   // Public 속성, 메소드
   return {
       name: name,
       sex: sex,
       position: position,
       paySalary: patSalary,
       payBonus: payBonus
    };
}());

// name 속성은 public
console.log(clerk.name); // 'Teo' 출력

// salary 변수는 즉시실행함수 내부 변수이므로 private
console.log(clerk.salary); // undefined 출력

// paySalary 속성은 public
console.log(clerk.paySalary()); // 1800 출력

// payBonus 속성은 public
console.log(clerk.payBonus()); // 90 출력
console.log(clerk.payBonus()); // 80 출력
```
위에 작성된 **즉시실행함수**는 name, sex, position 속성과 payBouns메소드를 가진 객체를 clerk변수에 반환한다. 다시말해 즉시실행함수가 clerk변수에 저장되는 것이 아니라 <u>즉시실행함수의 반환값이 clerk변수에 저장된다.</u>  name, sex, position 변수를 글로벌 스코프에 추가하지 않고 단지 clerk변수만 추가되었다. 애플리케이션의 규모가 커지면 글로벌 변수에 대한 충돌을 고려해야 하므로 글로벌 변수의사용을 자제해야 한다.

또한,  name, sex, position 속성은 clerk변수에 저장된 객체의 속성이므로 외부에서 접근이 가능(public)하지만 salary, totalBonus변수는 즉시실행함수 내의 변수이므로 외부에서 접근할수 없다.(private)

paySalary()메소드를 호출하여 지금된 급여인 1800을 반환값을 가져올수 있고,<br>
payBonus()메소드를 호출하여 지급된 상여금인 90을 반환할 수 있으며<br>
payBonus()메소드를 한번 더 호출하여 80을 반환 할 수 있다.<br>

여기서 주목해야할 부분은 payBonus()메소드를 호출할 때마다 totalBonus 변수에 저장된 값이
업데이트 되지만 <u>paySalary()메소드는 여러번 호출해도 salary 변수에 저장된 값은 업데이트되지 않는다.</u>

**즉시실행함수**를 위와 같이 사용하여 언어레벨에서 제공하지 못하는 모듈화 지원도구를 극복할 수 
있으며 이렇게 작성된 코드를 분리된 파일로 구성하면 재사용성을 높일 수 있다.



#### 자바스크립트 모듈 작성시 코드 순서

1. 모듈 스코프 내에서 사용할 변수 작성
2. 유틸리티 메소드 작성
3. DOM 조작 메소드 작성
4. 이벤트 핸들러 작성
5. Public 메소드 작성