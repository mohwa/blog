---
layout: post
title: "InterView 후기"
date:   2015-11-27
categories: [InterView]
tags: [InterView]
---

## **모** 사에서 치뤘던 인터뷰 내용중, 면접 당시에는 아쉽게도 제대로 풀지못한 문제들을 다시한번 정리하는 시간을 가져보았다.
 
##1. 무한 스크롤(현재는 구현 방법에만 집중해가며, 프로토타입 정도만 구현해보았지만, 차후 실무에 적용할 수 있도록 라이브러리화 시킬 생각이다)

- **프로토타입** 을 구현해본 결과, 면접관님도 말씀하신바와 같이 **논리 적인 문제** 보다는 실제 **구현** 시, 그 **복잡도**가 훨씬 더 클듯 하다.

    <iframe height='468' scrolling='no' src='//codepen.io/yanione/embed/WQqKmE/?height=468&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='http://codepen.io/yanione/pen/WQqKmE/'>WQqKmE</a> by mohwa (<a href='http://codepen.io/yanione'>@yanione</a>) on <a href='http://codepen.io'>CodePen</a>.
    </iframe>


##2. Window.requestAnimationFrame

[Window.requestAnimationFrame](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame)

##3. 싱글톤 and 팩토리

```javascript

var P = function(name){
    this.name = name;
}

P.prototype.getName = function(){
    return this.name;
}

P.prototype.setName = function(name){
    this.name = name;
}

var S = (function(){

    // 싱글턴 패턴
    var instance = null;

    return function(name){

        if (instance){
            instance.setName(name);
            return instance;
        }
        else{
            instance = new P(name);
            return instance;
        }

    }
})();

var o1 = S('a');
var o2 = S('b');

console.log(o1 === o2); // true

console.log(o1.getName()); // b
console.log(o2.getName()); // b

console.log(o1.getName() === o2.getName()); // true

```

## 마치며

**당사**의 **합/불**을 떠나, **개인적으로** 아주 좋은 **경험**을 했다고 생각한다. 또한 지금 느끼는 **후회**와 **아쉬움**은 앞으로 가질 스스로의 **도전 과제**라고 생각하면 될 듯 하다.  




