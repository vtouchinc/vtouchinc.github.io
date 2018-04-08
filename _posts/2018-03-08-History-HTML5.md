---
layout: post
title: 'vue-router 왜 사용하시나요?'
author: junghyun.lee
date: 2018-03-09 15:11
tags: [history, spa]
---

# History API in HTML5

현재 Electron 환경에서 Vue.js 를 통해 개발을 진행중이다.
본인은 웹 개발을 하는 것은 아니지만, 웹 개발에 익숙하기 때문에 웬만하면 그렇게 접근한다.

본인은 SPA 처럼 구현하기 위해 vue-router 를 도입해서 사용하고 있다.
그러던 중, 관련 질문을 받았다.
<p/>
<strong>"단순히 SPA와 같은 페이지 전환을 위해서라면 굳이 vue-router 를 쓸 필요가 있는가? 그냥 dynamic component 로 써도 되지 않는가?"</strong><p/>
쉽게 결론이 나지 않았다.
그 이유는 vue-router 는 SPA 를 위해 존재하는 것만 생각하고 사용했기 때문이다.

---

vue-router, react-router 등과 같은 라우터 모듈은 쉽고 편하게 SPA 구축을 위해 사용되고 있다.
이러한 모듈은 사실 내부적으로는 History API 를 기반으로 구현되었다.
History API 는 HTML5에서 새롭게 지원되었다.

History API 를 위해서는 세션 히스토리(Session History) 개념을 이해하는 것이 좋다.

브라우저에 로드되는 웹 페이지는 일종의 문서로써, 자신의 문서(document) 객체를 갖는다.
모든 문서 객체는 [document interface](https://www.w3.org/TR/dom/#document) 를 구현한다.
웹 페이지의 컨텐츠의 진입점 역할을 하는데, 다음 그림과 같은 형식들을 많이 접해봤을 것이다.

![HTML 트리 구조](/files/history-html-tree.jpg)[출처](https://www.tutorialspoint.com/javascript/javascript_html_dom.html)

browsing context 이란 document 객체들이 사용자에게 표현되는 환경이다.
일반적으로 웹 브라우저의 탭 또는 윈도우는 browsing context 포함한다.
`<frameset>` 의 `<iframe>` 또는 `<frames>` 도 마찬가지이다.

browsing context 는 세션 히스토리(Session History) 를 가진다.
세션 히스토리는 browsing context 에서 표현되었던, 표현되고 있는, 표현할 document 객체들의 목록이다.
조금 더 말하면, 세션 히스토리는 세션 히스토리 엔트리(Session History Entry) 라고 부르는 URL, serialized object, document, title 등으로 구성된 항목들이 존재한다.

세션 히스토리 엔트리를 보면 다음과 같다.
serialized object 는 사용자 인터페이스 상태를 말한다.
상태 객체(state object) 라고 불리기도 하고, 이 객체의 존재로 인해 SPA 페이지 개념을 사용할 수 있다.
상태 객체의 주된 목적은 새로운 document 가 열리면 재생성해야하기 상태를 저장하기 위함이다.
간단한 예로, 캐릭터 위치를 이동시켰는데 페이지를 갱신시키면 위치는 초기화가 되는데 페이지 변화에 상관없이 위치 변화를 적용하고 싶을 경우를 들 수 있다.

document 객체는 유일한 History 객체와 연결되고, 같은 세션 히스토리를 모델링한다.
결론적으로, Window 객체의 history 속성은 최신 doucment 를 위한 History 인터페이스를 구현하는 객체를 반환한다.
이 반환된 객체는 세션 히스토리를 조작할 수 있게 된다.
관련 내용은 [History](http://w3c.github.io/html/browsers.html#history) 통해 확인할 수 있다.
또한 개발자 도구 콘솔창에 window.history 를 통해 확인할 수 있다.

![history 콘솔창](/files/history-console.png)

History 관련 메소드 사용 방법에 대해 알아본다.
위에서 개발자 도구를 통해 확인할 수 있듯이 다음과 같은 속성이 존재한다.
`state, go, back, forward, pushState, replaceState`

<li>state - 위에서 언급한 state object 로써, 현재 state object 를 반환한다.</li>
<li>go() - 세션 히스토리의 특정 페이지로 로딩한다.</li>
<li>back() - 세션 히스토리의 이전 페이지로 로딩한다.</li>
<li>forward() - 세션 히스토리의 n번 페이지로 로딩한다.</li>
<li>pushState() - 주어진 정보를 세션 히스토리 최상단에 넣는다.</li>
<li>replaceState() - 주어진 정보로 세션 히스토리의 현재 엔트리를 갱신한다.</li>

history.go(-1) 는 이전 페이지, history.go(1) 는 다음 페이지 이동에 대한 처리로 익숙할 것이다.
처음 접했더라도 크게 어렵지 않고, back() === go(-1), forward() === go(1) 와 같이 단순히 1단계 페이지 이동 메소드이다.
History API 에 있어, 반드시 숙지해야할 것은 `pushState()` 와, `replaceState()` 이다.

```javascript
let state = {
  data: {}
}
shop.addEventListener("click" , (e) => {
  let output = document.querySelector("#output");
  output.innerHTML = getContent() // call ajax function to load the proper content for our url
  history.pushState(state, "shop", "shop.html")
})
```

*/index.html 에서 위 코드를 실행하면 URL 은 */shop.html 으로 변경된다.
이 때 shop.html 파일을 로드하지 않고, 파일의 존재 여부와도 상관없이 이루어진다.
일반적으로 ajax 와 같은 요청을 통해 url 에 맞는 컨텐츠를 가져와 뿌려주게 된다.

`replaceState()` 또한 동작은 `pushState()` 동일하다.
기억해야할 것은 히스토리에 있어서, replace 는 갱신이고, push 는 넣는다는 것이다.

위의 2가지 메소드는 `window.onpopstate` 이벤트와 연동하여 동작한다.

popstate 이벤트는 활성화된 히스토리 엔트리에 변화가 있을 때마다 발생된다.
발생 시 전달되는 state 속성은 state object의 사본이 된다.
그로 인해, 뒤로 가기의 상황에서 서버 요청없이 이전 목록의 화면을 보여주는 것과 같은 처리를 할 수 있다.

```javascript
window.addEventListener("popstate", (e) => {
  let copyState = e.state
  output.innerHTML = copyState.content
})
```

주의할 사항은 history.pushState() 또는 history.replaceState() 를 호출한다고, popstate 이벤트가 트리거 되지는 않는다.
뒤로가기 버튼과 같은 브라우저 액션 또는 history.back() 과 같은 자바스크립트를 통한 호출로 트리거가 발생한다.

다음은 `replaceState()`, `pushState()`, `popstate event` 를 활용한 예제이다.
마지막 이동시킨 빨간점의 위치를 항상 기억해서 페이지 이동 시에 위치가 유지된다.
개발자 도구의 콘솔창을 통해 관련 정보를 확인하길 바란다.
<p/>
<iframe height='265' scrolling='no' title='History API' src='//codepen.io/mygumi/embed/GxKaoQ/?height=265&theme-id=0&default-tab=result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/mygumi/pen/GxKaoQ/'>History API</a> by leejunghyun (<a href='https://codepen.io/mygumi'>@mygumi</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>