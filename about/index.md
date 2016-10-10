---
layout: page
title: 제이키의 MVC 이야기
modified: 2016-10-09T00:43:55
image:
  feature: so-simple-sample-image-4.jpg
  credit: WeGraphics
  creditlink: http://wegraphics.net/downloads/free-ultimate-blurred-background-pack/
---

2000년에 마이크로소프트의 클래식 ASP를 시작으로 웹 개발을 시작했다. 쉼없이 변하는 IT 환경이지만 웹 애플리케이션의 발전은 그 중에서도 눈이 부시다. 웹 사용자의 안목은 빠르게 높아졌고 이런 기대를 맞추기 위해 웹 개발은 점점 난해해졌다.

MVC (Model-View-Controller) 패턴이 웹 개발에 도입된지는 오래되었지만 개인적으로 사용하게된 시기는 그리 오래지 않다. ASP.NET MVC 프레임워크를 사용하면서부터 좀더 창의적으로 개발을 하게되었고 그 과정에서 알아가는 즐거움을 블로그에 기록하고 있다.

### 백엔드

이 글을 쓰는 시점인 2016년은 ASP.NET Core 출시로 마이크로소프트 웹 스택이 완전히 새롭게 작성되었다. 그 변화의 정도는 2002년에 ASP 개발자가 ASP.NET을 받아들여야 했던 그 때와 비슷하다. 

닷넷 코어 덕분에 ASP.NET Core로 작성된 웹 애플리케이션은 리눅스에서도 동작한다. 또한, NodeJS 서버만큼 빠른 성능을 내기 위해 ASP.NET Core는 HTTP 요청 파이프라인을 bare minimum으로 정리하면서 비대한 IIS 서버의 의존성을 없애고 Kestrel이라는 초경량 서버를 도입했다. 

C# 개발자가 AWS의 저렴한 리눅스 VM 상에서 가볍고 빠른 WEB API를 운영하면서 모바일 클라이언트를 탄력적으로 지원할 수 있는 시나리오가 가능해진 것이다.

### 프론트엔드

점점더 많은 로직이 클라이언트단에서 수행되고 있다. UI는 단순하면서도 상호작용이 부드러운 방식이 선호되고 모바일 기기에서의 표현력 또한 중요한 고려대상이 되었다.

2016년 9월 15일. 깔끔한 TypeScript를 사용할 수 있는 컴포넌트 기반의 MVC 프레임워크인 Angular 2가 발표되었다. JavaScript 라이브러리들이 특정 목적에 따라 개발되다 보니 서로 많은 의존성을 갖고 있어서 라이브러리들을 서로 연결하는 일이 그리 쉬운 일은 아니다. 그러나, Angular는 **프레임워크**로서 routing, web api call, testing, dependency injection 등의 기능을 빌트인으로 포함하고 있다. 

ASP.NET MVC 애플리케이션 개발 경험이 Angular 2를 익히는데 많은 도움이 될 것이다. 

### 이 블로그에서는

* 풀스택 프로그래머가 되기 위한 백엔드, 프론트엔드 기술을 살펴본다
* ASP.NET Core의 설계를 이해하고 잘 활용하는 방법에 대해 알아본다
* Angular 2를 활용한 SPA(Single Page Application)을 작성한다
* 테스트 가능한 코드를 작성한다
