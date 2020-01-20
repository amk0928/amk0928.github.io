---
layout: post
title:  "자바스크립트 기초"
date:   2020-01-20 10:00:00 +0900
author: 지민규
categories: Rookie7
---
## Todolist를 만들자

* Todo를 추가, 완료할 수 있다.
* Todo는 미완료 상태와 완료된 상태 디자인 적용
* 종류에 따라 갯수 카운팅(전체/활성/완료)
* Todo를 삭제할 수 있따.
* 종류별로 보기(전체/활성/완료)

## 자바스크립트의 사용

* 스크립트 파일을 include 하는 방식

```html
<srcipt src="path/to/jsfils.js"> </script>
```

* 스크립트를 직접 코딩하는 방식
    * 해당 방식으로 만든 변수는 전역변수가 된다.

## use strict 사용

```javascript
'use strict'
```

* 조금 더 까다롭게 보는 것. 경고하지 않던 것들도 경고해줌. 웬만하면 써라

## 스크립트와 DOM과의 삭호작용 시점

* DOM(Document Object Model) : 자바스크립트로 HTML 문서를 다룰 수 있게 브라우저 환경 제공하는 API
* 브라우저는 HTML문서를 첫줄부터 순서대로 파싱하며 필요한 정보를 수집하고 렌더링한다.
    * 순서대로 파싱하면서 바로바로 렌더링 함.
* JS가 평가되는 시점에 Document에 렌더링되어 있는 엘리먼트에만 접근 가능
    * Document 렌더링 후에 스크립트를 실행하도록 아래쪽으로 내린다.
    * window의 onload 이벤트를 이용함.
        * onload는 DOM level 1 수준의 이벤트임(현재 level 3)
        * window는 전역 객체

```javascript
window.onload = function() {
    event
}
```

## DOM 전체에서 엘리먼트 찾기

```javascript
document.getElementById("myId"); // returns HTML Element or null
document.getElementsByTagName("div"); // returns HTML Collection or null
document.getElementsByClassName("myClass"); // returns HTML Collection or null
```

* document
    * 브라우저 상의 HTML 문서를 모델링한 객체
    * DOM API의 시작점
* HTML Collection
    * 유사배열 : Array 비슷하게 사용가능하지만 Array는 아님.(Array-like Object)
        * 숫자로 인덱싱 가능, length 존재
        * Array가 아니라서 Array API를 사용할 수 없음.
        * Array의 API를 사용하려면 Array로 변환한 뒤 사용해야함.
    * live한 컬렉션이다.
        * ex) div Tag를 가져온 후에, div Tag를 새로 추가하면 이전에 Collection에서도 추가된 것을 알 수 있음
* HTML Element는 다른 Element를 포함할 수 있다.(DOM은 트리구조로 모델링됨)
* 특정 엘리먼트의 자식 엘리먼트를 검색해 검색의 범위를 좁힐 수 있다.

```javascript
var myContentsEl = document.getElementById('myContents');
myContentsEl.getElementByTagName('A');
```

* Element에 이벤트 핸들러 적용
    * Parameter
        1. 이벤트 명
        2. 이벤트 핸들러
        3. 캡쳐링을 사용할지 여부, default = false
    * 이벤트 핸들러 안에서의 this는 이벤트가 적용된 엘리먼트
        * 기존에 onclick이라는 메소드에서 사용할 때 this가 해당 엘리먼트였기 때문에 현재까지 유지되고 있음

```javascript
element.addEventListener("click", function() {

}, false)
```

* Element 이벤트 해제
    * Parameter
        1. 이벤트 명
        2. 이벤트 핸들러(함수의 참조를 저장해놔야함)
        3. 캡처링을 사용할지 여부

```javascript
element.removeEventListener("click", handler, false);
```

## 동적으로 엘리먼트 만들기

* document.createElement(tagName)

```javascript
var newDiv = document.createElement("div");
var newP = document.createElement("p");
```

* document.createTextNode(text)

```javascript
var newText = document.createTextNode("텍스트 노드");
var newText2 = document.createTextNode("HELLO WORLD");
```

* 만들고 BODY 엘리먼트에 붙이지(append) 않으면 화면에 나타나지 않는다.

## 엘리먼트에 자식 엘리먼트 추가하기

* element.appendChild(node)

```javascript
var newDiv = document.createElement("div");
var newP = document.createElement("p");
var newText = document.createTextNode("HELLO ROOKIES");

newP.appendChild(newText);
newDiv.appendChild(newP);

document.body.appendChild(newDiv);
```

## 자식 엘리먼트 삭제하기

* element.removeChild(node);
    * 지우려는 대상의 참조를 알아야 지울 수 있다.
    * 노드 하나만 알면 뭐든 지울 수 있다.
    * 의미적으로는 부모로부터 자식을 지운다.

```javascript
var newDiv = document.createElement("div");
var newP = document.createElement("p");
var newText = document.createTextNode("HELLO ROOKIES");

newP.appendChild(newText);
newDiv.appendChild(newP);

document.body.appendChild(newDiv); //추가했지만
document.body.removeChild(newDiv); // 바로 삭제됨
```

## innerHTML을 이용해 동적으로 엘리먼트 만들기

```javascript
element.innerHTML = '<div>text</div>
```

* html 텍스트를 이용해 자식 노드를 생성

```javascript
document.body.innerHTML = "<div><p>HELLO ROOKIES</p></div>"
```

* 장점
    * 새로운 엘리먼트들을 만드는 상황에서는 보통 제일 빠름(내부 엔진에서 생성)
        * 큰 차이 안나긴 함
    * DOM API를 이용하는 것보다 편한 경우가 많다.
        * 실제로 가독성이 좋음
* 단점
    * 엘리먼트에 작은 변화를 주는 경우 비효율적(DOM 트리를 모드 지우고 다시 만듬)
* 주의점
    * 값이 세팅될 때마다 모든 자식 엘리먼트를 지우고 다시 만들기 떄문에 Addition assignment(+=) 연산자를 직접 사용하는 것을 피해야 한다.
    * 엘리먼트를 만드는 동안 GC가 계속 돌아감
    * String을 먼저 만들고, innerHTML로 할당하여 해결

```javascript
//wrong
div.innerHTML = '<p>text1</p>';
    // 자식으로 <p>text1</p> 을 만든다.
div.innerHTML += '<p>text2</p>';
    // innerHTML === '<p>text1</p><p>text2</p>'
    // 모두 지우고 <p>text1</p> 다시만들고 <p>text2</p>도 만든다.

//correct
var content = '';

// HTML 스트링을 미리 만든다.
content += '<p>text1</p>'; // 루프에서 사용된다고 가정
content += '<p>text2</p>';

div.innerHTML = content; // innerHTML 할당은 한번만
```

## textContent

* innerHTML과 비슷하지만 텍스트 노드를 생성하거나 참조
* 자식 엘리먼트들의 모든 텍스트 노드를 확인할 수 있다.

```javascript
element.textContent = 'new text content';
console.log(element.textContent);
```

* 자바스크립트 코드도 나온다
    * 브라우저 입장에서는 자바스크립트 코드도 텍스트 다발

## 다른 DOM API 정보

* http://developer.mozilla.org

## Javascript 함수

* 함수 표현식

```javascript
cont add = function() {}
```

* 함수 선언식

```javascript
function add() {

}
```

* 선언식으로 선언하면 순서에 영향을 받지 않음

## 체크박스 다루기

* html
    * checked가 추가되어 있으면 체크 표시가 나옴

```html
<input type="checkbox" value="someValue" checked />
```

* js

```javascript
input.value // 값
input.checked // 체크여부(radio, checkbox)
```

## 이벤트의 전파

* 이벤트는 특별한 방향으로 전파된다.
    * 위에서 아래로 => Capturing
    * 아래에서 위로 => Bubbling
    * Capturing을 먼저 하고 Bubbling을 진행함.
    * Capturing이 가능한 것이 있는지 먼저 확인한 후 이벤트를 실행
    * Capturing이 다 일어난 후에 Bubbling이 있는지 확인 후 실행
        * check box가 속한 li에 이벤트를 등록하면 check box에 의해 체크된 박스가 li에 속한 이벤트 때문에 다시 취소됨
        * 버블링에 의한 것임
* 이벤트의 전파 제어
    * addEventListener의 세번째 인자로 캡쳐링/버블링 이벤트를 선택할 수 있다. `true`면 캡쳐링
    * 이벤트 핸들러에 첫번째 인자로 넘어오는 Event 객체촐 전파를 차단할 수 있다.

```javascript
var useCapturing = true;

element.addEventListener('click', function(eventObject) {
  eventObject.stopPropagation(); // 캡처링이나 버블링을 취소한다.(이벤트 전파를 차단한다)

  //etc
  eventObject.preventDefault(); // 디폴트 동작을 취소한다.(ex. 링크 이동 차단)
  //체크 박스의 클릭을 취소하는경우 브라우저마다 다른 동작을 한다.
  //그밖에 많에 정보를 포함
}, useCapturing);
```

* 이벤트 객체에는 흐름제어 외에도 많은 유용한 정보를 포함하고 있음
    * 이벤트 타입에 따라 조금씩 다름
* 내 자식에 이벤트가 발생했을 때, 내가 언제 이벤트를 제어할 것인지 선택하는 것이 캡쳐링과 버블링임
* 전파를 막고자하는 엘리먼트에서 stopPropagation()을 사용하면 됨

## 크롬 개발자 도구

* Sources 탭에서 Break point를 만들고, 이벤트 객체를 확인할 수 있음
    * ev의 currentTarget으로 이벤트 핸들러가 걸려있는 엘리먼트를 볼 수 있음
    * ev의 target으로 이벤트가 어디서 발생됐는지 알 수 있음
        * target.tagName이 'INPUT'이 아닐 때만 이벤트를 실행하도록 하면 버블링, 캡쳐링없이 이벤트 전파를 막을 수 있음

## CSS 제어

* 엘리먼트 객체의 style 어트리뷰트를 이용해 CSS를 적용한다.

```javascript
// css 속성을 그대로 사용함
element.style.width = '30px';
element.style.height = '100px';

element.style.display = 'block';
element.style.display = 'none';

// background-color같이 하이픈으로 이어진 속성은 카멜케이스로 변경
element.style.backgroundColor = '#f00';

// border같은 short-hand 속성도 그대로 사용함.
element.style.border = "1px solid red";

// 적용 가능 목록 확인
console.log(element.style);
```

## 엘리먼트에 CSS 아이디 & 클래스 적용

```html
<style>
  .myClass {border:1px solid #f00}
  #myId {padding: 5px}
</style>
```

```javascript
//HTML의 class속성과 동일
element.className = 'myClass';

//다중 적용시 띄어쓰기
element.className = 'myClass1 myClass2';

//아이디도 동일하게 적용가능(다중 적용 X)
element.id = 'myId';
```

* 아이디는 한 개의 엘리먼트에만 지정이 가능
    * 엘리먼트당 한 개만 적용
* 클래스는 여러 개의 엘리먼트에 적용이 가능하고 한 엘리먼트에 다중 적용이 가능
* 디자인 적용은 클래스를 이용하는 것을 추천
    * 재사용성이 높음
* 아이디는 엘리먼트를 찾기 위한 식별 용도로만 사용

## querySelector

``` javascript
document.querySelector('.myClass'); // 처음 찾은 한개만 리턴
document.querySelectorAll('.myClass'); // 해당되는 모든 엘리먼트 찾음
```

* element, document 모두 사용가능
* CSS 셀렉터를 이용해 엘리먼트를 찾음
* jQuery와 유사
* 최신 브라우저와 IE8 이상부터 지원

## 엘리먼트 노드 속성

* DOM상의 엘리먼트들은 트리구조로 서로 연결되어 있음
* 엘리먼트의 노드 관련 속성들로 연결되어있는 엘리먼트들을 탐색할 수 있음

``` javascript
element.firstChild // 첫번째 자식
element.lastChild // 마지막 자식
element.parentNode // 부모
element.nextSibling // 다음 형제
element.previousSibling  // 이전 형제
element.childNodes // 자식들을 모두 담고 있는 HTMLCollection
```