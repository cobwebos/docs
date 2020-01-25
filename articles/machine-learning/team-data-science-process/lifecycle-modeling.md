---
title: Team Data Science Process 수명 주기의 모델링 단계
description: 데이터 과학 프로젝트의 모델링 단계에 대한 목표, 작업 및 결과물입니다.
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
ms.openlocfilehash: 1d3cd61ea3da88c4c5231f22c0e127508591fb8d
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720463"
---
# <a name="modeling-stage-of-the-team-data-science-process-lifecycle"></a>Team Data Science Process 수명 주기의 모델링 단계

이 문서에서는 TDSP(팀 데이터 과학 프로세스)의 모델링 단계와 관련된 목표, 작업 및 결과물을 설명합니다. 이 프로세스는 데이터 과학 프로젝트를 구조화하는 데 사용할 수 있는 권장 수명 주기를 제공합니다. 수명 주기는 일반적으로 프로젝트에서 반복적으로 자주 실행하는 주요 단계를 설명합니다.

   1. **비즈니스 이해**
   2. **데이터 취득 및 이해**
   3. **모델링**
   4. **배포**
   5. **고객 승인**

다음은 TDSP 수명 주기를 시각적으로 나타낸 것입니다.

![TDSP 수명 주기](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>목표
* Machine Learning 모델을 위한 최적의 데이터 기능을 결정합니다.
* 대상에 대해 가장 정확하게 예측하는 정보 제공용 Machine Learning 모델을 만듭니다.
* 프로덕션 환경에 적합한 Machine Learning 모델을 만듭니다.

## <a name="how-to-do-it"></a>수행 방법
이 단계에서 설명하는 세 가지 기본 작업은 다음과 같습니다.

  * **기능 엔지니어링**: 모델 학습을 용이하게 하기 위해 원시 데이터로부터 데이터 기능을 만듭니다.
  * **모델 학습**: 성공 메트릭을 비교하여 질문에 가장 정확하게 대답하는 모델을 찾습니다.
  * **프로덕션에 적합한** 모델인지 확인합니다.

### <a name="feature-engineering"></a>기능 엔지니어링
기능 엔지니어링은 원시 변수의 포함, 집계 및 변환을 포함하여 분석에 사용되는 기능을 만듭니다. 모델에 영향을 주는 요인들에 대한 통찰력을 원하면 기능이 서로 어떻게 관련되어 있고 Machine Learning 알고리즘에서 이러한 기능을 어떻게 사용하는지를 이해해야 합니다. 

이 단계에서 도메인 전문지식과 데이터 탐색 단계에서 얻은 통찰력을 창의적으로 결합해야 합니다. 기능 엔지니어링은 관련이 없는 변수를 상당히 많이 제거하면서 유용한 정보를 제공하는 변수를 찾아 포함합니다. 정보 제공 변수는 결과를 향상시키지만 관련이 없는 변수는 모델에 불필요한 혼란만 가져옵니다. 또한 점수 매기기 중에 가져온 새로운 데이터에 대해 이러한 기능을 생성해야 합니다. 따라서 이러한 기능은 점수를 매길 때 사용할 수 있는 데이터에만 기반하여 생성할 수 있습니다. 

다양한 Azure 데이터 기술을 사용하는 경우 기능 엔지니어링에 대한 기술 지침은 [데이터 과학 프로세스의 기능 엔지니어링](create-features.md)을 참조하세요. 

### <a name="model-training"></a>모델 학습
대답하려는 질문 유형에 따라 다양한 모델링 알고리즘을 사용할 수 있습니다. 알고리즘 선택에 대한 지침은 [Microsoft Azure Machine Learning을 위한 알고리즘 선택 방법](../studio/algorithm-choice.md)을 참조하세요. 이 문서는 Azure Machine Learning을 사용하지만 제공되는 지침은 모든 Machine Learning 프로젝트에 유용합니다. 

모델 학습 프로세스에는 다음 단계가 포함됩니다. 

   * 모델링을 위해 학습 데이터 집합과 테스트 데이터 집합으로 **입력 데이터를 임의로 분할**합니다.
   * 학습 데이터 집합을 사용하여 **모델을 빌드**합니다.
   * 학습 및 테스트 데이터 집합을 **평가**합니다. 현재 데이터로 관심 있는 질문에 대답하는 데 관련된 다양한 튜닝 매개 변수(*매개 변수 스윕*이라고 함)와 함께 일련의 경쟁적인 컴퓨터 학습 알고리즘을 사용합니다.
   * 대체 방법 간의 성공 메트릭을 비교하여 질문에 대답하는 **"최상"의 솔루션을 결정**합니다.

> [!NOTE]
> **유출 방지**: 모델 또는 Machine Learning 알고리즘을 통해 비현실적으로 좋은 예측을 만들기 위해 학습 데이터 집합 외부의 데이터를 포함하면 데이터 유출이 발생할 수 있습니다. 유출은 데이터 과학자들이 진실하기에는 너무 좋아 보이는 예측 결과를 가져올 때 불안해 하는 일반적 이유입니다. 이러한 종속성은 감지하기 어려울 수 있습니다. 유출을 방지하려면 종종 분석 데이터 집합을 빌드하고 모델을 만들어 결과의 정확성을 평가하는 과정을 반복해야 합니다. 
> 
> 

여러 알고리즘 및 매개 변수 스윕을 통해 실행하여 기본 모델을 생성할 수 있는 [자동화된 모델링 및 보고 도구](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling)에 TDSP를 제공합니다. 또한 각 모델의 성능과 변수 중요도를 포함한 매개 변수 조합을 요약한 기본 모델링 보고서를 생성합니다. 이 프로세스는 추가 기능 엔지니어링을 진행할 수 있으므로 반복적입니다. 

## <a name="artifacts"></a>Artifacts
이 단계에서 생성되는 아티팩트는 다음과 같습니다.

   * [기능 집합](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md): 모델링을 위해 개발된 기능은 **데이터 정의** 보고서의 **기능 집합** 섹션에 설명되어 있습니다. 여기에는 기능을 생성하는 코드에 대한 조언과 기능을 생성한 방법에 대한 설명이 있습니다.
   * [모델 보고서](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): 시도하는 각 모델에 대해 각 실험에 대한 세부 정보를 제공하는 템플릿 기반 표준 보고서가 생성됩니다.
   * **检查点决策**：评估模型是否足以用于生产。 몇 가지 주요 질문은 다음과 같습니다.
     * 테스트 데이터가 제공되면 질문에 대해 모델에서 매우 신뢰성 있게 대답합니까? 
     * 대안을 시도해야 할까요? 추가 데이터를 수집하고 더 많은 기능 엔지니어링을 수행하거나 다른 알고리즘으로 시험해 볼까요?

## <a name="next-steps"></a>다음 단계

TDSP의 수명 주기에서 각 단계에 대한 링크는 다음과 같습니다.

   1. [비즈니스 이해](lifecycle-business-understanding.md)
   2. [데이터 취득 및 이해](lifecycle-data.md)
   3. [모델링](lifecycle-modeling.md)
   4. [배포](lifecycle-deployment.md)
   5. [고객 승인](lifecycle-acceptance.md)

특정 시나리오 프로세스의 모든 단계를 보여주는 전체 엔드투엔드 연습도 제공됩니다. [예제 연습](walkthroughs.md) 문서는 링크 및 썸네일 설명을 포함하는 시나리오 목록을 제공합니다. 이 연습에서는 클라우드 및 온-프레미스 도구와 서비스를 워크플로 또는 파이프라인에 결합하여 지능형 애플리케이션을 만드는 방법을 설명합니다. 

Azure Machine Learning Studio를 사용하는 TDSP의 단계 실행 방법의 예제를 보려면 [Azure Machine Learning에서 TDSP 사용](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)을 참조하세요. 
