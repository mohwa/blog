---
layout: post
title: "Scope Chain 그리고 Closure"
date:   2015-10-11
categories: javascript
tags: [JavaScript]
---

- Execution Context 와 <span style="color:#c11f1f">ECStack</span> 내부
	
      ```javascript
        
        // global execution context
    
        function A(){
          
          // A 함수 선언식 내부
          // function execution context
          var x = 1; // AO.x
    
          function B(){
    
            // B 함수 선언식 내부
            // active function execution context
            var y = 2; // AO.y
          }
    
          B(); // AO.B() == null.B()
        }
    
        A(); // this.A();
      ```
- <span style="color:#c11f1f">ECStack</span> 내부
	
      ```javascript
      var ECStack = [
        // B function execution context
        <B> activeFunctionExecutionContext: { 
          AO(VO): {
            y: 2
          }
        },
        // A function execution context
        <A> functionExecutionContext: {
          AO(VO): {
            x: 1,
            B: < reference to function >
          }
        },
        // global execution context
        globalExecutionContext: {
          VO: {
            A: < reference to function >
          }
        }
      ];
      ```

- Global Execution Context 의 **Scope Chain**

  - global execution context 에 생성되는 **Scope Chain** 은 globalExecutionContext.VO 만 **포함**한다.
    
          ```javascript
            globalExecutionContext:{
              VO: ...
              Scope(Scope Chain): [
                globalExecutionContext.VO
              ]
            }
          ```
- **함수 생명 주기** 와 Function Execution Context 의 **Scope Chain**
      
	- 함수 생명 주기는 **함수 생성**과 **함수 호출**로 나눌 수 있다.
	
	- 함수 생성

		- **함수 생성 시** [[Scope]] property 가 **생성**된다.<p>
		
		- [[Scope]] property 는 직접적인 접근이 불가능하며, **초기화**된 값은 변경되지 않는다.<p>

		- [[Scope]] property 는 해당 function execution context 상위에 있는 <u>모든 부모 계층의 <span style="color:#c11f1f">VO</span> 를 갖는다.</u><p>
		
      - A 함수를 선언한다
  
              ```javascript
              
              // global execution context
              
              function A(){
      
              }
        
              ```
  
      - <span style="color:#c11f1f">함수 생성</span> 시 함수 객체 내부
      
              ```javascript
              A: {
                [[Scope]]: [
                  // A 함수는 [[Scope]] property 를 통해 global execution context 의 VO 를 갖는다.
                  globalExecutionContext.VO
                ];
              }
              ```
      - 함수 내부에 또 다른 함수 생성
      
              ```javascript
              
              // global execution context
              
              // 변수 선언 VO.x
              var x = 1; 
      
              function A(){
      
                // function execution context
                // 변수 선언 AO.y
                var y = 1;
      
                function B(){
                
                  // function execution context
                }
              }
              
              A();
        
              ```
  
      - 각 함수 객체 내부
      
              ```javascript
                A: {
                  [[Scope]]: [
                    // A 함수는 global execution context 의 VO 를 갖는다.
                    globalExecutionContext.VO
                  ];
                }
        
                B: {
                  [[Scope]]: [
                    // B 함수는 global execution context 의 VO 와 부모 함수인 A 함수의 AO(VO)를 갖는다.					
                    <A> functionExecutionContext.AO(VO),
                    globalExecutionContext.VO
                  ];
                }
                
              ```

	- 함수 호출
   
		- 함수 호출 시, function execution context 내부에 **Scope Chain** 이 추가된다.<p>
		
		- 생성된 **Scope Chain** 은 함수 [[Scope]] property 를 그대로 가져가 **할당**되며, function execution context 내부 <span style="color:#c11f1f">AO</span>(VO)는 **Scope Chain** 의 <u>**가장 앞**에 추가된다.</u>

            ```javascript
        
              // global execution context
        
              var x = 1;
        
              function A(){
        
                // function execution context
        
                var y = 2;
              }
        
              A(); // call A function object
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
                    // 함수 호출 시, 생성된 AO 는 Scope Chain 의 가장 앞(ScopeChain[0])에 추가되며, 식별자 검색 시 가장 먼저 검색된다.
                    AO(VO): {
                      y: 2
                    },                  
                    globalExecutionContext.VO: {
                      x: 1,
                      A: < reference to function >
                    }
                  ]
                },
                globalExecutionContext: {
                  VO: {
                    x: 1,
                    A: < reference to function >								
                  }
                }
              ];
            ```

	- 식별자 해석 과정
		
		- **식별자** 검색 순서는 <u>**Scope Chain** 의 가장 **바닥**(ScopeChain[0])에서부터</u>, <u>가장 **상위**(ScopeChain[scope.length]) 까지</u> 검색 후, 그 값을 반환한다.

            ```javascript
        
              // global execution context
        
              var x = 1;
        
              function A(){
        
                // function execution context
        
                var y = 2;
                
                // functionExecutionContext.scopeChain.globalExecutionContext.VO.x                
                console.log(x); // 1
                // functionExecutionContext.scopeChain.AO.y
                console.log(y); // 2
              }
        
              A();
            ```
		- <span style="color:#c11f1f">ECStack</span> 내부

            ```javascript
              var ECStack = [
                <A> functionExecutionContext: {
                  AO(VO): {
                    y: 2
                  },
                  Scope(Scope Chain): [
                    AO(VO): {
                      y: 2
                    },                  
                    globalExecutionContext.VO: {
                      x: 1,
                      A: < reference to function >
                    }
                  ]
                },
                globalExecutionContext: {
                  VO: {
                    x: 1,
                    A: < reference to function >								
                  }
                }
              ];
            ```

		- 만약, 각 Execution Context 에 선언된 <strong>식별자 이름</strong>이 같은 경우, 위에서 언급한 <strong>Scope Chain</strong> 의 <strong>검색 순서</strong>로 해당 값이 정해지게 된다.
        
        - BFunctionExecutionContext.AO.y
        
            ```javascript
            
            // global execution context
            var y = 1;
      
            function A(){
      
              // function execution context
              
              var y = 2;
      
              function B(){
      
                // active function execution context
      
                var y = 3;
                
                // BFunctionExecutionContext.AO.y
                console.log(y); // 3
              }
      
              B(); // AO.B() === null.B();
            }
      
            A();
            ```
        - AFunctionExecutionContext.AO.y
    
            ```javascript
        
              // global execution context
              
              var y = 1;
        
              function A(){
        
                // function execution context
        
                var y = 2;
        
                function B(){
        
                  // active function execution context
              
                  // B 함수에 선언된 y 변수를 삭제한다.
                  // var y = 3;
                  // AFunctionExecutionContext.AO.y
                  console.log(y); // 2
                }
        
                B(); // AO.B() === null.B();
              }
        
              A();
            ```
        - globalExecutionContext.VO.y
    
            ```javascript
            
              // global execution context
              var y = 1;
        
              function A(){
        
                // function execution context
                
                // A 함수에 선언된 y 변수를 삭제한다.
                // var y = 2;
        
                function B(){
        
                  // active function execution context
                  // B 함수에 선언된 y 변수를 삭제한다.
                  // var y = 3;
        
                  // globalExecutionContext.VO.y
                  console.log(y); // 1
                }
        
                B(); // AO.B() === null.B();
              }
        
              A();
            ```
            
      - <span style="color:#c11f1f">ECStack</span> 내부

            ```javascript
            var ECStack = [
      
              <B> activeFunctionExecutionContext: {
                AO(VO): {
                  y: 3
                },
                Scope(Scope Chain): [
                  AO(VO): {
                    y: 3
                  },                
                  <A> functionExecutionContext.AO(VO): {
                    y: 2,
                    B: < reference to function >
                  },                
                  globalExecutionContext.VO: {
                    y: 1,
                    A: < reference to function >
                  }
                ]
              },
              <A> functionExecutionContext: {
                AO(VO): {
                  y: 2,
                  B: < reference to function >
                },
                Scope(Scope Chain): [
                  AO(VO): {
                    y: 2,
                    B: < reference to function >
                  },                
                  globalExecutionContext.VO: {
                    y: 1,
                    A: < reference to function >
                  }
                ]
              },
              globalExecutionContext: {
                VO: {
                  y: 1,
                  A: < reference to function >
                }
              }
            ];
            ```

      - 또 다른 Scope Chain 예
          
            ```javascript
        
            // global execution context
      
            var num2 = 5;
      
            function add(num1, num2){
      
              // function execution context
                
              // result 변수는 undefined 로 초기화된다.
              console.log(result); // undefined
              
              // functionExecution.AO.num1
              // functionExecution.AO.num2
              return num1 + num2;
            };
            
            var result = add(5, 10);
            
            
            // 실행 코드 처리 후 result 변수에 15 가 할당된다.
            console.log(result); // 15
            ```
        - <span style="color:#c11f1f">ECStack</span> 내부
          
              ```javascript
              var ECStack = [
                <add> functionExecutionContext: {
                  AO(VO): {
                    arguments: {
                      0: 5,
                      1: 10
                    },
                    num1: 5,
                    num2: 10
                  },
                  Scope(Scope Chain): [
                    AO(VO): {
                      arguments: {
                        0: 5,
                        1: 10
                      },
                      num1: 5,
                      num2: 10
                    },                  
                    globalExecutionContext.VO: {
                      result: undefined,
                      add: < reference to function >,
                      num2: 5
                    }
                  ]
                },
                globalExecutionContext: {
                  VO: {
                    result: undefined,                  
                    add: < reference to function >,
                    num2: 5
                  }
                }
              ];
              ```

- JS 에서 말하는 [Closure](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/Closures)란? 별도의 **개념**이 아닌, <u>**Scope Chain 매커니즘**</u>에 의한 **바인딩 환경**을 말하는것이다.<p>
  
    - Closure 설명하는 일반적인 예제
    
          ```javascript
          
          // global execution context
          
          function makeAdder(x) {
            
            // function execution context
            
            // 반환되는 익명함수에는 Scope Chain 매커니즘에 의해 부모 계층의 모든 VO 가 포함되어있다.
            // 부모 계층의 AO(VO)({x: 5 or 10})
            return function(y) {
            
              // function execution context
              // Scope Chain 의 식별자 검색을 통해 부모 계층의 Vo.x 속성을 사용한다.
              
              // anonymousFunctionExecutionContext.scopeChain.makeAdderFunctionExecutionContext.AO.x
              // anonymousFunctionExecutionContext.scopeChain.AO.y
              return x + y;
            };
          }
          
          var add5 = makeAdder(5);
          var add10 = makeAdder(10);
          
          print(add5(2));  // 7
          print(add10(2)); // 12
          ```

- Eval Execution Context 의 **Scope Chain**

  - eval execution context 의 **Scope Chain** 은 calling context 의 **Scope Chain** 을 따라간다.
  
  - 즉 <span style="color:#6298c1">eval</span> 함수가 포함된 execution context 의 **Scope Chain** 을 갖는다.

          ```javascript
          evalExecutionContext.Scope === callingContext.Scope;
          ```	
  - 실행 코드 
      
          ```javascript
          
          // global execution context
          var x = 1;
          
          // global execution context 의 Scope Chain 을 갖는다.
          eval('console.log(x)'); // 1
    
          function A(){
    
            // function execution context
    
            var y = 2;
    
            // A function execution context 의 Scope Chain 을 갖는다.
            eval('console.log(x, y)'); // 1 2
    
            function B(){
    
              // B function execution context 의 Scope Chain 을 갖는다.
    
              var z = 3;
    
              eval('console.log(x, y, z)'); // 1 2 3
            }
    
            B();
          }
    
          A();		
          ```
          
- Function 생성자 함수

  - <span style="color:#c11f1f">Function 생성자 함수</span>를 통한, **함수 생성 시** 생성되는 [[Scope]] property 에는 global execution context 의 <span style="color:#c11f1f">VO</span> 만 포함된다.

        ```javascript
        
        // global execution context
        var x = 1;
        var y = 2;
  
        var fn = Function('console.log(x, y);'); // 1, 2
  
        fn();
        ```
  - globalExecutionContext 의 x 변수에 접근
      
        ```javascript
        
        // global execution context
        var x = 1;
  
        function A(){
  
          // function execution context
  
          var y = 2;
  
          var fn = Function('console.log(x);'); // 1
  
          fn();
        }
  
        A();
        ```
    - functionExecutionContext 의 y 변수에 접근
          
        ```javascript
  
        // global execution context
  
        var x = 1;
  
        function A(){
          
          // function execution context
          
          // AFunctionExecutionContext.AO.y
          var y = 2;
  
          var fn = Function('console.log(y);'); // Uncaught ReferenceError: y is not defined
  
          fn();
        }
  
        A();
        ```
			
- Prototype Chain 확장 검색

  - 만약 **Scope Chain** 내부 <span style="color:#c11f1f">VO</span> 에 원하는 **식별자 이름**이 없을경우, <span style="color:#c11f1f">VO</span> 객체의 **원형**인 Object.prototype 까지 검색 후 찾아낸다.
    
    - 즉 *global execution context 내부 <span style="color:#c11f1f">VO</span> 는 그 원형인 Object.prototype 객체를 참조할 수 있다.*

        ```javascript
          
        // global execution context
        
        function A(){
        
            // 어떤 execution context 의 VO 에도 x 속성이 선언하지 않았다.
        
            // function execution context
            // globalExecutionContext.VO.__proto__.x 속성을 참조한다.
            console.dir(x); // 1
        }
                          
        // Object 객체의 원형을 확장한다.
        Object.prototype.x = 1;
        
        A();
        ```
    - <span style="color:#c11f1f">ECStack</span> 내부
    
        ```javascript
          var ECStack = [
            functionExecutionContext: { // A function object
              AO(VO): {
              },
              Scope(Scope Chain): [
                AO(VO): {
                },              
                globalExecutionContext.VO: {
                  A: < reference to function >,
                  __proto__: {
                    x: 1
                  }
                }
              ]
            },
            globalExecutionContext: {
              VO: {
                A: < reference to function >,
                __proto__: {
                  x: 1
                }
              }
            }
          ];
        ```
- **Scope Chain** 을 **확장**하는 with 문 과 catch 절

    - with 문
    
        - with 문을 만나면, 해당 Execution Context 의 **Scope Chain** 은 전달된 인자로 **확장**된다.<p>
    
        - <span style="color:#c11f1f">실행 코드</span>
        
            ```javascript
      
            // global execution context
            
            // globalExecutionContext.VO.person
            var person = {
                name: 'Nicholas',
                age: 30
            };
            
            // globalExecutionContext.VO.num2
            var num2 = 10;
            
            // globalExecutionContext.VO.displayInfo
            function displayInfo(){
            
                // function execution context
                // displayInfoFunctionExecutionContext.AO.count
                var count = 5;
            
                // with 문에 전달된 인자(person)로 해당 Scope Chain 이 확장된다.
                with ({name: 'Angel', age: 18}){
            
                    // 확장된 Scope Chain 을 통해 객체 속성에 접근한다.
                    // displayInfoFunctionExecutionContext.scopeChain.withObject.name
                    console.log(name); // 'Angel'
                    // displayInfoFunctionExecutionContext.scopeChain.withObject.age
                    console.log(age); // 18
                }
            };
            
            displayInfo();
            ```
        - <span style="color:#c11f1f">ECStack</span> 내부
        
            ```javascript
              var ECStack = [
                <displayInfo> functionExecutionContext: {
                  AO(VO): {
                    arguments: {
                    },
                    count: 5
                  },
                  Scope(Scope Chain): [
                    // with 문으로 인해, Scope Chain 이 확장된다.(Scope Chain 의 가장 앞(ScopeChain[0])에 추가된다.
                    withObject(person): {
                      name: 'Angel',
                      age: 18
                    },    
                    AO(VO): {
                      arguments: {
                      },
                      count: 5
                    },                                  
                    globalExecutionContext.VO: {
                      person: {
                        name: 'Nicholas',
                        age: 30
                      },
                      displayInfo: < reference to function >,
                      num2: 10
                    }
                  ]
                },
                globalExecutionContext: {
                  VO: {
                    person: {
                      name: 'Nicholas',
                      age: 30
                    },
                    displayInfo: < reference to function >,
                    num2: 10
                  }
                }
              ];
            ```
    
    - catch 절
    
        - catch 절을 만나면, 해당 Execution Context 의 **Scope Chain** 은 전달된 인자(ex)로 **확장**된다.<p>
      
            ```javascript
      
              // global execution context
      
              try{
              
                a; // error
              }
              catch(ex){
              
                // globalExecutionContext.scopeChain.catchObject(ex).message
                console.log(ex.message);
              }
              ```
        - <span style="color:#c11f1f">ECStack</span> 내부
        
              ```javascript
                var ECStack = [
                  globalExecutionContext: {
                    VO: {
                    },
                    Scope(Scope Chain): [
                      catchObject(<exception object>): {
                        message: 'a is not defined',
                        ...
                      },                    
                      globalExecutionContext.VO: {
                      }
                    ]					
                  }
                ];
              ```	
