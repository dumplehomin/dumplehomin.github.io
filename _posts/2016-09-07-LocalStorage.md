---
layout: post
title: "로컬스토리지"
date: 2016-09-07
image: '/assets/img/dica-rapida-2/main.png'
description: 'html'
main-class: 'js'
color: '#EB7728'
tags:
- HTML5
- LocalStorage
- 로컬스토리지
categories: HTML
twitter_text: ''
introduction: "HTML5에서는 클라이언트에 데이터를 저장하는 2개의 객체를 제공한다.
하나의 세션 단위로 데이터를 저장하는 SessionStorage 와,
만료 기간이 없는 LocalStorage 가 있다."
---

HTML5에서는 클라이언트에 데이터를 저장하는 2개의 객체를 제공한다.
하나의 세션 단위로 데이터를 저장하는 SessionStorage 와,
만료 기간이 없는 LocalStorage 가 있다.

두 가지 모두 key/value 쌍으로 저장하며, 사용 방법은 거의 비슷하다.

### 사용법

사용법은 정말 간단하다.
전역으로 할당되어 있는 localStorage의 메서드를 사용하며, 데이터는 key/value 쌍으로 구성된다.

아래와 같이 setItem() 과 getItem()으로 값을 저장하거나 가져올 수 있다.

    localStorage.setItem('foo', 'bar');
    localStorage.getItem('foo'); //--> "bar"

주의할 것은 key와 value 모두 string 으로 저장된다는 것이다.
예를 들어, 아래 코드에서처럼 원시 타입으로 저장해도, 가져오는 값의 타입은 string 이다.

    var n = 1;
    localStorage.setItem('n', n);
    typeof localStorage.getItem('n'); //--> "string"

Object 타입을 추가할 경우, toString()을 호출한 형태로 저장된다.

    var obj = {};
    var obj2 = {
      toString: function () {
        return "I'am obj2";
      }
    };

    localStorage.setItem('obj', obj);
    localStorage.setItem('obj2', obj2);

    localStorage.getItem('obj'); //--> "[object Object]"
    localStorage.getItem('obj2'); //--> "I'm obj2"


setItem()과 getItem()은 대부분의 경우 에러 없이 암묵적으로 수행된다.
  - setItem()은 기존 아이템이 있을 경우 덮어쓴다.
  - getItem()으로 값을 찾지 못했다면, 에러를 발생하지 않고 null을 리턴한다.


값은 [ ]  형태로도 설정하거나 가져올 수도 있다.

    localStorage['foo'] = 'bar';
    localStorage['foo']; //--> "bar"


그 외에도 몇 가지 유용한 메서드와 속성이 있다.

    localStorage.removeItem(키); // 해당 키를 지운다.
    localStorage.clear(); // 모두 지운다.

    localStorage.length; // 저장된 키의 개수
    localStorage.key(값); // 값으로 키를 찾는다.


이벤트 처리

로컬 스토리지의 내용이 변경되었을 때엔 'storage' 이벤트가 발생한다.
setItem()이 호출되었을 때가 아닌, 실제로 값이 변경되었을 때 발생한다.

window 객체에 이벤트를 할당하는 방식으로 처리할 수 있다.

    window.addEventListener('storage', function (evt) {
      // evt = 이벤트 객체
    }, false);


evt 객체에는 다음 속성이 존재한다.

    key
    oldValue
    newValue
    url

데이터가 removeItem()에 의해 삭제되었다면, newValue 값엔 null이 할당되고,
clear()로 모든 데이터가 삭제되었다면, key에는 공백 문자열('')이, value에는 각각 null이 할당된다.


주의:
대부분의 브라우저에서는 다른 윈도우에서 로컬 스토리지의 데이터를 변경했을 때에만 이벤트가 발생한다.
즉, 같은 윈도우에서 이벤트를 추가하고, localStroage의 값을 변경했을 때엔 이벤트가 발생하지 않는다.
아마도, 자신의 window에서는 변경된 상황을 알고 있을 것이라는 가정 때문인가 보다.

http://stackoverflow.com/questions/5370784/localstorage-eventlistener-is-not-called


어떤 제한이 있나?

- 동일 근원 정책을 기준으로 동작한다.
  즉, 프로토콜, 호스트, 포트 중 어느 하나라도 다르면, 각각 별도의 공간에 데이터가 저장된다.
  (document.domain 을 설정하더라도 공유되지 않는다)

- 디바이스마다 다르지만, 대부분 Origin 당 약 5MB 정도를 저장할 수 있다.
  문자열만 저장할 수 있으며, 2바이트 캐릭터(UTF-16)으로 저장된다.
  5메가를 넘어설 경우, QUOTA_EXCEEDED_ERR 오류가 발생한다.
  추가 공간을 요구할 수 없다.

- Safari아 iOS Safari에서 Private Browsing 옵션을 켜는 경우에는 사용할 수 없다.
  Private Browsing 을 사용하는 경우,
    - getItem()에서는 항상 null을 리턴하고,
    - setItem()에서는 QUOTA_EXCEEDED_ERR 을 발생한다.
    - clear()를 호출할 때에는 아무 동작을 수행하지 않는다. 에러를 발생하지도 않는다.


어디에 저장되나?

로컬 스토리지 데이터는 물리적으로 저장된다.
크롬은 localStorage로 SQLite 를 사용하며, Mac OS 에서는 해당 파일을 아래 디렉토리에 저장한다.

    ~/Library/Application Support/Google/Chrome/Default/Local Storage

데이터 파일은 `프로토콜_도메인_인덱스.localstorage` 라는 이름의 파일로 저장된다.
예를 들어 ohgyun.com에서 생성한 로컬 스토리지라면, `http_ohgyun.com_0.localstorage`라는 파일로 저장된다.

페이지가 로드되기 전에, 브라우저는 물리적으로 저장되어 있는 데이터를 읽어 메모리에 올려둔다.

참고:
sqlite3 커맨드라인 툴로 직접 해당 파일을 조회할 수 있다.
http://stackoverflow.com/questions/9669184/how-is-html5-webstorage-data-physically-stored


언제까지 저장되나? 만료 기간은?

사용자가 직접 데이터를 삭제하기 전까지 영구적으로 보관된다.
만료 기간을 설정할 수 없다.


성능은 어떤가?

로컬 스토리지는 동기적으로 작동한다.
즉 로컬 스토리지에 값을 설정하거나 가져올 때에는 렌더링이 블락된다.
또한 로컬 스토리지는 File I/O를 발생하기 때문에 다른 작업에 비해 비용이 큰 편이다.
따라서, 빈번하게 많은 양의 데이터를 읽고 쓰거나, 1MB 이상의 큰 데이터를 쓰는 것은 피하는 것이 좋다.

참고:
'모바일 디바이스에서 로컬 스토리지가 브라우저의 캐시보다 빠르다'는 포스트가 있다.
http://www.mobify.com/blog/smartphone-localstorage-outperforms-browser-cache/

위 포스트는 브라우저 캐시를 사용하는 것보다 로컬 스토리지에 저장해둔 것을 가져오는 게 더 빠르다는 내용이다.
25KB 크기의 스크립트를, 브라우저 캐시를 사용해 로드하는 것과,
로컬 스토리지에 저장해뒀다가 로드하는 것의 시간을 비교하는 방식으로 실험했다.
테스트 대상이었던 모든 모바일 기기에서 로컬 스토리지에서 불러오는 평균 속도가 더 빨랐다.

댓글 중에, 성능 분야에서 유명한 스티브 사우더스가 남긴 글이 있다.
로컬 스토리지는 페이지가 로드되기 전 데이터를 메모리에 올리는데, 실험에서는 이 시간을 고려하지 않았다는 의견이다.
다음 실험 결과를 지켜볼만 하다.


안전한가?

일단은 쿠키와 비교했을 때, 클라이언트의 디바이스에 직접 저장되고
네트워크로는 전송되지 않기 때문에 네트워크 레벨에서는 안전하다고 볼 수 있다.
하지만 평문으로 저장하는 경우, 그 값이 그대로 노출되어 XSS 등의 공격으로부터 안전하지 않다.

고려대학교 정보보호대학원에서 작성한,
'안전한 HTML5 로컬스토리지 구현에 대한 연구'라는 이름의 논문이 있다.
http://scholar.ndsl.kr/schArticleDetail.do?cn=JAKO201227935976492
(로그인 없이 다운로드 가능하다)

사용자로부터 받은 암호로 로컬 스토리지에 저장되는 데이터를 암호화한다는 아이디어다.
대칭키 방식으로 사용하며, 보안을 위해 도메인마다 랜덤하게 salt가 생성되도록 하는 방식이다.

암호화/복호화 비용이 꽤 클 것 같은데, 논문에서는 성능 차이가 거의 없다는 결론이다.
암호화할 레코드의 개수를 최대 10만 번까지 시도하며 걸린 시간의 평균값으로 비교를 했는데,
레코드의 크기에 대한 언급이 없고 특정 PC에서만의 결과라 신뢰하기엔 좀 조심스럽다.

아이디어는 괜찮은 것 같은데, 모바일에서의 성능이 궁금하기도 하고,
실제로 로컬 스토리지에 민감한 데이터를 저장하는 경우에는 어떤 게 있을지도 궁금하다.

일단은, 보안이 민감한 데이터는 평문으로 넣지 않는게 (당연하지만) 좋겠다.


로컬 스토리지를 활용한 라이브러리에는 어떤 게 있나?

https://github.com/addyosmani/basket.js

로컬 스토리지를 활용해 스크립트를 캐싱하고 로딩하는 모듈이다.
Addy Osmani가 만든 거라 왠지 믿음이 간다. ㅎㅎ

간단한 사용법에 대해 정리한 포스트가 있다.
http://rkjun.wordpress.com/2012/03/09/javascript-loader-basket-js-with-localstorage/




그 외 참고:
Session Storage

localStorage 와 사용법은 거의 동일하다.
단, 세션 범위 내, 즉, 새 탭이나 새 윈도우로 범위가 제한된다.
http://www.nczonline.net/blog/2009/07/21/introduction-to-sessionstorage/
