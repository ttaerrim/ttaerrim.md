# 이벤트 위임

## 이벤트

* 이벤트는 무언가 일어났다는 신호
* 모든 DOM 노드는 이벤트를 만들어 낸다
* DOM에만 이벤트가 한정되는 것은 아님

### 이벤트 종류

* 마우스 이벤트
  * click
  * contextmenu: 요소 위에서 마우스 오른쪽 버튼을 눌렀을 때
  * mouseover / mouseout
  * mousedown / mouseup
  * mousemove
* 폼 요소 이벤트
  * submit
  * focus
* 키보드 이벤트
  * keydown
  * keyup
* 문서 이벤트
  * DOMContentLoaded
* CSS 이벤트
  * transitionend: CSS 애니메이션이 종료되었을 때

### 이벤트 핸들러

*   HTML의 on\<event> 속성에 핸들러를 할당

    ```html
    <button onclick="alert('click')" type="button" />
    ```
*   DOM 프로퍼티의 on\<event>로 할당

    ```html
    <input id="elem" type="button" value="클릭해 주세요.">
    <script>
      elem.onclick = function() {
        alert('감사합니다.');
      };
    </script>
    ```

→ onclick 프로퍼티는 하나밖에 없기 때문에 복수의 이벤트를 할당할 수 없음

#### addEventListener

* HTML이나 DOM 프로퍼티로는 하나의 이벤트에 복수의 핸들러를 할당할 수 없음
*   핸들러를 여러 개 할당하기 위해서는 `addEventListener` 사용

    ```jsx
    element.addEventListener(event, handler, [options]);
    ```

    * options
      * once: true이면 이벤트가 한 번만 트리거되고 자동으로 삭제
      * capture: 어느 단계에서 이벤트를 다뤄야 하는지 알려 주는 프로퍼티, 버블링/캡처링과 관련 있음
      * passive: true면 리스너에서 지정한 함수가 preventDefault를 호출하지 않음

#### removeEventListener

* `addEventListener` 로 등록한 핸들러를 삭제한다
*   변수에 함수를 저장해 놓지 않으면 핸들러를 삭제할 수 없음

    ```jsx
    // ❌
    element.addEventListener("click", () => alert("hi"));
    element.removeEventListener("click", () => alert("hi"));

    // ⭕️
    function alertHi() {
    	alert("hi");
    }
    element.addEventListener("click", alertHi);
    element.removeEventListener("click", alertHi);
    ```

어떤 이벤트는 `addEventListener`를 써야만 동작

* `DOMContentLoaded`

### 이벤트 객체

*   이벤트가 발생하면 브라우저는 **이벤트 객체**를 만든다

    * 여기에 이벤트에 관한 상세한 정보가 담겨 있다

    ```jsx
    element.addEventListener('click', (event) => {
    	alert(event.type + " 이벤트가 " + event.currentTarget + "에서 발생했습니다.");
      alert("이벤트가 발생한 곳의 좌표는 " + event.clientX + ":" + event.clientY +"입니다.");
    })
    ```

    * `event.type`
      * 이벤트 타입, 위에서는 click
    * `event.currentTarget`
      * 이벤트를 처리하는 요소
      * 화살표 함수로 핸들러를 만들어나 다른 곳에 바인딩하지 않은 경우에는 this가 가리키는 값과 같음
      * `event.currentTarget` 을 사용해 이벤트가 처리되는 요소 정보 얻을 수 있음
    * `event.clientX` / `event.clientY`
      * 포인터 관련 이벤트에서 커서의 상대 좌표 (모니터 기준이 아닌 브라우저 화면 기준)
    * 이벤트 타입에 따라 이벤트 객체에서 제공하는 프로퍼티가 다르다

### 객체 핸들러

*   객체나 클래스를 핸들러로 등록할 수 있음

    ```html
    <button id="elem">click</button>
    <script>
    	let obj = {
    		handleEvent(event) {
    			alert(`${event.type}이 ${event.currentTarget}에서 발생했습니다`)
    		}
    	}
    	elem.addEventListener("click", obj)
    </script>
    ```

    ```html
    <button id="elem">click</button>
    <script>
    	class Menu {
    		handleEvent(event) {
    			switch(event.type) {
    				case 'mousedown':
    					elem.innerHTML = '마우스 버튼을 눌렀습니다.';
    					break;
    				case 'mouseup':
    					elem.innerHTML += ' 그리고 마우스 버튼을 뗐습니다.';
    					break;
    			}
    		}
    	}
    	let menu = new Menu();
    	elem.addEventListener('mousedown', menu);
    	elem.addEventListener('mouseup', menu);
    </script>
    ```

### 📌 정리

이벤트 할당 방법

* HTML 속성
* DOM 프로퍼티
* 메서드: `addEventListener`와 `removeEventListener`

HTML 속성을 사용한 이벤트 핸들러 할당은 자주 사용되지 않음

DOM 프로퍼티를 사용한 방법은 복수의 핸들러 할당이 불가능함

메서드를 사용하는 방법은 유연하지만 코드가 가장 길어짐

일부 이벤트는 메서드 방법으로만 처리할 수 있음

`addEventListener`는 객체 형태의 이벤트를 지원함

어떤 방법으로 이벤트 핸들러를 할당하든간에 첫 번째 인자는 이벤트 객체이다

## 이벤트 전파

* DOM 트리 상에 존재하는 DOM 요소 노드에서 발생한 이벤트는 DOM 트리를 통해 전파된다

```html
<!DOCTYPE html>
<html>
	<body>
		<ul id="fruits">
			<li id="apple">Apple</li>
			<li id="banana">Banana</li>
			<li id="orange">Orange</li>
		</ul>
	</body>
</html>
```

1. 두 번째 바나나 li 요소를 클릭하면 클릭 이벤트가 발생한다
2.  생성된 이벤트 객체는 이벤트를 발생시킨 DOM 요소인 이벤트 타겟을 중심으로 DOM 트리를 통해 전파된다



    ![](https://i.imgur.com/QOPXWj8.png)
3. 캡처링 단계(Capturing Phase): 이벤트가 상위 요소에서 하위 요소 방향으로 전파
4. 타겟 단계(Target Phase): 이벤트가 이벤트 타겟에 도달
5. 버블링 단계(Bubbling Phase): 이벤트가 하위 요소에서 상위 요소 방향으로 전파

```jsx
const fruits = document.querySelector("#fruits")

fruits.addEventListener("click", (e)=>{
  console.log(`이벤트 단계: ${e.eventPhase}`)
  console.log(`이벤트 타겟: ${e.target}`)
  console.log(`커런트 타겟: ${e.currentTarget}`)
}, true)

fruits.addEventListener("click", (e)=>{
  console.log(`이벤트 단계: ${e.eventPhase}`)
  console.log(`이벤트 타겟: ${e.target}`)
  console.log(`커런트 타겟: ${e.currentTarget}`)
  
})
```

* ul에 이벤트 리스너를 등록하고 li 요소를 클릭하면 캡처링 단계의 이벤트를 캐치할 수 있는데
  * addEventListener의 세 번째 인자로 true를 넘겨 줘야 캐치할 수 있다
  * 넘겨주지 않았다면 버블링 단계의 이벤트를 캐치한다
* 이벤트 타겟과 커런트 타겟이 같으면 타겟 단계의 이벤트 객체를 캐치한다
* 보통은 이벤트를 발생시킨 이벤트 타깃에서 이벤트를 캐치하고
* 상위 DOM 요소에서도 캐치할 수 있다

🫧 버블링을 통해 전파되지 않는 이벤트

* 포커스 이벤트: focus/blur
* 리소스 이벤트: load/unload/abort/error
* 마우스 이벤트: mouseenter/mouseleave

→ 이벤트 객체의 event.bubbles 값이 false

버블링되지 않으므로 상위 요소에서 이벤트를 캐치하려면 캡처링 단계의 이벤트를 캐치해야 함

* 포커스 이벤트: focus/blur → **focusin/focusout**
* 리소스 이벤트: load/unload/abort/error
* 마우스 이벤트: mouseenter/mouseleave →**mouseover/mouseout**

→ 대체해서 사용할 수 있는 표시한 이벤트들은 버블링을 통해 전파가 되므로 캡처를 단계에서 이벤트 캐치하는 경우는 거의 없다

🌟 이벤트 전파 순서는 **캡처링 → 타겟 → 버블링**

## 이벤트 위임

* 아이템이 100개라면 모든 아이템에 이벤트 핸들러를 등록해야 할까?
  * 많은 DOM 요소에 이벤트 핸들러를 등록하기 때문에 성능 저하, 유지보수 부적합

✔️ **이벤트 위임**: 여러 개 하위 DOM 요소 대신 하나의 상위 DOM 요소에 이벤트 핸들러를 등록하는 방법

* 요소의 공통 조상에 이벤트 핸들러를 하나만 할당해서 여러 요소 한꺼번에 다루기
* 동적으로 하위 DOM 요소 추가해도 일일이 추가된 요소에 이벤트 등록할 필요도 없음‼️
* 이벤트 위임을 통해 상위 DOM 요소에 이벤트를 바인딩한 경우 이벤트 객체의 target 프로퍼티와 currentTarget 프로퍼티가 다른 DOM 요소를 가리킬 수 있다
  * `currentTarget`: 이벤트가 바인딩된 DOM 요소 (변하지 않음)
  * `target`: 실제로 이벤트를 발생시킨 DOM 요소 (클릭된 정확한 DOM 요소를 가리키므로 때마다 다름)

```jsx
modalContainer.addEventListener('click', ({ target }) => {
    if (!target.matches('.modal .buttons button.cancle')) return;
    closeModal();
  });
```

* 다음과 같이 이벤트가 어디에서 발생했는지만 검사해서 적용시켜 주면 된다

## 이벤트 동작 중단

### 기본 동작 중단

`event.preventDefault()` 는 DOM 요소의 기본 동작을 중단시킨다

### 이벤트 전파 방지

`event.stopPropagation` 은 이벤트 전파를 중지시킨다

*   하위 DOM 요소에서 발생한 이벤트를 상위 DOM 요소가 캐치하여 이벤트를 처리하지 않도록 자신이 발생시킨 이벤트가 전파되는 것을 중단하여 자신에게 바인딩된 이벤트 핸들러만 실행되도록 한다

    → 하위 DOM 요소의 이벤트를 개별적으로 처리하기 위해 이벤트의 전파를 중단

## 참고

[브라우저 이벤트 소개](https://ko.javascript.info/introduction-browser-events)

모던 자바스크립트 Deep Dive
