---
layout: post
title: InfoQ | Are you a Software Architect? 나는 소프트웨어 아키텍트일까?
header: InfoQ | Are you a Software Architect? 나는 소프트웨어 아키텍트일까?
permalink: infoq-are-you-a-software-architect
tags: 
---

이 글은 2010년 2월 9일 InfoQ에 게재된 Simon Brown의 글을 일부 번역한 것입니다. [[원문 보기](https://www.infoq.com/articles/brown-are-you-a-software-architect#anch53080)]

원문을 번역 또는 의역한 부분은 인용으로 표시하였습니다. 원문의 내용을 이해하는데 굳이 필요없는 문장은 생략하거나, 내용을 이해하는데 문장의 순서를 변경하는데 도움이 되면 순서를 변경하기도 했음을 참고하시기 바랍니다.

저자는 자신이 소프트웨어 아키텍트인지 아닌지, 또는 얼마나 훌륭한 소프트웨어 아키텍트인지 여부를 생각해볼 수 있도록 하고자 한 것 같다. 소프트웨어 아키텍트는 개발자보다 위에 있는 등급(rank)이 아니라 단지 역할(role)이라는 것을 강조하면서, 다음과 같은 10 가지 소프트웨어 아키텍트의 특징을 통해서 얼마나 소프트웨어 아키텍트로써의 역할을 하고 있는지 평가해볼 수 있게 하였다. 저자는 아키텍트가 참여하는 단계를 크게 아키텍처를 정의하는 단계와 딜리버리하는 단계로 나누었고, 각 단계에서 다섯 가지 역할을 기술하였다. 각 역할에서는 훌륭한 아키텍트로써의 특징과 아키텍트라고 하기에는 약한 특징을 그림으로 보여준다. 이 글에서 가장 강조한 부분은 아키텍처 정의하고 끝이 아니라 적극적으로 최종적으로 소프트웨어 프로젝트가 완료될 때까지 밀접하게 관여해야 한다는 점이다.

- 소프트웨어 아키텍처 정의 단계
  - 비기능적 요구사항 관리하기
  - 아키텍처 정의하기
  - 기술 선택하기
  - 아키텍처 평가하기
  - 아키텍처 협업하기
- 소프트웨어 개발 단계 (Delivery of software architecture)
  - 큰 그림에 대한 오너십
  - 리더십 
  - 코칭과 멘토링
  - QA
  - 설계, 개발, 테스팅

>소프트웨어 아키텍쳐와 소프트웨어 개발을 구분하는 뚜렷한 경계선이 있을까. 아키텍쳐는 개발자의 설계 과정의 확장일 뿐이라서 경계선이 없다는 견해도 있고, 대단한 개발자만이 아키텍트라는 경계선을 넘어설 수 있기에 개발자와 아키텍트 간에는 엄청난 간극이 존재한다는 견해도 있다.    
>   소프트웨어 아키텍처, 설계(design), 개발(development)을 구분하는 중요 요소에는 규모의 증가(increase in scale), 추상화 수준의 증가(increase in the level of abstraction), 올바른 설계 결정의 중요성 증가(increase in the significance of making the right design decisions)가 있다.
소프트웨어 아키텍처는 소프트웨어 시스템 전체가 어떻게 동작하는지 알려주는 큰 그림이고 전체적인 조망(holistic view)에 관련된 모든 것이다. 이 정의가 소프트웨어 개발과 아키텍처의 차이를 이해하는데 도움은 주지만, 개발자가 어떻게 아키텍트가 될 수 있는지에 대한 설명으로는 부족하다. 또한, 누가 좋은 소프트웨어 아키텍트가 될 수 있는지, 어떻게 아키텍트를 채용해야 하는지, 내가 소프트웨어 아키텍트인지 여부를 판별하는데에는 별다른 도움이 안된다.


## 1. 소프트웨어 아키텍처의 정의
---
### 첫번째, 비기능적 요구사항 관리하기

> 흔히 사용자에게 어떤 기능(feature)을 원하는지 물어보기는 하지만, 시스템의 품질처럼 어떤 비기능적 요구사항이 필요한지 물어보는 경우는 드물다. 이해당사자가 "시스템은 아주 빨라야 한다"고 하지만 이건 너무 주관적이다. 비기능적 요구사항은 구체적이고 측정가능하며 도달가능하고 테스트 가능해야 한다. 그래야 만족시킬 수 있다. 대부분의 비기능적 요구사항은 기술적 측면이 강하고 소프트웨어 아키텍처에 큰 영향을 미친다. 비기능적 요구사항을 이해하는 것은 소프트웨어 아키텍트에게 중요한 역할이다. 

아래 그림은 아키텍트의 비기능적 요구사항의 관리 수준을 보여준다. 왼쪽이 낮은 수준이고 오른쪽이 높은 수준이다. 비기능적 요구사항을 추측하기만 하는 것은 낮은 수준이고, 사용자 또는 이해당사자로부터 비기능적 요구사항을 도출한 것이 중간 수준(Captured)이다. 그리고, 맨 오른쪽은 비기능적 요구사항을 시험하고 평가하여 개선까지하는 수준을 나타낸다.

![]({{ site.url}}/img/170101/architecture-definition-1.png)

### 두번째, 아키텍처 정의하기

> 비기능적 요구사항을 파악한 다음 단계에서는 문제를 어떻게 풀어나갈지 생각하고 아키텍처를 정의한다. 모든 소프트웨어 시스템은 아키텍처를 갖고 있다고 할 수 있다. 그러나 모든 소프트웨어 시스템이 "정의된 아키텍처"를 갖고 있지는 않다. 아키텍처 정의 프로세스는 제약사항들이 존재하는 가운데 어떻게 요구사항을 해결할 것인가에 대해 생각하게 한다. 아키텍처 정의는 프로젝트의 기술적 관점에 구조, 가이드라인, 원칙, 그리고 리더십을 제공한다.

아키텍처를 정의함에 있어서도 기존 시스템을 확장하는 수준과 백지에서 새로운 시스템의 아키텍처를 설계하는 수준에 차이가 있음을 언급하고 있다. 중간의 "Contributed"는 새로운 시스템을 만들 때 아키텍처 설계에 부분적으로 기여하는 수준을 의미하는 것 같다.
![](./img/170101/architecture-definition-2.png)

### 세번째, 기술 선택 (Technology Selection)

> 기술을 선택함에 있어서 비용, 라이선스, 벤더 관계, 기술 전략, 호환성, 상호운용성, 지원, 배포, 업그레이드 정책, 최종 사용자 환경 등 많은 요소를 고려한다는 것을 무척 어렵다. 기술 선택은 리스크를  관리하는 것이다. 복잡도나 불확실성이 매우 높은 곳의 리스크를 줄이거나 얻을 수 있는 이득이 있다면 리스크를 감수할 수도 있다. 기술 도입에 대한 결정은 많은 요소를 고려해야 하고 평가해야 한다. 기술이라 함은 개발에서 사용할 라이브러리부터 프레임웍을 포함한다. 아키텍트는 기술을 선택함에 있어서 확신이 있어야 한다.
기술을 선택함에 있어서도 기존 시스템을 위한 기술을 선택하는 것과 새로운 시스템을 위하여 기술을 평가하고 선택하는 것에는 큰 수준에 차이가 있다.

![](./img/170101/architecture-definition-3.png)

### 네번째, 아키텍처 평가 (Architecture Evaluation)

> 소프트웨어를 설계할 때에는 그 아키텍처가 잘 동작할지 자신에게 물음을 던져야 한다. 비기능적 요구사항을 만족하고 개발에게 필요한 기초를 제공하며 비즈니스 문제를 해결하는데 충분한 플랫폼을 제공한다면, 아키텍처는 동작할 것이라고 생각할 수 있다. 가장 큰 문제 중 하나는, 소프트웨어가 복잡하고 추상적이어서 UML 다이아그램이나 코드로는 실행시 특징을 가시화하기 어렵다는 것이다. 소프트웨어를 개발하는 동안 여러 종류의 테스트를 거치도록 하면 시스템이 최종적으로 잘 동작할 것이라는 확신을 가질 수 있다. 그렇다면, 아키텍처도 테스트하고 평가할 수 없을까? 아키텍처를 테스트할 수 있다면 그 아키텍처가 동작한다고 증명할 수 있다. 이것을 개발 프로세스에서 일찍 할 수록 프로젝트의 리스크를 줄일 수 있다.

아키텍처를 평가함에 있어서 소프트웨어 아키텍처가 그저 기대한 대로 동작할 것이라고 가정하는 것은 낮은 수준이고 소프트웨어 아키텍처가 기대한대로 동작할 수 있다고 증명하는 것은 높은 수준이다.

![](./img/170101/architecture-definition-4.png)


### 다섯번째, 아키텍처 협업 (Architecture Collaboration)

> 아키텍처를 이해하고 활용할 개발팀부터 보안, 데이터베이스, 운영, 유지보수, 지원(support)에 관련된 이해당사자들까지 소프트웨어 시스템에 관련된 다양한 사람들이 존재한다. 아키텍트는 이 모든 이해당사자들과 협업해야 아키텍처가 성공적인 소프트웨어 시스템으로 만들어질지 확신할 수 있다. 불행히도 아키텍처 협업은 대부분 잘 안되고 있다.

아키텍처와 디자인 결정 요소들을 문서화하여 기록하는 정도는 낮은 수준의 아키텍트이고, 내부 이해당사자들과 협업하여 아키텍처를 정의한다면 중간 정도 수준이며, 내외부 모든 이해당사자/팀들과 협업하여 아키텍처를 정의한다면 가장 높은 수준의 아키텍트이다.

![](./img/170101/architecture-definition-5.png)

## 2.소프트웨어 개발 단계(Delivery of software architecture)
---
정의된 소프트웨어 아키텍처를 활용하여 실제 설계, 구현, 테스트하는 단계를 의미하기에 소제목을 "소프트웨어 개발 단계"라고 의역하였다.

### 첫번째, 큰 그림에 대한 오너십 (Ownership of the bigger picture)

> 프로젝트의 성공을 위해서는 큰 그림을 이해하고 있는 사람이 전체 개발 프로세스에 참여하여 아키텍처를 진화시켜나가는 것이 좋다. 따라서, 당신이 아키텍처를 정의했다면, 구현팀에 아키텍처를 던져준 후 빠지지 말고 계속 같이하는 것이 좋다.

개발팀에 큰 그림을 주고 빠지는 것은 낮은 수준이며, 개발팀을 감독하는 수준으로 참여하는 것은 중간 수준이고, 적극적으로 참여하여 아키텍처를 이해시키고 리드하는 것은 높은 수준의 아키텍트이다.

![](./img/170101/architecture-delivery-1.png)


### 두번째, 리더십

> 큰 그림을 파악하고 있는 것은 리더십에서 중요한 부분이지만 책임을 지는 것, 기술적인 안내를 제공하는 것, 권한을 갖고 기술적인 결정을 하는 것도 리더십의 중요한 요소이다. 아키텍트로써 모든 사안들을 빠짐없이 인지하고 관리하며 팀이 올바른 방향으로 가고 있는지 지속적으로 리드해야 한다. 많은 프로젝트에서 정의된 아키텍처를 토대로 성공적인 산출물을 만들어내는지 여부는 아키텍트의 책임이 아니라고 간주하기도 한다. 

개발팀에게 가이드만 제공하는 것이 가장 낮은 수준이고, 개발팀의 방향을 조정하고 이끄는 것은 중간 수준이며, 책임과 권한을 갖고 기술적 결정을 하면서 프로젝트 결과물까지 책임지려는 것은 가장 높은 수준의 아키텍트이다.
![](./img/170101/architecture-delivery-2.png)

### 세번째, 코칭과 맨토링

코칭과 멘토링이 아키텍트의 역할임에도 불구하고 많은 소프트웨어 개발 프로젝트에서는 간과되어 지원이 필요한 팀 멤버가 적절한 지원을 받지 못한다고 한다. 아키텍트의 코칭과 멘토링의 가장 높은 수준은 아키텍처나 설계, 그리고 소프트 스킬(soft skills)을 코칭하는 것이고, 그 다음은 설계와 코딩을 코치하는 것, 가장 낮은 수준은 코칭과 멘토링을 받는 수준이라고 한다.

![](./img/170101/architecture-delivery-3.png)

### 네번째, QA
> QA는 아키텍트의 역할 중 매우 중요한 부분인데 코드 리뷰 이상의 것을 해야 한다. 예를 들면 작업 방식에 대한 표준을 통해서 만족할 수 있는 품질에 대한 기준선이 필요하다. 코딩 표준, 설계 원칙, 소스코드 분석 도구, CI(Continuous Integration), 자동화된 단위 테스트, 코드 커버리지 도구들이 필요하다. 대부분의 프로젝트들은 QA가 충분하지 않으므로, 가장 중요한 부분을 식별하여 그 부분에 대해서는 충분히 안심하거나 확신할 수 있도록 한다. 중요한 부분이란 아키텍처 측면에서 중요할 뿐만 아니라 비지니스 측면에서 크리티컬하고 복잡하면서 눈에 잘 띄는 것이다. 모든 것을 확신할 수는 없으나, 그렇다고 아무것도 안하는 것보다는 무엇인가는 하는게 낫다는 것을 인지해야 한다.

개발팀에게 지켜야 할 표준과 업무 방식을 제시하기만 하는 것이 가장 낮은 수준이고, 책임지고 소프트웨어의 품질을 책임지는 것이 가장 높은 수준의 아키텍트이다.

![](./img/170101/architecture-delivery-4.png)

### 다섯번째, 설계, 개발, 그리고 테스팅

> 설계, 개발, 테스팅은 모두 아키텍트의 역할이다. 아키텍트가 일상적으로 코딩한다는 것은 코딩 역량을 최신 상태로 유지한다는 점 외에도 개발자와 동일한 고통을 경험하면서 개발자 관점에서 자신이 정의한 아키텍처를 이해할 수 있게 하는 장점이 있다. "너무나 가치가 큰 소프트웨어 아키텍트가 사소한 코딩을 하면 적절하지 않다"며 소프트웨어 아키텍트가 코딩 활동에 참여하지 못하도록 하는 회사들이 있는데 이는 잘 못된 것이다. 프로젝트의 규모가 너무 커서 코딩 활동에 참여할 시간이 없는 경우는 어쩔 수 없다. 일반적으로 코딩하는 아키텍트가 그렇지 않은 아키텍트보다 더 효과적이고 행복하다.

개발 과정을 지켜보는 것은 낮은 수준이고 개발 과정에 깊이 관여하고 참여하는 것이 높은 수준의 아키텍트이다.

![](./img/170101/architecture-delivery-5.png)

저자의 결론은 다음과 같다.
> 개발자들은 이미 일부 소프트웨어 아키텍트 역할을 하고 있다. 아키텍처 설계에 도움을 주는 것과 아키텍처 정의에 책임을 지는 것은 크게 다르다. 개발자에서 소프트웨어 아키텍트로이 선을 넘을 것인지 여부는 당신에게 달렸다. 그러나, 소프트웨어 아키텍트로써 여정을 시작하기 전에 당신의 경험 수준을 이해할 필요가 있다.


* 모든 이미지들은 원문의 이미지입니다.