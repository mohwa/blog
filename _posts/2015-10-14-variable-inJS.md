---
layout: post
title: "Variable in JS"
date:   2015-10-14
categories: javascript
tags: [JavaScript]
---

- 변수는 오직 <span style="color:#6298c1">var</span> 키워드를 통해, **선언**된다.
	
	- 즉 <span style="color:#6298c1">var</span> 키워드가 생략된 y 속성은, <u>**변수가 아닌** <span style="color:#c11f1f">VO</span> 의 **속성**일 뿐이며</u>, Execution Context 진입 시에는, <span style="color:#c11f1f">VO</span> 의 새로운 **속성**으로 추가되지 않는다.

          ```javascript
        
          // global Execution Context
          
          // var 키워드를 통해 변수가 선언되었다.
          // Execution Context 진입 시 undefined 로 초기화된다.
          console.log(this.x); // undefined
          console.log(x); // undefined
          
          // var 키워드를 통한 변수 선언
          var x = 1;
          
        
          try{
        
            // Execution Context 진입 시 VO 의 새로운 속성으로 추가되지않으며, 그에 따라 런타임 에러가 발생한다.
            console.log(y);
          }
          catch(ex){
            console.log(ex.message); // y is not defined
          }
         
          
          // var 키워드가 생략된 y 속성
          y = 2;
        
          ```

	- Execution Context 진입 시 <span style="color:#c11f1f">ECStack</span> 내부

          ```javascript
          var ECStack = [
            globalExecutionContext: {
              VO: {
                x: undefined
              }
            }
          ];
          ```
	- **실행 코드 처리 후**에는 <span style="color:#c11f1f">VO</span> 의 새로운 **속성**으로 추가된다.

          ```javascript
        
          // global Execution Context
          
          // Execution Context 진입 시 VO 의 새로운 속성으로 추가되지않는다.
          y = 2; // 실행 코드
          
          // 실행 코드 처리 후 VO 의 속성으로 추가되었다.
          console.log(y); // 2
          console.log(this.y); // 2
          ```

	- **변수 선언식**을 통해, 생성된 **변수**는 <u>non-configurable</u> **속성**으로 정의되어있으며, <span style="color:#c11f1f">delete</span> 연산자를 통해 **삭제**되지 않는다.
	
          ```javascript
          
          // global Execution Context
          
          // var 키워드를 통한 x 변수는 non-configurable 속성을 가지고있다.
          var x = 1;
          
          // var 키워드가 생략된 y 속성은 configurable 속성을 가지고있다.
          y = 2;
          
          
          // Object {value: 1, writable: true, enumerable: true, configurable: false}
          console.log(Object.getOwnPropertyDescriptor(this, 'x'));
          
          // Object {value: 2, writable: true, enumerable: true, configurable: true}
          console.log(Object.getOwnPropertyDescriptor(this, 'y'));
          
          // delete 연산자를 통해 x 변수를 삭제한다.
          delete this.x;
          
          // delete 연산자를 통해 y 속성을 삭제한다.
          delete this.y;
          
          // x 변수는 non-configurable 속성으로 delete 연산자를 통해, 삭제되지 않는다.
          console.log(x); // 1
          
          // y 속성은 configurable 속성으로 delete 연산자를 통해, 삭제된다.
          console.log(this.y); // undefined
          ```
	- <span style="color:#6298c1">eval</span> 함수를 통한 **변수 선언식**은 **변수**로 선언되지 않는다.(<span style="color:#c11f1f">VO</span> 의 **속성**으로 추가된다)
	
	    - **변수**로 선언되지 않는다
	    
            ```javascript
            
            // global Execution Context
             
            // Execution Context 진입 시 VO 의 새로운 속성으로 추가되지않아, 런타임 에러가 발생한다.
            console.log(x); // Uncaught ReferenceError: x is not defined
        
            // var 키워드를 통한 변수 선언식
            eval('var x = 1');
            ```

      - <span style="color:#c11f1f">VO</span> 의 **속성**으로 추가된다
      
            ```javascript
            // global Execution Context
            
            // eval 함수를 통한 변수 선언식
            eval('var x = 1');
            
            // 정의된 객체 속성을 나열한다.(x 속성은 configurable 속성으로 정의되어있다)
            // Object {value: 1, writable: true, enumerable: true, configurable: true}
            console.log(Object.getOwnPropertyDescriptor(this, 'x'));
            
            console.log(this.x); // 1
            
            // delete 연산자를 통해 x 속성을 삭제한다.
            delete this.x;
            
            // 변수(non-configurable)가 아닌, VO 의 x 속성은 delete 연산자를 통해 삭제된다.
            console.log(this.x); // undefined

            ```
- [var](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/var)

- [ECMA-262-3 in detail. Chapter 2. Variable object 번역 글](http://huns.me/development/189)

- [Strict Mode (JavaScript)](https://msdn.microsoft.com/en-us/library/br230269(v=vs.94).aspx)

- [Special Operators](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/Obsolete_Pages/Core_JavaScript_1.5_Guide/Operators/Special_Operators#delete)
