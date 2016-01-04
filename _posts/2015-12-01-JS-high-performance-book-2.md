---
layout: post
title: "Javascript 성능 최적화 책을 읽고.. - DOM 스크립팅 -"
description: "Javascript 성능 최적화 책을 읽고.. - DOM 스크립팅 -"
date:   2015-12-01
categories: book
tags: [book]
---

##DOM 스크립팅

- <h3>원글: <em><strong>DOM</strong>이란? XML 문서와 HTML 문서에서 동작하는 언어 독립적인 <span style="color:#c11f1f">API</span>(Application Programing language) 입니다.(<span style="color:#c11f1f">API 관점</span>에서 바라본 DOM)</em></h3>

- **DOM**(Document Object Model)에 대한 용어 정리:
  
    ![](/blog/assets/images/posts/20151207/dom_7.png)
            
  - <span style="color:#c11f1f">[Interface](http://www.w3.org/TR/DOM-Level-2-Core/glossary.html#dt-interface) 관점</span>에서 바라본 DOM: DOM Interfaces([Attr](https://developer.mozilla.org/en-US/docs/Web/API/Attr), [Document](https://developer.mozilla.org/en-US/docs/Web/API/Document) Interface 등), HTML Interfaces([HTMLHtmlElement](https://developer.mozilla.org/en-US/docs/Web/API/HTMLHtmlElement) Interface 등), SVG Interfaces([SVGAElement](https://developer.mozilla.org/en-US/docs/Web/API/SVGAElement) Interface 등) 등을 포함한 <span style="color:#c11f1f">Programing Interface</span> 를 의미한다.<p />
  
      - [Document Object Model (DOM)](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model)<p />
      
      - [What is the Document Object Model?](http://www.w3.org/TR/DOM-Level-2-Core/introduction.html)<p />
      
      - [DOM](http://www.w3.org/DOM/)<p />
      
      - [DOM을 구현한다는 표현은 반드시 binding이 되어야 할까요?](http://okjungsoo.tistory.com/entry/DOM%EC%9D%84-%EA%B5%AC%ED%98%84%ED%95%9C%EB%8B%A4%EB%8A%94-%ED%91%9C%ED%98%84%EC%9D%80-%EB%B0%98%EB%93%9C%EC%8B%9C-binding%EC%9D%B4-%EB%90%98%EC%96%B4%EC%95%BC-%ED%95%A0%EA%B9%8C%EC%9A%94)<p />
  
  - <span style="color:#c11f1f">API 관점</span>에서 바라본 DOM: 특정 [플랫폼](https://ko.wikipedia.org/wiki/%EC%BB%B4%ED%93%A8%ED%8C%85_%ED%94%8C%EB%9E%AB%ED%8F%BC)(JVM, .NET, 브라우저 등) 상에서 특정 **언어**(C#, JAVA, JS 등)를 통해 DOM(<span style="color:#c11f1f">Programing Interface</span>) 을 구현한 <span style="color:#c11f1f">API</span> 를 의미한다.<p />

        ```javascript
        DOM API = DOM(Interfaces) + 특정 언어
        
        DOM API = DOM(Interfaces) + JavaScript
        ```              
      
  - <span style="color:#c11f1f">DOM 트리</span>란? 브라우저를 통해 내려받은 <span style="color:#c11f1f">문서</span>(HTML, XML, SVG)를 렌더링 엔진을 통해 파서(어휘/구문 분석)한 후, 그에 대한 각 Node 들을 JS 를 통해 제어할 수 있도록 구현된, <span style="color:#c11f1f">계층화</span>(Node 를 통한 계층화)한 <span style="color:#c11f1f">트리 구조</span>를 의미한다.
      
      - <em>컴퓨터 과학에서 파싱((syntactic) parsing)은 일련의 문자열을 의미있는 토큰(token)으로 분해하고 이들로 이루어진 파스 트리(parse tree)를 만드는 과정을 말한다.</em><p />
                            
      - <em>트리 구축 알고리즘 파서가 생성되면 문서 객체가 생성된다. 트리 구축이 진행되는 동안 문서 최상단에서는 DOM 트리가 수정되고 요소가 추가된다. 토큰화에 의해 발행된 각 노드는 트리 생성자에 의해 처리된다. <u>각 토큰(어휘분석을 통한 토큰화)을 위한 DOM 요소의 명세는 정의되어 있다.....</u></em>

              ```javascript
              // 즉 DOM TREE 란? 문서 + 파서 + DOM API 로 생성된, 또 다른 API 를 의미한다.
              DOM TREE = Document(HTML, XML..) + Document Parser + DOM API
              
              ```
              
    - [파서](http://www.terms.co.kr/parser.htm)
   
    - [파싱과 파서란 무엇인가요?? 그리고 어디서 구할수 있을까요??](http://tip.daum.net/question/3257344)
      
- <h3>원글: <em>브라우저의 DOM 인터페이스는 자바스크립트를 통해 구현됩니다.</em></h3>
        
  - <span style="color:#c11f1f">JS</span> 를 통해 구현된 DOM Interface([Attr](https://developer.mozilla.org/en-US/docs/Web/API/Attr), [Document](https://developer.mozilla.org/en-US/docs/Web/API/Document) 등) 이며, 실행 불가능한 <span style="color:#c11f1f">함수 객체</span>로 생성된다.<p />
                
          ![](/blog/assets/images/posts/20151207/dom_1.png)
          
          ![](/blog/assets/images/posts/20151207/dom_6.png)
      
          ![](/blog/assets/images/posts/20151207/dom_3.png)
          
  - Attr 함수 객체는 [위임 과정](http://mohwa.github.io/blog/javascript/2015/10/16/prototype/)을 통해 [Node](https://developer.mozilla.org/en-US/docs/Web/API/Node) 및 [EventTarget](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget) 함수 객체를 상속받는다.   
  
      - 여기서 특히한점은 Attr 함수 객체의 생성자 함수 객체가 <span style="color:#c11f1f">Function 생성자 함수 객체</span>라는 것이며, Attr.\_\_proto\_\_ 속성(나를 만든 생성자 함수 객체의 원형)은 <span style="color:#c11f1f">Node 함수 객체</span>를 가리키고 있다는 것이다.(일반적인 위임 과정이라면 Attr.\_\_proto\_\_ 속성은 Function.prototype 을 가리키게된다)
      
              ![](/blog/assets/images/posts/20151207/dom_4.png)
              
      - 일반적인 원형 위임 과정
      
              ```javascript
              function A(){
              
              }
              
              console.log(A.constructor === Function) // true
              console.log(A.__proto__ === Function.prototype); // true
              ```

- <h3>원글: <em>브라우저의 대부분이 DOM 과 JS 를 별도로 구현합니다.</em></h3>

   - IE: DOM 구현([트라이던트](https://ko.wikipedia.org/wiki/%ED%8A%B8%EB%9D%BC%EC%9D%B4%EB%8D%98%ED%8A%B8_(%EB%A0%88%EC%9D%B4%EC%95%84%EC%9B%83_%EC%97%94%EC%A7%84))(레이아웃 엔진)) + JS 구현([JScript](https://ko.wikipedia.org/wiki/J%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8)(JS 엔진))
   
       [브라우저는 어떻게 동작하는가?](http://d2.naver.com/helloworld/59361)
       
       [Reflow 원인과 마크업 최적화 Tip](http://lists.w3.org/Archives/Public/public-html-ig-ko/2011Sep/att-0031/Reflow_____________________________Tip.pdf)

##글을 마치며

- **말머리**에서 **언급**한바와같이, 이 글은 책의 내용을 **비판**하고자하는 목적이 아닌, 책 내용중 개인적으로 중요하다고 판단한 부분과, 의미상 **모호**했던 부분을 다시한번 정리해본것이다.
