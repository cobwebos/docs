---
title: Team Data Science Process의 데이터 취득 및 이해
description: 데이터 과학 프로젝트의 데이터 취득 및 이해 단계에 대한 목표, 작업 및 결과물입니다.
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
ms.openlocfilehash: 3c299e9ec42d63812804b5ff7e50324a2de94200
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720497"
---
# <a name="data-acquisition-and-understanding-stage-of-the-team-data-science-process"></a>Team Data Science Process의 데이터 취득 및 이해 단계

이 문서에서는 TDSP(팀 데이터 과학 프로세스)의 데이터 취득 및 이해 단계와 관련된 목표, 작업 및 결과물을 설명합니다. 이 프로세스는 데이터 과학 프로젝트를 구조화하는 데 사용할 수 있는 권장 수명 주기를 제공합니다. 수명 주기는 일반적으로 프로젝트에서 반복적으로 자주 실행하는 주요 단계를 설명합니다.

   1. **비즈니스 이해**
   2. **데이터 취득 및 이해**
   3. **모델링**
   4. **배포**
   5. **고객 승인**

다음은 TDSP 수명 주기를 시각적으로 나타낸 것입니다. 

![TDSP 수명 주기](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>목표
* 대상 변수와의 관계를 이해할 수 있는 정리된 고품질 데이터 집합을 생성합니다. 모델링할 수 있도록 해당 데이터 집합을 적절한 분석 환경에 배치합니다.
* 데이터를 정기적으로 새로 고치고 점수를 매기는 데이터 파이프라인의 솔루션 아키텍처를 개발합니다.

## <a name="how-to-do-it"></a>수행 방법
이 단계에서 설명하는 세 가지 기본 작업은 다음과 같습니다.

   * **데이터를 대상 분석 환경에 수집**합니다.
   * **데이터를 탐색**하여 데이터 품질이 질문에 대답하기에 적합한지 판단합니다. 
   * **데이터 파이프라인을 설정**하여 새롭거나 정기적으로 새로 고쳐지는 데이터에 대해 점수를 매깁니다.

### <a name="ingest-the-data"></a>데이터 수집
원본 위치에서 분석 작업(예: 학습, 예측)을 실행할 대상 위치로 데이터를 이동하는 프로세스를 설정합니다. 다양한 Azure 데이터 서비스에서의 데이터 이동 방법에 대한 기술 세부 정보 및 옵션에 대해서는 [분석을 위해 스토리지 환경에 데이터로드 ](ingest-data.md)를 참조하세요. 

### <a name="explore-the-data"></a>데이터 탐색
모델을 학습하기 전에 데이터에 대한 적절한 이해를 개발해야 합니다. 실제 데이터 집합은 종종 난해하거나 가치가 없거나 상당히 일치하지 않습니다. 데이터를 모델링할 준비가 되기 전에 데이터 요약 및 시각화를 사용하여 데이터 품질을 평가하고, 처리하는 데 필요한 정보를 제공할 수 있습니다. 이 프로세스는 종종 반복적입니다.

TDSP는 [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils)라고 하는 자동화 유틸리티를 제공하여 데이터를 시각화하고 데이터 요약 보고서를 준비합니다. 먼저 IDEAR로 시작하여 데이터를 탐색하고, 코딩 없이 대화식으로 초기 데이터를 이해하는 방식을 개발하는 것이 좋습니다. 그런 후 데이터 탐색 및 시각화를 위한 사용자 지정 코드를 작성할 수 있습니다. 데이터 정리에 대한 지침은 [향상된 기계 학습을 위한 데이터 준비 작업](prepare-data.md)을 참조하세요.  

정리된 데이터 품질이 만족스러우면 다음 단계는 데이터에 내재된 패턴을 더 잘 이해하는 것입니다. 此数据分析有助于为目标选择和开发适当的预测模型。 데이터가 목표에 얼마나 잘 연결되는지에 대한 증거 자료를 확인합니다. 그런 후 다음 모델링 단계를 진행할 수 있는 충분한 데이터가 있는지 확인합니다. 다시금 말하지만 이 프로세스는 종종 반복적입니다. 이전 단계에서 처음 식별된 데이터 집합을 보강하기 위해 더 정확하거나 관련성이 더 높은 데이터로 새 데이터 원본을 찾아야 할 수도 있습니다. 

### <a name="set-up-a-data-pipeline"></a>데이터 파이프라인 설정
데이터의 초기 수집 및 정리 외에도 일반적으로 지속적인 학습 프로세스의 일부로 새 데이터에 점수를 매기거나 정기적으로 데이터를 새로 고치는 프로세스를 설정해야 합니다. 可以使用数据管道或工作流来完成评分。 [Azure Data Factory를 사용하여 온-프레미스 SQL Server 인스턴스에서 Azure SQL Database로 데이터 이동](move-sql-azure-adf.md) 문서에서는 [Azure Data Factory](https://azure.microsoft.com/services/data-factory/)를 사용하여 파이프라인을 설정하는 방법의 예제를 제공합니다. 

이 단계에서는 데이터 파이프라인의 솔루션 아키텍처를 개발합니다. 데이터 과학 프로젝트의 다음 단계를 진행하면서 동시에 파이프라인을 개발합니다. 根据你的业务需求以及此解决方案所集成到的现有系统的约束，管道可以是以下选项之一： 

   * Batch 기반
   * 스트리밍 또는 실시간 
   * 하이브리드 

## <a name="artifacts"></a>Artifacts
이 단계의 결과물은 다음과 같습니다.

   * [데이터 품질 보고서](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/DataSummaryReport.md): 이 보고서에는 데이터 요약, 각 속성과 대상 간의 관계, 변수 순위 등이 포함됩니다. TDSP의 일부로 제공되는 [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) 도구는 CSV 파일이나 관계형 테이블과 같이 테이블 형식의 데이터 집합에서 이 보고서를 신속하게 생성할 수 있습니다. 
   * **솔루션 아키텍처**: 솔루션 아키텍처는 모델을 만든 후에 새로운 데이터에 대한 점수 매기기 또는 예측을 실행하는 데 사용되는 데이터 파이프라인의 다이어그램 또는 설명이 될 수 있습니다. 또한 새로운 데이터에 기반하여 모델을 다시 학습하는 파이프라인도 포함되어 있습니다. 이 문서를 TDSP 디렉터리 구조 템플릿을 사용할 때 [Project](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) 디렉터리에 저장합니다.
   * **检查点决策**：在开始功能设计和建模前，可以重新评估项目，以确定预期值是否足以继续处理。 예를 들어 계속 진행할 준비가 되었거나, 더 많은 데이터를 수집해야 하거나, 질문에 대답하기 위한 데이터가 존재하지 않아 프로젝트를 중단할 수 있습니다.

## <a name="next-steps"></a>다음 단계

TDSP의 수명 주기에서 각 단계에 대한 링크는 다음과 같습니다.

   1. [비즈니스 이해](lifecycle-business-understanding.md)
   2. [데이터 취득 및 이해](lifecycle-data.md)
   3. [모델링](lifecycle-modeling.md)
   4. [배포](lifecycle-deployment.md)
   5. [고객 승인](lifecycle-acceptance.md)

我们提供了完整的演练，演示特定方案在过程中的所有步骤。 [예제 연습](walkthroughs.md) 문서는 링크 및 썸네일 설명을 포함하는 시나리오 목록을 제공합니다. 이 연습에서는 클라우드 및 온-프레미스 도구와 서비스를 워크플로 또는 파이프라인에 결합하여 지능형 애플리케이션을 만드는 방법을 설명합니다. 

Azure Machine Learning Studio를 사용하는 TDSP의 단계 실행 방법의 예제를 보려면 [Azure Machine Learning에서 TDSP 사용](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/lifecycle-data)을 참조하세요.
