---
layout: post
title: "웹 클라이언트 정보 저장(LocalStorage, SessionStorage)"
date: 2018-08-22
tags: [javascript,web]
excerpt: ""
comments: true
---
# 웹 클라이언트 정보 저장(LocalStorage, SessionStorage)

## Intro
웹사이트의 정보를 저장하기 위해 반드시 서버를 사용하는 것만은 아니다. 클라이언트의 저장공간도 충분히 좋은 저장소로 사용가능하다. 방금 당신의 머릿속에 쿠키가 생각났을지도 모른다. 하지만 HTML5에는 더 효과적으로 클라이언트 저장 공간을 제공하는데 이는 쿠키(cookie)가 아닌 새로운 API이다.

HTML에서는 더 간단한 웹 스토리지를 제공하기 위해 LocalStorage와 SessionStorage이다. 둘의 가장 큰 차이점은 저장소로 기능이 동일하지만, SessionStorage는 말 그대로 **세션이 만료되면 저장된 데이터도 함께 사라진다**는 점이 다르다.

## Variable WebStorage

### Cookie
- 저장 크기가 가장 작으며 하나의 텍스트로 저장
- 간단한 텍스트 타입의 데이터 저장 용이
- ex) 24시간 뒤 만료하는 팝업에 이용가능

### LocalStorage & SessionStorage
- 대부분 임시적인 용도 또는 cash, history 기능을 위해 사용
- 서버에 반드시 저장할 필요가 없는 경우, 외로 필요하다가 느끼는 모든 것
- ex) 글 작성중 임시저장
- ex) 장바구니나 좋아하는 컨텐츠 등 수시로 변경되는 경우
- ex) 암호화된 비밀번호 형식

## LocalStorage Usage

### Save Data
```javascript
localStorage.name = "finn";
// 또는
localStorage.setItem("name","finn");
```

### Load Data
```js
localStorage.name;
// 또는
localStorage.getItem("name");
```

### Delete Data
```js
// localStorage 객체에서 원하는 값을 지우는 방법
localStorage.removeItem('name');

// 한번에 저장된 모든 값을 삭제하는 방법
localStorage.clear();
```
## SessionStorage Usage
세션을 기준으로 데이터가 저장 및 유지되기 때문에 이 객체에 저장된 값은 일시적인 수명을 가지게된다. 만약 세션이 종료되는 경우, 브라우저를 닫거나 일정시간 아무런 동작도 하지 않는다면 사라진다.

만약 데이터가 삭제를 피하고 싶다면 localStorage를 사용하자 아래의 사용방법은 localStorage와 거의 동일한 메소드와 프로퍼티 갖는다. (참고로 세션을 사용한 저장방법은 보안 및 중요한 데이터의 경우 무방비로 노출되는 경우를 피하기 위해서 사용되는 경우가 많다.)

```js
//&nbsp; domain이란 키(key) 값을 사용하여 해당 텍스트를 저장함
sessionStorage.setItem("domain", "webisfree.com");

// 키에 저장된 값을 반환. 여기서는 webisfree.com 출력됨
sessionStorage.getItem("domain");

// domain 키와 데이터 모두 삭제
sessionStorage.removeItem("domain");

// 저장된 모든 값 삭제
sessionStorage.clear();
```

## Perfomance
### 성능
로컬 스토리지는 동기적으로 작동한다. 로컬스토리지의 값을 설정하거나 가져올 때에는 렌더링이 블락된다.

또한 로컬 스토리지는 File I/O를 발생하기 때문에 다른 작업에 비해 비용이 큰 편이다. 따라서, 빈번하게 많은 양의 데이터를 읽고 쓰거나, 1MB 이상의 큰 데이터를 쓰는 것은 피하는 것이 좋다.

참고:
'[모바일 디바이스에서 로컬 스토리지가 브라우저의 캐시보다 빠르다](http://www.mobify.com/blog/smartphone-localstorage-outperforms-browser-cache/)'는 포스트가 있다.


위 포스트는 브라우저 캐시를 사용하는 것보다 로컬 스토리지에 저장해둔 것을 가져오는 게 더 빠르다는 내용이다.
25KB 크기의 스크립트를, 브라우저 캐시를 사용해 로드하는 것과,
로컬 스토리지에 저장해뒀다가 로드하는 것의 시간을 비교하는 방식으로 실험했다.
테스트 대상이었던 모든 모바일 기기에서 로컬 스토리지에서 불러오는 평균 속도가 더 빨랐다

### 저장공간
로컬 스토리지의 데이터들은 물리적으로 저장이 된다. 크롬같은 경우는 SQLite하여 컴퓨터 내부 라이브러리 디렉토리에 저장하게 된다.

데이터 파일 형식은  **protocol_domain_index.localstorage**라는 이름의 파일로 저장된다. 이렇게 저장된 데이터 파일은 페이지가 로드되기 전에, 브라우저는 물리적으로 저장되어 있는 데이터를 읽어 메모리에 올려둔다.

### 만료기간
로컬 스토리지는 사용자가 삭제하지 않는 한 영구적이며, 세션스토리지는 세션이 만료되었을때 증발하게 된다.

### 보안
쿠키와 비교했을 때, 클라이언트의 디바이스에 직접 저장되고 네트워크로는 전송되지 않기 때문에 네트워크 레벨에서는 안전하다고 볼 수 있다. 하지만 평문으로 저장하는 경우, 그 값이 그대로 노출되어 XSS 등의 공격으로부터 안전하지 않다.

고려대학교 정보보호대학원에서 작성한 '**[안전한 HTML5 로컬스토리지 구현에 대한 연구](http://scholar.ndsl.kr/schArticleDetail.do?cn=JAKO201227935976492")**'라는 이름의 논문이 있다.

사용자로부터 받은 암호로 로컬 스토리지에 저장되는 데이터를 암호화한다는 아이디어다.
대칭키 방식으로 사용하며, 보안을 위해 도메인마다 랜덤하게 salt가 생성되도록 하는 방식이다.

요약하면 확실한 보안방법은 민감한 데이터는 평문으로 넣지 말고 암호화하자.

**참고 출처**
<http://ohgyun.com/417>
