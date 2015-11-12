---
layout: post
title: "식별자 검색 테스트"
date:   2015-11-12
categories: JavaScript
tags: [JavaScript]
---

현재 정리중인 "[JavaScript 성능 최적화](http://www.hanbit.co.kr/book/look.html?isbn=978-89-7914-855-8) 책을 읽고.." 라는 포스트 내용 중 **Scope Chain** 을 통한, **식별자 검색**에 대한 이야기가 나온다.
 
또, 이 내용을 **검증**하기위한 **테스트 코드**를 아래와 같이 작성해보았다.

##식별자 검색 테스트

- (<span style="color:#c11f1f">n</span>) 개의 **전역 변수** 및 **중첩 함수**를 생성하여, **식별자 검색**에 대한 <span style="color:#c11f1f">성능 테스트</span>를 진행할 수 있다.

    <iframe height='350' scrolling='no' src='//codepen.io/yanione/embed/epbYzm/?height=268&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='http://codepen.io/yanione/pen/epbYzm/'>epbYzm</a> by mohwa (<a href='http://codepen.io/yanione'>@yanione</a>) on <a href='http://codepen.io'>CodePen</a>.
    </iframe>

##개선된 식별자 검색

- 마지막 **중첩 함수**의 **지역 변수**를 통해, **식별자 검색**을 개선시켰다.

  - 생성된 모든 **전역 변수**를 해당 **지역 변수**에 할당하여, **식별자 검색**에 걸리는 시간을 **최소화**한다.
  
          ```javascript
          // 마지막 중첩 함수일 경우
          if (i === (fnDepthCount - 1)) {

              // 식별자 검색 시작 시간
              fns.push('var start = new Date().getTime();');
              
              /******************************************************************/
              /******************************************************************/
              // 생성된 모든 전역 변수를 해당 function execution context 내부 AO 에 할당한다.
              for (var jj = 0; jj < globalVariableCount; jj++) {
                  // 지역 변수에 값을 할당한다.
                  fns.push('var y_' + jj + ' = x_' + jj + ';');
              }
              /******************************************************************/
              /******************************************************************/

              // 마지막 중첩 함수 내부에서, 생성된 n 개의 지역 변수를 n 번 순회하며, 식별자 검색을 통해 값을 가져온다.
              for (var ii = 0; ii < globalVariableLoopCount; ii++) {
              
                  /******************************************************************/
                  /******************************************************************/
                  // 생성된 모든 지역 변수를 순회한다.
                  for (var jjj = 0; jjj < globalVariableCount; jjj++) {
                      // 식별자 검색을 통해, 값을 가져온다.
                      fns.push('y_' + jjj + ';');
                  }
                  /******************************************************************/
                  /******************************************************************/
              }

              // 식별자 검색 종료 시간
              fns.push('var end = new Date().getTime();');
              fns.push('document.getElementById(\'searchTime\').value = (end - start) + \' ms\';');

              for (var j = i; j > -1; j--) {
                  fns.push('} A_' + j + '();');
              }
          }
          ```
                      
##테스트 결과

  - 이 결과는 **테스트 환경**에 따라, <span style="color:#c11f1f">오차 범위</span>를 보일 수 있다.
     
    - IE: <span style="color:#c11f1f">차후 테스트 진행...</span><p>
    
    - Chrome: <span style="color:#c11f1f">7</span>ms(개선 전) / <span style="color:#c11f1f">1</span>ms(개선 후)<p>
     
    - Safari: <span style="color:#c11f1f">5</span>ms(개선 전) / <span style="color:#c11f1f">0</span>ms(개선 후)<p>
    
    - FF: <span style="color:#c11f1f">2</span>ms(개선 전) / <span style="color:#c11f1f">0</span>ms(개선 후)


##마치며

- 이 코드는 **ms 단위**의 **정확한 테스트**를 위해 사용하기에는 무리가 따르며, 환경(브라우저)별, **엔진 최적화** 범위를 알 수 있는 정도로 사용하는것이 좋다.
  
##참고 URL

- [Scope Chain 그리고 Closure](http://mohwa.github.io/blog/javascript/2015/10/11/scope-chain-inJS/)
