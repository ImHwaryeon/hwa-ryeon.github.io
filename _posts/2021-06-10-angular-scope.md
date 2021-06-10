---
layout: post
title: Angular - Scope와 데이터바인딩
tags: [angular]
author: Hwaryeon Im
comments : True
---


<br><br><br>


### AngularJS : Scope와 데이터바인딩[ $apply, $watch ]

오늘의 주제는 AngularJS에서 가장 기초가 되고 중요한 **scope**다. 앞선 사용자정의 디렉티브 생성시에도 scope 옵션을 설정하는것을 볼 수 있듯이 AngularJS를 접하게 되면 가장 많이 접하게되는것이 scope다. 그리고 scope와 깊은 관계가 있으며 AngularJS를 강력한 도구로 만들어주는 **양방향 데이터바인딩**에 대해 알아보자.

1. **Scope**

   AngularJS에서 <u>Scope는 컨트롤러나 디렉티브의 유효범위내의 저장공간</u>이라고 해석하는것이 이해하기 쉽다. 뷰는 scope를 통해 컨트롤러 내부에 정의된 모델(데이터)이나 핸들러 함수에 접근할 수 있다. 즉 뷰에서는 해당 컨트롤러의 scope에서 <u>모델과 핸들러 함수</u>를 찾게된다. 아래 예제에서는 컨트롤러Ctrl의 scope에 name과 핸들러 함수인 say()를 정의한 소스다.

   [ a.script.js]

   ```
   function Ctrl($scope){
     $scope.name = 'Nextree';
   
     $scope.say = function() {
       $scope.showme = 'Hello ' + $scope.name ;
     }
    };
   ```

   [ b. index.html ]

   ```
   <div ng-controller="Ctrl">
     	My name :
       	<input type="text" ng-model="name">
       	<button ng-click='say()'>click</button>
   	<hr>
     	{{showme}}
   </div>
   ```

   index.html에서는 Ctrl컨트롤러의 scope에 정의된 모델(name)과 핸들러 함수(say)에 접근하고 있다. 이때 사용자가 값을 입력할 때마다  scope의 'name' 프로퍼티에 값이 저장되며 버튼을 클릭할 경우 사용자가 입력한 값이 출력된다.

   <br>

2. **Scope Hierarchies**

   AngularJS에서 scope는 **최상위 root scope**와 **여러개의 child scope**로 구성된다. 즉 <u>scope는 계층구조의 형태를 가진다.</u> <br>AngularJS의 가장 최상위 scope인 "root scope"는 ng-app 디렉티브가 정의된 DOM에 할당된다. 즉 웹페이지 로드 후 AngularJS가 로드될때 root scope가 생성되게 된다. 이후  root scope를 기준으로 컨트롤러나 디렉티브내의 scope(child scope)가 계층구조를 이루어 생성되고 각 scope들은 해당 DOM에 할당된다.

   다음은 scope의 계층구조를 나타내는 소스와 그림이다.

   [ a. index.html ]

   ```
   <html ng-app>                   ---> <html>에 할당된 root scope
   <div ng-controller="Ctrl">      ---> <div>에 할당된 Ctrl의 scope
       <div ng-controller="Ctrl2"> ---> <div>에 할당된 Ctrl2의 scope
       </div>
   </div>
   </html>
   ```

   [ b. index.html내의 scope의 계층구조 ]

   ![scope2](/assets/imges/scope2.png)

   위 그림처럼 HTML의 DOM구조와 마찬가지로 scope도 동일한 계층구조를 가지는것을 확인할 수 있다. 이때 중요한것은 scope는 JavaScript의 Prototypical 상속구조로 인해 단순히 <u>형태만 계층적 구조를 이루는 것이 아닌, 실제로 뷰가 scope내의 모델에 접근하는 방식에 있어서도 계층적 구조를 따르고 있다는 것이다.</u> 즉 뷰에서 모델에 접근 시 **부모 scope를 거슬러 올라갈 수 있다**. <br>

   아래의 예제는 자식 scope에서 부모 scope의 데이터 접근이 가능한 것을 보여주는 예제이다.

   [ c.script.js ]

   ```
   function Ctrl($scope) {         ---> name : Nextree
     $scope.name = 'Nextree';
   }
   function Ctrl2($scope) {        ---> name : 존재안함.
     $scope.year = '2014';
   }
   ```

   [ d.index.html ]

   ```
   <html ng-app>                         
   <div ng-controller="Ctrl>             
       {{name}}</br>               ----> Nextree 출력
       <div ng-controller="Ctrl2">         
            {{name}}               ----> Nextree 출력
       </div>
   </div>
   </html>
   ```

   script.js의 Ctrl2의 scope에는 'name'프로퍼티가 존재하지 않는다. 하지만 자신의 부모 scope를 거슬러 올라가 'name'프로퍼티를 찾아내어 'Nextree' 값을 출력하였다. 즉 scope는 모델(데이터)을 찾을시 <u>자신의 scope에서 먼저 찾게되고, 없다면 부모scope를 거슬러 올라가고 최종적으로 root scope까지 거슬러 올라간다.</u><br>

   이러한 계층적 구조는 유용하기도 하지만 이런 구조의 특성상 사용자정의 디렉티브를 이용해 위젯을 개발하여 사용할 경우 <u>의도치 않은 값을 출력하게 되는 경우가 생기기도 한다.</u> 위젯이라는 것이 단위 컴포넌트 형태로 만들어져 필요한 곳에 삽입되는 용도다. 그러다 보니 어떤 위치에 삽입되어 사용될지 예측할 수 없으므로, **자신만의 scope에서 독립적으로 작동되도록 할 필요가 있다.** 이러한 경우를 위해 AngularJS는 **isolate scope**를 지원함으로써 원치않은 부모 scope에 있는 값을 읽게되어 비정상적으로 작동하는 것을 막아 준다.

   <br>

3.  **Isolate scope**

   isolate scope는 앞서 설명한 scope와 동일하지만, 모델이나 핸들러를 검색할 경우 <u>그 검색이 상위로 진행되지 않는다는 것이 유일한 차이점</u>이다.

   ![](/assets/imges/scope3.png)

   위 그림처럼 isolate scope 역시 부모 scope에게 상속을 받은 형태로 계층구조를 이루고 있다.(parent와 child관계) 하지만, 일반 scope와 달리 isolate scope는 <u>오직 자신의 scope 내에서만 모델과 핸들러를 찾는다.</u> isolate scope를 이용하면 자신만의 독립적인 사용자정의 디렉티브를 만드는데 활용할 수 있다.

   <br>

4.  **데이터 바인딩 ( Data-Binding )**

   AngularJS를 사용하다보면 scope에 할당된 모델(데이타)이 변경되었을 경우 뷰(html)에 변경된 모델이 즉시 업데이트 되는것을 보았을 것이다. 반대로 뷰가 변경(ex : 사용자가 input에 값 입력)되었을 경우에도 뷰와 연결되어있는 스코프의 모델이 변경되게 된다.<br>

   이것이 AngularJS를 사용함에 있어 개발자에게 개발의 편의성과 강력한 능력을 제공해주는 **"양방향 데이터바인딩"** 이다. 이러한 양방향 데이터바인딩을 사용하기 위해서 AngularJS는 매우 간단한 표현식인 **{{ expression }}**을 사용함으로써 해결할 수 있다. 실제 javascript나 jQuery를 사용함에 있어서도 양방향 데이터바인딩을 구현할 수 있지만, 이 경우 <u>첫번째로</u> DOM을 조작하기 위해 특정 DOM에 id 또는 name 속성을 부여해야하며, <u>두번째로</u> javascript 코드에서해당 DOM을 셀렉트하는 작업과 업데이트하는 개발자의 코딩이 필요하게된다. 뷰에 업데이트할 내용이 한 두개라면 번거롭더라도 이러한 작업으로 구현할 수 있지만, 테이블이나 그리드를 표현함에 있어서는 매우 많은 개발비용이 들어가게된다.<br>

   이런 많은 개발비용을 절약하고 개발자에게 편의성을 제공하는 "양방향 데이터바인딩"이 어떻게 구현될 수 있는지 알아보자.

   <br>

   4.1 **angular context**

   일반적으로 브라우저는 사용자의 이벤트에 따라 동작을 하게 된다. 브라우저는 사용자의 이벤트를 기다리고 이벤트 발생시 **callback(자바스크립트 코드)**에서 해당 이벤트를 처리하게 된다. AngularJS에서는 이러한 일반적인 e<u>vents-loop를 확장하여 "angular context" 를 생성</u>하였다.

   <br>

   아래 그림은  AngularJS 공식 홈페이지에 있는 그림으로 위에서 설명한 angular context를 표현한 그림이다.

   ![](/assets/imges/angular-context-300x227.png)

   angular context내에는 **$digest loop**가 존재하는것을 볼 수 있다. 이 $digest loop는 $watch list를 루프 돌며 **$watch**를 통해 모델의 <u>변경을 체크하고 변경되었을 경우 뷰를 업데이트한다.</u>

   <br>

   모델의 변경을 감시하는 감시자의 역할을 하는 **$watch**는 UI와 모델이 바인딩될 경우 생성되어 $watch lit에 추가된다.<br>

   아래는 ng-model로 인해 뷰와 모델이 바인딩되었으므로 2개의 $watch가 생성되었다.

   ```
   // HTML //
   User id : <input type = "text" ng-model = "id" />
   Password : <input type = "password" ng-model = "pwd" />
   ```

   또 다른 예제로  {{ expression }}을 사용하여 뷰와 모델을 바인딩하고 있다. 이 경우 컨트롤러의 내부에 2개의 scope( id, pwd )가 생성되었지만 html에서는 1개(id)의 스코프에 해당하는 {{ expression }}을 사용하였기 때문에 1개의 $watch가 생성된다.

   ```
   // JavaScript //
   app.controller('MainCtrl', function($scope) {
     $scope.id  = "Nextree";
     $scope.pwd = "1234";
   });
   
   // HTML //
   User id : {{id}}
   ```

   이렇게 생성된 $watch는 $watch list에 담기게 된다.

   <br>

   4.2 **$apply()**

   AngularJS의 데이터바인딩이 어떻게 이루어지는지 보자. 위의 angular context 그림에서 확인할 수 있듯이 브라우저는 이벤트를 기다리고, 이벤트가 발생하면 이벤트는  angular context내에 접근하게 된다. ( **이때 $apply()을 통해 angular context에 접근**하는데 중요함으로 꼭 기억하자.)

   <br>

   이후  $digest loop를 호출하여 $watch list내의 각 $watch에게 변경이 일어낫는지 확인을 요청한다.  **dirty-checking**으로 인하여 $watch가 변경되었다면 다시 $digest loop를 호출하여 모든 $watch의 변경을 다시 요청하게 된다. **무한루프**에 빠질 우려가 있으므로 루프는 **최대 10번만 수행**하게 되며 루프를 모두 수행한 후에는 변경이 일어난 $watch에 해당하는 스코프 객체를 뷰에 업데이트 하게 된다.

   <br>

   여기서 주목해야할 것은 <u>이벤트가  $apply()으로 angular context로 접근한다는 것</u>이다. ng-click 등 AngularJS에서 지원하는 이벤트 리스너를 사용할 경우 에는 내부적으로 $apply()를 호출하기 때문에 angular context로 접근이 가능하며 <u>$digest loop를 수행하여 모델의 변경을 감지할 수 있다.</u>

   <br>

   4.3 **3rd-party Library를 이용한 양방향 데이터 바인딩**

   순수 AngularJS를 사용하여 개발한다면 문제가 없겠지만 실제 프로젝트 시에는  jQuery등 3rd-party Library를 사용하기도 한다. 예를 들어 어떤 HTML 엘리먼트에 대해 ng-click이 아니라 jQuery의 이벤트 핸들러를 사용한다면 $apply()가 호출되지 않기 때문에 angular context내부로 접근할 수가 없다. 즉 $watch를 통한 변경을 감지할 수 없게되므로 실제로는 모델이 변경되었다 하더라고 뷰에 업데이트가 되지않는다는것이다. 아래의 예제를 보자.

   ```
   <!DOCTYPE html>
   <html ng-app>
   <head>
      <script src="https:/angular.min.js"></script>
      <script src="http:/jquery.min.js"></script>
   </head>
   
   <script>
   function MainCtrl( $scope )
   {
       $scope.text = "";
       $scope.btnClick = function()
       {
           $scope.text = "Hi AngularJS"; //변경 내용이 화면에 나타남
       }
   
       $( "#btn2" ).click( function()
       {
           $scope.text = "Hi jQuery"; //변경 내용이 화면에 나타나지 않음
       });
   }
   </script>
   
   <body ng-controller="MainCtrl">
       <div>{{text}}</div>
       <input id="btn1" type="button" value="AngularJS" ng-click="btnClick()" />
      <input id="btn2" type="button" value="jQuery" />
   </body> 
   
   </html>
   ```

   btn1은 ng-click 디렉티브를 이용하여 모델과 뷰가 바인딩되었기 때문에 **양방향 데이터바인딩**이 작동하는것을 확인할 수 있다. 하지만 btn2는  jQuery로 click이벤트를 바인딩하였기 때문에 양방향 데이터바인딩이 안되는것을 확인 할 수 있다.

   <br>

   AngularJS에서는  모델값이 변경되면 자동으로 **$apply() ->$digest loop**가 호출되어 모델의 변경을 확인 후 뷰를 업데이트하지만  angular context <u>외부에서 변경( 위의 예제 : jQuery를 이용한 click이벤트 바인딩)이 되었을 경우 직접 $apply()를 호출해줘야한다.</u>

   ```
   $("#btn").click( function()
   {
       $scope.text = "Hi jQuery";  //변경 내용이 화면에 나타남
       $scope.$apply(); //수동으로 호출하여 angular context로 접근함.
   });
   ```

   위의 소스처럼 $apply()를 호출함으로써 angular context내부로 접근하게 하여 양방향데이터바인딩이 가능케한다.

   <br>

   인자를 받지 않는 $apply()는 에러를 캐치하지 못하기 문제점이 있기 때문에 다음과 같이 <u>function을 인자로 받는 $apply(fn)을 사용</u>하자. (내부적으로 try/catch를 하고있기 때문에 exception처리를 가능케한다.)

   ```
   $( "#btn2" ).click( function()
   {
   	$scope.$apply(function(){
           $scope.text = "Hi jQuery";  //변경 내용이 화면에 나타남
       });
   });
   ```

   $apply()는 3rd-party Library를 이용해 데이터바인딩을 구현하기 위한 함수로  순수 AngularJS내에서 $apply()를 사용하면 동작하지 않는다.

   <br>

   4. 4 **$watch()**

      $apply()와 동일한 메카니즘을 가졌지만 **3rd-party Library**가 아닌 AngularJS 내부에서 사용되며 <u>특정 모델의 변경에 대한 함수를 등록</u>할 수 있다.

      ```
      <!DOCTYPE html>
      <html ng-app="app">
      <head>
          <script src="https://angular.min.js"></script>
      </head>
      
      <body ng-controller="MainCtrl">
          이름 : 
          <input ng-model="name" /></br>
          업데이트 횟수 : {{updated}}
      </body>
      
      <script type="text/javascript">
      app = angular.module('app', []);
      
      app.controller('MainCtrl', function($scope) {
        $scope.name = "Nextree";
      
        $scope.updated = -1;
      
        //name이 변경되었을 경우 $watch를 통해 이벤트 조작
        $scope.$watch('name', function() {
          $scope.updated++;
        });
      });
      </script>
      
      </html>
      ```

      위의 소스에서는 **$watch()**를 이용하여 name모델값이 변경될 경우 updated모델값을 +1 증가시키고 있다. <u>$watch는 첫번째 인자로  변경을 감지할 모델의 string값을 받고 2번째 인자로 function을 받는다.</u>