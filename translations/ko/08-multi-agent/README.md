<!--
CO_OP_TRANSLATOR_METADATA:
{
  "original_hash": "c692a8975d7d5b99575a553de1c5e8a7",
  "translation_date": "2025-07-12T10:59:58+00:00",
  "source_file": "08-multi-agent/README.md",
  "language_code": "ko"
}
-->
[![Multi-Agent Design](../../../translated_images/lesson-8-thumbnail.278a3e4a59137d625df92de3f885d2da2a92b1f7017abba25a99fb25edd83a55.ko.png)](https://youtu.be/V6HpE9hZEx0?si=A7K44uMCqgvLQVCa)

> _(위 이미지를 클릭하면 이 강의의 영상을 볼 수 있습니다)_

# 다중 에이전트 디자인 패턴

여러 에이전트가 참여하는 프로젝트를 시작하면 다중 에이전트 디자인 패턴을 고려해야 합니다. 하지만 언제 다중 에이전트로 전환해야 하는지, 그리고 그 장점이 무엇인지 바로 이해하기 어려울 수 있습니다.

## 소개

이번 강의에서는 다음 질문들에 답하고자 합니다:

- 다중 에이전트가 적용될 수 있는 시나리오는 무엇인가?
- 여러 작업을 수행하는 단일 에이전트 대신 다중 에이전트를 사용하는 장점은 무엇인가?
- 다중 에이전트 디자인 패턴을 구현하는 구성 요소는 무엇인가?
- 여러 에이전트가 서로 어떻게 상호작용하는지 어떻게 파악할 수 있는가?

## 학습 목표

이 강의를 마치면 다음을 할 수 있어야 합니다:

- 다중 에이전트가 적용될 수 있는 시나리오를 식별할 수 있다.
- 단일 에이전트보다 다중 에이전트를 사용하는 장점을 인식할 수 있다.
- 다중 에이전트 디자인 패턴 구현의 구성 요소를 이해할 수 있다.

큰 그림은 무엇일까요?

*다중 에이전트는 여러 에이전트가 협력하여 공통의 목표를 달성할 수 있게 하는 디자인 패턴입니다.*

이 패턴은 로봇공학, 자율 시스템, 분산 컴퓨팅 등 다양한 분야에서 널리 사용됩니다.

## 다중 에이전트가 적용되는 시나리오

그렇다면 어떤 시나리오에서 다중 에이전트를 사용하는 것이 적합할까요? 답은 여러 가지가 있지만 특히 다음과 같은 경우에 다중 에이전트 활용이 유리합니다:

- **대규모 작업 부하**: 큰 작업 부하는 더 작은 작업으로 나누어 여러 에이전트에 할당할 수 있어 병렬 처리와 빠른 완료가 가능합니다. 예를 들어 대규모 데이터 처리 작업이 이에 해당합니다.
- **복잡한 작업**: 복잡한 작업도 작은 하위 작업으로 분할하여 각기 특정 부분에 특화된 에이전트에 할당할 수 있습니다. 예를 들어 자율주행차에서는 내비게이션, 장애물 감지, 차량 간 통신을 각각 담당하는 에이전트가 있습니다.
- **다양한 전문성**: 서로 다른 전문성을 가진 에이전트들이 각기 다른 작업 측면을 더 효과적으로 처리할 수 있습니다. 예를 들어 의료 분야에서는 진단, 치료 계획, 환자 모니터링을 담당하는 에이전트들이 있습니다.

## 단일 에이전트보다 다중 에이전트를 사용할 때의 장점

단일 에이전트 시스템은 단순한 작업에는 적합할 수 있지만, 복잡한 작업에서는 다중 에이전트가 여러 장점을 제공합니다:

- **전문화**: 각 에이전트가 특정 작업에 특화될 수 있습니다. 단일 에이전트는 모든 작업을 처리하려다 복잡한 작업에서 혼란을 겪을 수 있으며, 적합하지 않은 작업을 수행할 위험이 있습니다.
- **확장성**: 단일 에이전트에 과부하를 주기보다 에이전트를 추가하는 방식이 시스템 확장에 더 용이합니다.
- **내결함성**: 한 에이전트가 실패해도 다른 에이전트가 계속 작동하여 시스템 신뢰성을 보장합니다.

예를 들어, 사용자의 여행 예약을 생각해 봅시다. 단일 에이전트 시스템은 항공편 검색부터 호텔, 렌터카 예약까지 모든 과정을 처리해야 합니다. 이를 위해서는 모든 작업을 처리할 도구를 갖춰야 하므로 복잡하고 유지보수 및 확장이 어려운 단일체(monolithic) 시스템이 될 수 있습니다. 반면 다중 에이전트 시스템은 항공편 검색, 호텔 예약, 렌터카 예약을 각각 전문으로 하는 에이전트들이 있어 시스템이 더 모듈화되고 유지보수 및 확장이 용이합니다.

이것을 소규모 여행사(단일 에이전트)와 프랜차이즈 여행사(다중 에이전트)로 비교할 수 있습니다. 소규모 여행사는 한 사람이 모든 예약을 처리하지만, 프랜차이즈는 각기 다른 담당자가 각 부분을 처리합니다.

## 다중 에이전트 디자인 패턴 구현의 구성 요소

다중 에이전트 디자인 패턴을 구현하기 전에, 이 패턴을 구성하는 기본 요소를 이해해야 합니다.

다시 여행 예약 예시를 통해 구체적으로 살펴보겠습니다. 이 경우 구성 요소는 다음과 같습니다:

- **에이전트 간 통신**: 항공편 검색, 호텔 예약, 렌터카 예약 에이전트는 사용자의 선호도와 제약 조건에 대해 정보를 주고받아야 합니다. 통신 프로토콜과 방법을 결정해야 합니다. 예를 들어, 항공편 검색 에이전트는 호텔 예약 에이전트와 여행 날짜 정보를 공유하여 호텔 예약이 항공편 일정과 맞도록 해야 합니다. 즉, *어떤 에이전트가 어떤 정보를 어떻게 공유할지* 결정해야 합니다.
- **조정 메커니즘**: 에이전트들은 사용자의 선호와 제약 조건을 충족시키기 위해 행동을 조율해야 합니다. 예를 들어, 사용자가 공항 근처 호텔을 원하고 렌터카는 공항에서만 대여 가능하다면, 호텔 예약 에이전트와 렌터카 예약 에이전트가 협력해야 합니다. 즉, *에이전트들이 어떻게 행동을 조율할지* 결정해야 합니다.
- **에이전트 아키텍처**: 에이전트는 의사결정을 내리고 사용자와의 상호작용에서 학습할 내부 구조를 가져야 합니다. 예를 들어, 항공편 검색 에이전트는 과거 선호도를 바탕으로 추천할 항공편을 결정할 수 있어야 합니다. 즉, *에이전트가 어떻게 의사결정하고 학습할지* 결정해야 합니다.
- **다중 에이전트 상호작용 가시성**: 여러 에이전트가 어떻게 상호작용하는지 파악할 수 있어야 합니다. 이를 위해 에이전트 활동과 상호작용을 추적하는 도구와 기법이 필요합니다. 예를 들어 로그 및 모니터링 도구, 시각화 도구, 성능 지표 등이 있습니다.
- **다중 에이전트 패턴**: 중앙집중형, 분산형, 하이브리드 아키텍처 등 다양한 다중 에이전트 시스템 구현 패턴이 있습니다. 사용 사례에 가장 적합한 패턴을 선택해야 합니다.
- **휴먼 인 더 루프**: 대부분의 경우 사람이 개입하는 상황이 있으며, 에이전트가 언제 사람의 개입을 요청할지 지시해야 합니다. 예를 들어, 사용자가 에이전트가 추천하지 않은 특정 호텔이나 항공편을 요청하거나 예약 전에 확인을 요구하는 경우가 있습니다.

## 다중 에이전트 상호작용 가시성

여러 에이전트가 서로 어떻게 상호작용하는지 파악하는 것은 매우 중요합니다. 이 가시성은 디버깅, 최적화, 시스템 전반의 효율성 확보에 필수적입니다. 이를 위해 에이전트 활동과 상호작용을 추적하는 도구와 기법이 필요합니다. 예를 들어 로그 및 모니터링 도구, 시각화 도구, 성능 지표 등이 있습니다.

예를 들어, 사용자의 여행 예약 상황을 보여주는 대시보드를 생각해 봅시다. 이 대시보드는 각 에이전트의 상태, 사용자의 선호와 제약 조건, 에이전트 간 상호작용을 보여줍니다. 사용자의 여행 날짜, 항공편 에이전트가 추천한 항공편, 호텔 에이전트가 추천한 호텔, 렌터카 에이전트가 추천한 차량 등이 표시되어 에이전트 간 상호작용과 사용자의 요구 충족 여부를 명확히 파악할 수 있습니다.

각 요소를 좀 더 자세히 살펴보겠습니다.

- **로그 및 모니터링 도구**: 각 에이전트가 수행한 작업에 대해 로그를 남겨야 합니다. 로그 항목에는 작업을 수행한 에이전트, 수행한 작업, 작업 시간, 결과 등이 포함될 수 있습니다. 이 정보는 디버깅과 최적화에 활용됩니다.
- **시각화 도구**: 시각화 도구는 에이전트 간 상호작용을 직관적으로 보여줍니다. 예를 들어, 에이전트 간 정보 흐름을 그래프로 나타내어 병목 현상, 비효율성, 문제점을 파악하는 데 도움을 줍니다.
- **성능 지표**: 성능 지표는 다중 에이전트 시스템의 효율성을 추적하는 데 유용합니다. 예를 들어 작업 완료 시간, 단위 시간당 완료 작업 수, 에이전트 추천의 정확도 등을 측정하여 개선 영역을 찾고 시스템을 최적화할 수 있습니다.

## 다중 에이전트 패턴

다중 에이전트 앱을 만들 때 사용할 수 있는 구체적인 패턴을 살펴보겠습니다. 다음은 고려할 만한 흥미로운 패턴들입니다:

### 그룹 채팅

이 패턴은 여러 에이전트가 서로 소통할 수 있는 그룹 채팅 애플리케이션을 만들 때 유용합니다. 일반적인 사용 사례로는 팀 협업, 고객 지원, 소셜 네트워킹이 있습니다.

이 패턴에서 각 에이전트는 그룹 채팅 내의 한 사용자를 나타내며, 메시징 프로토콜을 통해 메시지를 주고받습니다. 에이전트는 그룹 채팅에 메시지를 보내고, 메시지를 받고, 다른 에이전트의 메시지에 응답할 수 있습니다.

이 패턴은 모든 메시지가 중앙 서버를 통해 전달되는 중앙집중형 아키텍처나, 메시지가 직접 교환되는 분산형 아키텍처로 구현할 수 있습니다.

![Group chat](../../../translated_images/multi-agent-group-chat.ec10f4cde556babd7b450fd01e1a0fac1f9788c27d3b9e54029377bb1bdd1db6.ko.png)

### 핸드오프

이 패턴은 여러 에이전트가 작업을 서로 넘겨가며 처리하는 애플리케이션에 적합합니다.

일반적인 사용 사례로는 고객 지원, 작업 관리, 워크플로우 자동화가 있습니다.

이 패턴에서 각 에이전트는 작업이나 워크플로우의 한 단계를 나타내며, 미리 정의된 규칙에 따라 작업을 다른 에이전트에 넘길 수 있습니다.

![Hand off](../../../translated_images/multi-agent-hand-off.4c5fb00ba6f8750a0754bf29d49fa19d578080c61da40416df84d866bcdd87a3.ko.png)

### 협업 필터링

이 패턴은 여러 에이전트가 협력하여 사용자에게 추천을 제공하는 애플리케이션에 유용합니다.

여러 에이전트가 협력하는 이유는 각 에이전트가 서로 다른 전문성을 가지고 있어 추천 과정에 다양한 방식으로 기여할 수 있기 때문입니다.

예를 들어, 사용자가 주식 시장에서 어떤 주식을 사야 할지 추천을 받고 싶어 한다고 가정해 봅시다.

- **산업 전문가**: 한 에이전트는 특정 산업 분야의 전문가일 수 있습니다.
- **기술적 분석**: 다른 에이전트는 기술적 분석 전문가일 수 있습니다.
- **기본적 분석**: 또 다른 에이전트는 기본적 분석 전문가일 수 있습니다. 이들이 협력하면 사용자에게 더 포괄적인 추천을 제공할 수 있습니다.

![Recommendation](../../../translated_images/multi-agent-filtering.d959cb129dc9f60826916f0f12fe7a8339b532f5f236860afb8f16b63ea10dc2.ko.png)

## 시나리오: 환불 프로세스

고객이 제품 환불을 요청하는 상황을 생각해 봅시다. 이 과정에는 여러 에이전트가 관여할 수 있지만, 환불 프로세스에 특화된 에이전트와 다른 프로세스에서도 사용할 수 있는 일반 에이전트로 나누어 보겠습니다.

**환불 프로세스에 특화된 에이전트**:

다음은 환불 과정에 관여할 수 있는 에이전트들입니다:

- **고객 에이전트**: 고객을 대표하며 환불 절차를 시작하는 역할을 합니다.
- **판매자 에이전트**: 판매자를 대표하며 환불 처리를 담당합니다.
- **결제 에이전트**: 결제 과정을 담당하며 고객에게 환불을 진행합니다.
- **해결 에이전트**: 환불 과정에서 발생하는 문제를 해결하는 역할을 합니다.
- **준수 에이전트**: 환불 절차가 규정과 정책을 준수하는지 확인합니다.

**일반 에이전트**:

이 에이전트들은 비즈니스의 다른 부분에서도 사용할 수 있습니다.

- **배송 에이전트**: 제품을 판매자에게 반송하는 배송 과정을 담당합니다. 환불뿐 아니라 구매 시 일반 배송에도 사용됩니다.
- **피드백 에이전트**: 고객의 피드백을 수집하는 역할을 하며, 환불 과정뿐 아니라 언제든 피드백을 받을 수 있습니다.
- **에스컬레이션 에이전트**: 문제를 상위 지원 단계로 이관하는 역할을 하며, 문제를 에스컬레이션해야 하는 모든 프로세스에 사용됩니다.
- **알림 에이전트**: 환불 과정의 여러 단계에서 고객에게 알림을 보내는 역할을 합니다.
- **분석 에이전트**: 환불 과정과 관련된 데이터를 분석합니다.
- **감사 에이전트**: 환불 절차가 올바르게 수행되는지 감사를 담당합니다.
- **보고 에이전트**: 환불 과정에 대한 보고서를 생성합니다.
- **지식 에이전트**: 환불 및 비즈니스의 다른 부분과 관련된 지식 기반을 유지합니다.
- **보안 에이전트**: 환불 과정의 보안을 책임집니다.
- **품질 에이전트**: 환불 과정의 품질을 보장합니다.

앞서 나열한 에이전트들은 환불 프로세스에 특화된 것과 비즈니스의 다른 부분에서도 활용 가능한 일반 에이전트들입니다. 이를 통해 다중 에이전트 시스템에서 어떤 에이전트를 사용할지 결정하는 데 도움이 되길 바랍니다.

## 과제
## 이전 강의

[설계 계획](../07-planning-design/README.md)

## 다음 강의

[AI 에이전트의 메타인지](../09-metacognition/README.md)

**면책 조항**:  
이 문서는 AI 번역 서비스 [Co-op Translator](https://github.com/Azure/co-op-translator)를 사용하여 번역되었습니다. 정확성을 위해 최선을 다하고 있으나, 자동 번역에는 오류나 부정확한 부분이 있을 수 있음을 유의해 주시기 바랍니다. 원문은 해당 언어의 원본 문서가 권위 있는 출처로 간주되어야 합니다. 중요한 정보의 경우 전문적인 인간 번역을 권장합니다. 본 번역의 사용으로 인해 발생하는 오해나 잘못된 해석에 대해 당사는 책임을 지지 않습니다.