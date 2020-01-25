---
title: 팀 데이터 과학 프로세스의 비즈니스 이해
description: 팀 데이터 과학 프로세스의 데이터 과학 프로젝트에서 비즈니스 이해 단계의 목표, 작업 및 결과물입니다.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a7aaed519f8f97a9be77a263568aeed5257c16d6
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76710337"
---
# <a name="the-business-understanding-stage-of-the-team-data-science-process-lifecycle"></a>팀 데이터 과학 프로세스 수명 주기의 비즈니스 이해 단계

이 문서에서는 TDSP(팀 데이터 과학 프로세스)의 비즈니스 이해 단계와 관련된 목표, 작업 및 결과물을 설명합니다. 이 프로세스는 데이터 과학 프로젝트를 구조화하는 데 사용할 수 있는 권장 수명 주기를 제공합니다. 수명 주기는 일반적으로 프로젝트에서 반복적으로 자주 실행하는 주요 단계를 설명합니다.

   1. **비즈니스 이해**
   2. **데이터 취득 및 이해**
   3. **모델링**
   4. **배포**
   5. **고객 승인**

다음은 TDSP 수명 주기를 시각적으로 나타낸 것입니다. 

![TDSP 수명 주기](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>목표
* 모델 대상의 역할을 하며 관련 메트릭이 프로젝트의 성공 여부를 결정하는 데 사용되는 주요 변수를 지정합니다.
* 비즈니스가 액세스할 수 있거나 가져와야 하는 관련 데이터 원본을 식별합니다.

## <a name="how-to-do-it"></a>수행 방법
이 단계에서 설명하는 두 가지 기본 작업은 다음과 같습니다. 

   * **목표 정의**: 고객 및 기타 이해 관계자와 협력하여 비즈니스 문제를 이해하고 파악합니다. 데이터 과학 기술이 목표로 삼을 수 있는 비즈니스 목표를 정의하는 질문을 작성합니다.
   * **데이터 원본 식별**: 프로젝트 목표를 정의하는 질문에 답하는 데 도움이 되는 관련 데이터를 찾습니다.

### <a name="define-objectives"></a>목표 정의
1. 이 단계의 주요 목적은 분석에서 예측해야 하는 주요 비즈니스 변수를 식별하는 것입니다. 이러한 변수를 *모델 대상*이라고 하며, 관련 메트릭을 사용하여 프로젝트의 성공 여부를 결정합니다. 판매 예측이나 사기성 주문의 확률이 이러한 대상의 두 가지 예입니다.

2. 적절하고 명확하며 모호하지 않은 "예리한" 질문을 요청하고 수정하여 프로젝트 목표를 정의합니다. 데이터 과학은 이러한 질문에 대답하기 위해 이름과 숫자를 사용하는 프로세스입니다. 데이터 과학 또는 Machine Learning은 일반적으로 다음과 같은 다섯 가지 유형의 질문에 대답하는 데 사용합니다.
 
   * 양 또는 개수는 얼마인가요? (회귀)
   * 어떤 범주? (분류)
   * 어떤 그룹? (클러스터링)
   * 이것은 이상한가요? (이상 감지)
   * 수행해야 할 옵션은? (권장)

   이러한 질문 중 어떤 질문을 하고 비즈니스 목표 달성을 위해 질문에 어떻게 대답할지를 결정합니다.

3. 구성원의 역할과 책임을 지정하여 프로젝트 팀을 정의합니다. 더 많은 정보를 찾아내면 반복하는 높은 수준의 획기적인 계획을 개발합니다. 

4. 성공 메트릭을 정의합니다. 예를 들어 다음 고객 변동 예측을 수행하려고 할 수 있습니다. 이 3개월 프로젝트가 끝날 때까지 "x"%의 정확도가 필요합니다. 이 데이터를 바탕으로 고객 프로모션을 통해 변동을 줄일 수 있습니다. 메트릭은 **SMART**여야 합니다. 

   * **S**pecific(특정) 
   * **M**easurable(측정 가능)
   * **A**chievable(달성 가능) 
   * **R**elevant(관련성) 
   * **T**ime-bound(시간 제한) 

### <a name="identify-data-sources"></a>데이터 원본 식별
예리한 질문에 대한 답변의 알려진 예가 들어있는 데이터 원본을 확인합니다. 다음 데이터를 찾습니다.

* 질문과 관련된 데이터입니다. 대상과 관련된 측정값과 기능이 있나요?
* 모델 대상 및 관련 기능의 정확한 측정값 데이터입니다.

예를 들어 문제를 해결하고 프로젝트 목표를 달성하기 위해 기존 시스템에서 다른 종류의 데이터를 더 수집하고 기록해야 한다고 사실을 확인할 수 있습니다. 이 경우 외부 데이터 원본을 찾거나 새 데이터를 수집하도록 시스템을 업데이트할 수 있습니다.

## <a name="artifacts"></a>Artifacts
이 단계의 결과물은 다음과 같습니다.

   * [선언 문서](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): TDSP 프로젝트 구조 정의에서 표준 템플릿을 제공하는 실무 문서입니다. 새로운 결과를 발견했으며 비즈니스 요구 사항이 달라질 때 프로젝트 전체에서 템플릿을 업데이트합니다. 검색 프로세스를 진행하면서 이 문서에서 반복적으로 더 자세히 추가하는 것이 핵심입니다. 고객 및 기타 이해 관계자가 변경 작업을 수행하도록 만들고 변경 이유를 명확하게 전달해야 합니다.  
   * [데이터 원본](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md#raw-data-sources): TDSP 프로젝트 **데이터 보고서** 폴더에 있는 **데이터 정의** 보고서의 **원시 데이터 원본** 섹션에 데이터 원본이 포함되어 있습니다. 이 섹션은 원시 데이터의 원본 및 대상 위치를 지정합니다. 이후 단계에서 추가 세부 정보(예: 스크립트)를 입력하여 데이터를 분석 환경으로 옮깁니다.  
   * [데이터 사전](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Data_Dictionaries): 이 문서는 클라이언트가 제공하는 데이터를 설명합니다. 이러한 설명에는 스키마(데이터 형식, 유효성 검사 규칙(있는 경우)에 대한 정보) 및 엔터티-관계 다이어그램(해당되는 경우)에 대한 정보가 포함됩니다.

## <a name="next-steps"></a>다음 단계

TDSP의 수명 주기에서 각 단계에 대한 링크는 다음과 같습니다.

   1. [비즈니스 이해](lifecycle-business-understanding.md)
   2. [데이터 취득 및 이해](lifecycle-data.md)
   3. [모델링](lifecycle-modeling.md)
   4. [배포](lifecycle-deployment.md)
   5. [고객 승인](lifecycle-acceptance.md)

我们提供了完整的演练，演示特定方案在过程中的所有步骤。 [예제 연습](walkthroughs.md) 문서는 링크 및 썸네일 설명을 포함하는 시나리오 목록을 제공합니다. 이 연습에서는 클라우드 및 온-프레미스 도구와 서비스를 워크플로 또는 파이프라인에 결합하여 지능형 애플리케이션을 만드는 방법을 설명합니다. 
