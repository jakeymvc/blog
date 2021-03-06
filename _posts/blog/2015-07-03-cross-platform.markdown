---
layout: post
title:  "병렬 수행과 크로스 플랫폼"
date:   2015-07-03T22:05:00
categories: blog
tags: [aspnet5,cross-platform]
author: ryu
permalink: /cross-platform/
share: true
---

윈도우 2003 서버에는 `닷넷 프레임워크 4.5`를 설치할 수 없다. 닷넷 프레임워크는 이전 버전과 호환성을 유지하면서 새로운 기능을 추가하는 방식으로 발전해 왔고 이런 방식에는 여러 가지 내부적인 문제가 있었다. 변경, 확장이 어렵기 때문에 어쩔 수 없이 호환성을 포기해야 하는 시점이 필요했을 것이다.

새 프로젝트를 위해 새 서버를 준비하는 것이 이상적이겠지만 기존 웹 서버에 새 웹 애플리케이션을 추가하는 일이 현실적으로 더 많다. 이것은 동일 서버에서 다른 버전의 런타임을 실행해야 하는 상황(side-by-side execution)을 유발하기도 한다. 닷넷 웹 애플리케이션은 대상 런타임을 외부환경에 의존하고 있기 때문에 이를 위한 설정이 필요하다. 예를 들어, IIS의 애플리케이션 풀을 새롭게 생성하여 대상 닷넷 프레임워크를 지정한 후, 웹 애플리케이션에서 사용하게 구성하면 된다.

CoreCLR은 애플리케이션의 일부가 되어 배포되므로, 배포 관점에서는 실행환경이 애플리케이션에 포함되었다고 할 수 있다. 닷넷 프레임워크가 `machine-wide` 영향력을 갖고 있는 반면 CoreCLR은 `application-local`수준에 한정된다. 이런 방식은 재시작 없는 배포와 병렬 수행을 가능하게 해준다.

병렬 수행보다 더 큰 혜택은 `크로스 플랫폼`에 있다. 애플리케이션의 실행환경에 대한 의존성이 CoreCLR 에만 있기 때문에 CoreCLR이 지원하는 모든 운영 체제에서 애플리케이션이 동작한다는 것은 대단한 변화임에 틀림없다.

오픈소스로 진행되고 있는 CoreCLR 은 `Linux`, `Windows`, `Mac OS X`, `FreeBSD` 운영체제를 지원할 예정이다. 

한편으로는 기존 닷넷 프레임워크와 새로운 닷넷코어를 동시에 고려하면서 애플리케이션을 컴파일 하고 실행할 필요가 생겼다. 비주얼 스튜디오(Visual Studio)와 같은 개발 도구에 종속적이지 않으면서 런타임을 관리하는 도구가 필요하다.<br /><br />


#### DNVM(.NET Version Manager)
DNVM은 다른 버전, 다른 아키텍쳐(x86 / x64)의 CLR을 관리한다. `런타임들(CLRs)`을 다운로드하고, 설치하고, 업그레이드 하고, 활성화된(Active) 런타임이 무엇인지 기억하고 알려준다. 이전 까지 `KVM` 으로 불려오다가 Visual Studio 2015 RC 등장과 함께 `DNVM`으로 공식 이름을 달았고 마찬가지로 Project K에서 시작된 다른 도구들도 D 네이밍으로 공식화 되었다. 

`DNVM`은 버전 매니저라는 이름에 걸맞게 list 명령어를 통해 컴퓨터에 설치된 모든 CLR들을 보여준다. 아래 그림은 아키텍쳐에 따라 제공되는 `CLR(Full CLR)`과 `CoreCLR`을 보여준다. 런타임을 선택하려면 version, runtime, architecture를 지정해야 하는 번거로움이 있기 때문에 alias를 지정하여 dnvm use default 와 같이 간편하게 사용할 수도 있다.

![dnvm list 명령으로 보는CLR 목록](/images/post/dnvm-list.png)

[그림-5] dnvm list 명령으로 보는 CLR 목록<br /><br />

#### DNX(.NET Execution Environment)
DNX는 SDK(Software Development Kit)로서 애플리케이션을 빌드하고 실행하기까지 모든 것에 대한 실행환경이다. DNX는 또한 `Portable CLR Host`다. 크로스 플랫폼에서 동작하면서 CLR을 호스팅하고 애플리케이션의 시작점을 찾아 실행한다. dnvm list 명령어를 실행하면 볼 수 있는 목록에서 각 행은 하나의 `dnx` 를 의미한다.<br /><br />

#### DNU(.NET Utilities)
마지막 D 도구 그룹의 멤버는 `DNU`(.NET Utilities) 이다. 이 유틸리티는 여러 가지 다양한 기능을 제공하지만 주로 애플리케이션이 종속성을 갖는 `NuGet` 패키지를 설치하고 관리하는데 사용된다. <br /><br />

>이렇게 세 가지 도구로 구성되는 환경을 통칭하여 DNX (umbrella term) 라고도 한다. 따라서 글의 문맥에 따라 CoreCLR과 함께 등장한 실행 환경, 또는 특정 명령어 도구로 해석할 수 있겠다.

