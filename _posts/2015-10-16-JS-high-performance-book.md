---
layout: post
title: "`Javascript 성능 최적화 책을 읽고..`<p></p>- 데이터 접근 -"
date:   2015-10-16
categories: JavaScript
tags: [JavaScript]
---

꽤 오래전에 구입했었던, [JavaScript 성능 최적화](http://www.hanbit.co.kr/book/look.html?isbn=978-89-7914-855-8)라는 책을 다시 한번 읽어보며, 책 내용 중 개인적으로 중요하다고 느꼈던 부분과, 그 당시 [모호](http://krdic.naver.com/detail.nhn?docid=13706300)했었던 부분들을, 이 포스트를 통해, 다시한번 정리해나갈 생각이다. 아마 이 책을 소지하고계신 분이라면, 책 내용을 이해하는데, 조금은 도움이될 듯 합니다.

##데이터 접근

- <h3>원글: <em><strong>리터널 값</strong>이란? 단순히 자신을 나타낼 뿐이며, 특정 장소에 저장되지 않는 <strong>모든 값</strong>입니다. JS 에서는 문자열, 숫자, 불리언, 객체, 배열, <span style="color:#c11f1f">함수</span>, 정규 표현식, 특별한 값인 null 과 undefined 가 <strong>리터널</strong>입니다.</em></h3>

  - 여기서 말하는 <span style="color:#c11f1f">함수</span>는 **함수 선언식**이 아닌, <span style="color:#c11f1f">함수 표현식</span>을 의미한다.
      
      - **함수 선언식**은 단순히 <u>함수를 정의하는 **문장**으로 해석</u>되며, 실행 코드 처리 시 <span style="color:#c11f1f">값</span>으로 평가되지않는다.
          
          - 함수 선언식
          
                ![](/blog/assets/images/posts/20151019/performance_1.png)
      
      - <span style="color:#c11f1f">함수 표현식</span>은 <span style="color:#c11f1f">값</span>으로 평가된다.
  
          - 함수 표현식
                        
                ![](/blog/assets/images/posts/20151019/performance_3.png)
          
      - **함수 선언식**은 **함수 표현식**과 달리, 반드시 **함수 이름**을 가져야한다.(**익명 함수**가 허용되지 않는다)
      
          - *The main difference between a function expression and a function statement is the function name, which can be omitted in function expressions to create anonymous functions.*
                        
          - 함수 선언식
        
                ```javascript
                
                // global execution context
                
                // 함수 선언식은 반드시 함수 이름을 가져야한다.
                function (){
                    
                    // function execution context
                }
                ````
            
                ![](/blog/assets/images/posts/20151019/performance_2.png)
                  
          - 함수 표현식                  
  
                ```javascript
                
                // global execution context
                
                // 익명 함수 표현식(AFE)
                var fn1 = function(){
                    // function execution context
                };
                
                // 기명 함수 표현식(NFE)
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
                (function() {
                    // function execution context
                    console.log('immediately invoked function'); // immediately invoked function
                })();
                
                (function C(){
                    // function execution context
                    console.log(C); // function object
                    console.log('immediately invoked function'); // immediately invoked function
                })();
                
                
                // 그밖의 함수 표현식들
                
                // 하지만 아래 표현식들은 의도한 함수 반환 값을 가질 수 없다는 단점이 존재한다.
                console.log(0,function(){ return 1; }()); // 0 1
                
                console.log(!function(){ return 1; }()); // false
                console.log(!function(){ return false; }()); // true
                
                console.log(~function(){ return 1; }()); // -2
                console.log(-function(){ return -100; }()); // 100
                console.log(+function(){ return -100; }()); // -100
                
                // 위에서 언급했던것처럼 함수 표현식은 값으로 평가된다. 
                // 즉 아래, 함수 표현식들은 값으로 평가되어 new 연산자를 통해, 새로운 객체를 생성하게된다.
                new function A(){
                    console.dir(this); // function object
                }; // === new A;
                
                new function A(){
                    console.dir(this); // function object
                }(); // === new A();                
                ````
                
  - [문법과 타입](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/Values,_variables,_and_literals#Literals)
  
  - [JavaScript Function Literals](http://www.tutorialspoint.com/javascript/javascript_function_literals.htm)
  
  - [3.4. Functions](http://docstore.mik.ua/orelly/webprog/jscript/ch03_04.htm)
  
  - [Function Declarations(함수선언) vs Function Expressions(함수표현)](http://insanehong.kr/post/javascript-function/)
  
  - [Function Declarations vs. Function Expressions](https://javascriptweblog.wordpress.com/2010/07/06/function-declarations-vs-function-expressions/)
  
  - [function expression](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/function)
  
  - [What is the difference between a function expression vs declaration in JavaScript?](http://stackoverflow.com/questions/1013385/what-is-the-difference-between-a-function-expression-vs-declaration-in-javascrip)
  
  - [IIFE](http://benalman.com/news/2010/11/immediately-invoked-function-expression/)
    
- <h3>원글: <em><strong>변수</strong>는 데이터를 저장할 목적으로, <u><span style="color:#c11f1f">var</span> 키워드를 써서 만든</u>, 개발자가 정의한 위치입니다.</em></h3>

  - **개발자가 정의한 위치**라는 표현은 지금도 이해하기 힘들다;;;
  
  - **변수**는 오직 <span style="color:#c11f1f">var</span> 키워드를 통해서만 선언된다.

          ```javascript
          // global Execution Context
          
          // var 키워드를 통한(변수 선언식) x 변수는 execution context 진입 시 VO 의 속성으로 추가되며, undefined 로 초기화된다.
          console.log(x); // undefined
          
          try{
              // var 키워드가 생략된 (VO).y 속성은 execution context 진입 시 VO 의 속성으로 추가되지않는다.
              console.log(y);
          }
          catch (ex){
              console.log(ex.message); // y is not defined
          }
          
          // var 키워드를 통한 변수 선언식
          var x = 1;
          
          // var 키워드가 생략된 (VO).y 속성
          y = 2;
          
          // var 키워드를 통한 x 변수는 non-configurable 로 정의된다.
          console.log(Object.getOwnPropertyDescriptor(this, 'x')); // Object {value: 1, writable: true, enumerable: true, configurable: false}
          
          // var 키워드가 생략된 (VO).y 속성은 configurable 로 정의된다.
          console.log(Object.getOwnPropertyDescriptor(this, 'y')); // Object {value: 2, writable: true, enumerable: true, configurable: true}
          
          
          // 실행 코드 처리 후 1 이 할당된다.
          console.log(x); // 1 === this.x
          
          // 실행 코드 처리 후 2 가 할당된다.
          console.log(y); // 2 === this.y
          
          // non-configurable 로 정의된 x 변수는 delete 연산자를 통해 삭제되지 않는다.
          delete this.x;
          
          // configurable 로 정의된 (VO).y 속성은 delete 연산자를 통해 삭제된다.
          delete this.y;
          
          // delete 연산자를 통해 삭제되지 않았다.[non-configurable property]
          console.log(x); // 1 === this.x
          
          // delete 연산자를 통해 삭제되었다.[configurable property]
          try{
              console.log(y);
          }
          catch (ex){
              console.log(ex.message); // y is not defined
          }
          ```

  - [Variable in JS](http://mohwa.github.io/blog/javascript/2015/10/14/variable-inJS/)
  
##**Scope Chain** 과 **식별자 해석**

- <h3>원글: <em><u>JS 함수는 <strong>객체</strong>이고, 엄밀히 말해 <span style="color:#c11f1f">Function</span> 의 인스턴스 입니다.</u> 함수 객체는 다른 모든 객체와 마찬가지로 속성이 있는데, 이 속성은 프로그램에서 접근할 수 있는 속성과 JS 엔진에서 사용되며, 코드에서 접근할 수 없는 <strong>내부 속성</strong>으로 나뉩니다. 이러한 <strong>내부 속성</strong> 중 하나가 ECMA-262 세 번째 판에서 정의한 [[Scope]] 입니다.</em></h3>

  - JS 함수는 <span style="color:#c11f1f">Function</span> **생성자 함수 객체**로 만든 또 다른 **함수 객체** 이다.
  
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
              
      - 이 말은 JS **함수 객체**에서도 동일하게 적용될 수 있다. 즉 **함수**를 생성하는 <span style="color:#c11f1f">Function</span> **생성자 함수 객체**를 생성하기 위해서는, 또 다른 <span style="color:#c11f1f">Function</span> **생성자 함수 객체**가 무한히 반복되기 때문이다.
             
             ![](/blog/assets/images/posts/20151019/performance_4.png)
      
- <h3>원글: <em>함수가 만들어질 때(함수 생성 시), 함수가 생성된 스코프에서 접근할 수 있는, 데이터를 나타내는 객체(<span style="color:#c11f1f">VO</span>)와 함께 <strong>스코프 체인</strong>이 만들어집니다.</em></h3>

- <h3><em>실행 문맥도 식별자 해석에 쓸 자기만의 <strong>Scope Chain</strong> 이 있습니다. 실행 문맥이 만들어질 때 이 문맥의 <strong>Scope Chain</strong>은 실행 중인 함수의 [[Scope]] 속성에 있는 <strong>객체로 초기화</strong>됩니다. 이 과정이 끝나면 활성화 객체(<span style="color:#c11f1f">AO</span>)라고 부르는 새로운 객체가 실행 문맥에 생성됩니다. 활성화 객체(<span style="color:#c11f1f">AO</span>)는 이 실행에 대해 변수 객체(<span style="color:#c11f1f">VO</span>) 구실을 하며, 모든 지역 변수, 명명된 매개변수, arguments 집합, this 항목을 포함합니다.</em></h3>

- <h3><em>활성화 객체(<span style="color:#c11f1f">AO</span>)는 <strong>Scope Chain</strong> 의 앞에 자리 잡습니다. 실행 문맥이 파괴될 때, 활성화 객체(<span style="color:#c11f1f">AO</span>)도 같이 파괴됩니다.</em></h3>


  - **Scope Chain** 은 <u><span style="color:#c11f1f">함수 생성 시점</span></u>이 아닌, <u><span style="color:#c11f1f">함수 호출 시점</span></u>에서 해당 Function Execution Context 내부에 **생성**된다.
      
  - <span style="color:#c11f1f">함수 생성 시</span>, 해당 Function Execution Context 상위에 있는, 모든 부모 계층의 <span style="color:#c11f1f">VO</span> 들은 [[Scope]] 속성으로 **할당**된다.
      
          ```javascript
          // global Execution Context
          var x = 1;
          
          function A(){
              // function Execution Context
              var y = 2;
          }          
          ```
          
   - 생성된 **함수 객체** 내부
   
          ```javascript
            A: {
            [[Scope]]: [
              // [[Scope]] 속성에는 해당 execution context 상위에 있는, 모든 부모 계층의 VO 가 할당된다.
              globalExecutionContext.VO: {
                x: 1,
                A: < reference to function >
              }
            ]
          }
          ```       
      
  - <span style="color:#c11f1f">함수 호출 시</span>, 함수 [[Scope]] 속성과, Function Execution Context 내부 <span style="color:#c11f1f">AO</span>(VO) 를 포함하는, **Scope Chain** 이 생성된다.
  
      -  [<span style="color:#c11f1f">AO</span>(VO), [[Scope]]] = **Scope Chain**(해당 <span style="color:#c11f1f">AO</span> 는 **Scope Chain** 의 가장 <span style="color:#c11f1f">앞</span>(ScopeChain[0])에 추가된다.)<p>
      
              ![](http://figures.oreilly.com/tagoreillycom20090601oreillybooks300541I_book_d1e1/figs/I_mediaobject7_d1e6895-web.png)
      
              ```javascript
              // global Execution Context
              var x = 1; // VO.x
              
              function A(){
                  // function Execution Context
                  var y = 2; // AO(VO).y
              }  
                  
              // A 함수객체를 호출한다.     
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
                    // 함수 호출 시, 생성된 AO(VO) 는 Scope Chain 의 가장 앞에(ScopeChain[0]) 추가되며, 식별자 검색 시, 가장 먼저 검색된다.
                    AO(VO): {
                      y: 2
                    },              
                    // 함수 생성 시, [[Scope]] 속성에 추가되었던, globalExecutionContext 의 VO
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
                    // global execution context 내부 Scope Chain 에는 globalExecutionContext.VO 만 포함된다.
                    globalExecutionContext.VO
                  ],
                  this: global object                            
                }
              ];
            ```
        
- <h3>원글: <em><span style="color:#c11f1f">식별자 검색</span>을 통해, 끝까지 찾지 못하면 변수가 선언되지 않은 것으로 간주합니다.</em></h3>

  - 만약 위와 같은 상황이라면, 결국 **런타임 오류**가 발생된다.
  
        ```javascript
        // global execution context
        
        function A(){
          
            // function execution context
            console.log(x);
        }
        
        A();
        ```
        
        ![](/blog/assets/images/posts/20151019/performance_7.png)

        
- <h3>원글: <em>함수 스코프 바깥에 있는 값을 한번 이상 사용할 거라면, 항상 지역 변수에 저장한다는 원칙을 가지는게 좋습니다. 만약 수십 개의 <strong>전역 변수</strong>에 반복해서 접근해야하는 큰 함수일경우, 성능이 비약적으로 좋아질것입니다.</em></h3>

  - 이 말은 <span style="color:#c11f1f">식별자 검색</span>이 **브라우저 엔진**에 의해, **최적화**되지않은 **상황**을 고려해야한다는 말이다.(즉 **APP** 이 오래된 브라우저(IE 7 ~ 8 등)를 지원해야하는 경우)
  <br/>
  
  - 그럼 조금 억지스러운 **테스트**를 한번 진행 해보자.(물론, **Production Code** 가 이렇게 작성될 일은 없다고 생각한다)
      
      - Chrome, Safari, FF 브라우저경우 <span style="color:#c11f1f">식별자 검색</span>에 대한, 브라우저 **최적화**가 잘되어있는지 **Scope Chain Depth** 에 대한, **속도 차이**가 크게 나타나지 않았다.
      
      - 하지만 **APP** 상에서 **오래된 브라우저**(IE 7 ~ 8 등)를 지원해야하는 경우, 이와같은 <span style="color:#c11f1f">식별자 검색</span>에 대한, **TEST CASE** 를 만들어보는것도 좋을 듯 하다.
      
              ```javascript
              // 변수 선언 배열
              var vars = [];
              // 함수 선언 배열
              var fns = [];
          
              // 생성할 전역 변수의 수
              var globalVariableCount = 200;
          
              // 각 전역 변수에 접근하는 수
              // globalVariableCount(생성할 전역 변수의 수) * globalVariableLoopCount(각 전역 변수에 접근하는 수) = 모든 전역 변수에 접근하는 총수
              var globalVariableLoopCount = 300;
          
              // 생성할 중첩 함수의 수
              var fnDepthCount = 100;
          
              // n 개의 전역 변수를 생성한다.
              for (var i = 0; i < globalVariableCount; i++) {
                  vars.push('var x_' + i + ' =' + i + ';');
              }
          
              // eval 함수를 통해 전역 변수를 생성한다.
              eval(vars.join(''));
          
              // n 개의 중첩 함수를 생성한다.
              for (var i = 0; i < fnDepthCount; i++) {
          
                  fns.push('function A_' + i + '(){');
          
                  // 마지막 중첩 함수일 경우
                  if (i === (fnDepthCount - 1)){
          
                      fns.push('var y;');
          
                      // 식별자 검색 시작 시간
                      fns.push('var start = new Date().getTime();');
          
                      // 마지막 중첩 함수 내부에서, n 개의 전역 변수를 n 번 순회하며, 식별자 검색을 통해 값을 가져온다.
                      for (var ii = 0; ii < globalVariableLoopCount; ii++) {
                          // 생성된 모든 전역 변수를 순회한다.
                          for (var jj = 0; jj < globalVariableCount; jj++) {
                              // 식별자 검색을 통해, 값을 가져온다.
                              fns.push('var y = x_' + jj + ';');
                          }
                      }
          
                      // 식별자 검색 종료 시간
                      fns.push('var end = new Date().getTime();');
                      fns.push('console.log((end - start) + \' ms\');');
          
                      for (var j = i; j > -1; j--) {
                          fns.push('} A_' + j + '();');
                      }
                  }
              }
          
              console.log(fns.join(''));
          
              // eval 함수를 통해 중첩 함수를 생성한다.
              eval(fns.join(''));
              ```
              
              ![](/blog/assets/images/posts/20151019/performance_5.png)
          
      - **최적화**를 진행한 코드(함수 **지역 변수**에 **전역 변수** 값을 할당한다)
              
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
                              var tmp$ = $;
              
                              console.log(new Date().getTime());
              
                              for (var i = 0; i < 2000000000; i++){
              
                                  // DFunctionExecutionContext.scopeChain[0].AO(VO).tmp$.version
                                  ver = tmp$.version;
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


- <h3>원글: <em>실행 중에 with 문을 만나면 실행 문맥의 <strong>Scope Chain</strong> 이 임시로 확장되면서, 명시한 객체의 속성 전체를 포함하는 새로운 변수 객체(<span style="color:#c11f1f">VO</span>)가 만들어집니다.</em></h3>

- <h3><em>만약 document 객체를 with 문에 전달하면 document 객체의 속성을 모두 포함하는 새로운 객체가 <strong>Scope Chain</strong>의 가장 앞에 삽입됩니다. 이렇게하면 document 객체 속성에는 매우 빠르게 접근할 수 있지만, 함수 <strong>지역 변수</strong>에 접근히는것은 느려집니다.</em></h3>
    
  - 실행 코드
  
          ```javascript
          // global execution context
          
          function A(){
          
              // function execution context
          
              // 지역 변수
              var x = 1;
            
              // with 문에 document 객체를 전달한다.
              with (document){
          
                  // 전달된 document 객체 속성을 수정한다.
                  getElementById = null;
              }
          
              console.log(document.getElementById); // null
          }
          
          A();
          ```

  - <span style="color:#c11f1f">ECStack</span> 내부
   
          ```javascript
          var ECStack = [
            // A function execution context
            <A> functionExecutionContext: {
              AO(VO): {
                x: 1
              },
              Scope(Scope Chain): [
                // with 문으로 인해, 해당 Scope Chain 이 확장된다.
                with(VO(document object)): {
                  getElementById: null,
                  ...
                },
                // 확장된 Scope Chain 으로 인해, AO(VO) 에 대한 접근이 느려진다.
                AO(VO): {
                  x: 1
                },              
                globalExecutionContext.VO: {
                  A: < reference to function >
                }
              ],
              this: global object              
            },
            // global execution context
            globalExecutionContext: {
              VO: {
                A: < reference to function >        
              },
              Scope(Scope Chain): [
                globalExecutionContext.VO
              ],
              this: global object                            
            }
          ];
        ```

- <h3>원글: <em><span style="color:#c11f1f">동적 스코프</span>는 with 문, catch 절, eval() 함수와 같이 실행 코드 처리시, 동적으로 해당 <strong>Scope Chain</strong> 이 변경되는 환경을 의미합니다.</em></h3>

- <h3><em>일반적인 브라우저 엔진의 경우, 전통적인 <strong>Scope Chain</strong> 을 참조를 하지 않고, <strong>식별자 색인</strong>을 따로 만들어 식별자를 더 빠르게 해석합니다. 하지만 코드에 <span style="color:#c11f1f">동적 스코프</span>가 있다면, 이런 식으로 최적화할 수 없습니다. 따라서 동적 스코프는 꼭 필요할때만 쓰길 권장합니다.</em></h3>

  - eval 함수를 활용한 **동적 스코프** 예제
  
          ```javascript
          // global execution context
          
          var x = 1;
          
          function A(){
          
              // function execution context
              console.log(x); // 1
          }
          
          function B(){
          
              // function execution context
          
              var x = 2;
          
              // eval execution context 는 calling context 의 Scope Chain 을 갖는다.
              // 여기서 callingContext 는 BFunctionExecutionContext 와 동일하다.
              eval(String(A));
          
              A(); // 2
          }
          
          A();
          
          B();
          ```
          
  - [Scope Chain 그리고 Closure](http://mohwa.github.io/blog/javascript/2015/10/11/scope-chain-inJS/)          

- <h3>원글: <em>클로저(스코프, 메모리)란 함수가 자신의 스코프 밖에 있는 데이터에 접근할 수 있게 하는 <strong>환경</strong>을 말한다.</em></h3>

  - Closure **환경**으로 인한, **메모리 누수**
  
        - 즉 makeAdder 함수(상위 계층) 종료 시, 소멸되는 <span style="color:#c11f1f">AO(VO)</span> 가, 해당 [[Scope]] 속성 내부에 영구적으로 보관된다는 것이다.(**메모리 누수의 원인**)
        
              ```javascript
              
              // global execution context
              
              function makeAdder(x) {
                
                // function execution context
                
                // 반환되는 익명함수에는 Scope Chain 매커니즘에 의해 부모 계층의 모든 VO 가 포함되어있다.
                // 부모 계층의 AO(VO)({x: 5})
                return function(y) {
                
                  // function execution context
                  // Scope Chain 의 식별자 검색을 통해 부모 계층의 Vo.x 속성을 사용한다.
                  
                  // anonymousFunctionExecutionContext.scopeChain.makeAdderFunctionExecutionContext.AO.x
                  // anonymousFunctionExecutionContext.scopeChain.AO.y
                  return x + y;
                };
              }
              
              var add5 = makeAdder(5);
              
              print(add5(2));  // 7
         
              ```

      - anonymousFunction(= add5) 함수 [[Scope]] 속성 내부
      
             ```javascript
             anonymousFunction(= add5): {
              [[Scope]]: [
                <makeAdder> functionExecutionContext.AO(VO): {
                  arguments: {
                    0: 5                
                  },
                  x: 5
                },
                globalExecutionContext.VO: {
                  makeAdder: < reference to function >,
                  add5: undefined
                }
              ]
            }
            ```
      - anonymousFunction(= add5) 함수 **Scope Chain** 내부
      
             ```javascript
             anonymousFunction(= add5): {
              Scope(Scope Chain): [
                AO: {
                  arguments: {
                    0: 2                
                  },
                  y: 2                
                },
                <makeAdder> functionExecutionContext.AO(VO): {
                  arguments: {
                    0: 5                
                  },
                  x: 5
                },
                globalExecutionContext.VO: {
                  makeAdder: < reference to function >,
                  add5: < reference to function >
                }
              ]
            }
            ```
              
      -  성능 개선
      
          - 만약 <span style="color:#c11f1f">식별자 검색</span>에 대한, **성능 개선**을 하고자 한다면, 함수 **지역 변수**를 통해 간단히 개선할 수 있다.
      
                  ```javascript
                  
                  // global execution context
                  
                  function makeAdder(x) {
                  
                      // function execution context
                  
                      // 반환되는 익명함수에는 Scope Chain 매커니즘에 의해 부모 계층의 모든 VO 가 포함되어있다.
                      // 부모 계층의 AO(VO)({x: 5})
                      return function(y) {
                      
                          // function execution context
                          
                          // _x 변수에 상위 함수의 x 매개변수 값을 할당한다.
                          var _x = x;
                  
                          // function execution context
                  
                          // anonymousFunctionExecutionContext.scopeChain.AO.x
                          // anonymousFunctionExecutionContext.scopeChain.AO.y
                          return _x + y;
                      };
                  }
                  
                  var add5 = makeAdder(5);
                  
                  add5(2); // 7
                
                  ```      

          - anonymousFunction(= add5) 함수 **Scope Chain** 내부
      
                  ```javascript
                  anonymousFunction(= add5): {
                    Scope(Scope Chain): [
                      AO: {
                        arguments: {
                          0: 2                
                        },
                        _x: 5
                        y: 2                
                      },
                      <makeAdder> functionExecutionContext.AO(VO): {
                        arguments: {
                          0: 5                
                        },
                        x: 5
                      },
                      globalExecutionContext.VO: {
                        makeAdder: < reference to function >,
                        add5: < reference to function >
                      }
                    ]
                  }
                  ```
