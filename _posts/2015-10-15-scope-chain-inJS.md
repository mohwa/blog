---
layout: post
title: "Scope Chain in JS"
date:   2015-10-15
categories: javascript
#tags: [JavaScript]
---

### [Scope Chain](http://javascriptweblog.wordpress.com/2010/10/25/understanding-javascript-closures/)

- 실행 컨텍스트와 <span style="color:red">ECStack</span> 내부
	
	- 예제 코드
	
          ```javascript
            // global execution context
        
            function A(){
        
              // function execution context
              var x = 1;
        
              function B(){
        
                var y = 2;
                // active function execution context
              }
        
              B(); // AO.B() == null.B()
            }
        
            A(); // this.A();
          ```
	- <span style="color:red">ECStack</span> 내부
	
          ```javascript
          var ECStack = [
            activeFunctionExecutionContext: { // B function object
              AO(VO): {
                y: 2
              }
            },
            functionExecutionContext: { // A function object
              AO(VO): {
                x: 1
              }
            },
            globalExecutionContext: {
              VO: {
                A: < reference to function >
              }
            }
          ];
          ```

- Function Life Cycle

	- <span style="color:red">Function Life Cycle</span> 은 함수 생성과 함수 호출로 나뉜다.
	
	- 함수 생성

		- <span style="color:red">함수 생성</span> 시점에서 함수 객체의 [[Scope]] property 가 생성된다.
		
		- [[Scope]] property 는 직접적인 접근이 불가능하다.

		- <span style="color:red">함수 생성</span> 시점에서 초기화된 [[Scope]] property 는 변경되지 않는다.

		- [[Scope]] property 는 fucntion execution context 상위에 있는 모든 부모 계층의 VO를 갖는다.

			- *하지만, 함수 객체가 Free Variable 를 사용하지 않는 경우에는 JS 엔진이 성능 최적화를 위해 부모 계층의 VO 를 함수 내부에 저장하지 않을 수도 있다. 그러나 ECMA-262-3 스펙은 이에 대해 언급하고 있지 않다. 따라서 공식적으로 모든 함수는 생성 시점에 [[Scope]] 프로퍼티에 스코프 체인을 저장한다.*

		  - *자유 변수(Free Variable) 란? 함수 객체가 접근 가능한 변수 중, 함수로 전달되는 Arguments 와 지역 변수를 제외한 변수를 말한다.(함수 Active Object(이하 <span style="color:red">AO</span>)에 포함되지 않는 변수들)*

			- 예제 1

                  ```javascript
                  
                    // global execution context
                    
                    function A(){
            
                    }
            
                  ```

			- <span style="color:red">함수 생성</span> 시점에서의 함수 객체 내부
			
                ```javascript
                  A: {
                    [[Scope]]: [
                      globalExecutionContext.VO
                    ];
                  }
                ```
              
			- 예제 2 (함수 객체 몸체에 또 다른 함수 객체 존재 시)
			
                  ```javascript
                  
                    // global execution context
            
                    var x = 1;
            
                    function A(){
            
                      // function execution context
                      var y = 1;
            
                      function B(){
            
                      }
                    }
                    
                    A();
            
                  ```

			- 각 함수 객체 내부
			
                  ```javascript
                    A: {
                      [[Scope]]: [
                        // 부모 계층의 VO
                        globalExecutionContext.VO
                      ];
                    }
            
                    B: {
                      [[Scope]]: [
                        // 부모 계층의 VO					
                        <A> functionExecutionContext.AO(VO)
                        globalExecutionContext.VO
                      ];
                    }
                    
                  ```
          - console.log 통해서는 [[Scope]] 프로퍼티 내부를 정확히 볼 수 없다.
    
          ![](/Users/sgjeon/blog_doc/img/113.jpg)
			
	- 함수 호출
	
		- <span style="color:red">함수 호출</span> 시점에서 function execution context 내부에 Scope Chain 이 추가된다.

		- <span style="color:red">함수 호출</span> 시점에서 function execution context 내부 AO(VO)는 Scope Chain 의 가장 앞에 추가된다.

            ```javascript
        
              // global execution context
        
              var x = 1;
        
              function A(){
        
                // function execution context
        
                var y = 2;
              }
        
              A(); // call A function object
            ```
		- <span style="color:red">ECStack</span> 내부

            ```javascript
              var ECStack = [
                functionExecutionContext: {
                  AO(VO): {
                    y: 2
                  },
                  Scope(Scope Chain): [
                    globalExecutionContext.VO: {
                      x: 1,
                      A: < reference to function >
                    },
                    AO(VO): {
                      y: 2
                    }
                  ]
                },
                globalExecutionContext: {
                  VO: {
                    x: 1,
                    A: < reference to function >								}
                }
              ];
            ```
	- 식별자 해석 과정
		
		- <span style="color:red">식별자 해석 과정</span> 은 변수 이름에 해당하는 VO 의 검색 과정을 말한다.

		- 검색 순서는 Scope Chain 의 가장 깊은 곳 부터 가장 상위 까지 검색하여 값을 반환한다.

            ```javascript
        
              // global execution context
        
              var x = 1;
        
              function A(){
        
                // function execution context
        
                var y = 2;
        
                console.log(x); // 1
                console.log(y); // 2
              }
        
              A(); // call A function object
            ```
		- <span style="color:red">ECStack</span> 내부

            ```javascript
              var ECStack = [
                functionExecutionContext: {
                  AO(VO): {
                    y: 2
                  },
                  Scope(Scope Chain): [
                    globalExecutionContext.VO: {
                      x: 1,
                      A: < reference to function >
                    },
                    AO(VO): {
                      y: 2
                    }
                  ]
                },
                globalExecutionContext: {
                  VO: {
                    x: 1,
                    A: < reference to function >								}
                }
              ];
            ```

		- *만약 각 실행 컨텍스트에 선언된 변수 이름이 같은 경우, Scope Chain 의 검색 순서로 인해 해당 값이 정해지게 된다.(Scope Chain 의 가장 깊은 곳에 있는 변수가 먼저 검색된다)*
		
        - 예제 1
    
            ```javascript
        
              var y = 1;
        
              function A(){
        
                // function execution context
        
                var y = 2;
        
                function B(){
        
                  // active function execution context
        
                  var y = 3;
        
                  console.log(y); // 3
                }
        
                B(); // AO.B() === null.B();
              }
        
              A(); // call A function object
            ```
        - 예제 2

            ```javascript
            
            var y = 1;
            
            function A(){
            
              // function execution context
            
              var y = 2;
            
              function B(){
            
                // active function execution context
            
            //        var y = 3;
            
                console.log(y); // 2
              }
            
              B(); // AO.B() === null.B();
            }
            
            A(); // call function object
            ```
        - 예제 3

            ```javascript
            var y = 1;
      
            function A(){
      
              // function execution context
      
            //    var y = 2;
      
              function B(){
      
                // active function execution context
      
            //        var y = 3;
      
                console.log(y); // 1
              }
      
              B(); // AO.B() === null.B();
            }
      
            A(); // call function object
            ```
		- <span style="color:red">ECStack</span> 내부

            ```javascript
              var ECStack = [
        
                activeFunctionExecutionContext: {
                  AO(VO): {
                    y: 3
                  },
                  Scope(Scope Chain): [
                    globalExecutionContext.VO: {
                      y: 1,
                      A: < reference to function >
                    },
                    AO(VO): {
                      y: 2,
                      B: < reference to function >
                    },
                    AO(VO): {
                      y: 3
                    }
                  ]
                },
                functionExecutionContext: {
                  AO(VO): {
                    y: 2,
                    B: < reference to function >
                  },
                  Scope(Scope Chain): [
                    globalExecutionContext.VO: {
                      y: 1,
                      A: < reference to function >
                    },
                    AO(VO): {
                      y: 2,
                      B: < reference to function >
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
            ![](/Users/sgjeon/blog_doc/img/132.jpg)

		- 그럼 아래 코드의 <span style="color:red">ECStack</span> 내부는?
		
            ```javascript
        
              var y = 1;
        
              function A(){
        
                // function execution context
        
                return function B(){
        
                  // active function execution context
                  console.log(y); // `
                }
              }
        
              A()();
            ```

	- 또 다른 Scope Chain 예
			
		- <span style="color:red">실행 코드</span>
		
            ```javascript
        
              // global execution context
        
              var num2 = 10;
        
              function add(num1, num2){
        
                // function execution context
        
                console.log(result); // undefined
        
                return num1 + num2;
              };
        
              var result = add(5, 10);
        
              console.log(result); // 15
            ```
		- <span style="color:red">ECStack</span> 내부
		
            ```javascript
              var ECStack = [
                functionExecutionContext: { // A function object
                  AO(VO): {
                    arguments: {
                      0: 5,
                      1: 10
                    },
                    num1: 5,
                    num2: 10
                  },
                  Scope(Scope Chain): [
                    globalExecutionContext.VO: {
                      add: < reference to function >,
                      result: undefined,
                      num2: 10
                    },
                    AO(VO): {
                      arguments: {
                        0: 5,
                        1: 10
                      },
                      num1: 5,
                      num2: 10
                    }
                  ]
                },
                globalExecutionContext: {
                  VO: {
                    add: < reference to function >,
                    result: undefined,							
                    num2: 10
                  }
                }
              ];
            ```

	          ![](/Users/sgjeon/blog_doc/img/130.png)
				
	- Function 생성자 함수
	
	  - <span style="color:red">Function 생성자 함수</span>를 통해 함수 생성 시 생성되는 [[Scope]] property 에는 global <span style="color:red">VO</span> 만 포함된다.

              ```javascript
              
              //global context
              var x = 1;
              var y = 2;
        
              var fn = Function('console.log(x, y);'); // 1 2
        
              fn();
              ```
              
              ```javascript
              
              // global context
              var x = 1;
        
              function A(){
        
                // function context
        
                var y = 2;
        
                var fn = Function('console.log(x);'); // 1
        
                fn();
              }
        
              A();
              ```
              ```javascript
        
              // global context
        
              var x = 1;
        
              function A(){
                
                // function context
                
                // AO(VO).y
                var y = 2;
        
                var fn = Function('console.log(x, y);'); // Uncaught ReferenceError: y is not defined
        
                fn();
              }
        
              A();
              ```
              ![](/Users/sgjeon/blog_doc/img/115.jpg)
			
	- prototype chain 확장 검색
	
		- 만약 <span style="color:red">Scope Chain</span> 내부 VO에 원하는 식별자가 없을경우, 해당 Prototype 까지 검색해 찾아낸다.
			
			- *global execution context 내부 VO 는 그 원형인 Object.prototype 객체를 참조한다.*
 
                  ```javascript
            
                    function A(){
            
                      console.log(x); // 1
                    }
            
                    Object.prototype.x = 1;
            
                    A(); // A is reference Type
                  ```
			- <span style="color:red">ECStack</span> 내부
			
                  ```javascript
                    var ECStack = [
                      functionExecutionContext: { // A function object
                        AO(VO): {
                          arguments: {
            
                          }
                        },
                        Scope(Scope Chain): [
                          globalExecutionContext.VO: {
                            A: < reference to function >,
                            __proto__: {
                              x: 1
                            }
                          },
                          AO(VO): {
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
                  ![](/Users/sgjeon/blog_doc/img/116.jpg)
			
	- Global Execution Context 의 <span style="color:red">Scope Chain</span>
	
		- global execution context <span style="color:red">Scope Chain</span> 은 global.VO 만 포함한다.

            ```javascript
              globalExecutionContext:{
                VO: ...
                Scope(Scope Chain): [
                
                  // global.VO 만 포함한다.
                  globalExecutionContext.VO
                ]
              }
            ```
	- eval execution context 의 <span style="color:red">Scope Chain</span>
	
		- eval execution context 의 <span style="color:red">Scope Chain</span> 은 calling context 의 Scope Chain 을 갖는다.

            ```javascript
              evalExecutionContext.Scope === callingContext.Scope;
            ```	
      - 예제		
      
            ```javascript
            
              // global execution context
              var x = 1;
        
              eval('console.log(x)'); // 1
        
              function A(){
        
                // function execution context
        
                var y = 2;
        
                eval('console.log(x, y)'); // 1 2
        
                function B(){
        
                  // function execution context
        
                  var z = 3;
        
                  eval('console.log(x, y, z)'); // 1 2 3
                }
        
                B();
              }
              A();		
            ```
	- Scope Chain 확장과 with 문
	
		- 함수 실행 시점에서 초기화된 Scope Chain 이 변경된다.
			- *기본적으로 한번 초기화된 Scope Chain 은 변경되지 않는다.*

		- with 문에 전달된 객체는 Scope Chain 의 가장 앞에 놓여진다.
			- *함수 실행 시점에서 with 문을 만나면, 해당 Scope Chain 은 전달된 인자로 확장된다.*

				- 예제 1 (with 문으로 전달된 객체 수정)

                    ```javascript
            
                      // global execution context
                      var x = 1;
            
                      // with 문
                      with (this /* global.VO */){
            
                        // global execution context
                        x = 4; // this.x = 4;
            
                        console.log(x); // 4
                      }
            
                      console.log(x); // 4
                    ```
				- <span style="color:red">ECStack</span> 내부
				
                    ```javascript
                      var ECStack = [
                        globalExecutionContext: {
                          VO: {
                            x: 4 // 1 >> 4
                          },
                          Scope(Scope Chain): [
                            globalExecutionContext.VO: {
                              x: 4 // 1 >> 4
                            },
                            WithObject: {
                              x: 4 // 1 >> 4
                            }
                          ]					
                        }
                      ];
                    ```	
				
				- 예제 2 (with 문 내부에서 동일한 이름의 변수 선언 시)
								
                    ```javascript
            
                      // global execution context
                      var x = 1;
            
                      // with 문
                      with ({x: 2}){
            
                        // global execution context 진입 시 선언된 x 변수는 이미 초기화 되었기때문에, 선언을 위한 var 키워드는 무시된다.
            
                        // 즉, with 문 전달 객체인 withObject 의 x 프로퍼티를 평가 후 변경한다.
            
                        var x = null; // withObject.x = null;
            
                        console.log(x); // widthObject.x === null;
                      }
            
                      console.log(x); // this.x === 1;
                    ```
				- <span style="color:red">ECStack</span> 내부
				
                    ```javascript
                      var ECStack = [
                        globalExecutionContext: {
                          VO: {
                            x: 1
                          },
                          Scope(Scope Chain): [
                            globalExecutionContext.VO: {
                              x: 1
                            },
                            WithObject: {
                              x: null // 2 >> null
                            }
                          ]					
                        }
                      ];
                    ```	
				- 예제 3 (with 문 내부에서 global.VO 의 프로퍼티 값 변경)
								
                    ```javascript
                      // global execution context
                      var x = 1;
            
                      // with 문
                      with ({x: 2}){
            
                        x = null; // withObject.x = null;
            
                        this.x = 2; // global.VO.x = 2;
            
                        console.log(x); // null
                      }
            
                      console.log(x); // 2
                    ```
				- <span style="color:red">ECStack</span> 내부
				
                    ```javascript
                      var ECStack = [
                        globalExecutionContext: {
                          VO: {
                            x: 2  // 1 >> 2
                          },
                          Scope(Scope Chain): [
                            globalExecutionContext.VO: {
                              x: 2 // 1 >> 2
                            },
                            WithObject: {
                              x: null // 2 >> null
                            }
                          ]
                        }
                      ];
                    ```

		- 또 다른 With 문 Scope Chain 확장

			- <span style="color:red">실행 코드</span>
			
                  ```javascript
            
                    // global execution context
            
                    var person = {
                      name: 'Nicholas',
                      age: 30
                    };
            
                    var num2 = 10;
            
                    function displayInfo(){
            
                      // function execution context
            
                      var count = 5;
            
                      with (person){
                        console.log(name); // 'Nicholas'
                        console.log(age); // 30
                      }
                    };
            
                    displayInfo();
                  ```
			- <span style="color:red">ECStack</span> 내부
			
                  ```javascript
                    var ECStack = [
                      functionExecutionContext: { // A function object
                        AO(VO): {
                          arguments: {
                          },
                          count: 5
                        },
                        Scope(Scope Chain): [
                          globalExecutionContext.VO: {
                            person: {
                              name: 'Nicholas',
                              age: 30
                            },
                            displayInfo: < reference to function >,
                            num2: 10
                          },
                          AO(VO): {
                            arguments: {
                            },
                            count: 5
                          },
                          withObject: {
                            name: 'Nicholas',
                            age: 30
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
    
                  ![](/Users/sgjeon/blog_doc/img/131.png)

	- catch 절
	
		- 함수 실행 시점에서 초기화된 Scope Chain 이 변경된다.

		- 추가된 객체는 Scope Chain 의 가장 앞에 놓여진다.
			- *함수 실행 시점에서 catch 절을 만나면, Scope Chain 은 전달된 인자(ex)로 확장된다.(with 문 확장과 거의 동일하다)*

              ```javascript
        
                // global execution context
        
                try{
                
                  a; // error
                }
                catch(ex){
                  console.log(ex);
                }
              ```
			- <span style="color:red">ECStack</span> 내부
			
              ```javascript
                var ECStack = [
                  globalExecutionContext: {
                    VO: {
                    },
                    Scope(Scope Chain): [
                      globalExecutionContext.VO: {
                      },
                      catchObject(<exception object>): {
                        message: 'a is not defined',
                        ...
                      }
                    ]					
                  }
                ];
              ```	
              ![](/Users/sgjeon/blog_doc/img/117.jpg)
				
