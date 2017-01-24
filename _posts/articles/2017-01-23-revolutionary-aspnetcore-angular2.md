---
layout: post
title:  "Revolutionary ASP.NET Core & Angular 2"
excerpt: "ASP.NET 과 Angular는 각자의 위치에서 웹 개발의 주류(main stream) 프레임워크로 많이 사용되어 왔다. 최근 이 두개의 프레임워크는 바닥부터 새로 작성되었다. 닷넷 프레임워크는 비대해진 크기와 그로 인한 새 버전 릴리즈 
지연이 클라우드와 모바일 환경에 적합하지 않기 때문이었고..."
date:   2017-01-23
categories: articles
tags: [aspnet-core, angualr2]
author: ryu
permalink: /revolutionary-aspnetcore-angualr2/
share: true
image:
  feature: so-simple-sample-image-1.jpg
---

ASP.NET 과 Angular는 각자의 위치에서 웹 개발의 주류(main stream) 프레임워크로 많이 사용되어 왔다. 최근 이 두개의 프레임워크는 바닥부터 새로 작성되었다. 닷넷 프레임워크는 비대해진 크기와 그로 인한 새 버전 릴리즈 
지연이 클라우드와 모바일 환경에 적합하지 않기 때문이었고, Angular는 5년간 커뮤니티의 피드백을 기초로 그간 문제점을 해결하고 모바일 환경을 지원하기 위해 새롭게 작성되었다고 한다. 이런 변화를 보면 스마트폰이 개발 생태계에 있어 얼마나 큰 영향력을 미쳤는지 알 수 있다. 

## Beta와 RC로 프로젝트를

2016년 5월에 프로젝트를 기획하면서 과감한 결정을 했다. ASP.NET Core 가 RC 후반 버전, Angular 2가 RC도 아닌 beta 버전인 상태에서 SPA (Single Page Application) 프로젝트를 위한 프레임워크으로 결정한 것이다. 
Angular 2의 RC 2버전과 RC 5버전에서 breaking changes 가 있어 고생을 좀 했지만 Angular 2 Final버전의 모습은 그 고생에 보답이라도 하듯 성숙해 졌다고 생각한다.

프로젝트의 목적은 자동차의 모델 및 스펙을 총괄하는 제품 팀이 모델/트림/스펙/가격 등을 효율적으로 관리할 수 있는 애플리케이션과 외부 업체가 이 정보에 쉽게 접근할 수 있도록 API를 제공하는 것이었다. 데이터 입력이 많아서 데스트탑 애플리케이션 같은 UI를 구현하기 위해 Angular 2와 PrimeNG 컴포넌트들을 사용했다. 백엔드는 Augular 앱을 지원하는 API 모음과 외부 공개용 API 모음으로 구분했다.

## 새로 작성된 프레임워크

![Aspnet Core + Angular 2](/images/post/aspnetcore-angular2.png)

SPA를 구현하기 위해 Angular 프레임워크를 별다른 고민없이 선택했다. 시장에서는 React와 Angular가 경쟁하는 양상이었고 구글이 Angular의 새로운 버전을 TypeScript로 새롭게 작성하고 있다는 사실이 선택에 힘을 실어 주었다.

그동안 ASP.NET 으로 웹 개발을 해왔기 때문에 ASP.NET Core에 대한 선택은 언제 시작할까의 선택만이 있던 상황이었다. 닷넷 개발 프레임워크가 클라우드와 모바일 환경에 부응하고 시장 지배력을 높이기 위해 바뀌는 모습을 매우 긍정적으로 생각했고 프로젝트 시작전에 닷넷 코어 및 ASP.NET Core 에 대해 블로깅을 하면서 개념을 꽤 익혔기에 ASP.NET Core는 익숙한 편이었다.

Angular 2와 ASP.NET Core는 그 이전 버전과의 호환성을 과감히 포기하고 새로 작성되었다는 공통점이 있다. 새로운 프레임워크가 릴리즈되면 그 주변의 툴 또는 패키지등도 바껴야 한다. Angualr 2에서는 디자인과 그리드 때문에 몇 가지를 전전하다 PrimeNG로 최종 정리했고 ASP.NET Core는 다행이도 주변 툴이 일찌감치 지원되어 큰 어려움이 없었다. 프로젝트에 사용했던 툴은 Autofac, Serilog, AutoMapper, Swagger 등이다. 

## Entity Framework Core 의 제약

SPA의 성격상 백엔드는 웹페이지를 다루지 않기 때문에 API를 호스팅하는 가벼운 방식이라, 모듈화되고 응답 성능이 대폭 개선된 ASP.NET Core는 좋은 선택이었다고 생각한다. 다만, 타켓 프레임워크까지 닷넷 코어로 선택한 것이 Entity Framework Core 사용을 강제화한 바람에 아쉬운 점으로 남는다 (ASP.NET Core 앱을 생성할 때, 닷넷 코어 또는 Full 닷넷 프레임워크를 선택할 수 있다).

운영 환경이 멀티플랫폼도 아니고 모두 윈도우 서버라서 닷넷 프레임워크를 선택해도 문제가 없었으나 단순히 가볍고 모듈화된 닷넷 코어를 써보고 싶은 순수한 마음에 닷넷 코어로 간 것이다. 발목을 잡은 것은 엔티티 프레임워크 때문인데, EF Core는 성숙단계에 이른  버전 6.x 과 달리 테이블 상속을 지원하지 않는 등 제약사항이 좀 있다. 결국 엔티티를 상속 구조로 잡지 못하고 flat하게 사용할 수 밖에 없어서 모델링은 만족스럽지 않았다. 

## TypeScript

앞서 Angular 2는 새롭게 작성되었다고 했는데 JavaScript가 아닌 TypeScript로 작성되었다. TypeScript는 C#의 아버지 Anders Hejlsberg가 마이크로소프트에서 만든 두번째 작품이다. C#에 익숙한 개발자가 프론트엔드 프레임워크를 두고 고민에 있다면 TypeScript를 메인으로 지원하는 Angular 2를 선택하는 합리적일 것이다
(Angular 2 앱을 작성하려면 JavaScript, TypeScript, Dart 중 하나를 사용한다).

마이크로소프트와 구글은 TypeScript 를 매개로 협력해 왔고 Angular 2를 작성하면서 마이크로소프트의 많은 지원을 받았다고 한다. 언어적 차원의 기능 추가 및 확장일 것이다. 한편, 마이크로소프트는 TypeScript 확산에 Angular 만큼 좋은 사례를 찾을 수는 없을터였으니 서로 윈윈했다고 볼 수 있겠다.

## TypeScript와 JavaScript

TypeScript는 JavaScript의 수퍼셋이다. 즉, JavaScript를 .ts 파일(TypeScript의 파일 확장자) 에서 사용해도 동작한다. 그러나,  TypeScript는 JavaScript에서 지원하지 않는 class 또는 interface를 사용할 수 있게 해주고, static type check 하므로 코딩시 오류를 알리고, 인텔리전스를 지원하는 등 훌륭한 툴링을 자랑한다.

TypeScript의 궁극적인 목적은 JavaScript를 객체지향적 언어처럼 다룰 수 있게 해주는 것이며 컴파일한 결과물은 JavaScript라는 것을 기억하자. Transform + Compile 한다는 의미로 트랜스파일한다고 주로 표현한다.

JavaScript는 ECMAScript 표준을 따른다. 이 표준이 업데이트되면서 언어적 기능이 새로 추가되었을때, 이 기능을 지원하기 위해 브라우저가 업데이트되기까지는 시간적 차이가 존재한다. 이런 간극이 존재할 때, TypeScript의 가치가 빛을 발한다. TypeScript를 사용해 새로운 언어적 기능을 사용하면서도 브라우저가 이해할 수 있는 ECMAScript 버전으로 다운레벨 트랜스파일할 수 있기 때문이다. 

![TypeScript is supersest of JavaScript](/images/post/typescript-es6-es5.png)

Anders Hejlsberg 가 TypeScript를 만든 배경과 2.0 버전에서 지원하는 기능에 대해 마이크로소프트 [채널 9](https://channel9.msdn.com/Blogs/Seth-Juarez/Anders-Hejlsberg-on-TypeScript-2)에서 이야기하고 있다. 


## Angular 2와 ASP.NET Core는 잘 어울릴까

이 둘이 잘 어울릴까하는 문제보다는 동거에 문제가 없는가 하는 걱정이 앞선다. Knockout을 만들었고 지금은 영국 마이크로소프트에서 일하고 있는 [Steve Sanderson](http://blog.stevensanderson.com/)이 이분야의 전문가다. 그는 ASP.NET Core 프로젝트에서 Angular 2, React, Knockout 용 [템플릿 팩](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.ASPNETCoreTemplatePack)을 개발하여 제공하고 있다. 이 템플릿을 사용하여 ASP.NET Core에서 Angular 2 Starter 프로젝트를 만들 수 있다는 것은 다행스러운 일이다. 
[Angular.io](https://angular.io/) 에서 Get Started를 진행해 보면 초간단 앱을 보기까지 그 설정과 다루는 파일이 꽤 많다는 사실을 알 수 있다. 템플릿 팩을 통해 이런 수고를 덜 수 있는 것이다. 

Angular 2 와 ASP.NET Core는 웹 개발 프레임워크로써 MV* 패턴을 사용하고 내장 Dependency Injection을 지원하며 테스트 친화적이다. 이런 공통점은 (비록 프론트엔드에서 뷰를 중심으로 다루는 것과 
백엔드에서 데이터를 중심으로 다루는 것에 차이가 있긴 하지만) 비슷한 스타일의 코딩을 할 수 있게 해준다.

## ASP.NET Core + Angualr 2 예제들

앞으로 이 블로그에서도 이 둘의 동거를 다루겠지만 참고할만한 리소스를 아래에 정리했다.

* [ASP.NET Core + Angular 2 template for Visual Studio](http://blog.stevensanderson.com/2016/10/04/angular2-template-for-visual-studio/)
* [Single Project Full-Stack Angular 2](http://developer.telerik.com/products/kendo-ui/single-project-full-stack-angular/)
* [jonhilton.net](https://jonhilton.net/)
* [Cross-platform Single Page Applications with ASP.NET Core 1.0, Angular 2 & TypeScript](https://chsakell.com/2016/01/01/cross-platform-single-page-applications-with-asp-net-5-angular-2-typescript/)
* [ASP.NET Core and Angular 2](https://www.amazon.co.uk/d/cka/ASP-NET-Core-Angular-2-Valerio-Sanctis/178646568X/ref=sr_1_1?ie=UTF8&qid=1485185550&sr=8-1&keywords=asp.net+core+angular+2)
