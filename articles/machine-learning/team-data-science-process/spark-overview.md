---
title: Azure HDInsight의 Spark를 사용하는 데이터 과학 - Team Data Science Process
description: Spark MLlib 도구 키트를 통해 많은 기계 학습 모델링 기능을 이 분산 HDInsight 환경에서 사용할 수 있습니다.
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
ms.openlocfilehash: 63148b99e65a5ccc49d54d4ae6c58adebc72c6d3
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718508"
---
# <a name="overview-of-data-science-using-spark-on-azure-hdinsight"></a>Azure HDInsight에서 Spark를 사용하는 데이터 과학 개요

이 항목 모음에서는 HDInsight Spark를 사용하여 데이터 수집, 기능 엔지니어링, 모델링 및 모델 평가와 같은 일반적인 데이터 과학 작업을 완료하는 방법을 보여 줍니다. 사용되는 데이터는 2013 NYC Taxi Trip 및 요금 데이터 세트의 샘플입니다. 작성된 모델은 로지스틱 및 선형 회귀, 임의 포리스트 및 그라데이션 향상된 트리를 포함합니다. 또한 이 항목은 이러한 모델을 Azure Blob Storage(WASB)에 저장하고 예측 성능의 점수를 매기며 평가하는 방법도 보여 줍니다. 고급 항목에서는 교차 유효성 검사 및 하이퍼 매개 변수 스위핑을 사용하여 모델을 학습할 수 있는 방법을 다룹니다. 이 개요 토픽은 제공된 연습의 단계를 완료하는 데 필요한 Spark 클러스터를 설정하는 방법을 설명하는 토픽도 참조합니다.

## <a name="spark-and-mllib"></a>Spark 및 MLlib
[Spark](https://spark.apache.org/)는 메모리 내 처리를 지원하여 빅데이터 분석 애플리케이션의 성능을 향상하는 오픈 소스 병렬 처리 프레임워크입니다. 속도, 간편한 사용 및 정교한 분석을 위해 Spark 처리 엔진이 빌드되었습니다. Spark는 메모리 내 분산형 계산 기능을 지원하여 기계 학습 및 그래프 계산에 사용된 반복 알고리즘에 적합합니다. [MLlib](https://spark.apache.org/mllib/)는 Spark의 확장형 기계 학습 라이브러리로, 분산형 환경에서 알고리즘 모델링 기능을 사용할 수 있습니다.

## <a name="hdinsight-spark"></a>HDInsight Spark
[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md)는 Azure에서 호스트하는 오픈 소스 Spark의 제품입니다. 또한 Azure Blob(WASB)에 저장된 데이터를 변환, 필터링 및 시각화하기 위해 Spark SQL 대화형 쿼리를 실행할 수 있는 Spark 클러스터상의 **Jupyter PySpark Notebook**에 대한 지원도 포함하고 있습니다. PySpark는 Spark용 Python API입니다. 솔루션을 제공하고 데이터 시각화를 위해 관련 플롯을 여기에 보여 주는 코드 조각은 Spark 클러스터에 설치된 Jupyter Notebook에서 실행됩니다. 이러한 항목의 모델링 단계는 각 모델 유형을 학습, 평가, 저장 및 사용하는 방법을 보여 주는 코드를 포함하고 있습니다.

## <a name="setup-spark-clusters-and-jupyter-notebooks"></a>설정: Spark 클러스터 및 Jupyter Notebook
설치 단계와 코드는 HDInsight Spark 1.6을 사용하는 이 연습에 제공됩니다. 하지만 Jupyter Notebook은 HDInsight Spark 1.6과 Spark 2.0 클러스터 둘 다에 제공됩니다. 노트북과 이에 연결된 링크의 설명은 이들을 포함하는 GitHub 리포지토리의 [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md)에 제공됩니다. 그뿐 아니라 여기에 있는 코드와 연결된 Notebook에 있는 코드는 일반적이므로 아무 Spark 클러스터에서나 작동할 것입니다. HDInsight Spark를 사용하지 않는 경우 클러스터 설치 및 관리 단계가 여기에 나오는 내용과 약간 다를 수 있습니다. 편의를 위해, Jupyter Notebook 서버의 pySpark 커널에서 실행되는 Spark 1.6 및 Jupyter Notebook 서버의 pySpark3 커널에서 실행되는 Spark 2.0용 Jupyter Notebook에 연결된 링크는 다음과 같습니다.

### <a name="spark-16-notebooks"></a>Spark 1.6 Notebook
이러한 Notebook은 Jupyter Notebook 서버의 pySpark 커널에서 실행됩니다.

- [pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): 몇 가지 알고리즘으로 데이터 탐색, 모델링, 그리고 점수 매기기 등을 수행하는 방법에 대한 정보를 제공합니다.
- [pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): 노트북 #1의 토픽과 하이퍼 매개 변수 조정 및 교차 유효성 검사를 사용하는 모델 개발을 포함합니다.
- [pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb): HDInsight 클러스터에서 Python을 사용하여 저장된 모델을 운용하는 방법을 보여줍니다.

### <a name="spark-20-notebooks"></a>Spark 2.0 노트북
이러한 Notebook은 Jupyter Notebook 서버의 pySpark3 커널에서 실행됩니다.

- [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): 이 파일은 NYC Taxi Trip 및 [여기](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data) 설명된 데이터 집합을 사용하여 Spark 2.0 클러스터에서 데이터 탐색, 모델링, 점수 매기기를 수행하는 방법에 대한 정보를 제공합니다. 이 Notebook은 Spark 2.0에 대해 제공했던 코드를 신속하게 탐색하기 위한 좋은 시작점일 수 있습니다. NYC Taxi 데이터를 분석하는 Notebook 상세 정보는 이 목록에서 다음 Notebook을 참조하세요. 请参阅此列表中对这些笔记本进行比较的注释。
- [Spark2.0 pySpark3_NYC_Taxi_Tip_Regression.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): 이 파일은 [여기](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data)에 설명된 NYC 택시 여정 및 요금 데이터 집합을 사용한 데이터 랭글링(Spark SQL 및 데이터 프레임 작업), 탐색, 모델링 및 점수 매기기를 수행하는 방법을 보여줍니다.
- [Spark2.0-pySpark3_Airline_Departure_Delay_Classification.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): 이 파일은 2011년 및 2012년의 유명 항공사 정시 출발 데이터 세트를 사용한 데이터 랭글링(Spark SQL 및 데이터 프레임 작업), 탐색, 모델링 및 점수 매기기를 수행하는 방법을 보여줍니다. 在建模之前，我们将航空公司数据集与机场天气数据（例如，windspeed、温度、海拔等）集成，因此这些天气功能可以包含在模型中。

<!-- -->

> [!NOTE]
> 항공사 데이터 세트는 분류 알고리즘의 사용 이해를 돕기 위해 Spark 2.0 Notebook에 추가되었습니다. 항공사 정시 출발 데이터 세트 및 날씨 데이터 세트에 대한 내용은 다음 링크를 참조하세요.
> 
> - 항공사 정시 출발 데이터: [https://www.transtats.bts.gov/ONTIME/](https://www.transtats.bts.gov/ONTIME/)
> 
> - 공항 날씨 데이터: [https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/)

<!-- -->

<!-- -->

> [!NOTE]
> NYC taxi의 Spark 2.0 Notebook 및 항공사 비행 지연 데이터 집합은 실행하는 데 10분 이상이 소요될 수 있습니다(HDI 클러스터의 크기에 따라 다름). 上述列表中的第一个笔记本显示了笔记本中数据浏览、可视化和 ML 模型定型的许多方面，使用下采样的 NYC 数据集（在该数据集内预联接了出租车和费用文件）的运行时间更少： [spark 2.0-pySpark3--ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb)。 이 노트는 훨씬 더 빠르게 완료할 수 있으며(2-3분) Spark 2.0에 대해 제공했던 코드를 신속하게 탐색하기 위한 좋은 시작점일 수 있습니다.

<!-- -->

점수 매기기를 위한 Spark 2.0 모델 및 모델 사용량의 운용에 대한 지침은 [사용량에 관한 Spark 1.6 문서](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb)에서 해당 단계를 설명하는 예제를 참조하세요. 若要在 Spark 2.0 上使用此示例，请将 Python 代码文件替换为[此文件](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py)。

### <a name="prerequisites"></a>필수 조건

다음 절차는 Spark 1.6에 대한 내용입니다. Spark 2.0 버전의 경우 이전에 설명 및 링크된 Notebook을 사용합니다.

1. Azure 구독이 있어야 합니다. 아직 가지고 있지 않은 경우 [Azure 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

2. 이 연습을 완료하는 데는 Spark 1.6 클러스터가 필요합니다. 만드는 방법은 [시작: Azure HDInsight에서 Apache Spark 만들기](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)를 참조하세요. 클러스터 유형 및 버전은 **클러스터 유형 선택** 메뉴에서 지정됩니다.

![클러스터 구성](./media/spark-overview/spark-cluster-on-portal.png)

<!-- -->

> [!NOTE]
> Python이 아닌 Scala를 사용하여 엔드투엔드 데이터 과학 프로세스에 대한 작업을 완료하는 방법을 보여 주는 항목에 대해서는 [Azure에서 Spark를 사용하는 데이터 과학](scala-walkthrough.md)을 참조하세요.
>
>

<!-- -->

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
>
>

## <a name="the-nyc-2013-taxi-data"></a>NYC 2013 Taxi 데이터
NYC Taxi Trip 데이터는 1억 7,300만 개가 넘는 개별 여정 및 각 여정의 요금으로 구성된 약 20GB의 압축된 CSV(쉼표로 구분된 값) 파일(압축되지 않은 경우 약 48GB)입니다. 각 여정 레코드는 승차 및 하차 위치, 익명 처리된 hack(기사) 면허증 번호 및 medallion(택시의 고유 ID) 번호를 포함합니다. 데이터는 2013년의 모든 여정을 포괄하며, 매월 다음 두 개의 데이터 세트로 제공됩니다.

1. 'trip_data' CSV 파일은 승객 수, 승차 및 하차 지점, 여정 기간, 여정 거리 등 여정 세부 정보를 포함합니다. 다음은 몇 가지 샘플 레코드입니다.

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. 'trip_fare' CSV 파일은 지불 유형, 금액, 추가 요금 및 세금, 팁 및 통행료, 총 지불 금액 등 각 여정의 요금에 대한 세부 정보를 포함합니다. 다음은 몇 가지 샘플 레코드입니다.

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

이러한 파일 중 0.1% 샘플을 선택하고 trip\_data 및 trip\_fare CVS 파일을 이 연습의 입력 데이터 세트로 사용할 단일 데이터 세트에 조인했습니다. trip\_data와 trip\_fare를 조인할 고유 키는 medallion, hack\_licence 및 pickup\_datetime 필드로 구성됩니다. 데이터 세트의 각 레코드는 NYC Taxi 여정을 나타내는 다음과 같은 특성을 포함하고 있습니다.

| 필드 | 간략한 설명 |
| --- | --- |
| medallion |익명 처리된 택시 medallion(고유 택시 id) |
| hack_license |익명 처리된 Hackney 운전 면허 번호 |
| vendor_id |택시 공급 업체 id |
| rate_code |NYC 택시 요율 |
| store_and_fwd_flag |저장소 및 전달 플래그 |
| pickup_datetime |승차 날짜 및 시간 |
| dropoff_datetime |내린 날짜 및 시간 |
| pickup_hour |승차 시간 |
| pickup_week |연간 누적 승차 주 수 |
| weekday |요일(범위 1-7) |
| passenger_count |택시 여정의 승객 수 |
| trip_time_in_secs |여정 시간(초) |
| trip_distance |주행한 여정 거리(마일 단위) |
| pickup_longitude |승차 경도 |
| pickup_latitude |승차 위도 |
| dropoff_longitude |내린 경도 |
| dropoff_latitude |내린 위도 |
| direct_distance |태운 위치와 내린 위치 사이의 직접 거리 |
| payment_type |付款类型（现金、信用卡等） |
| fare_amount |요금 금액 |
| surcharge |추가 요금 |
| mta_tax |MTA 地铁运输税 |
| tip_amount |팁 금액 |
| tolls_amount |통행료 금액 |
| total_amount |총 금액 |
| tipped |팁 지불 여부(아니요 또는 예에 대해 0/1 지정) |
| tip_class |팁 클래스(0: $0, 1: $0-5, 2: $6-10, 3: $11-20, 4: > $20) |

## <a name="execute-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Spark 클러스터의 Jupyter Notebook에서 코드 실행
Azure 포털에서 Jupyter Notebook을 시작할 수 있습니다. 대시보드에서 Spark 클러스터를 찾아 클릭하여 클러스터에 대한 관리 페이지로 들어갑니다. 若要打开与 Spark 群集关联的笔记本，请单击 "**群集仪表板**" -> **Jupyter Notebook**"。

![클러스터 대시보드](./media/spark-overview/spark-jupyter-on-portal.png)

***https://CLUSTERNAME.azurehdinsight.net/jupyter***로 이동하여 Jupyter Notebook에 액세스할 수 있습니다. 이 URL의 CLUSTERNAME 부분을 사용자 고유의 클러스터 이름으로 바꿉니다. Notebook에 액세스하려면 관리자 계정에 대한 암호가 필요합니다.

![Jupyter 노트북 찾아보기](./media/spark-overview/spark-jupyter-notebook.png)

选择 PySpark 可以查看包含使用 PySpark API 的预打包笔记本示例的目录。 [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark)中提供了包含此 Spark 主题的代码示例的笔记本

노트북을 [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark)에서 Spark 클러스터의 Jupyter Notebook 서버에 직접 업로드할 수 있습니다. Jupyter의 홈 페이지에서 화면 오른쪽의 **업로드** 버튼을 클릭합니다. 파일 탐색기가 열립니다. 여기서 Notebook의 GitHub(원시 콘텐츠) URL을 붙여넣고 **열기**를 클릭할 수 있습니다.

**업로드** 버튼을 다시 사용하면 Jupyter 파일 목록에서 파일 이름을 확인할 수 있습니다. 이 **업로드** 버튼을 클릭합니다. 이제 Notebook을 가져왔습니다. 이 단계를 반복하여 이 연습에서 다른 Notebook을 업로드합니다.

> [!TIP]
> 브라우저의 링크를 마우스 오른쪽 단추로 클릭하고 **링크 복사**를 선택하여 GitHub 원시 콘텐츠 URL을 가져올 수 있습니다. Jupyter 업로드 파일 탐색기 대화 상자에 이 URL을 붙여넣을 수 있습니다.
> 
> 

이제 다음을 수행할 수 있습니다.

* Notebook을 클릭하여 코드를 확인합니다.
* **Shift+Enter**를 눌러 각 셀을 실행합니다.
* **셀** -> **실행**을 클릭하여 전체 Notebook을 실행합니다.
* 쿼리의 자동 시각화를 사용합니다.

> [!TIP]
> PySpark 커널은 SQL(HiveQL) 쿼리의 출력을 자동으로 시각화합니다. Notebook의 **형식** 메뉴 버튼을 사용하여 다양한 시각화 형식(테이블, 원형, 꺾은선형, 영역 또는 막대) 중에서 선택할 수 있는 옵션이 제공됩니다.
>
>

![일반적인 접근 방식에 대한 로지스틱 회귀 분석 ROC 곡선](./media/spark-overview/pyspark-jupyter-autovisualization.png)

## <a name="whats-next"></a>다음은 무엇일까요?
이제 HDInsight Spark 클러스터를 설치하고 Jupyter Notebook을 업로드했으므로 이 세 PySpark Notebook에 해당하는 항목을 진행할 준비가 되었습니다. 이들 항목은 데이터 탐색 방법을 보여 준 후 모델을 만들고 사용하는 방법을 보여 줍니다. 고급 데이터 탐색 및 모델링 Notebook은 교차 유효성 검사, 하이퍼 매개 변수 비우기 및 모델 평가를 포함하는 방법을 보여 줍니다.

**Spark로 데이터 탐색 및 모델링:** [Spark MLlib 도구 키트를 사용하여 데이터에 대한 이진 분류 및 회귀 모델 만들기](spark-data-exploration-modeling.md) 항목을 수행하여 데이터 세트를 탐색하고 기계 학습 모델 만들기, 점수 매기기 및 평가를 수행합니다.

**모델 사용:** 이 항목에서 만든 분류 및 회귀 모델의 점수를 매기는 방법을 알아보려면 [Spark로 빌드된 기계 학습 모델 점수 매기기 및 평가](spark-model-consumption.md)를 참조하세요.

**교차 유효성 검사 및 하이퍼 매개 변수 비우기**: 교차 유효성 검사 및 하이퍼 매개 변수 비우기를 사용하여 모델을 학습하는 방법은 [Spark로 고급 데이터 탐색 및 모델링](spark-advanced-data-exploration-modeling.md) 을 참조하세요.

