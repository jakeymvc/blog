---
layout: post
title:  "앵귤러와 서버"
excerpt: "앵귤러 애플리케이션을 만들 때, 서버단 애플리케이션과 어떻게 통합해야 할까? 선택 가능한 어떤 옵션이 있고 또, 그 선택이 맞는지 어떻게 알 수 있을까? 이런 주제는 앵귤러 위주의 자료들에서 종종 쉽게 지나치는 문제이다. 한편으로, 서버단을 다루는 글에서는 독자가 이미 강력한 프론트엔드 프레임워크를 사용하고 있다는 가능성을 배제하고 서버단 기능만을 강조한다."
date:   2017-03-14
categories: blog
tags: [angualr2]
author: ryu
permalink: /angular-and-server/
share: true
image:
  feature: so-simple-sample-image-2.jpg
---

>이 글은 저자의 동의를 얻어 번역한 글이다. [원문](http://angularfirst.com/angular-and-the-server/)이 있는 블로그에는 마이크로소프트 스택에서 앵귤러를 다루는 좋은 글들이 많이 있다.

비주얼스튜디오가 제공하는 스타터 프로젝트는 UI 렌더링, 인증, 데이터를 다루는 샘플 API 등  모든 것을 기본으로 포함한다. 이런 설정이 시작시 좋은 출발점이긴 하지만 자신의 애플리케이션을 위한 최고의 디자인이라고 하기는 어려울 것이다.

앵귤러 애플리케이션을 만들 때, 서버단 애플리케이션과 어떻게 통합해야 할까? 선택 가능한 어떤 옵션이 있고 또, 그 선택이 맞는지 어떻게 알 수 있을까? 이런 주제는 앵귤러 위주의 자료들에서 종종 쉽게 지나치는 문제이다. 한편으로, 서버단을 다루는 글에서는 독자가 이미 강력한 프론트엔드 프레임워크를 사용하고 있다는 가능성을 배제하고 서버단 기능만을 강조한다.

이 글은 애플리케이션의 책임 또는 역할을 따져보고, 그 것을 호스트 애플리케이션과 통합하는 관점에서 몇 가지 그룹으로 나누어 보겠다. 이 글의 목적상, 호스트 애플리케이션이라 함은 서버단 애플리케이션으로써 앵귤러 애플리케이션의 하단에 위치하여 브라우저에 내용을 전달하는 애플리케이션이다 (앵귤러 하단에는 사실 아무것도 존재하지 않을수도 있다). 앵귤러 애플리케이션은 TypeScript 또는 JavaScript 만으로 작성이 가능하며 또는, 둘다 사용할 수도 있다. 

## 레벨 0 – 서버 없는 애플리케이션

서버단에 의존하지 않고도 구현이 가능한 기능만을 포함하는 레벨이다. 이 의미는 애플리케이션이 동작할 때, 앵귤러가 기능함에 있어 어떤 로직도 서버단의 도움없이 처리한다는 것이다. 이런 구성은 정적 파일만을 호스팅하는 것이 배포 또는 운영상 유리할 때 바람직하다.

런타임시 서버단의 종속성이 전혀 없다하더라도, 애플리케이션을 최적화하기 위한 빌드의 장점을 누릴 수 있다. 이런 것들은 서버단 백엔드 없이도 구성할 수 있는 기능들이다. (역주: Webpack이 좋은 예다. TypeScript 로 작성된 앵귤러 앱을 JavaScript 로 빌드하면서 동시에 bundling, minification 등 필요한 일을 할 수 있다)

#### 라우팅

앵귤러 라우터는 해시(hash; #) URL 스타일로 구성될 수 있다. 이런 구성에서 각 라우터는 해시 심볼(#) 뒤의 URL로 표현된다. example.com/#/about or example.com/#/products/1 과 같은 것이 그 예다. 애플리케이션이 로딩되면서 라우터가 동작하고 사용자의 요청을 뷰로 네비게이트한다.

#### 국제화; 다국어 지원

앵귤러는 다국어 지원을 위한 유팉리티를 갖고 있다. 다국어 처리를 위해 거쳐야 할 몇가지 단계가 [문서](https://angular.io/docs/ts/latest/cookbook/i18n.html)로 정리되어 있다. 결론적으로 말해서, 국제화에 대응하는 여러 가지 버전이 국가별 폴더에 정적 파일로 배포된다. 이 시점에서 사용자들의 로케일 버전으로 라우팅하는데 몇가지 옵션이 있다. UI에서 사용자들이 직접 언어를 선택하거나 또는, 사용자 브라우저의 언어 설정에 따라 자동으로 라우팅할 수 있다. 

#### 뷰 렌더링

앵귤러 프레임워크의 첫번째 하일라이트는 렌더링이다. 자바스크립트 객체를 템플릿에 바인딩하여 데이터를 확인하고 이벤트 바인딩으로 변화를 알아내는 것은 거의 마술에 가깝다. 서버단 프레임워크가 자신만의 렌더링 엔진이 있는 것에 반해, 앵귤러는 이 작업을 브라우저에서 처리한다. 당신이 앵귤러를 사용한다면 서버의 도움 없이도 뷰를 통제할 수 있다.

## 레벨 1 - 호스트 애플리케이션과 통합

이 레벨은 호스트 애플리케이션과 앵귤러 애플리케이션이 상호 제공하는 기능의 조합이다. 서버가 앵귤러 애플리케이션에 대해 잘 알지 못하더라도 서버와 클라이언트가 상호 존중하는 방식으로 구성해야 하고 이 둘은 상대방의 행동 방식을 염두에 두어야 한다.

#### 라우팅 

앞서 살펴본 해시 방식 URL과는 반대로, 조금더 일반적인 HTML 5 pushState 방식(*) URL은 서버의 지원여부에 달려 있다. 이런 URL은 example.com/about 또는 example.com/products/1 처럼 해시를 갖지 않는다. 여기서 문제는 사용자가 URL을 사용하여 당신의 애플리케이션에 최초로 접속할 때, 앵귤러 애플리케이션 입장에서는 라우트를 처리해볼 기회가 없다는 것이다. 대신, 서버가 요청을 먼저 받고 클라이언트로 앵귤러 애플리케이션을 돌려 준 다음, 라우팅 처리를 마치게 한다. [여기](http://angularfirst.com/your-first-angular-2-asp-net-core-project-in-visual-studio-code-part-6/)에서 관련한 예제를 살펴볼 수 있다.

(*) HTML 5에서는 브라우저의 히스토리에 접근할 수 있는 API를 정의하고 있다. pushState()는 히스토리에 새 엔트리를 작성할 수 있게 도와준다.

#### 인증

때로는, 인증된 사용자만 앵귤러 애플리케이션에 접근하도록 제한해야할 때가 있다. 앵귤러 애플리케이션이 Web API 를 통해 사용자를 인증할 수 있지만 호스트 애플리케이션에서 인증하도록 하는 옵션도 가능하다. 예를 들면, 사용자가 당신의 웹 애플리케이션에 최초로 접근을 시도할 때, 서버가 Unauthorized 메시지를 반환할 수 있다. 이 과정에서 어떤 앵귤러 코드도 브라우저로 반환되지 않는다.

#### 뷰 렌더링

앞서 말했듯이 앵귤러 렌더링은 애플리케이션의 UI를 만들어내는 것, 그 이상의 능력을 갖고 있다. 일반적으로, 렌더링은 브라우저에서 발생한다. 그러나, 로딩 시간과 SEO 에 최적화된 애플리케이션은 서버단 렌더링의 도움을 받는다.

이 부분이 바로 Augular Universal(https://universal.angular.io/)이 딱 들어맞는 곳이다. 서버 프레임워크의 뷰 엔진을 사용하는 대신, 앵귤러 유니버셜은 앵귤러 템플릿을 서버단에서 렌더한다. 브라우저는 클라이언트단 렌더링이 필요없이 즉각적으로 HTML과 CSS를 받아 화면에 출력한다.

앵귤러 유니버셜을 사용하는 것은 앵귤러 애플리케이션을 작성하는데 있어 복잡도를 높이고 어떤 제약을 강제화한다. 따라서, 이렇게 추가적인 성능  향상을 위해서는 그에 따른 비용을 감수해야 한다는 것을 이해해야 한다. 더 자세한 내용은 [GitHub](https://github.com/angular/universal)에서 확인할 수 있다.

#### 국제화; 다국어 지원

다국어 지원은 호스트 애플리케이션이 직접적인 역할을 하는 부분이 아닐수도 있지만, 어쩌면 통합된 라우팅, 인증 또는 원하는 사용자 경험을 제공하기 위해 역할을 해야할 수도 있다.

#### 로깅

로깅은 호스트 애플리케이션과 앵귤러 애플리케이션사이에 긴밀하게 통합되어 있을 필요는 없다. 그러나, 라우팅, 인증, 렌더링 등에서 서버의 역할이 많아졌다는 것을 고려할 때, 이렇게 추가된 역할에서 발생하는 에러를 로깅해야 할 것을 고려해야 한다.

## 레벨 2 - Web APIs

Web API 는 서버단 종속성이다. 그렇다고, 꼭 호스팅 애플리케이션에 위치해야 할 필요는 없다. 이 종속성(Web API)을 자체 코드베이스에 유지하는 것만으로도 많은 잇점이 있다. 빌드와 배포는 변경사항이 있는 애플리케이션에서만 수행하면 된다. API 작성은 그것이 (HTTPS 처럼 일반적인 프로토콜위에서) 동작하기만 한다면 어떤 기술로 작성되었는지 상관없다. 회사 또는 팀에서 익숙한 기술을 사용할 수 있다. 만약, [Web APIs](https://en.wikipedia.org/wiki/Web_API) 와 [Microservices](https://en.wikipedia.org/wiki/Microservices) 개념에 익숙하다면 레벨 2 그룹이 이 개념들을 포함한다.

#### Web APIs

애플리케이션이 필요로 하는 Web API의 역할은 데이터 액세스, 로깅, 사용 분석등을 포함한다. 흥미 있는 점은, 이런 역할 중 어떤 것도 앵귤러 애플리케이션에 대한 지식을 필요로 하지 않는다는 것이다. 사실, API는 여러 다른 종류의 프론트엔드 애플리케이션에 서비스를 제공한다.

#### 인증

이 레벨에서의 인증은 전형적으로 앵귤러 애플리케이션과 Web API 서버에서 직접 처리한다. 여러 가지 다양한 방법이 있지만 [JSON web tokens](https://jwt.io/) 으로 사용자 신원을 관리하는 방식이 현재의 솔루션이라고 할 수 있다.

#### 국제화; 다국어 지원

이 레벨에서의 다국어 지원은 web API를 통해 서버단 기술을 사용하여 처리될 것이다. 다시 한번 강조하지만, API라는 계약을 넘어서는 추가적인 어떤 결합도 없다.

#### 번들링이 필요한 경우

어떤 경우에는 애플리케이션도 작고 애플리케이션이 의존하는 API 역시 작은 경우가 있다. 이런 경우에는, 같은 애플리케이션에 모든 기능을 모아 두자고 결정할 수도 있을 것이다. 그렇게 되면, 빌드와 배포를 공유하는 셈이 되어 한결 간편해진다. 종종, 기능을 쪼개어 서버와 클라이언트로 나누는 것은 들이는 노력만큼의 충분한 보상이 없을 때도 있다.

#### 결론

모든 애플리케이션은 각기 다르다. 이글을 통해 애플리케이션의 관심사가 어떻게 서버와 앵귤러 애플리케이션 사이에서 종속성을 만들어 내는지 이해하고, 이런 고려사항을 여러분의 설계에 반영할 수 있기를 바란다.

독자 중에서는 서버와 통합하는 개발 툴에 대해서는 전혀 다루지 않았음을 눈치챈 독자도 있을 것이다. 이 것은 향후 포스팅의 주제가 될테니 이 블로그를 계속 주시해주기 바란다.

결국, 애플리케이션을 디자인하는 오직 한가지 방법이란 없다. 다양한 옵션을 아는 것만으로도 여러분은 롱런하는 애플리케이션을 디자인할 채비를 갖춘것이다.

여러분 생각은 어떤가? 서버에서 운영하는 것이 낫다고 판단되는 특별한 기능이 있는가? 아니면 클라이언트에서? 의견을 함께 나누면 좋겠다.
