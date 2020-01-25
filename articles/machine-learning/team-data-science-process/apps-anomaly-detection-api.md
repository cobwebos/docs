---
title: Azure Machine Learning 이상 감지 API - Team Data Science Process
description: 이상 감지 API는 Microsoft Azure Machine Learning을 사용하여 빌드한 예로서, 시간 간격이 불균일한 숫자 값이 있는 시계열 데이터에서 이상을 감지합니다.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=alokkirpal, previous-ms.author=alok
ms.openlocfilehash: a09094cf0d1bd3c2e299e968d7de8410dcd9c3cb
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721874"
---
# <a name="machine-learning-anomaly-detection-api"></a>Machine Learning 이상 감지 API

> [!NOTE]
> 此项处于维护下。 我们鼓励你使用由 Azure 认知服务下的机器学习算法库提供支持的[异常探测器 API 服务](https://azure.microsoft.com/services/cognitive-services/anomaly-detector/)，以检测业务、操作和 IoT 指标中的异常。

## <a name="overview"></a>개요
[이상 감지 API](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2)는 Azure Machine Learning을 사용하여 빌드한 예로서, 시간 간격이 불균일한 숫자 값이 있는 시계열 데이터에서 이상을 감지합니다.

이 API는 시계열 데이터에서 다음과 같은 유형의 이상 패턴을 감지할 수 있습니다.

* **긍정적인 추세 및 부정적인 추세**: 예를 들어, 계산에서 메모리 사용량을 모니터링 할 때, 상향 추세는 메모리 누수를 나타낼 수 있기 때문에 유용할 수 있습니다.
* **값의 동적 범위 변화**: 예를 들어 클라우드 서비스에서 발생하는 예외를 모니터링할 때 값의 동적 범위 변화는 서비스 상태의 불안정성을 나타낼 수 있습니다.
* **급증 및 급락**: 예를 들어 서비스 로그인 실패 횟수 또는 전자 상거래 사이트의 체크 아웃 수를 모니터링할 때 급증 또는 급락은 이상 동작을 나타낼 수 있습니다.

이러한 기계 학습 감지기는 시간에 따른 값의 변화를 추적하여 해당 값의 계속적인 변화를 이상 점수로 보고합니다. 임시 임계값을 조정할 필요 없이 해당 점수를 사용하여 가양성 비율을 제어할 수 있습니다. 이상 감지 API는 시간에 따른 KPI 추적을 통한 서비스 모니터링, 검색 횟수, 클릭 횟수 등의 메트릭을 통한 사용 현황 모니터링, 시간에 따른 메모리, CPU, 파일 읽기 등과 같은 카운터를 통한 성능 모니터링 등의 몇 가지 시나리오에 유용합니다.

이상 감지 제품에는 시작하기에 유용한 도구가 함께 제공됩니다.

* [웹 애플리케이션](https://anomalydetection-aml.azurewebsites.net/)은 데이터에서 비상 감지 API의 결과를 평가하고 시각화하는 데 도움이 됩니다.

> [!NOTE]
> [이 API](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2)에서 제공되는 **IT Anomaly Insights 솔루션**을 사용해 봅니다.
>
<!-- This Solution is no longer available
> To get this end to end solution deployed to your Azure subscription <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank">**Start here >**</a>
-->

## <a name="api-deployment"></a>API 배포
API를 사용하려면 Azure Machine Learning 웹 서비스로 호스팅되는 Azure 구독에 API를 배포해야 합니다.  이 작업은 [Azure AI 갤러리](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2)에서 수행할 수 있습니다.  这会将两个 Azure 机器学习 Studio （经典） Web 服务（及其相关资源）部署到你的 Azure 订阅-一个用于季节性检测的异常情况检测，另一个不包含季节性检测。  部署完成后，你将能够从[Azure 机器学习 Studio （经典） web 服务](https://services.azureml.net/webservices/)页管理 api。  이 페이지에서 API를 호출하기 위한 샘플 코드와 엔드포인트 위치, API 키를 찾을 수 있습니다.  더 자세한 지침은 [여기](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice)에 있습니다.

## <a name="scaling-the-api"></a>API 크기 조정
默认情况下，部署将有一个免费的开发/测试计费计划，其中包括1000个交易/月和2个计算小时/月。  필요에 따라 다른 계획으로 업그레이드할 수 있습니다.  다른 계획의 가격 책정에 대한 자세한 내용은 [여기](https://azure.microsoft.com/pricing/details/machine-learning/)의 "프로덕션 웹 API 가격"에 있습니다.

## <a name="managing-aml-plans"></a>AML 관리 계획
[여기](https://services.azureml.net/plans/)서 청구 계획을 관리할 수 있습니다.  계획 이름은 API를 배포할 때 선택한 리소스 그룹 이름과 구독에 고유한 문자열을 기반으로 합니다.  계획을 업그레이드하는 방법에 대한 지침은 [여기](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice)의 "청구 계획 관리" 섹션에 있습니다.

## <a name="api-definition"></a>API 정의
Web 服务提供 HTTPS 上基于 REST 的 API，可采用不同的方式使用该 API，包括 web 或移动应用程序、R、Python、Excel 等。 可以通过 REST API 调用将时序数据发送到此服务，它会运行下面所述的三种异常类型的组合。

## <a name="calling-the-api"></a>API 호출
API를 호출하려면 엔드포인트 위치 및 API 키를 알고 있어야 합니다.  这两个要求以及用于调用 API 的示例代码可从[Azure 机器学习 Studio （经典） web 服务](https://services.azureml.net/webservices/)页获取。  원하는 API로 이동한 다음 "사용" 탭을 클릭하여 찾습니다.  可以将 API 调用为 Swagger API （即，使用 URL 参数 `format=swagger`），也可以将其作为非 Swagger API （即没有 `format` URL 参数）调用。  샘플 코드에서는 Swagger 형식을 사용합니다.  아래는 Swagger가 아닌 형식의 예제 요청 및 응답입니다.  이러한 예제는 계절성 엔드포인트에 관한 것이며,  비계절성 엔드포인트도 비슷합니다.

### <a name="sample-request-body"></a>샘플 요청 본문
요청에는 두 개의 개체, 즉 `Inputs`과 `GlobalParameters`가 있습니다.  아래 예제 요청에서는 일부 매개 변수가 명시적으로 전송되는 반면 다른 매개 변수는 전송되지 않습니다(각 엔드포인트의 전체 매개 변수 목록을 보려면 아래로 스크롤함).  요청에서 명시적으로 전송되지 않은 매개 변수는 아래에 지정된 기본값을 사용합니다.

    {
                "Inputs": {
                        "input1": {
                                "ColumnNames": ["Time", "Data"],
                                "Values": [
                                        ["5/30/2010 18:07:00", "1"],
                                        ["5/30/2010 18:08:00", "1.4"],
                                        ["5/30/2010 18:09:00", "1.1"]
                                ]
                        }
                },
        "GlobalParameters": {
            "tspikedetector.sensitivity": "3",
            "zspikedetector.sensitivity": "3",
            "bileveldetector.sensitivity": "3.25",
            "detectors.spikesdips": "Both"
        }
    }

### <a name="sample-response"></a>샘플 응답
若要查看 `ColumnNames` 字段，必须在请求中包含 `details=true` 作为 URL 参数。  이러한 필드 각각의 의미에 대해서는 아래 표를 참조하세요.

    {
        "Results": {
            "output1": {
                "type": "table",
                "value": {
                    "Values": [
                        ["5/30/2010 6:07:00 PM", "1", "1", "0", "0", "-0.687952590518378", "0", "-0.687952590518378", "0", "-0.687952590518378", "0"],
                        ["5/30/2010 6:08:00 PM", "1.4", "1.4", "0", "0", "-1.07030497733224", "0", "-0.884548154298423", "0", "-1.07030497733224", "0"],
                        ["5/30/2010 6:09:00 PM", "1.1", "1.1", "0", "0", "-1.30229513613974", "0", "-1.173800281031", "0", "-1.30229513613974", "0"]
                    ],
                    "ColumnNames": ["Time", "OriginalData", "ProcessedData", "TSpike", "ZSpike", "BiLevelChangeScore", "BiLevelChangeAlert", "PosTrendScore", "PosTrendAlert", "NegTrendScore", "NegTrendAlert"],
                    "ColumnTypes": ["DateTime", "Double", "Double", "Double", "Double", "Double", "Int32", "Double", "Int32", "Double", "Int32"]
                }
            }
        }
    }


## <a name="score-api"></a>Score API
Score API는 비계절성 시계열 데이터에 이상 감지를 실행하는 데 사용됩니다. API는 데이터에 다수의 비정상 감지기를 실행하고 그에 해당하는 비정상 점수를 반환합니다.
아래 그림은 Score API에서 감지할 수 있는 이상의 예를 보여줍니다. 此时序有两个不同的级别更改，三个峰值。 빨간색 점은 수준 변화가 감지된 시간을 표시하며, 검은색 점은 감지된 급증을 표시합니다.
![점수 매기기 API][1]

### <a name="detectors"></a>감지기
异常情况检测 API 支持三个广义类别的检测程序。 각 감지기의 구체적인 입력 매개 변수 및 출력에 대한 자세한 내용은 다음 테이블에서 찾을 수 있습니다.

| 감지기 범주 | 감지기 | Description | 입력 매개 변수 | outputs |
| --- | --- | --- | --- | --- |
| 급증 감지기 |TSpike 감지기 |값이 첫 번째와 세 번째 사분위 수에서 얼마나 떨어져 있는지를 기반으로 급증과 급락을 감지합니다. |*tspikedetector.sensitivity:* 1-10 범위의 정수 값을 갖습니다. 기본값: 3. 값이 높을수록 더 극단값을 포착하므로 민감도는 낮습니다. |TSpike: 이진 값 – 급증/급락이 감지되면 ‘1’, 그 외 경우는 ‘0’ |
| 급증 감지기 | ZSpike 감지기 |데이터 요소가 평균값에서 얼마나 떨어져 있는지를 기반으로 급증 및 급락을 감지합니다. |*zspikedetector.sensitivity:* 1~10 범위의 정수 값을 갖습니다. 기본값: 3. 값이 높을수록 더 극단값을 포착하므로 민감도는 낮습니다. |ZSpike: 이진 값 – 급증/급락이 감지되면 ‘1’, 그 외 경우는 ‘0’ |
| 느린 추세 감지기 |느린 추세 감지기 |집합 민감도에 따른 느리고 긍정적인 추세 감지 |*trenddetector：* 探测器分数上的阈值（默认值：3.25，3.25 –5是要从中选择的合理范围;敏感程度较低的 |tscore: 추세에 대한 이상 점수를 나타내는 부동 소수점 숫자 |
| 수준 변화 감지기 | 양방향 수준 변화 감지기 |집합 민감도에 따른 상향 및 하향 수준 변경 감지 |*bileveldetector：* 探测器分数上的阈值（默认值：3.25，3.25 –5是要从中选择的合理范围;敏感程度较低的 |rpscore: 상향 및 하향 수준 변화에 대한 이상 점수를 나타내는 부동 소수점 숫자 |

### <a name="parameters"></a>매개 변수
이러한 입력 매개 변수에 대한 자세한 정보는 아래 테이블에 나와 있습니다.

| 입력 매개 변수 | Description | 기본 설정 | 유형 | 유효 범위 | 제안 범위 |
| --- | --- | --- | --- | --- | --- |
| detectors.historywindow |이상 점수 계산에 사용된 내역(데이터 요소 수) |500 |integer |10-2000 |시계열에 종속 |
| detectors.spikesdips | 급증만, 급락만 또는 둘 다 감지할지 여부 |둘 다 |열거형 |Both, Spikes, Dips |둘 다 |
| bileveldetector.sensitivity |양방향 수준 변화 감지기에 대한 민감도. |3.25 |double |없음 |3.25-5(값이 낮을수록 높은 민감도) |
| trenddetector.sensitivity |긍정적인 추세 감지에 대한 민감도. |3.25 |double |없음 |3.25-5(값이 낮을수록 높은 민감도) |
| tspikedetector.sensitivity |TSpike 감지기의 민감도 |3 |integer |1-10 |3-5(값이 낮을수록 높은 민감도) |
| zspikedetector.sensitivity |ZSpike 감지기의 민감도 |3 |integer |1-10 |3-5(값이 낮을수록 높은 민감도) |
| postprocess.tailRows |출력 결과에 유지할 최신 데이터 요소의 수 |0 |integer |0(모든 데이터 요소 유지), 또는 결과에 유지할 데이터 요소의 수 지정 |N/A |

### <a name="output"></a>출력
API는 시계열 데이터에서 모든 감지기를 실행하고 각 시점에 대한 이진 급증 표시기와 이상 점수를 반환합니다. 아래 테이블은 API의 결과 목록입니다.

| outputs | Description |
| --- | --- |
| 시간 |원시 데이터의 타임스탬프 또는 집계 (및/또는) 누락 데이터 대체가 적용되는 경우 집계 (및/또는) 대체된 데이터 |
| 데이터 |원시 데이터의 값 또는 집계 (및/또는) 누락 데이터 대체가 적용되는 경우 집계 (및/또는) 대체된 데이터 |
| Tspike |TSpike 감지기의 급증 감지 여부를 나타내는 이진 표시기 |
| ZSpike |ZSpike 감지기의 급증 감지 여부를 나타내는 이진 표시기 |
| rpscore |양방향 수준 변화에 대한 이상 점수를 나타내는 부동 소수점 숫자 |
| rpalert |입력 민감도를 기반으로 양방향 수준 변화 이상이 있는지를 나타내는 1/0 값 |
| tscore |긍정적인 추세에 대한 이상 점수를 나타내는 부동 소수점 숫자 |
| talert |입력 민감도를 기반으로 긍정적인 추세 이상이 있는지를 나타내는 1/0 값 |

## <a name="scorewithseasonality-api"></a>ScoreWithSeasonality API
ScoreWithSeasonality API는 계절성 패턴이 있는 시계열에 대한 이상 감지를 실행하는 데 사용됩니다. 이 API는 계절성 패턴의 편차를 감지하는 데 유용합니다.
다음 그림은 계절성 시계열에서 감지된 이상의 예를 보여줍니다. 时序有一个峰值（第一个黑点）、两个 dip （第二个黑点和一端的点）和一个级别更改（红点）。 在从序列中移除季节性组件后，时间序列中间的 dip 和级别更改只是明显。
![계절성 API][2]

### <a name="detectors"></a>감지기
계절성 엔드포인트의 감지기는 비계절성 엔드포인트의 감지기와 비슷하지만 매개 변수 이름이 약간 다릅니다(아래 참조).

### <a name="parameters"></a>매개 변수

이러한 입력 매개 변수에 대한 자세한 정보는 아래 테이블에 나와 있습니다.

| 입력 매개 변수 | Description | 기본 설정 | 유형 | 유효 범위 | 제안 범위 |
| --- | --- | --- | --- | --- | --- |
| preprocess.aggregationInterval |입력 시계열 집계에 대한 초 단위 집계 간격 |0(집계가 수행되지 않음) |integer |0: 집계 건너뜀, > 0 기타 |5분에서 1일, 시계열 종속 |
| preprocess.aggregationFunc |지정된 AggregationInterval로 데이터를 집계하는 데 사용되는 함수 |평균 |열거형 |평균, 합계, 길이 |N/A |
| preprocess.replaceMissing |누락된 데이터를 입력하는 데 사용되는 값 |lkv(마지막으로 알려진 값) |열거형 |0, lkv, 평균 |N/A |
| detectors.historywindow |이상 점수 계산에 사용된 내역(데이터 요소 수) |500 |integer |10-2000 |시계열에 종속 |
| detectors.spikesdips | 급증만, 급락만 또는 둘 다 감지할지 여부 |둘 다 |열거형 |Both, Spikes, Dips |둘 다 |
| bileveldetector.sensitivity |양방향 수준 변화 감지기에 대한 민감도. |3.25 |double |없음 |3.25-5(값이 낮을수록 높은 민감도) |
| postrenddetector.sensitivity |긍정적인 추세 감지에 대한 민감도. |3.25 |double |없음 |3.25-5(값이 낮을수록 높은 민감도) |
| negtrenddetector.sensitivity |부정적인 추세 감지기에 대한 민감도 |3.25 |double |없음 |3.25-5(값이 낮을수록 높은 민감도) |
| tspikedetector.sensitivity |TSpike 감지기의 민감도 |3 |integer |1-10 |3-5(값이 낮을수록 높은 민감도) |
| zspikedetector.sensitivity |ZSpike 감지기의 민감도 |3 |integer |1-10 |3-5(값이 낮을수록 높은 민감도) |
| seasonality.enable |계절성 분석이 수행될지 여부 |true |boolean |true, false |시계열에 종속 |
| seasonality.numSeasonality |감지할 정기적 주기의 최대 수 |1 |integer |1, 2 |1-2 |
| seasonality.transform |이상 감지를 적용하기 전에 계절성 (및) 추세 구성 요소가 제거될지 여부 |deseason |열거형 |none, deseason, deseasontrend |N/A |
| postprocess.tailRows |출력 결과에 유지할 최신 데이터 요소의 수 |0 |integer |0(모든 데이터 요소 유지), 또는 결과에 유지할 데이터 요소의 수 지정 |N/A |

### <a name="output"></a>출력
API는 시계열 데이터에서 모든 감지기를 실행하고 각 시점에 대한 이진 급증 표시기와 이상 점수를 반환합니다. 아래 테이블은 API의 결과 목록입니다.

| outputs | Description |
| --- | --- |
| 시간 |원시 데이터의 타임스탬프 또는 집계 (및/또는) 누락 데이터 대체가 적용되는 경우 집계 (및/또는) 대체된 데이터 |
| OriginalData |원시 데이터의 값 또는 집계 (및/또는) 누락 데이터 대체가 적용되는 경우 집계 (및/또는) 대체된 데이터 |
| ProcessedData |以下选项之一： <ul><li>명확한 계절성이 감지되고 deseason 옵션이 선택된 경우 계절에 따라 조정된 시계열</li><li>명확한 계절성이 감지되고 deseasontrend 옵션이 선택된 경우 계절에 따라 조정되고 추세가 제거된 시계열</li><li>否则，此选项与 OriginalData 相同。</li> |
| Tspike |TSpike 감지기의 급증 감지 여부를 나타내는 이진 표시기 |
| ZSpike |ZSpike 감지기의 급증 감지 여부를 나타내는 이진 표시기 |
| BiLevelChangeScore |수준 변화에 대한 이상 점수를 나타내는 부동 소수점 숫자 |
| BiLevelChangeAlert |입력 민감도에 기반하여 수준 변화 이상이 있는지 나타내는 1/0 값 |
| PosTrendScore |긍정적인 추세에 대한 이상 점수를 나타내는 부동 소수점 숫자 |
| PosTrendAlert |입력 민감도를 기반으로 긍정적인 추세 이상이 있는지를 나타내는 1/0 값 |
| NegTrendScore |부정적인 추세에 대한 이상 점수를 나타내는 부동 소수점 숫자 |
| NegTrendAlert |입력 민감도에 기반하여 부정적인 추세 이상이 있는지 나타내는 1/0 값 |

[1]: ./media/apps-anomaly-detection-api/anomaly-detection-score.png
[2]: ./media/apps-anomaly-detection-api/anomaly-detection-seasonal.png

