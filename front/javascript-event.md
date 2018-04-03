## JavaScript의 이벤트 전파와 이벤트 처리

DOM Node들에 addEventListener 메소드를 이용해 이벤트 핸들러를 등록하면 해당 이벤트가 발생할 경우 등록된 이벤트 핸들러(함수)가 호출되게 된다.

Node들에 addEventListener 메소드를 이용해 핸들러를 등록 가능한것은 아래와 같은 Node 인터페이스 구조 때문이다.

![domNode](/assets/images/js/domNode.png)

위처럼 addEventListener 메소드를 가진 EventTarget이 최상위에 있어 프로토타입 체이닝을 통해 하위의 노드들에서 모두 핸들러를 등록 가능한 구조를 가지게 된다.

### Event Capturing

HTML 태그들은 트리 구조로, 여러 태그들이 중첩되어 있다. 특정 태그에서 발생한 이벤트가 하위에도 적용되거나 상위에도 적용되어 이벤트가 중첩하여 발생할 수 있는데 이때 이벤트의 전파가 어떻게 될까?

```javascript
element.addEventListener('click', func, true);
```

위와같이 addEventListener의 세번째 파라미터를 true로 주면, 캡처링 방식으로 이벤트가 전파되게 된다.

캡처링 방식은 클릭한 타겟이 아니라, 그 타겟의 부모부터 이벤트가 전파되는 것이다.

### Event Bubbling

이벤트 버블링은 캡처링과 반대로, 자식으로부터 이벤트가 시작되어 부모로 전파되는 것이다.

```javascript
element.addEventListener('click', func, false);
element.addEventListener('click', func);
```

아까 설정한 세번째 파라미터를 false로 주거나, 디폴트로 주지 않으면 버블링으로 설정되게 된다. 버블링은 click 이벤트를 예로 클릭된 타겟으로부터 이벤트가 전파되게 된다.

### Bubbling과 Capturing 이벤트 전파

아래의 예제를 실행해보자.

[예제코드 링크](https://codepen.io/tramyu/pen/eVXQBq?editors=1111)

example.html

```html
<html>
  <head> 
    <style>
       .box {
          width: 100px;
          height: 100px;
          padding: 10px;
          border: 1px solid #000;
        }

        .box2 {
          color: #fff;
          background: #000;
          width: 50px;
          height: 50px;
          border: 1px solid #000;
        }

        .box3 {
          color: #fff;
          background: #fff;
          width: 10px;
          height: 10px;
          border: 1px solid #000;
        }
    </style>
  </head>
  <body>
    <h1>DOM event flow</h1>
    <div id="box1" class="box">
      <div id="box2" class="box2">
        <div id="box3" class="box3">
        </div>
      </div>
    </div>
  </body>
</html>
```

example.js

```javascript
box1.addEventListener('click', function(e) {
  console.log('box1 클릭')
}, true);
// 3번째 인자 true, capture

box2.addEventListener('click',function(e) {
  console.log('box2 클릭')
});

box3.addEventListener('click',function(e) {
  console.log('box3 클릭')
});
```

box1은 capture, 나머지 box2, box3는 bublling으로 동작한다. 구조는 아래 그림과 같으며 제일 큰게 부모 객체이다.

![](/assets/images/js/event.png)

가장 작은 하얀 박스를 클릭하면 어떤 순서로 호출될까? box1은 capturing, box2, box3은 bubblling 방식으로 이벤트 핸들러를 등록했다.

가장 작은 하얀 box3를 클릭하면 `box1`, `box3`, `box2`의 순서로 호출된다.

capturing으로 등록된 box1이 가장 먼저 실행되며, 그 뒤로 box3가 실행되고 box2가 실행된다. 이는 클릭 이벤트 발생시 캡처링으로 내려가며 캡처링 핸들러에 등록된 이벤트가 실행되고, 다시 타겟에 도착하여 다시 올라가면서 버블링 이벤트를 발생시키기 때문이다.

![](/assets/images/js/eventflow.png)

위 그림은 DOM-Level-3-Events 문서에 있는 이벤트 전파에 관한 플로우를 나타낸 그림이다.

대략적인 이벤트 플로우에 대해 설명해보자면

- 클릭 이벤트 발생시 부모로부터 내려가며 거치는 단계에서 캡처링 이벤트로 등록된 이벤트를 실행시킨다.
- 타겟까지 내려온 후, 타겟의 버블링 단계로 등록된 이벤트를 실행시킨다.
- 이제 다시 올라가면서 버블링으로 등록된 이벤트들을 실행시킨다.

위에서는 먼저 캡처링으로 등록된 box1의 이벤트가 실행되고, 클릭한 target인 box3까지 가서 버블링 이벤트 실행, 올라가며 box2의 버블링 이벤트가 실행되게 된다.

### 이벤트 전파 제어(stopPropagation())

이벤트가 전파되는 과정 중간에 이벤트를 막을수도 있다. 위의 예제 코드에서

box3에 stopPropgation()을 건다면 box1이야 캡처링으로 먼저 실행된 부분이라 막을 수 없지만 버블링으로 올라가며 실행 될 box2 이벤트는 실행되지 않게 된다.

기존 코드에 아래 부분을 추가하고 클릭해보자. `box1 클릭`, `box3 클릭` 만 나타나게 된다.

```javascript
box3.addEventListener('click',function(e) {
  console.log('box3 클릭')
  e.stopPropagation();
});
```

이외에 `preventDefault()`라는 메소드를 통해서 클릭 이벤트시 기본적으로 하는 디폴트 이벤트를 막을수도 있다.

### 이벤트 위임 처리(EventDelegation)

이벤트가 버블링될때 부모 요소에서 자식 요소들의 이벤트들을 처리하는 방식이다. 

개별 요소 하나하나에 이벤트 리스너를 등록할 필요가 없어서 메모리 사용량이 줄고, 누수 가능성이 줄어든다고 한다.

```javascript
document
  .querySelector("#result-board")
  .addEventListener("click", function(event) {
    if (event.target.className.indexOf("remove-button") < 0 ) return;
    let pane = event.target.closest(".list-group-item");
    pane.remove();
  });

```

위처럼 부모 객체에 클릭 이벤트로 등록하고, remove-button이라는 클래스의 객체를 클릭할 경우 list-group-item이라는 클래스명을 가진 가장 가까운 부모를 찾아 삭제하는 내용이다. 위처럼 자식 객체 하나하나에 이벤트 리스너를 등록하기보다 부모에서 저렇게 위임해서 처리하는 방식이 더 효율적인 것 같다.

##### 참고

- [DOM Level 3 spec](https://www.w3.org/TR/DOM-Level-3-Events/#dom-event-architecture)