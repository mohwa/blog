---
layout: post
title: "Dynamic or Static Scope in JS"
date:   2015-10-16
categories: javascript
tags: [JavaScript]
---

동적 스코프와 정적 스코프

동적 스코프는 정적 스코프와 달리 현재 실행중인 함수 객체의 Scope Chain 내부에서 free variable 를 찾는다.

동적 스코프 구현


  //dynamically scope implementation

  var x = 1;

  function A() {

      // function execution context
      console.log(x);
  };

  (function() {

      // function execution context

      var x = 2;

      // eval calling context === current execution context

      // eval Scope Chain === current execution context internal Scope Chain

      eval(String(A));
      A(); // 2

  }());

  A(); // 1
ECStack 내부


  var ECStack = [

  functionExecutionContext: { // Generator is the code in eval execution context.
      AO(VO): {
      },
      Scope(ScopeChain): [
          globalExecutionContext.VO: {
              x: 1,
              A: < reference to function >
          },
          AO(VO): {
              x: 2
          }
      ]
  },
  functionExecutionContext: { // Anonymous function object
      AO(VO): {
          x: 2
      },
      Scope(ScopeChain): [
          globalExecutionContext.VO: {
              x: 1,
              A: < reference to function >
          },
          AO(VO): {
              x: 2
          }
      ]
  },
  functionExecutionContext: { // A function object
      AO(VO): {
      },
      Scope(ScopeChain): [
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
하지만 ECMAScript 는 정적 스코프만을 사용한다.


  // static scope implementation

  var x = 1;

  function A() {

      // function execution context
      console.log(x);
  };

  (function() {

      // function execution context

      var x = 2;

      A(); // 1

  }());

  A(); // 1
ECStack 내부


  var ECStack = [
      functionExecutionContext: { // A function object
          AO(VO): {
          },
          Scope(ScopeChain): [
              globalExecutionContext.VO: {
                  x: 1,
                  A: < reference to function >
              }
          ]
      },
      functionExecutionContext: { // Anonymous function object
          AO(VO): {
              x: 2
          },
          Scope(ScopeChain): [
              globalExecutionContext.VO: {
                  x: 1,
                  A: < reference to function >
              },
              AO(VO): {
                  x: 2
              }
          ]
      },                
      functionExecutionContext: { // A function object
          AO(VO): {
          },
          Scope(ScopeChain): [
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
