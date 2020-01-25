---
title: 데이터 과학 작업 실행 - Team Data Science Process
description: 데이터 과학자가 추적 가능하고, 버전을 제어하고, 공동 작업을 수행하는 방식으로 데이터 과학 프로젝트를 실행하는 방법을 설명합니다.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 984b03288b8dae644fc04a2cd78fb03a2e027f62
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722197"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>데이터 과학 작업 실행: 탐색, 모델링 및 배포

일반적인 데이터 과학 작업으로 데이터 탐색, 모델링 및 배포가 있습니다. 이 문서에서는 **IDEAR(대화형 데이터 탐색, 분석 및 보고)** 및 **AMAR(자동 모델링 및 보고)** 유틸리티를 사용하여 대화형 데이터 탐색, 데이터 분석, 보고 및 모델 생성 등과 같은 여러 가지 일반적인 데이터 과학 작업을 완료하는 방법을 보여 줍니다. 用于将模型部署到生产环境中的选项可能包括：

- [Azure Machine Learning](../index.yml)
- [ML 서비스 포함 SQL-Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-services)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)


## 1. <a name='DataQualityReportUtility-1'></a>浏览 

데이터 과학자는 다양한 방법으로, 예를 들어 Python(예: matplotlib) 또는 R(예: ggplot 또는 lattice)과 함께 사용할 수 있는 라이브러리 및 패키지를 사용하여 탐색 및 보고를 수행할 수 있습니다. 데이터 과학자는 해당 코드를 사용자 지정하여 특정 시나리오의 데이터 탐색 요구에 맞출 수 있습니다. 구조화된 데이터를 다루기 위한 요구는 텍스트나 이미지 등 구조화되지 않은 데이터의 경우와 다릅니다. 

产品（如 Azure 机器学习）还为数据整理和探索提供[高级数据准备](../how-to-create-register-datasets.md)，包括功能创建。 사용자는 자신의 요구에 가장 맞는 도구, 라이브러리 및 패키지에 대해 결정해야 합니다. 

이 단계가 종료할 때의 결과물은 데이터 탐색 보고서입니다. 보고서는 모델링 및 데이터가 모델링 단계로 진행하는 데 적합한지 여부를 평가에 사용할 데이터의 매우 포괄적인 뷰를 제공해야 합니다. 반자동화 탐색, 모델링 및 보고에 대해 다음 섹션에서 설명하는 TDSP(팀 데이터 과학 프로세스) 유틸리티도 표준화된 데이터 탐색 및 모델링 보고서를 제공합니다. 

### <a name="interactive-data-exploration-analysis-and-reporting-using-the-idear-utility"></a>IDEAR 유틸리티를 사용한 대화형 데이터 탐색, 분석 및 보고

이 R Markdown 기반 또는 Phython 노트북 기반 유틸리티는 데이터 집합을 평가하고 탐색할 수 있는 유연한 대화형 도구를 제공합니다. 사용자는 최소한의 코딩으로 데이터 집합에서 보고서를 빠르게 생성할 수 있습니다. 사용자가 단추를 클릭하여 대화형 도구에서 표시된 탐색 결과를 최종 보고서로 내보낼 수 있습니다. 이 보고서는 클라이언트에 전달되거나 후속 모델링 단계에 포함될 변수를 결정하는 데 사용됩니다.

현재 이 도구는 메모리 내 데이터 프레임에서만 작동합니다. YAML 파일이 탐색할 데이터 집합의 매개 변수를 지정하는 데 필요합니다. 자세한 내용은 [TDSP 데이터 과학 유틸리티의 IDEAR(영문)](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils)을 참조하세요.


## 2. <a name='ModelingUtility-2'></a>建模

다양한 언어의 수많은 모델 교육용 도구 키트와 패키지가 있습니다. 데이터 과학자는 관련 비즈니스 사용 사례 및 프로덕션 시나리오에 대한 정확도 및 대기 시간에 관한 성능을 고려하여 편하게 느끼는 키트와 패키지를 자유롭게 사용할 수 있습니다.

다음 섹션에서는 반자동화 모델링을 위해 R 기반 TDSP 유틸리티를 사용하는 방법을 보여 줍니다. 이 AMAR 유틸리티를 사용하여 기준선 모델을 빠르게 생성할 수 있을 뿐만 아니라 더 나은 성능의 모델을 제공하기 위해 조정해야 하는 매개 변수도 생성할 수 있습니다.
다음 모델 관리 섹션에서는 복수의 모델을 등록하고 관리하기 위한 시스템을 보유하는 방법을 보여 줍니다.


### <a name="model-training-modeling-and-reporting-using-the-amar-utility"></a>모델 교육: AMAR 유틸리티를 사용하여 모델링 및 보고

[AMAR(자동화 모델링 및 보고) 유틸리티](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling)는 사용자 지정 가능한 반자동화 도구를 제공하여 하이퍼 파라미터 스윕이 있는 모델을 만들고 이러한 모델의 정확도를 비교합니다. 

모델 생성 유틸리티는 다른 섹션을 쉽게 탐색할 수 있는 목차가 포함된 자체 포함 HTML 출력을 생성하기 위해 실행할 수 있는 R Markdown 파일입니다. Markdown 파일이 실행(knit)될 때 세 가지 알고리즘, 즉 glmnet 패키지를 사용하는 정규화된 회귀, randomForest 패키지를 사용하는 임의의 포리스트, xgboost 패키지를 사용하는 트리 승격이 실행됩니다. 이러한 알고리즘 각각은 학습된 모델을 생성합니다. 이러한 모델의 정확도를 비교한 다음 상대적 기능 중요도 플롯을 보고합니다. 현재 두 가지 유틸리티가 있습니다. 각각 이진 분류 작업과 회귀 작업을 위한 유틸리티입니다. 이들 간의 주요 차이점은 제어 매개 변수와 정확도 메트릭이 이러한 학습 작업에 지정되는 방식에 있습니다. 

YAML 파일은 다음을 지정하는 데 사용됩니다.

- 데이터 입력(SQL 원본 또는 R 데이터 파일) 
- 학습 및 테스트에 사용되는 부분
- 실행할 알고리즘 
- 모델 최적화에 대한 제어 매개 변수 선택:
    - 교차 검증 
    - 부트스트래핑
    - 교차 검증 겹(폴드)
- 각 알고리즘에 대한 하이퍼 파라미터 집합 

또한 모델을 빠르게 실행하기 위해 Yaml 파일에서 알고리즘 수, 최적화 겹(폴드) 횟수, 하이퍼 파라미터 및 스윕하도록 설정된 하이퍼 파라미터 집합 수를 수정할 수 있습니다. 예를 들어 더 적은 수의 CV(교차 검증) 겹과 파라미터 집합으로 모델을 실행할 수 있습니다. 신뢰할 수 있는 경우 CV 접기 수를 높이거나 매개 변수 집합을 더 많이 사용하면 더 포괄적으로 실행할 수도 있습니다.

자세한 내용은 [TDSP 데이터 과학 유틸리티의 자동화된 모델링 및 보고 유틸리티](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling)를 참조하세요.

### <a name="model-management"></a>모델 관리
복수의 모델을 작성한 후에는 일반적으로 모델을 등록하고 관리하기 위한 시스템이 있어야 합니다. 일반적으로 스크립트 또는 API와 백 엔드 데이터베이스 또는 버전 관리 시스템의 조합이 필요합니다. 이러한 관리 작업에 대해 고려할 수 있는 몇 가지 옵션은 같습니다.

1. [Azure Machine Learning 모델 관리 서비스](../index.yml)
2. [MIT의 ModelDB](https://mitdbg.github.io/modeldb/) 
3. [모델 관리 시스템으로 SQL Server 사용](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

## 3. <a name='Deployment-3'></a>部署

프로덕션 배포를 사용하여 비즈니스의 현재 역할을 수행하도록 모델링할 수 있습니다. 배포된 모델의 예측을 비즈니스 의사 결정에 사용할 수 있습니다.

### <a name="production-platforms"></a>프로덕션 플랫폼
모델을 프로덕션으로 전환하기 위한 여러 접근법과 플랫폼이 있습니다. 다음은 몇 가지 옵션입니다.


- [Azure Machine Learning에서 배포 모델링](../how-to-deploy-and-where.md)
- [SQL-Server의 모델 배포](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

> [!NOTE]
> 배포 전에 모델 스코어링의 대기 시간이 프로덕션에 사용할 만큼 충분히 낮은지 확인해야 합니다.
>
>

**특정 시나리오**에 대한 프로세스의 모든 단계를 보여 주는 연습에서 추가 예제를 사용할 수 있습니다. 이러한 단계는 [예제 연습](walkthroughs.md) 자료에서 미리 보기 설명과 함께 나열되고 연결되어 있습니다. 이 연습에서는 클라우드 및 온-프레미스 도구와 서비스를 워크플로 또는 파이프라인에 결합하여 지능형 애플리케이션을 만드는 방법을 보여 줍니다.

> [!NOTE]
> Azure Machine Learning Studio를 사용하는 배포에 대해서는 [Azure Machine Learning 웹 서비스 배포](../studio/deploy-a-machine-learning-web-service.md)를 참조하세요.
>
>

### <a name="ab-testing"></a>A/B 테스트
프로덕션에 복수의 모델이 있는 경우 [A/B 테스트](https://en.wikipedia.org/wiki/A/B_testing)를 수행하여 모델의 성능을 비교하면 유용할 수 있습니다. 

 
## <a name="next-steps"></a>다음 단계

[데이터 과학 프로젝트의 진행률 추적](track-progress.md)에서는 데이터 과학자가 데이터 과학 프로젝트의 진행률을 추적할 수 있는 방법을 보여 줍니다.

[모델 작업 및 CI/CD](ci-cd-flask.md)에서는 개발된 모델로 CI/CD를 수행하는 방법을 보여줍니다.


