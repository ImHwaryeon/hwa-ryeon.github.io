---
layout: post
title: Angular - 사용자정의 디렉티브
tags: [angular]
author: Hwaryeon Im
comments : True
---


<br><br><br>

### AngularJS: 사용자정의 디렉티브(directive) 이야기

AngularJS에서는 이미 ngBind, ngModel등 built-in된 다양한 디렉티브를 다루고 있으며, 사용자가 AngularJS의 controller나 service를 새롭게 생성하는 것처럼 <u>사용자 정의 디렉티브를 마음껏 생성하고 확장 할 수 있다.</u>  AngularJS는 템플릿으로 HTML을 지원하기 때문에 사용자 정의 디렉티브를 HTML에서 매우 쉽게 적용할 수 있다는 매력을 가지고  있다. 이제부터 AngularJS의 디렉티브가 무엇인지, 또 어떻게 사용자정의 디렉티브를 생성하는지에 대해 알아보자.

1. **디렉티브**란?

   쉽게 말해 <u>"AngularJS의 HTML Compiler에 의해 해석된 특정한 행위의 기능을 가진 DOM 엘리먼트</u>"다.

   디렉티브를 이해하고 생성하기 앞서 사전지식으로 **HTML Compiler**에 대해 알아두어야할 필요가 있다. built-in된, 또는 사용자가 새롭게 생성한 사용자 정의 디렉티브를 HTML에서 사용할 수 있는 이유는 AngularJS의  <u>HTML Compiler가 HTML의 DOM을 돌면서 디렉티브 이름과 같은 DOM 엘리먼트를 찾아내기 때문이다.</u> AngularJS의 HTML Complier의 절차는 다음의 2 단계로 축약할 수 있다

   - **compile 단계** : HTML의 DOM 엘리먼트들을  돌면서 디렉티브를 찾는다. (attribute name, tag name, comments, class name을 이용하여 디렉티브를 매칭시킨다.)  결과로 **link function**을 리턴한다.

   - **link 단계** : 디렉티브와 HTML이 상호작용(동적인 view) 할 수 있도록 디렉티브에 **event listener**를 등록하며 scope와 DOM 엘리먼트간에 <u>2-way data binding을 위한</u> **$watch를 설정**한다.<br>

     위의 HTML Compiler의두 단계를 거쳐 HTML에서 디렉티브를 사용할 수 있게 된다.

     <br>

2. **디렉티브 작명법**

   - Javascript에서 AngularJS의 디렉티브 생성 시 디렉티브 이름은 **camelCase**작명법을 따라 작성. (ex: testDirective)
   - HTML에서 AngularJS의 디렉티브를 사용 시 '-'를 이용한 **snake-case** 작명법으로 사용. (ex: test-directive)

   [HTML에서 디렉티브 사용 예]

   ```
   <my-example></my-example>
   <my:example></my:example>
   <my_example></my_example>
   ```

   [Javascript에서 디렉티브 생성 예]

   ```
   angular.module.(....)
      .directive('myExample', function() {
      -- [생략] directive 내용 작성 --
      )};
   ```

   위처럼  HTML에서는 snake-case 작명법이 아닌 ':', '_'등 여러가지 방법으로 디렉티브를 사용할 수 있으며 모두 동일한 결과를 나타낸다. 하지만 <u>snake-case를 이용한 사용법을 선호한다.</u>

   <br>

3. **사용자정의 디렉티브 생성방법**

   아래는 AngularJS의 디렉티브를 생성하기 위한 **pseudo-code** 템플릿으로 아래의 방식으로 사용자정의 디렉티브를 생성할 수 있다. 디렉티브는 여러가지 rule을 가지고 있으며 사용자정의 디렉티브를 생성하기 위해서는 다음의 **디렉티브 규칙**(directive's rule)을 반드시 숙지해야한다. 각 규칙의 역할과 사용방법에 대해서 살펴보자

   ```
   var myModule = angular.module(...);
   myModule.directive('directiveName', function (injectables) {
     return {
       restrict: 'A',
       template: '&lt;div&gt;&lt;/div&gt;',
       templateUrl: 'directive.html',
       replace: false,
       priority: 0,
       transclude: false,
       scope: false,
       terminal: false,
       require: false,
       controller: function($scope, $element, $attrs, $transclude, otherInjectables) { ... },
       compile: function compile(tElement, tAttrs, transclude) {
         return {
           pre: function preLink(scope, iElement, iAttrs, controller) { ... },
           post: function postLink(scope, iElement, iAttrs, controller) { ... }
         }
       },
       link: function postLink(scope, iElement, iAttrs) { ... }
     };
   });
   ```

   3.1 **restrict**

   HTML에서 디렉티브를 사용하기 위한 <u>DOM 엘리먼트의 속성을 설정.</u>

   - restrict 규칙을 생략할 경우는 <u>'A'로 선언한 것과 같은 효과</u>를 나타내며 종류는 다음과 같다.

     ![](/assets/imges/restrict.png)

     [index.html]

     ```
     <!doctype html>
     <html ng-app="exampleDirective>
     <head>
         <script src="http://angular.min.js"></script>
         <script src="script.js"></script>
     </head>;
       <body>
         <div ng-controller="Ctrl">
           <my-example></my-example>
         </div>
       </body>;
     </html>
     ```

     [script.js]

     ```
     angular.module('exampleDirective', [])
       .controller('Ctrl', function($scope) {
         $scope.person = {
           name: 'nextreeMember',
           address: 'Gasan'
         };
       })
       .directive('myExample', function() {
         return {
           restrict: 'E',
           template: 'Name: {{person.name}}</br> Address: {{person.address}}'
         };
     });
     ```

     script.js에서 restrict를 'E'로 설정하였으므로 index.html에서 형태로 사용할 수 있다.

     <br>

     3.2 **template**

     html의 디렉티브를 사용한 부분에 보여줄 내용으로 In-Line value를 설정.

     [index.html]

     ```
     <!doctype html>
     <html ng-app="exampleDirective">
     <head>
        <script src="http://angular.min.js"></script>
        <script src="script.js"></script>
       </head>
       <body>
         <div ng-controller="Ctrl">
           <my-example></my-example>
         </div>
       </body>
     </html>
     ```

     [script.js]

     ```
     angular.module('exampleDirective', [])
     .controller('Ctrl', function($scope) {
         $scope.person = {
           name: 'nextreeMember',
           address: 'Gasan'
         };
       })
       .directive('myExample', function() {
         return {
           restrict: 'E',
           template: 'Name: {{person.name}} </br> Address: {{person.address}}'
         };
     });
     ```

     ```
     <결과>
     Name : nextreeMember 
     Adress : Gasan
     ```

     위의 예제를 보면  'myExample' 디렉티브는 In-Line value인 template를 리턴하고 있다. template의 내용이 많아지면 많아질수록 소스코드를 어지럽히기 때문에 template의 내용이 매우작지 않은 이상 templateUrl option를 이용하여 <u>template를 HTML파일로 분리시키는것을 지향한다.</u>

     <br>

     3.3 **templateUrl**

     template을 <u>별도의 html 파일로 관리.</u><br>

     - templateUrl에 선언한 url에 해당하는 HTML을 로드한다. 이때 **index.html 위치를 기준으로** 로드할 html의 상대위치를 정의한다.

       [index.html]

       ```
       <!doctype html>
       <html ng-app="exampleDirective">
         <head>
          <script src="http://angular.min.js"></script>
           <script src="script.js"></script>
         </head>
         <body>
           <div ng-controller="Ctrl">
             <my-example&gt;&lt;/my-example>
           </div>
         </body>
       </html>
       ```

       [script.js]

       ```
       angular.module('exampleDirective', [])
        .controller('Ctrl', function($scope) {
           $scope.person = {
             name: 'nextreeMember',
             address: 'Gasan'
           };
         })
         .directive('myExample', function() {
           return {
             templateUrl: 'my-example.html'
           };
       });
       ```

       [my-example.html]

       ```
       Name: {{person.name}} </br> Address: {{person.address}}
       ```

       templateUrl을 사용하여 my-example.html을 로드하였다. 만약 로드할 html과 index.html의 <u>위치가 다르다면 상대경로를 포함한 경로를 지정해준다.</u>

       <br>

       3.4 **replace**

       디렉티브를 사용한 HTML의 태그에 template 또는 templateUrl에 포함된 태그 내용을 추가할지 교체할지 설정.<br>

       - **true**로 설정할 경우  HTML의 디렉티브를 사용한 태그를 template 또는 templateUrl에 작성된 내용으로  교체한다.

         [index.html]

         ```
         <!doctype html>
         <html ng-app="exampleDirective">
           <head>
            <script src="http://angular.min.js"></script>
            <script src="script.js"></script>
           </head>
           <body>
             <div ng-controller="Ctrl">
               <my-example></my-example>
             </div>
           </body>
         </html>
         ```

         [script.js]

         ```
         angular.module('exampleDirective', [])
         .controller('Ctrl', function($scope) {
             $scope.person = {
               name: 'nextreeMember',
               address: 'Gasan'
             };
           })
           .directive('myExample', function() {
             return {
         	  restrict: 'E',
               template: '&lt;div&gt;Hello AngularJS</div>',
         	  replace: true
             };
         });
         ```

         replace를 true로 설정하였기 때문에  index.html의 로 작성된 부분이Hello AngularJS로 대체된다.

         3.5 **priority**

         디렉티브 별로 **compile()**과 **link()**의 호출 우선 순위를 지정. (기본값은 0)<br>

         - **priority 값이 클 수록** 우선순위가 높고 먼저 호출된다.

         <br>

         3.6 **transclude**

         ng-transclude를 이용하여 template 또는 templateUrl에서 디렉티브내의 원본내용을 포함시킬지 설정.<br>

         - **true**로 설정 시 디렉티브에 포함된 원본내용을 template의 ng-transclude를 사용한 곳으로 포함한다.

         [index.html]

         ```
         <!doctype html>
         <html ng-app="exampleDirective">
           <head>
           <script src="http:///angular.min.js"></script>
             <script src="script.js"></script>
           </head>
           <body>
             <div ng-controller="Ctrl">
               <my-example>Hello AngularJS</my-example>
             </div>
           </body>
         </html>
         ```

         [script.js]

         ```
         angular.module('exampleDirective', [])
          .controller('Ctrl', function($scope) {
             $scope.person = {
               name: 'nextreeMember',
               address: 'Gasan'
             };
           })
           .directive('myExample', function() {
             return {
               restrict: 'E',
               template: <div>Name: {{person.name}} </br> Address: {{person.address}} </br> <span ng-transclude></div>',
         	  transclude : true
             };
         });
         ```

         ```
         < 결과 >
         Name : nextreeMember
         Adress : Gasan
         Hello AngularJS
         ```

         transclue : true로 설정하였으므로 template에서  부분에 원본내용인 'Hello AngularJS'를 포함한다

         <br>

         3.7 **scope**

         디렉티브의 scope를 설정.

         <br>

         3.7 .1 **scope 옵션**

         - scope : **false** -&gt; 새로운 scope 객체를 생성하지 않고 부모가 가진 같은 scope 객체를 공유. (<u>default 옵션</u>)
         - scope : **true** -&gt; 새로운 scope 객체를 생성하고 부모 scope 객체를 상속.
         - scope: { ... } -&gt; isolate/isolated scope를 <u>새롭게 생성.</u>

         scope: { ... }는 재사용 가능한 컴포넌트를 만들 때 사용하는데 컴포넌트가 parent scope의 값을 read/write 못하게 하기 위함이다.<br>parent scope에 접근(access) 하고 싶을 경우 Binding 전략(=, @, &amp;)를 이용한다.

         <br>

         3.7.2 **Binding 전략**

         - =   :   부모 scope의 property와 디렉티브의 property를 data binding하여 부모 scope에 접근

         - @  :   디렉티브의 attribute value를 {{}}방식(interpolation)을 이용해 부모 scope에 접근

           <br>

           ['=' 를 이용한 예]

           ```
           <div ng-controller="Ctrl">
                 <nextree-directive company-info="nextree"></nextree-directive>
           </div>
           ```

           ```
           angular.module('testDirective', [])
             .controller('Ctrl', function($scope) {
               $scope.nextree = { name: 'Nextree'};
             })
             .directive('nextreeDirective', function() {
               return {
                 restrict: 'E',
                 scope: {
                   myCompany: '=companyInfo'
                 },
                 template: 'Name:{{myCompany.name}}'
               };
             });
           ```

           1. 부모 scope의 nextree에 접근하기 위하여 HTML에서는 'company-info' attribute에 부모 scope의 data인 nextree를 세팅하였다.

           2. 디렉티브 선언부에서는 **scope: {..}**옵션을 사용하여 isolate scope를 생성하고 Binding전략인 **'='**를 이용하여 template에서 사용할 local 변수명을 myCompany로 지정하였다.

           3. 이후 template에서는 scope 옵션에 설정한 myCompany로 부모 scope의 data 접근이 가능하다.

                 <br>

                 [@를 이용한 예]

                 ```
                 <div ng-controller="Ctrl">
                 <nextree-directive company-info={{name}}>
                 {{locate}}
                 </nextree-directive>
                 </div>
                 angular.module('testDirective', [])
                   .controller('Ctrl', function($scope) {
                 $scope.name = 'Nextree';
                 $scope.locate = 'Gasan';
                   })
                   .directive('nextreeDirective', function() {
                 return {
                   restrict: 'E',
                   scope: {
                     name: '@companyInfo'
                   }
                 };
                   });
                 ```

                 **@방식**은 scope의 내용을 string으로 치환한것과 같은 효과를 가진다. (  company-info='Nextree' )

                 <br>

         3.8 **controller()**

         다른 디렉티브들과 **통신**하기 위한 역할을 하는 controller명칭을 정의.

         - controller() 내부에서는 **$scope**와 **this** 를 사용하여 data 및 function을 정의

         - this로 정의된 data 및 function은 3.9의 'require' rule을 사용하여 다른 디렉티브에서 엑세스 할 수 있게 한다.

           <br>

         3.9 **require**

         AngularJS의 다른 컨트롤러나 디렉티브의 controller()에 **this**로 정의된 function을 사용할 때 선언.

         -  require에 컨트롤러 이름을 설정하면 해당 컨트롤러를 주입받게 된다. 이후 디렉티브의 link()내에서 주입받은 컨트롤러의 this로 선언된 모든 function을 사용할 수 있다.

           <br>

         3.9.1 **require 추가 옵션**

         - ? 를 디렉티브이름 앞에 추가 시 매칭되는 디렉티브가 없어도 에러가 발생안함.

         - ^ 를 추가 시 DOM 엘리먼트들을 거슬러 올라가면서 해당 디렉티브를 찾음.

           <br>

         3.10 **compile()**

         DOM 엘리먼트를 해석하여 디렉티브로 변환하며 두 종류의 link function을 리턴.

         <br>

         3.10.1 **리턴되는 link function 종류**

         - **preLink()**  :   compile phase가 실행되고 child 엘리먼트가 link 되기 전에 호출

         - **postLink()**  :   compile phase가 실행되고 child 엘리먼트가 link 된 후 호출 ( 따라서, DOM 구조를 변경하기 위해서는 postLink()를 이용. )

           <br>

         3.11 **link()**

         2-way data binding을 위해 해당 디렉티브 DOM엘리먼트의 event  listener를 등록. ( 디렉티브의 대부분의 로직을 여기에 선언하며 **postLink()**만 지원.)

         [index.html]

         ```
         <!doctype html>
         <html ng-app="appModule">
         	<head>
         		<script src="angular.min.js"></script>
         		<script src="script.js"></script>
         		<link rel="stylesheet" href="table.css" type="text/css">
         	</head>
         	<body ng-controller='Ctrl'>
         		<my-title>
         			<my-content class='table'
         				ng-repeat='item in items'
         				item-title='item.title'>
         				{{item.content}}
         			</my-content>
         		</my-title>
         	</body>
         </html>
         ```

         [script.js]

         ```
         angular.module('appModule', [])
            .controller('Ctrl', function($scope) {
               $scope.items = [
                   {title: 'What is Directive?',
                    content: '특정한 행위의 기능을 가진 DOM엘리먼트.'},
                   {title: 'Custom Directive',
                    content: '디렉티브를 직접 생성해보십시오.'},
                   {title: 'Bye~',
                    content: '디렉티브 이야기를 마치겠습니다.'}
               ];
            })
            .directive('myTitle', function() {
                return {
                   restrict: 'E',
                   replace: true,
                   transclude: true,
                   template: '<div ng-transclude></div>',
                   controller: function() {
                      var items = [];
                      this.addItem = function(item) {
                         items.push(item);
                      }
                  }
               };
            })
            .directive('myContent', function(){
                return {
                    restrict: 'E',
                    replace: true,
                    transclude: true,
                    require: '^?myTitle',
                    scope: { title:'=itemTitle' },
                    template : '<div>' +
                               '<div class="title" ng-click="click()">{{title}}</div>' +
                               '<div class="body" ng-show="showMe" ng-transclude></div>' +
                               '&lt;/div>',
                    link: function(scope, element, attrs, controller) {
                        scope.showMe = false;
                        controller.addItem(scope);
                        scope.click = function click(){
                           scope.showMe = !scope.showMe;
                        }
                    }
                };
            });
         ```

         [table.css]

         ```
         .table {
         border: 1px solid black;
         text-align: center;
         vertical-align: middle;
         width: 400px;
         }
         .table &gt; .title {
         background-color: #F5AF64;
         text-align: center;
         color: black;
         padding: .1em .3em;
         cursor: pointer;
         }
         .table &gt; .body {
         background-color: #FFE4E1;
         padding: .1em .3em;
         }
         ```

         ![](/assets/imges/directive2.png)

         위의 예제는 각 **row**를 클릭하면 하위의 내용을 show/hide하는 기능의 디렉티브다. 디렉티브 규칙 중 <u>controller와 require, link 규칙을 활용</u>하여 **디렉티브와 디렉티브간의 통신**을 보여주는 좋은 예제다. myContent 디렉티브의 **require 옵션**을 이용하여 myTitle 디렉티브를 주입받은 다음 자신의 **link()**내에서  주입받은 myTitle디렉티브의 **controller()**에 **this**로 정의한 addItem()를 사용하고 있다. 이처럼  앞에서 살펴본 디렉티브 규칙을 잘 활용한다면 무한하게 사용자 디렉티브를 확장할 수 있다.

         <br>

         **[compile()과 link() 분리 목적 및 차이점]**

         앞에서 설명하였듯 **compile()**은 AngularJS의 template를 변환하는 용도로 사용되며, **link()**는 model과 view간의 동적인 연결을 담당한다. <br>이때 <u>compile()은 오직 단 한번만 실행</u>되며 <u>link()는 디렉티브 인스턴스 별로 여러번 호출</u>되기 때문에 compile()과 link()를 분리한다. <br>AngularJS에 내장된 **'ngRepeat'** 디렉티브를 이용하여 compile()과 link()를 분리한 목적을 알아보자.

         ```
         <div ng-repeat='thing in things'>
         <my-widget config='thing'></my-widget>
         </div>
         ```

         위의 'ngRepeat' 디렉티브는 scope의 things변수를 루프를 돌면서 things갯수만큼 my-widget directive를 복사한다. 이때 디렉티브별로 compile()이 실행된다면 이것은 디렉티브 해석 시 많은 자원이 낭비되는 것을 뜻한다. <u>이 자원의 낭비를 막기 위해 compile()은 단 한번만 실행되고 리턴되는 link()를 이용하여 복제된 디렉티브의 DOM 엘리먼트들을 link 함으로써 자원의 낭비를 최소화 한다.</u> 둘의 차이점으로는 link()는 compile()과 달리 scope에 자유롭게 접근할 수 있다는 것이다. 쉽게 이해하기 위해 앞부분에서 설명한 디렉티브 pseudo-code의 compile()과 link() 설정부분을 다시 한번 살펴보자.

         ```
         -- 생략 --
         compile: function compile(tElement, tAttrs, transclude) {
               return {
                 pre: function preLink(scope, iElement, iAttrs, controller) { ... },
                 post: function postLink(scope, iElement, iAttrs, controller) { ... }
               }
             },
         link: function postLink(scope, iElement, iAttrs) { ... }
         ```

         보다시피 link()과 달리 <u>compile()은 매개변수로 scope를 받지 않는것을 볼 수 있다.</u> 이유는 compile단계에서는 아직 scope가 존재하지 않은 상태이기 때문에 compile()은 **scope를 매개변수로 가지고 자유롭게 접근할 수 있는 link()를 리턴**한다. 리턴된 link()은 scope와 디렉티브간의 $watch를 설정하여 model과 view간의 동적인 연결을 구성한다.

         <br>

         4 **마무리**

         AngularJS는 개발자의 역량에 따라 다양한 사용자정의 디렉티브를 생성할 수 있도록 한다. 각 디렉티브는 특정한 컴포넌트 기능을 포함하고 이것을 HTML에서 재사용 방식이기 때문에 widget과 같다고 볼 수 있다. 앞에서 디렉티브의 기본적인 내용을 설명하였지만 한번 읽고 한번에 모든 내용을 이해하고 구현하기는 쉽지 않다. 간단한 예제를 직접만들어보고 실행시켜보는것이 이해하는데에 많은 도움이 될 것이다.