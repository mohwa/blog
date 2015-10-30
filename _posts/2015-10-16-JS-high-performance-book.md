---
layout: post
title: "[Javascript 성능 최적화] 책을 읽고.."
date:   2015-10-16
categories: JavaScript
tags: [JavaScript]
---

1. 로딩과 실행

2. 데이터 접근

- 원글: *<strong>리터널 값</strong>: 단순히 자신을 나타낼 뿐이며, 특정 장소에 저장되지 않는 <strong>모든 값</strong>입니다. JS 에서는 문자열, 숫자, 불리언, 객체, 배열, 함수, 정규 표현식, 특별한 값인 null 과 undefined 가 <strong>리터널</strong>입니다.*

  - 여기서 **함수** 는 **함수 선언식**이 아닌, **함수 표현식**만을 의미한다.
      
      - **함수 선언식**은 함수를 정의하는 **문장**으로 해석되며, **값**으로 평가되지않는다.
          
          - 함수 선언식
          
                  ![](/blog/assets/images/posts/20151019/performance_1.png)
      
      - **함수 표현식**은 **값**으로 평가된다.

          - 함수 표현식
                        
                  ![](/blog/assets/images/posts/20151019/performance_3.png)
          
      - **함수 선언식**은 **함수 표현식**과 달리, 반드시 **함수 이름**을 가져야한다.(즉 **익명 함수**를 허용하지 않는다)
      
          - *The main difference between a function expression and a function statement is the function name, which can be omitted in function expressions to create anonymous functions.*
                        
          - 함수 선언식
          
                  ```javascript
                  
                  // global execution context
                  
                  // 함수 선언식
                  // 반드시 함수 이름을 가져야한다.
                  function (){
                      
                      // function execution context
                  }
                  ````
              
                  ![](/blog/assets/images/posts/20151019/performance_2.png)
                  
          - 함수 표현식                  

                  ```javascript
                  
                  // global execution context
                  
                  // 익명 함수 표현식
                  var fn1 = function(){
                      // function execution context
                  };
                  
                  // 기명 함수 표현식
                  var fn2 = function A(){
                      // function execution context
                  
                      console.log(A); // function object
                  };
                  
                  
                  // 괄호 연산자를 통한 익명 함수 표현식
                  (function (){
                      // function execution context
                  });
                  
                  
                  // 괄호 연산자를 통한 기명 함수 표현식
                  (function B(){
                      // function execution context
                  });
                  
                  
                  // 괄호 연산자를 통한 즉시 실행 함수(익명/기명)
                  
                  // http://benalman.com/news/2010/11/immediately-invoked-function-expression/
                  (function() {
                      // function execution context
                      console.log('immediately invoked function'); // immediately invoked function
                  })();
                  
                  (function C(){
                      // function execution context
                      console.log(C); // function object
                      console.log('immediately invoked function'); // immediately invoked function
                  })();
                  ````
                
  - [문법과 타입](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/Values,_variables,_and_literals#Literals)
  
  - [JavaScript Function Literals](http://www.tutorialspoint.com/javascript/javascript_function_literals.htm)
  
  - [3.4. Functions](http://docstore.mik.ua/orelly/webprog/jscript/ch03_04.htm)
  
  - [Function Declarations(함수선언) vs Function Expressions(함수표현)](http://insanehong.kr/post/javascript-function/)
  
  - [Function Declarations vs. Function Expressions](https://javascriptweblog.wordpress.com/2010/07/06/function-declarations-vs-function-expressions/)
  
  - [function expression](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/function)
  
  - [What is the difference between a function expression vs declaration in JavaScript?](http://stackoverflow.com/questions/1013385/what-is-the-difference-between-a-function-expression-vs-declaration-in-javascrip)
  
- 원글: *<strong>변수</strong>: 데이터를 저장할 목적으로, <u>var 키워드를 써서 만든</u>, 개발자가 정의한 위치입니다.*

  - 다시말해, **변수**는 오직 <span style="color:#6298c1">var</span> 키워드를 통해 선언된다.

          ```javascript
          // global Execution Context
          
          // var 키워드를 통한 x 변수는 execution context 진입 시 VO 의 새로운 속성으로 추가되며, undefined 로 초기화된다.
          console.log(x); // undefined
          
          try{
              // var 키워드가 생략된 y 속성은 execution context 진입 시 VO 의 새로운 속성으로 추가되지않는다.
              console.log(y);
          }
          catch (ex){
              console.log(ex.message); // y is not defined
          }
          
          // var 키워드를 통한 변수 선언식
          var x = 1;
          
          // var 키워드가 생략된 y 속성
          y = 2;
          
          // 변수 선언식을 통한(var 키워드를 통한) VO 의 x 속성은 non-configurable 로 정의된다.
          console.log(Object.getOwnPropertyDescriptor(this, 'x')); // // Object {value: 1, writable: true, enumerable: true, configurable: false}
          
          // var 키워드가 생략된 VO 의 y 속성은 configurable 로 정의된다.
          console.log(Object.getOwnPropertyDescriptor(this, 'y')); // // Object {value: 2, writable: true, enumerable: true, configurable: true}
          
          
          // 실행 코드 처리 후 1 이 할당된다.
          console.log(this.x); // 1
          
          // 실행 코드 처리 후 2 가 할당된다.
          console.log(this.y); // 2
          
          // non-configurable 로 정의된 x 속성은 delete 연산자를 통해 삭제되지 않는다.
          delete this.x;
          
          // configurable 로 정의된 y 속성은 delete 연산자를 통해 삭제된다.
          delete this.y;
          
          // delete 연산자를 통해 삭제되지 않는다.[non-configurable property]
          console.log(this.x); // 1
          
          // delete 연산자를 통해 삭제되었다.[configurable property]
          console.log(this.y); // undefined
          ```

- [Variable in JS](http://mohwa.github.io/blog/javascript/2015/10/14/variable-inJS/)
  
3. **Scope Chain** 과 **식별자 해석**

- 원글: *JS 함수는 객체이고, 엄밀히 말해 Function 의 인스턴스 입니다. 함수 객체는 다른 모든 객체와 마찬가지로 속성이 있는데, 이 속성은 프로그램에서 접근할 수 있는 속성과 JS 엔진에서 사용되며, 코드에서 접근할 수 없는 <strong>내부 속성</strong>으로 나뉩니다. 이러한 <strong>내부 속성</strong> 중 하나가 ECMA-262 세 번째 판에서 정의한 [[Scope]] 입니다.*

  - JS 함수는 **Function 생성자 함수 객체**로 만든 또 다른 **함수 객체** 이다.
  
      - 이전에 작성했었던, [객체 지향 언어의 두 가지 줄기](http://mohwa.github.io/blog/javascript/2015/10/16/prototype/)라는 글에서, **inifnite regress problem of classes** 라는 말을 언급한적이있었다.
      
              ```javascript
              프로토타입 기반 OO는 객체를 직접 이용하는데 비해, 클래스 기반의 OO는 클래스라는 틀을 이용해서 객체를 생성하죠.
              
              자연스럽게 나오는 질문은 "그럼 클래스는 대체 어디서 나온 것인가?"라는 거죠.
              
              대표적 클래스 기반 OO인 Java는 클래스도 객체로 봅니다. 그런데 클래스가 객체면 클래스를 찍어낸 클래스가 있어야 합니다. 클래스를 찍어낸 클래스도 객체이므로, 클래스를 찍어낸 클래스를 만드는 클래스도 있어야 합니다. 
              
              이렇게 무한히 반복 되기 때문에 "inifnite regress problem of classes"라고 불리는 것이죠.
              
              Java는 이 문제를 java.lang.Class가 모든 클래스의 틀이라고 이야기하면서, 은근 슬쩍 넘어갔지만 여기에 숨은 논리적인 문제를 확실히 못풀고 "태초에 Class가 있었다"라는 해법이 나와버리는거죠.
              
              다른 접근 방법을 취한 언어가 있는지 모르겠지만, 대부분의 클래스 기반 언어는 클래스는 second-order citizen으로 특별히 취급해서 찍어낼 수 있는 틀은 원래 있었다라고 얘기하게 되는거죠.
              
              그 대상이 클래스라는 거만 제외하면 접근 방법은 프로토타입 방식과 크게 다르지 않은 거고요. 
              ```         
              
      - 이 말은 JS **함수 객체**에서도 동일하게 적용될 수 있다. 즉 **함수**를 생성하는 **Function 생성자 함수 객체**를 생성하기 위해서는, 또 다른 **Function 생성자 함수 객체**가 무한히 반복되기 때문이다.
             
             ![](/blog/assets/images/posts/20151019/performance_4.png)
      
- 원글: *함수가 만들어질 때(함수 생성 시), 함수가 생성된 스코프에서 접근할 수 있는, 데이터를 나타내는 객체와 함께 <strong>스코프 체인</strong>이 만들어집니다.*

  - 원글과 달리, **Scope Chain** 은 <u>**함수 생성 시**</u>가 아닌, <u>**함수 호출 시**</u> 해당 function execution context 내부에 생성된다.
      
  - **함수** 생성 시 해당 function execution context 상위에 있는, 모든 부모 계층의 <span style="color:#c11f1f">VO</span> 를 갖는 [[Scope]] property 가 생성된다.
      
          ```javascript
          // global Execution Context
          var x = 1;
          
          function A(){
              // function Execution Context
              var y = 2;
          }          
          ```
          
   - **함수 객체** 내부
   
          ```javascript
          A(function): {
            [[Scope]]: [
              // A 함수의 [[Scope]] property 는 A function execution context 상위에 있는, global execution context 의 VO 를 갖는다.
              globalExecutionContext.VO: {
                x: 1,
                A: < reference to function >
              }
            ]
          }
          ```       
      
  - <span style="color:#c11f1f">**함수** 호출 시</span>, 해당 [[Scope]] property 와 function execution context 내부 <span style="color:#c11f1f">AO</span>(VO) 를 갖는 **Scope Chain** 이 생성된다.
      
          ```javascript
          // global Execution Context
          var x = 1; // VO.x
          
          function A(){
              // function Execution Context
              var y = 2; // AO(VO).y
          }  
              
          // A 함수를 호출한다.        
          A();                  
          ```
          
   - <span style="color:#c11f1f">ECStack</span> 내부
   
          ```javascript
          var ECStack = [
            // A function execution context
            <A> functionExecutionContext: {
              AO(VO): {
                y: 2
              },
              Scope(Scope Chain): [
                // 함수 호출 시, 생성된 AO(VO) 는 Scope Chain 의 가장 앞에(ScopeChain[0]) 추가되며, 식별자 검색 시 가장 먼저 검색된다.
                AO(VO): {
                  y: 2
                },              
                // 함수 생성 시, [[Scope]] property 에 할당되었던, globalExecutionContext 의 VO
                globalExecutionContext.VO: {
                  x: 1,
                  A: < reference to function >
                }
              ],
              this: global object              
            },
            // global execution context
            globalExecutionContext: {
              VO: {
                x: 1,
                A: < reference to function >        
              },
              Scope(Scope Chain): [
                globalExecutionContext.VO
              ],
              this: global object                            
            }
          ];
        ```
        
- 원글: *함수 스코프 바깥에 있는 값을 한번 이상 사용할 거라면, 항상 지역 변수에 저장한다는 원칙을 가지는게 좋습니다.*

  - 이 말은 **식별자 검색** 매커니즘이 **브라우저 엔진**에 의해, **최적화** 되지않은 **상황**을 고려해야한다는 말이다.(만약 노후된 브라우저(IE 7 ~ 8 등)를 지원해야하는 경우)
  
  - 그럼 조금 억지스러운 **테스트**를 한번 진행 해보자.(*이 같은 이유가 아니더라도, 설마 Production Code 를 이렇게 작성하는 일은 없을것이다?*)
      
      - Chrome 브라우저경우, **브라우저 엔진**을 통한 **최적화**가 잘되어있는지, **Scope Chain Depth** 에 의한, **속도 변화**가 크게 존재하지 않았다.
      
      - 또한 **어플리케이션**상에서 **구 버전 브라우저**(IE 7 ~ 8 등)를 지원해야하는경우에는, 아래와 같은 코드로 **식별자 검색**에 대한 **TEST CASE** 를 만들어보는것도 좋을 듯 하다.
      
              ```javascript
              // 일반적인 상황을 위해, 전역 변수에 JQuery 가 할당되었다고 생각해보자.
              var $ = {version: '1.0'};
              
              function A(){
              
                  // function execution context
                  function B(){
              
                      // function execution context
                      function C(){
              
                          // function execution context
                          function D(){
              
                              var ver = '';
              
                              console.log(new Date().getTime());
              
                              for (var i = 0; i < 2000000000; i++){
              
                                  // D 함수 내부에서, $ 변수에 접근하기위해서는, 아래와 같은 식별자 검색을 통해 접근하게 될것이다.
                                  
                                  /*
                                  
                                  [scopeChain[0].AO(VO) 의 식별자 검색]
                                  DFunctionExecutionContext.scopeChain[0].AO(VO) ==>
                                  [scopeChain[1].AO(VO) 의 식별자 검색]
                                  DFunctionExecutionContext.scopeChain[1].AO(VO) ==>
                                  [scopeChain[2].AO(VO) 의 식별자 검색]
                                  DFunctionExecutionContext.scopeChain[2].AO(VO) ==>
                                  [scopeChain[3].AO(VO) 의 식별자 검색]
                                  DFunctionExecutionContext.scopeChain[3].AO(VO) ==>
                                  [scopeChain[4].VO 의 식별자 검색]
                                  
                                  // scopeChain[4].VO 에서 원하는 식별자가 검색되었다.
                                  DFunctionExecutionContext.scopeChain[4].VO.$.version
                                  
                                  */
                                  
                                  // 즉 이 경우 식별자 검색을 위한, Scope Chain Depth 가 위로 4 단계가 존재하게되는 것이다.
                                  // DFunctionExecutionContext.scopeChain[4].globalExecutionContext.VO.$.version
                                  ver = $.version;
                              }
              
                              console.log(new Date().getTime());
                          }
              
                          D();
                      }
              
                      C();
                  }
              
                  B();
              }
              
              A();
              ```
              
              ![](/blog/assets/images/posts/20151019/performance_5.png)
          
      - 함수 **지역 변수**를 통해, **최적화**를 진행한 코드
              
              ```javascript
              // global execution context
              
              var $ = {version: '1.0'};
              
              function A(){
              
                  // function execution context
                  function B(){
              
                      // function execution context
                      function C(){
              
                          // function execution context
                          function D(){
              
                              var ver = '';
              
                              // 식별자 검색 최적화를 위해, 지역 변수에 VO.$ 를 할당한다.
                              var tmpVersion = $;
              
                              console.log(new Date().getTime());
              
                              for (var i = 0; i < 2000000000; i++){
              
                                  // DFunctionExecutionContext.scopeChain[0].AO.tmpVersion
                                  ver = tmpVersion.version;
                              }
              
                              console.log(new Date().getTime());
                          }
              
                          D();
                      }
              
                      C();
                  }
              
                  B();
              }
              
              A();
              ```
              
              ![](/blog/assets/images/posts/20151019/performance_6.png)              
     
- [Scope Chain 그리고 Closure](http://mohwa.github.io/blog/javascript/2015/10/11/scope-chain-inJS/)

- [ECMA-262-3 in detail. Chapter 4. Scope chain](http://dmitrysoshnikov.com/ecmascript/chapter-4-scope-chain/#definition)

- [ECMA-262-3 in detail. Chapter 4. Scope chain(번역)](http://huns.me/development/334)
     
