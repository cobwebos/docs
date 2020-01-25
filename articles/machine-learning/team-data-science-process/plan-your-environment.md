---
title: 시나리오 식별 및 분석 프로세스 계획 - Team Data Science Process | Azure Machine Learning
description: 일련의 주요 질문을 고려하여 시나리오를 파악하고 고급 분석 데이터 처리를 계획합니다.
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
ms.openlocfilehash: b0b811a2b7ed432b7fc5015886b28337ca33424e
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76710322"
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>시나리오를 파악하고 고급 분석 데이터 처리를 계획하는 방법

데이터 세트에서 고급 분석 처리를 수행할 수 있는 환경을 만드는 데 필요한 리소스는 무엇일까요? 이 문서는 시나리오에 적합한 작업과 리소스를 파악하는 데 도움이 될만한 것을 묻는 일련의 질문을 제시합니다.

예측 분석에 대한 고급 단계의 순서에 대해 자세히 알아보려면 [TDSP(팀 데이터 과학 프로세스)란 무엇입니까?](overview.md)를 참조하세요. 이러한 각 단계에는 개별적인 시나리오에 관련된 작업을 위한 구체적인 리소스가 필요합니다.

다음 영역의 핵심 질문에 답변하여 시나리오를 식별하세요.

* 데이터 물류
* 데이터 특성
* 데이터 세트 품질
* 기본 설정 도구 및 언어

## <a name="logistic-questions-data-locations-and-movement"></a>로지스틱 질문: 데이터 위치 및 이동

물류 질문에는 다음 항목이 포함됩니다.

* 데이터 원본 위치
* Azure의 목표 대상
* 일정, 양 및 관련된 리소스를 포함하여 데이터 이동을 위한 요구 사항

분석 프로세스 동안 데이터를 여러 번 이동해야 할 수 있습니다. 일반적인 시나리오는 로컬 데이터를 Azure에 있는 일정 형태의 스토리지로 이동한 후 Machine Learning Studio로 이동하는 것입니다.

### <a name="what-is-your-data-source"></a>데이터 원본은 무엇인가요?

사용하는 데이터가 로컬에 있나요 아니면 클라우드에 있나요? 가능한 위치는 다음과 같습니다.

* 공개적으로 사용할 수 있는 HTTP 주소
* 로컬 또는 네트워크 파일 위치
* SQL Server 데이터베이스
* Azure 存储容器

### <a name="what-is-the-azure-destination"></a>Azure 대상은 무엇인가요?

처리 또는 모델링을 위해서는 데이터가 어디에 있어야 하나요? 

* Azure Blob Storage
* SQL Azure 데이터베이스
* Azure VM의 SQL Server
* HDInsight(Azure의 Hadoop) 또는 하이브 테이블
* Azure Machine Learning
* 탑재식 Azure 가상 하드 디스크

### <a name="how-are-you-going-to-move-the-data"></a>데이터를 어떻게 이동할건가요?

다양한 스토리지 및 처리 환경에 데이터를 수집하거나 로드하기 위한 절차와 리소스에 대해서는 다음을 참조하세요.

* [분석용 스토리지 환경에 데이터 로드](ingest-data.md)
* [将训练数据从各种数据源导入 Azure 机器学习 Studio （经典）](../studio/import-data.md)

### <a name="does-the-data-need-to-be-moved-on-a-regular-schedule-or-modified-during-migration"></a>데이터를 정기적으로 이동해야 하나요? 마이그레이션 중에 수정되어야 하나요?

데이터 지속적으로 마이그레이션해야 하는 경우 ADF(Azure Data Factory)를 사용하는 것이 좋습니다. ADF는 다음에 유용할 수 있습니다.

* 온-프레미스 및 클라우드 리소스를 둘 다 포함하는 하이브리드 시나리오
* 마이그레이션 과정 중에 비즈니스 논리에 따라 데이터를 처리, 수정 또는 변경하는 시나리오

有关详细信息，请参阅[使用 Azure 数据工厂将数据从本地 SQL Server 移动到 SQL Azure](move-sql-azure-adf.md)。

### <a name="how-much-of-the-data-is-to-be-moved-to-azure"></a>얼마나 많은 양의 데이터를 Azure로 이동해야 하나요?

大型数据集可能会超过某些环境的存储容量。 有关示例，请参阅下一部分中有关机器学习 Studio （经典）的大小限制的讨论。 이런 경우, 분석하는 동안 데이터 샘플을 사용할 수 있습니다. 다양한 Azure 환경에서 데이터 세트를 다운 샘플링하는 방법에 대한 자세한 내용은 [팀 데이터 과학 프로세스의 데이터 샘플링](sample-data.md)을 참조하세요.

## <a name="data-characteristics-questions-type-format-and-size"></a>데이터 특성 질문: 유형, 형식, 크기

이러한 질문은 스토리지 및 처리 환경을 계획하는 데 매우 중요합니다. 데이터 형식에 적합한 시나리오를 선택하고 제한 사항을 이해하는 데 도움이 됩니다.

### <a name="what-are-the-data-types"></a>데이터 형식은 무엇인가요?

* 숫자
* 범주
* 문자열
* 이진

### <a name="how-is-your-data-formatted"></a>사용하는 데이터의 서식은 어떻게 지정되어 있나요?

* 쉼표로 구분되거나(CSV) 탭으로 구분된(TSV) 플랫 파일
* 압축되거나 압축되지 않은
* Azure Blob
* Hadoop 하이브 테이블
* SQL Server 테이블

### <a name="how-large-is-your-data"></a>데이터는 얼마나 큰가요?

* 작음: 2GB 미만
* 보통: 2GB보다 크고 10GB보다 작음
* 큼: 10GB 초과

采用 Azure 机器学习 Studio （经典）环境例如：

* Azure Machine Learning Studio에서 지원되는 데이터 형식 및 유형의 목록은 [지원되는 데이터 형식 및 데이터 유형](../studio/import-data.md#supported-data-formats-and-data-types) 섹션을 참조하세요.
* 분석 과정에 사용되는 다른 Azure 서비스의 제한 사항에 대한 정보는 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조하세요.

## <a name="data-quality-questions-exploration-and-pre-processing"></a>데이터 품질 질문: 탐색 및 전처리

### <a name="what-do-you-know-about-your-data"></a>데이터에 대해 무엇을 알고 있나요?

다음과 같은 데이터의 기본 특성을 이해하세요.

* 나타내는 패턴 또는 추세
* 포함하는 이상값
* 누락된 값 수

이 단계는 다음에 유용합니다.

* 필요한 전처리 작업의 양 결정
* 가장 적합한 기능 또는 분석 유형을 제안하는 가설 작성
* 추가 데이터 수집에 대한 계획 수립

유용한 데이터 조사 기술에는 기술 통계 계산 및 시각화 표현이 포함됩니다. 다양한 Azure 환경에서 데이터 세트를 탐색하는 방법에 대한 자세한 내용은 [팀 데이터 과학 프로세스에서 데이터 탐색](explore-data.md)을 참조하세요.

### <a name="does-the-data-require-preprocessing-or-cleaning"></a>데이터에 전처리 또는 정리가 필요한가요?

기계 학습을 위해 데이터 세트를 효과적으로 사용하려면 먼저 데이터를 전처리하고 정리해야 할 수 있습니다. 원시 데이터는 노이즈가 많고, 불안정한 경우가 많습니다. 값이 누락될 수도 있습니다. 이러한 데이터를 모델링에 사용하면 결과가 잘못될 수 있습니다. 관련 설명은 [향상된 기계 학습에 대한 데이터를 준비하는 작업](prepare-data.md)을 참조하세요.

## <a name="tools-and-languages-questions"></a>도구 및 언어 질문

언어, 개발 환경 및 도구에 대한 여러 옵션이 있습니다. 요구 사항 및 기본 설정을 잘 알고 있어야 합니다.

### <a name="what-languages-do-you-prefer-to-use-for-analysis"></a>분석에 사용하려는 언어는 무엇인가요?

* R
* Python
* SQL

### <a name="what-tools-should-you-use-for-data-analysis"></a>데이터 분석에 사용해야 하는 도구는 무엇인가요?

* [Microsoft Azure Powershell](/powershell/azure/overview) – 스크립트 언어로 Azure 리소스를 관리하는 데 사용되는 스크립트 언어입니다.
* [Azure Machine Learning Studio](../studio/what-is-ml-studio.md)
* [Revolution Analytics](https://www.microsoft.com/sql-server/machinelearningserver)
* [RStudio](https://www.rstudio.com)
* [Python Tools for Visual Studio](https://aka.ms/ptvsdocs)
* [Anaconda](https://www.continuum.io/why-anaconda)
* [Jupyter 노트북](https://jupyter.org/)
* [Microsoft Power BI](https://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>고급 분석 시나리오 파악

이전 섹션의 질문에 대답하면 어떤 시나리오가 가장 적합한지 결정할 수 있습니다. 샘플 시나리오는 [Azure Machine Learning의 고급 분석 시나리오](plan-sample-scenarios.md)에 약술되어 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [TDSP(팀 데이터 과학 프로세스)란 무엇인가요?](overview.md)
