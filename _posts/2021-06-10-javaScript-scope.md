---
layout: post
title: JavaScript - Scope 이해
tags: [javascript]
author: Hwaryeon Im
comments : True
---


<br><br><br>

### JavaScript : Scope 이해

1. 유효범위(Scope)

   Scope를 직역하면 영역, 범위라는 뜻이다. 하지만 프로그램 언어에서의 유효범위는 어느 범위까지 참조하는지. 즉, 변수와 매개변수(parameter)의 접근성과 생존기간을 뜻한다. 따라서 유효범위 개념을 잘 알고 있다면 변수와 매개변수의 접근성과 생존기간을 제어할 수 있다.<br>

   유효범위의 종류는 크게 두 가지가 있다. 하나는 전역 유효범위(Global Scope), 또 하나는 지역 유효범위(Local Scope)이다. 전역 유효범위는 스크립트 전체에서 참조되는 것을 의미하는데, 말그대로 스크립트 내 어느 곳에서든 참조된다. 지역 유효범위는 정의된 함수 안에서만 참조되는 것을 의미하며 함수 밖에서는 참조하지 못한다.

   ![](/assets/imges/scope.png)

    전역변수 => global_scope

    지역변수 => a_scope_param, local_scope_a,  local_scope_b, local_scope_c

    각각의 지역 변수의 유효범위 =>  a_scope_param, local_scope_a : 함수(Function) A의 중괄호 안의 영역,  local_scope_b : 함수 B의 중괄호 안의 영역,  local_scope_c : 함수 C의 중괄호 안의 영역

   <br>

### JavaScript 유효범위의 특징

- 함수 단위의 유효범위
- 변수명 중복 허용
- var 키워드의 생략
- 렉시컬 특성

```
function scopeTest(){
	var a = 0;
	if(true) {
		var b = 0;
		for (var c = 0; c < 5; c++){
			console.log("c=" + c);
		}
		console.log("c=" + c);
	}
	console.log("b=" + b);
}
scopeTest();
// 실행결과
/*
c = 0
c = 1
c = 2
c = 3
c = 4
c = 5
b = 0
*/
```

[예제 1] **유효범위 설정단위**

위의 코드는 JavaScript의 유효범위가 단위가 블록단위가 아닌 **함수단위로 정의**된다는것을 설명하기 위한 예제 코드이다. 다른 프로그래밍 언어들은 유효범위가 블록단위이기 때문에 중괄호 밖의 범위에서는 그 안의 변수를 사용할수 없지만 <u>JavaScript의 유효범위는 함수 단위이기 때문에 예제코드의 변수 a,b,c 모두 같은 유효범위를 갖는다.</u> 그 결과, 실행화면을 보면 알 수 있듯이 구문 밖에서 그 변수를 참조한다.

```
var scope = 10;
function scopeExam() {
	var scope = 20;
	console.log("scope =" + scope);
}
scopeExam();
// 실행결과
/*
scope = 20
*/
```

[예제 2] **변수 명 중복**

JavaScript는 다른 프로그래밍 언어와는 달리 변수명이 중복되어도 에러가 나지 않는다.<br>

단, <u>같은 변수명이 여러 개 있는 변수를 참조할 때 가장 가까운 범위의 변수를 참조한다.</u> 위의 코드 실행화면을 보면 함수 내에서 scope를 호출했을 때 전역 변수 scope를 참조하는 것이 아니라 같은 함수 내에 있는 **지역변수 scope**를 참조한다.

```
function scopeExam() {
	scope = 20;
	console.log("scope =" + scope);
}
function scopeExam2() {
	console.log("scope =" + scope);
}
scopeExam();
scopeExam2();
// 실행결과
/*
scope = 20
scope = 20
*/
```

[예제 3] **var 키워드 생략**

다른 프로그래밍 언어의 경우 변수를 선언할 때 int나 char와 같은 변수 형을 썼지만, JavaScript는 var 키워드를 사용한다. 또, 다른 프로그래밍 언어의 경우 변수를 선언할 때 변수형을 쓰지 않을 경우 에러가 나지만 JavaScript는 var 키워드가 생략이 가능하다. 단, <u>var 키워드를 빼먹고 변수를 선언할 경우 전역 변수로 선언된다.</u> 위 코드의 실행 결과를 보면 scope라는 변수가 함수 scopeExam 안에서 변수 선언이 이루어졌지만, var 키워드가 생략된 상태로 선언되어 함수 scopeExam2에서 호출을 했을때도 참조한다.

```
function f1(){
	var a = 10;
	f2();
}
function f2(){
	return console.log("호출 실행");
}
f1();

// 실행결과
/*
호출실행
*/

-----------------------------------------------------------------------------
function f1(){
	var a = 10;
	f2();
}
function f2(){
	return a;
}
f1();

// 실행결과
/*
Uncaught Reference Error
:a is not defined
*/
```

[예제 4] **렉시컬 특성**

렉시컬 특성이란 함수 실행 시 유효범위를 함수 실행 환경이 아닌 **함수 정의 환경으로 참조하는 특성**이다. 위의 코드를 봤을 때 함수 f1에서 함수 f2를 호출하면 실행이 된다. 함수 f1, f2 모두 전역에서 생성된 함수여서 서로를 참조할 수 있다. 하지만 아래코드처럼 함수 f1안에서 함수 f2를 호출했다고 해서 f2가 f1안에 들어온 것처럼 f1의 내부 변수  a를 참조할 수 없다. 렉시컬 특성으로 인해서 <u>함수 f2가 실행될 때가 아닌 정의 될때의 환경을 보기 때문에 참조하는 a라는 변수를 찾을 수 없다.</u> 그래서 실행결과는 위와 같이 나온다.

<br>

### 호이스팅(Hoisting)

Hoisting이라는 단어를 직역하면 끌어올리기, 들어 올려 나르기라는 뜻이다. JavaScript에서 호이스팅도 비슷한 의미를 샂고 있다. 간단하게 말해서 JavaScript에서의 호이스팅의 의미는 변수 선언문을 끌어올린다는 뜻으로 이해하면 된다. 

```
function hoistingExam() {
	console.log("value =" + value);
	var value = 10;
	console.log("value =" + value);
}
hoistingExam();

// 실행결과
/*
value = undefined
value = 10
*/

----------------------------------------------------------------------------
function hoistingExam() {
	var value;
	console.log("value =" + value);
	value = 10;
	console.log("value =" + value);
}
hoistingExam();

// 실행결과
/*
value = undefined
value = 10
*/
```

[예제 5] **호이스팅**

위의 코드를 보면 함수 hoistingExam안에서 변수 value의 호출이 두 번 일어난다. 한번은 변수 선언문 전에 또 한번은 변수 선언 후에 호출이 되는데, 다른 프로그래밍 언어의 경우에는 선언문 전에 호출했을때 에러가 난다. 하지만 JavaScript의 경우 호이스팅이 됨으로써 아래쪽 코드와 같은 구동이 이루어진다. 즉, <u>변수 선언문이 유효범위 안의 제일 상단부로 끌어올려 지게 되고, 선언문이 있던 자리에서 초기화가 이루어지는 결과를 갖는 것이다.</u>

```
var value = 30;
function hoistingExam() {
	console.log("value =" + value);
	value = 10;
	console.log("value =" + value);
}
hoistingExam();

// 실행결과
/*
value = undefined
value = 10
*/
```

[예제 6] **호이스팅_2**

그렇다면 위와같은 코드에서는 어떤 결과가 나올까? 다른 프로그래밍 언어에 익숙한 개발자는 변수 value의 첫 호출에서 전역변수가 참조된다고 생각할수 있다. 하지만 JavaScript의 호이스팅으로 인해서 선언 부가 함수 hoistingExam의 최 상단에서 끌어올려 짐으로써 전역변수가 아닌 지역변수를 참조한다. <br> 함수의 호이스팅을 이해할 때는 한 가지만 기억하면 될 것 같다. 바로, 여러가지의 함수 정의 방법 중 **'함수 선언문 방식만 호이스팅이 가능하다.'** 라는 점이다.

```
// 함수 선언문
hoistingExam();
function hoistingExam() {
	var hoisting_val = 10;
	console.log("hoisting_val =" + hoisting_val);
}

// 실행결과
/*
hoisting_val = 10
*/

--------------------------------------------------------------------------
// 함수 표현식
hoistingExam2();
var hoistingExam2 = function() {
	var hoisting_val = 10;
	console.log("hoisting_val =" + hoisting_val);
}

// 실행결과
/*
hoistingExam2 of object is not a function
*/

----------------------------------------------------------------------------
// function 생성자
hoistingExam3();
var hoistingExam3 = new function("","return console.log('Ya-ho!!');");

// 실행결과
/*
hoistingExam3 of object is not a function
*/
```

[예제 7] **함수 호이스팅**

앞서 말하였듯 위의 코드와 실행결과를 보면 <u>함수 선언문 방식만 호이스팅이 제대로 이루어진다.</u>이 결과를 보고 왜 함수 선언문 방식만 호이스팅이 되고 함수 표현 식과 function생성자를 통해 함수를 정의하는 방법은 호이스팅이 되지 않는 이유는 <u>함수 표현식과 function생성자를 통한 함수 정의 방법은 변수에 함수를 초기화 시키기때문에(이를 함수변수라고도 한다) 선언문이 호스팅이 되어 상단으로 올려진다 하더라도 함수가 아닌 변수로써 인지되기 때문이다.</u> 위의 코드에서 함수실행 구문이 아닌 변수를 호출하게 되면 변수의 호이스팅과 같은 undefined란 결과가 나온다.

<br>

### 실행문맥(Execution context)

실행문맥은 간단하게 말해서 실행 정보이다. 실행에 필요한 여러가지 정보들을 담고 있는데 정보란 대부분 함수를 뜻한다. JavaScript는 일종의 콜 스택(Call Stack)을 갖고 있는데, 이 곳에 실행 문맥이 쌓인다. 콜 스택의 제일 위에 위치하는 실행 문맥이 현재 실행되고 있는 실행 문맥이 되는 것이다.

```
console.log("전역 컨텍스트 입니다.");
function func1(){
	console.log("첫 번째 함수입니다.");
}
function func2(){
	func1();
	console.log("두 번째 함수입니다.");
}
func2();

// 실행결과
/*
전역 컨텍스트 입니다.
첫 번째 함수입니다.
두 번째 함수입니다.
*/
```

스크립트가 실행이 되면 콜 스택에 전역 컨텍스트가 쌓인다. 위의 코드에서 함수 Func2의 실행 문구가 나와 함수가 실행이 되면 그 위에 Func2의 실행 컨텍스트가 쌓인다. Func2가 실행되는 도중 함수 Func1이 실행이 되면서 콜 스택에는 Func2 실행 컨텍스트위에 Func1의 실행컨텍스트가 쌓인다. 그렇게 Func1이 종료가 되고 Func2가 종료가 되면서 차례로 컨텍스트들이 스택에서 빠져나오게된다. 마지막으로 스크립트가 종료가 되면 전역 컨텍스트가 빠져나오게 되는 구조이다.

> 그렇다면 실행 문맥은 어떤 구조로 이루어져있고 어떤 과정을 통해 생성이 될까?

<br>

### 실행문맥 생성

실행 문맥은 크게 3가지로 이루어져 있다. 

- 활성화 객체 : 실행에 필요한 여러 가지 정보들을 담을 객체이다. 여러가지 정보란 arguments객체와 변수등을 말한다.
- 유효범위 정보 : 현재 실행 문맥의 유효 범위를 나타낸다.
- this 객체 : 현재 실행 문맥을 포함하는 개체이다.

> 실행문맥 생성 순서
>
> 1. 활성화 객체 생성
> 2. **arguments객체** 생성
> 3. 유효범위 정보 생성
> 4. 변수 생성
> 5. this객체 바인딩
> 6. 실행

**arguments객체**는 함수가 실행될 때 들어오는 <u>매개변수들을 모아놓은 유사 배열 객체</u>이다. <br>**Scope Chain**이 유효범위 정보를 담는 일종의 리스트이며 0번지는 전역 변수 객체를 참조한다.<br>

<br>

### 유효범위 체인(Scope Chain)

유효범위 체인을 간단하게 설명하면 <u>함수가 중첩함수일 때 상위함수의 유효범위까지 흡수하는 것</u>을 말한다.<br>즉, 하위함수가 실행되는 동안 참조하는 상위 함수의 변수 또는 함수의 메모리를 참조하는 것이다. 앞서 실행 문맥 생성에 대해 설명했듯이 함수가 실행될 때 유효범위를 생성하고 해당 함수를 호출한 부모 함수가 가진 활성화 객체가 리스트에 추가된다.<br>

쉽게 말해서 전역 변수 객체, 상 하위 객체 간에 부모/자식 관계가 형성된다고 생각하면 쉽게 이해 할 수 있다. 

<br>

### 클로저(Closure)

클로저는 JavaScript의 유효범위 체인을 이용하여 <u>이미 생명 주기가 끝난 외부 함수의 변수를 참조하는 방법</u>입니다. 외부 함수가 종료되더라도 내부함수가 실행되는 상태면 내부함수에서 참조하는 외부함수는 닫히지 못하고 내부함수에 의해서 닫히게 되어 클로저라 불린다. 따라서 클로저란 **외부에서 내부 변수에 접근할 수 있도록 하는 함수**입니다.

내부 변수는 하나의 클로저에만 종속될 필요는 없으며 외부 함수가 실행 될 때마다 새로운 유효범위 체인과 새로운 내부 변수를 생성한다. 또, 클로저가 참조하는 내부 변수는 실제 내부 변수의 복사본이 아닌 그 내부 변수를 **직접 참조**한다.

```
function outerFunc(){ 
	var a= 0; 
	return { 
		innerFunc1 : function(){ 
			a+=1; 
			console.log("a :"+a); 
		}, 
		innerFunc2 : function(){ 
			a+=2; 
			console.log("a :"+a); 
		} 
	}; 
} 
var out = outerFunc(); 
out.innerFunc1(); 
out.innerFunc2(); 
out.innerFunc2(); 
out.innerFunc1(); 

//실행결과 
/* 
a = 1 
a = 3 
a = 5 
a = 6 
*/

------------------------------------------------------------------------------
function outerFunc(){ 
	var a= 0; 
	return { 
		innerFunc1 : function(){ 
			a+=1; 
			console.log("a :"+a); 
		}, 
		innerFunc2 : function(){ 
			a+=2; 
			console.log("a :"+a); 
		} 
	}; 
} 
var out = outerFunc(); 
var out2 = outerFunc(); 
out.innerFunc1(); 
out.innerFunc2(); 
out2.innerFunc1(); 
out2.innerFunc2(); 

//실행결과 
/* 
a = 1 
a = 3 
a = 1 
a = 3 
*/
```

[예제 8] **클로저의 상호작용, 서로 다른 객체**

클로저의 예제 코드이며 위의 코드는 <u>서로 다른 클로저가 같은 내부 변수를 참조</u>하고 있다는 것을 보여주고 있다. 서로 다른 클로저 innerFunc1과 innerFunc2가 내부 변수 a를 참조하고 a의 값을 바꿔주고 있다. 실행 결과를 보면 **내부 변수 a의 메모리를 같이 공유**한다는 것을 알 수 있다.

아래 코드는 <u>같은 함수를 쓰지만 서로 다른 객체</u>로 내부 변수를 참조하는 모습이다. 외부 함수가 여러 번 실행되면서 서로 다른 객체가 생성되고 객체가 생성될 때 마다 서로 다른 내부 변수가 생성되어 보기엔 같은 내부 변수 a로 보이지만 **서로 다른 내부 변수를 참조**한다.

<br>

### 클로저의 사용이유

클로저를 사용하게 되면 전역변수의 오,남용이 없는 깔끔한 스크립트를 작성 할 수 있다. 같은 변수를 사용하고자 할 때 전역 변수가 아닌 <u>클로저를 통해 같은 내부 변수를 참조하게 되면 전역변수의 오남용을 줄일 수 있다.</u> 또한, 클로저는 JavaScript에 적합한 방식의 스크립트를 구성하고 다양한 JavaScript의 디자인 패턴을 적용할 수 있다. 그의 대표적인 예로 **모듈 패턴**을 말 할 수 있다. 마지막으로 함수 내부의 함수를 이용해 함수 내부변수 또는 함수에 접근 함으로써 JavaScript에 없는 class의 역할을 대신해 <u>비공개 속성/함수, 공개 속성/함수에 접근을 함으로 class를 구현하는 근거이다.</u>

<br>

### 클로저 사용시 주의할 점

**for 문 클로저**는 상위 함수의 변수를 참조할 때 자신의 생성될 때가 아닌 <u>내부 변수의 최종 값을 참조</u>합니다.

```
<body> 
	<script> 
	window.onload = function(){ 
		var list = document.getElementsByTagName("button"); 
		for(var i =0, length = list.length; i<length; i++){ 
			list[i].onclick=function(){ 
			console.log(this.innerHTML+"은"+(i+1)+"번째 버튼입니다"); 
			} 
		} 
	} 
	</script> 
<button>1번째 버튼</button> 
<button>2번째 버튼</button> 
<button>3번째 버튼</button> 
</body> 

//실행결과 
/* 
1번째 버튼은 4번째 버튼입니다 
2번째 버튼은 4번째 버튼입니다 
3번째 버튼은 4번째 버튼입니다 
*/

```

[예제 9] **for문안의 클로저**

위의 코드는 각각의 버튼에 이벤트를 걸어 클릭된 버튼이 몇 번째 버튼인지를 알기 위한 예제다. 하지만, 실행 결과 값은 바라던 결과가 나오지 않는다. 위의 클로저인 클릭 이벤트가 참조 하는 변수 i의 값이 <u>버튼이 클릭될 때의 값이 아닌 for 구문을 다 돌고 난후 i의 값 4를 참조</u>하기 때문에 모두 4라는 결과가 나온다.

```
<body> 
	<script> 
	window.onload = function(){ 
		var list = document.getElementsByTagName("button"); 
		var gate = function(i){ 
			list[i].onclick=function(){ 
			console.log(this.innerHTML+"은"+(i+1)+"번째 버튼입니다"); 
			} 
		} 
		for(var i =0, length = list.length; i<length; i++){ 
			gate(i); 
		} 
	} 
	</script> 
<button>1번째 버튼</button> 
<button>2번째 버튼</button> 
<button>3번째 버튼</button> 
</body> 

//실행결과 
/* 
1번째 버튼은 1번째 버튼입니다 
2번째 버튼은 2번째 버튼입니다 
3번째 버튼은 3번째 버튼입니다 
*/

```

[예제 10] **예제9 해결법 : 중첩클로저**

위의 예제 코드를 통해 중첩 된 클로저를 사용하는 것 만으로 위와 같은 문제를 피하여 원하는 값이 나온다.

**성능문제 클로저가 필요하지 않는 부분에서 클로저를 사용하는 것은 처리 속도와 메모리 면에서 좋은 방법이 아니다.**

```
function MyObject(inputname) { 
	this.name = inputname; 
	this.getName = function() { 
		return this.name;
    }; 
    this.setName = function(rename) { 
    	this.name = rename; 
    }; 
} 
var obj= new MyObject("서"); 
console.log(obj.getName()); 

//실행결과 
/* 
서 
*/

```

[예제 11] **클로저의 오남용**

위의 코드와 같은 함수 내부의 클로저 구현은 함수의 객체가 생성될 때마다 클로저가 생성되는 결과를 가져온다. 같은 구동을하는 클로저가 객체마다 생성이 된다면 쓸데없이 메모리를 쓸데없이 차지하게 되는데, 이를 클로저의 오남용이라고 한다. 클로저의 오남용은 성능 문제 면에서 안 좋은 결과를 가져온다.

```
function MyObject(inputname) { 
	this.name = inputname; 
} 
MyObject.prototype.getName = function() { 
	return this.name; 
}; 
MyObject.prototype.setName = function(rename) { 
	this.name = rename; 
}; 
var obj= new MyObject("서"); 
console.log(obj.getName()); 

//실행결과 
/* 
서 
*/

```

[예제 12] **prototype객체를 이용한 클로저 생성**

클로저를 위의 코드와 같이 prototype객체에 생성하게 되면 객체가 아무리 생성되어도 클로저를 한 번만 생성하고 여러 객체에서 쓸 수 있게 되어 메모리의 낭비를 줄인다.

**this와 arguments객체 클로저를 통해서는 외부함수의 this객체와 arguments객체를 참조하지 못한다.**

```
function f1(){ 
	function f2(){ 
		console.log(arguments[0]); 
	} 
	return f2; 
} 
var exam = f1(1); 
exam(); 

//실행결과 
/* 
undefined
*/

-------------------------------------------------------------------------------
function f1(){ 
	var a= arguments[0]; 
	function f2(){ 
		console.log(a); 
	} return f2; 
} 
var exam = f1(1); 
exam(); 

//실행결과 
/* 
1 
*/

```

[예제 13] **arguments객체 참조**

위의 코드같이 클로저를 통해 arguments객체를 참조하게 되면 undefined라는 실행결과가 나온다. <br>즉, arguments객체는 참조가 불가능하며 굳이 참조하고 싶다면 아래 코드와 같이 새로운 내부 변수에 arguments객체의 값을 넣고 그 변수를 참조 하거나 매개변수를 만들어 매개 변수를 참조해야한다.

```
var a= 20; 
function function1(){ 
	var a= 10; 
	var function2 = new Function("","return a;"); 
	return function2; 
} 
var exam = function1(); 
exam(); 

//실행결과 
/* 
20 
*/

```

[예제 14] **Function생성자로 선언된 클로저**

위의 코드와 같이 클로저가 Function생성자로 생성된 경우 전역에서 생성된 것으로 인지한다. <br>클로저 function2를 통하여 내부 변수 a를 참조하려고 했지만 원했던 결과와 달리 전역 변수 a가 참조 된다.

```
function outerFunc(){ 
	var outer_val = {}; 
	function innerFunc(){ 
		console.log(outer_val); 
	} 
	
	outer_val.values = innerFunc; 
	return innerFunc; 
}
```

[예제 15] **인위적 순환참조**

위의 코드는 의도적으로 클로저의 순환참조를 만든 예제 코드이다. 내부 객체 outer*val의 속성 값 values 에 내부 함수 innerFunc를 참조하게 만들고 내부 함수 innerFunc는 내부 객체 outer*val을 호출 하고 있다. 위와 같은 순환 참조는 서로를 참조 하면서 영원히 끝나지 않는 호출로 인하여 메모리 누수를 야기한다.

```
function outerFunc(){ 
	var outer_val = {}; 
	function innerFunc(){
    	console.log("Hello"); 
    } 
    outer_val.values = innerFunc; 
    return innerFunc; 
}
```

[예제 16] **의도치 않은 순환참조**

그렇다면 위 코드같이 서로를 참조를 하지 않게 되면 순환참조가 끊어질? 아니다. JavaScript의 클로저는 특별한 문법을 쓰지 않고도 암묵적으로 생기는 특성을 가지고 있다. 이는 클로저를 쉽게 만들도록 해주지만 클로저가 사용되는 곳을 사용자가 식별하기 어렵게 만들기도 한다. 그렇게 되면 내부 함수의 innerFunc는 암묵적으로 상위 함수의 내부 객체인 outer_val을 참조하게 되고 이로인해 의도치 않게 순환참조가 만들어진다. 이런 의도치 않은 순환참조는 메모리 누수를 야기한다.

이 같은 의도치 않은 순환참조는 객체가 제거될 때 명시적으로 null값으로 초기화 해 주거나 try-catch-finally구문으로 해결한다. 또는 더 글라스 크락포드가 제시한 purge함수를 쓰게 되면 순환참조를 해결할 수 있다. 아래는 purge함수이다.

```
function purge(d) { 
	var a = d.attributes, i, l, n; 
	if (a) { 
		for (i = a.length - 1; i >= 0; i -= 1) {
        	n = a[i].name; 
        	if (typeof d[n] === 'function') {
            	d[n] = null; 
            } 
        } 
    } 
    a = d.childNodes; 
    if (a) { 
     	l = a.length; 
     	for (i = 0; i < l; i += 1) { 
     		purge(d.childNodes[i]); 
        } 
    } 
}
```

[더글라스 크락포드의 purge함수]

<br>

### 캡슐화

캡슐화란 간단하게 말하면 객체의 자료화 행위를 하나로 묶고, 실제 구현 내용을 외부에 감추는 것이다. 즉, <u>외부에서 볼 때는 실제 하는 것이 아닌 추상화 되어 보이게 하는 것으로 정보은닉에 쓰인다.</u> JavaScript는 이와 같은 캡슐화를 클로저를 사용하여 구현한다.

```
function Gugudan(dan){ 
	this.maxDan=3; 
	this.calculate = function(){ 
		for(var i =1; i<=this.maxDan; i++){ 
			console.log(dan+"*"+i+"="+dan*i); 
		} 
	} 
	this.setMaxDan = function(reset){ 
		this.maxDan = reset; 
	} 
} 
var dan5 = new Gugudan(5); 
dan5.calculate(); 

dan5.maxDan=2; 
dan5.calculate(); 

//실행결과 
/*
5*1=5 
5*2=10 
5*3=15 
5*1=5 
5*2=10 
*/
```

[예제 17] **캡슐화 전**

위의 코드는 JavaScript 캡슐화 하기 전의 코드이다. 내부 변수를 this객체로 바인딩 하여 선언하게 되면 내부 변수 maxDan에 대하여 외부에서 직접 접근이 가능하다. 이런식의 소스코드 구현은 사용자가 임의로 바꿔선 안될 값들이 외부에 공개가 되면서 보안문제에 안좋은 결과를 가져온다.

```
function Gugudan(dan){ 
	var maxDan=3; 
	this.calculate = function(){ 
		for(var i =1; i<=maxDan; i++){ 
			console.log(dan+"*"+i+"="+dan*i); 
		} 
	} 
	this.setMaxDan = function(reset){ 
		maxDan = reset;
    } 
} 
var dan5 = new Gugudan(5); 
dan5.calculate(); 

dan5.maxDan=2; 
dan5.calculate(); 

dan5.setMaxDan(2) 
dan5.calculate(); 

//실행결과 
/* 
5*1=5 
5*2=10 
5*3=15
5*1=5 
5*2=10 
5*3=15 
5*1=5 
5*2=10 
*/
```

[예제 18] **캡슐화 후**

하지만 위의 코드와 같이 var키워드를 통하여 내부 변수를 선언하게 되면 내부 변수 maxDan이 외부에서 직접 접근이 되지 않고 오직 클로저를 통해서만 접근이 가능하다. 즉, 사용자가 임의로 값을 바꿀수 없고 개발자가 만들어놓은 길(클로저)을 통해서만 값을 바꿔 줄수 있다. 아쉬운 점은 캡슐화를 하게되면 클로저를 prototype 맴버로 등록하지 못해 공용 메소드로 사용할 수 있는 이점은 사라진다. 하지만, **별도의 부모 객체를 정의**해서 공용 메소드나 상수 들을 위치시키고 이를 상속받는 방식으로 보완할 수 있다.

