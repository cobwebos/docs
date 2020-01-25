---
title: Spark로 고급 데이터 탐색 및 모델링 - Team Data Science Process
description: HDInsight Spark를 사용하여 데이터 탐색 및 학습 이진 분류를 수행하며 교차 유효성 검사 및 하이퍼 매개 변수 최적화를 사용하는 회귀 모델링을 수행합니다.
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
ms.openlocfilehash: 15d9d186ef36ee9181a6ce0386aa9cc5de7838e3
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718644"
---
# <a name="advanced-data-exploration-and-modeling-with-spark"></a>고급 Spark로 데이터 탐색 및 모델링

이 연습에서는 HDInsight Spark를 사용하여 데이터 탐색 및 학습 이진 분류를 수행하며 NYC Taxi Trip 및 요금 2013 데이터 세트 샘플에서 교차 유효성 검사 및 하이퍼 매개 변수 최적화를 사용하는 회귀 모델링을 수행합니다. 처리를 위한 HDInsight Spark 클러스터와 데이터 및 모델을 저장하는 Azure Blob을 사용하여 [데이터 과학 프로세스](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)의 단계를 엔드투엔드 안내합니다. 프로세스는 Azure Storage Blob에서 가져온 데이터를 탐색하고 시각화한 다음 데이터를 준비하여 예측 모델을 빌드합니다. Python은 솔루션을 코딩하고 관련 차트를 표시하는 데 사용합니다. 이러한 모델은 Spark MLlib 도구 키트를 사용하여 이진 분류 및 회귀 모델링 작업을 수행합니다. 

* **이진 분류** 작업은 여정에 대해 팁이 지불되었는지 여부를 예측합니다. 
* **회귀** 작업은 다른 팁 기능을 기반으로 하는 팁의 금액을 예측합니다. 

또한 모델링 단계에는 각 모델 유형을 학습, 평가 및 저장하는 방법을 보여주는 코드가 포함되어 있습니다. 이 항목에는 [Spark로 데이터 탐색 및 모델링](spark-data-exploration-modeling.md) 항목과 동일한 기본적인 내용이 일부 포함되어 있습니다. 하지만 좀 더 "고급" 내용으로, 하이퍼 매개 변수 비우기와 교차 유효성 검사를 함께 사용하여 정확한 분류 및 회귀 모델을 최적으로 학습할 수 있습니다. 

**CV(교차 유효성 검사)** 는 알려진 데이터 세트에서 학습된 모델이 학습되지 않은 데이터 세트의 기능 예측을 얼마나 잘 일반화하는지 평가하는 기술입니다.  여기에 사용된 일반적인 구현은 데이터 세트를 K 접기로 나눈 다음 접기 중 하나를 제외한 모든 접기에서 라운드 로빈 방식으로 모델을 학습하는 것입니다. 이 접기에서 모델을 테스트하는 데 사용되지 않은 독립 데이터 세트에 대해 모델을 테스트할 때 모델이 정확히 예측하는 기능이 평가됩니다.

**하이퍼 매개 변수 최적화** 는 학습 알고리즘에 대한 하이퍼 매개 변수 집합을 선택하는 문제이며, 일반적으로 독립된 데이터 집합에서의 알고리즘 성능 측정값을 최적화하는 것을 목표로 합니다. **하이퍼 매개 변수** 는 모델 학습 절차 외부에서 지정해야 하는 값입니다. 이러한 값에 대한 가정은 모델의 유연성 및 정확도에 영향을 줄 수 있습니다. 의사 결정 트리에는 원하는 깊이와 트리의 리프 수와 같은 하이퍼 매개 변수가 있습니다. SVM(Support Vector Machine)은 오분류 페널티 조건을 설정해야 합니다. 

여기에 사용된 하이퍼 매개 변수 최적화를 수행하는 일반적인 방법은 그리드 검색 또는 **매개 변수 비우기**입니다. 此搜索将经历学习算法的超参数空间子集。 교차 유효성 검사는 그리드 검색 알고리즘에 의해 생성되는 최적의 결과를 분류하는 성능 메트릭을 제공할 수 있습니다. 하이퍼 매개 변수 비우기와 함께 사용된 CV를 통해 데이터 학습 모델의 과잉 맞춤과 같은 문제를 제한하여 모델이 학습 데이터가 추출되는 일반 데이터 집합에 적용할 용량을 유지하도록 합니다.

사용하는 모델은 로지스틱 및 선형 회귀, 임의 포리스트, 그라데이션 향상된 트리를 포함합니다.

* [SGD로 선형 회귀](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) 는 SGD(Stochastic Gradient Descent) 메서드를 사용하는 선형 회귀 모델이며 최적화 및 기능에 대해 크기를 조정하여 결재된 팁 금액을 예측합니다. 
* [LBFGS로 로지스틱 회귀 분석](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) 또는 "로짓" 회귀는 종속 변수가 데이터 분류를 수행하는 범주인 경우 사용할 수 있는 회귀 모델입니다. LBFGS는 제한된 양의 컴퓨터 메모리를 사용하여 BFGS(Broyden–Fletcher–Goldfarb–Shanno) 알고리즘을 비슷하게 만들고 기계 학습에서 널리 사용되는 준 뉴턴 최적화 알고리즘입니다.
* [임의 포리스트](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) 는 결정 트리의 결합체입니다.  즉, 과잉 맞춤의 위험을 줄이기 위해 많은 결정 트리를 결합합니다. 임의 포리스트는 회귀 및 분류에 사용되며 범주 기능을 처리하고 다중 클래스 분류 설정으로 확장할 수 있습니다. 기능 크기 조정을 필요로 하지 않으며 비선형 및 기능 상호 작용을 캡처할 수 있습니다. 임의 포리스트는 분류 및 회귀를 위해 매우 성공적인 기계 학습 모델 중 하나입니다.
* [梯度提升树](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts)（gbt）是决策树的整体。 GBT 以迭代方式定型决策树，以最大程度地降低损失函数。 GBT 用于回归和分类，可处理分类特征，不需要功能缩放，并且能够捕获非非线性和特征交互。 또한 다중 클래스 분류 설정에도 사용할 수 있습니다.

이진 분류 문제에 대한 CV 및 하이퍼 매개 변수 비우기를 사용하는 모델링 예제가 표시됩니다. 더 간단한 예제(매개 변수 비우기 없음)가 회귀 작업에 대한 주요 토픽으로 표시됩니다. 하지만 부록에서는 선형 회귀에 대해 탄력적 net을 사용한 유효성 검사와 임의 포리스트 회귀에 대해 사용한 매개 변수 비우기가 있는 CV도 표시됩니다. **탄력적 net**은 L1 및 L2 메트릭을 선형으로 결합하는 선형 회귀 모델을 [lasso](https://en.wikipedia.org/wiki/Lasso%20%28statistics%29) 및 [ridge](https://en.wikipedia.org/wiki/Tikhonov_regularization) 메서드의 페널티로 맞추기 위한 정칙 회귀 메서드입니다.   

<!-- -->

> [!NOTE]
> Spark MLlib 도구 키트가 큰 데이터 세트에서 작동하도록 디자인되었지만 여기서는 편의상 비교적 작은 샘플을 사용합니다(30Mb보다 작은 170,000개 행 즉, 원래 NYC 데이터 세트의 약 0.1%를 사용함). 여기에 제공된 연습은 2개의 작업자 노드를 가진 HDInsight 클러스터에서 효율적으로 실행됩니다(약 10분 동안). 동일한 코드를 약간만 수정하면 데이터를 메모리에 캐시하고 클러스터 크기를 변경하도록 적절하게 수정하여 더 큰 데이터 집합을 처리하는 데 사용할 수 있습니다.

<!-- -->

## <a name="setup-spark-clusters-and-notebooks"></a>설정: Spark 클러스터 및 Notebook
설치 단계와 코드는 HDInsight Spark 1.6을 사용하는 이 연습에 제공됩니다. 하지만 Jupyter Notebook은 HDInsight Spark 1.6과 Spark 2.0 클러스터 둘 다에 제공됩니다. 노트북과 이에 연결된 링크의 설명은 이들을 포함하는 GitHub 리포지토리의 [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md)에 제공됩니다. 그뿐 아니라 여기에 있는 코드와 연결된 Notebook에 있는 코드는 일반적이므로 아무 Spark 클러스터에서나 작동할 것입니다. HDInsight Spark를 사용하지 않는 경우 클러스터 설치 및 관리 단계가 여기에 나오는 내용과 약간 다를 수 있습니다. 편의를 위해, Jupyter Notebook 서버의 pyspark 커널에서 실행되는 Spark 1.6 및 2.0용 Jupyter Notebook에 연결된 링크는 다음과 같습니다.

### <a name="spark-16-notebooks"></a>Spark 1.6 Notebook

[pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): 노트북 #1의 토픽과 하이퍼 매개 변수 조정 및 교차 유효성 검사를 사용하는 모델 개발을 포함합니다.

### <a name="spark-20-notebooks"></a>Spark 2.0 노트북

[Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): 이 파일은 Spark 2.0 클러스터에서 데이터 탐색, 모델링, 점수 매기기를 수행하는 방법에 대한 정보를 제공합니다.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>설정: 스토리지 위치, 라이브러리 및 사전 설정 Spark 컨텍스트
Spark는 Azure Storage Blob(WASB라고도 함)를 읽고 쓸 수 있습니다. 따라서 Spark 및 WASB에 다시 저장된 결과를 사용하여 해당 저장소에 저장된 기존 데이터를 처리할 수 있습니다.

모델 또는 파일을 WASB에 저장하려면 경로를 올바르게 지정해야 합니다. "wasb///"로 시작하는 경로를 사용하여 Spark 클러스터에 연결된 기본 컨테이너를 참조할 수 있습니다. 다른 위치를 “wasb://”에서 참조합니다.

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>WASB의 스토리지 위치에 대 한 디렉터리 경로를 설정합니다.
다음 코드 샘플은 읽을 데이터의 위치 및 모델 출력을 저장할 모델 스토리지 디렉터리에 대한 경로를 지정합니다.

    # SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

    # LOCATION OF TRAINING DATA
    taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";


    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";

    # PRINT START TIME
    import datetime
    datetime.datetime.now()

**OUTPUT**

datetime.datetime(2016, 4, 18, 17, 36, 27, 832799)

### <a name="import-libraries"></a>라이브러리 가져오기
다음 코드를 사용하여 필요한 라이브러리를 가져옵니다.

    # LOAD PYSPARK LIBRARIES
    import pyspark
    from pyspark import SparkConf
    from pyspark import SparkContext
    from pyspark.sql import SQLContext
    import matplotlib
    import matplotlib.pyplot as plt
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    import atexit
    from numpy import array
    import numpy as np
    import datetime


### <a name="preset-spark-context-and-pyspark-magics"></a>미리 설정된 Spark 컨텍스트 및 PySpark 매직
Jupyter Notebook과 함께 제공되는 PySpark 커널에는 사전 설정 컨텍스트가 있습니다. 따라서 개발 중인 애플리케이션으로 작업을 시작하기 전에 Spark 또는 Hive 컨텍스트를 명시적으로 설정할 필요는 없습니다. 이러한 컨텍스트는 기본적으로 사용할 수 있습니다. 이러한 컨텍스트는 다음과 같습니다.

* sc - Spark용 
* sqlContext - Hive용

PySpark 커널은 특수 명령인 일부 미리 정의된 "매직"을 제공하며 이러한 매직은 %%를 사용하여 호출할 수 있습니다. 이러한 코드 샘플에 사용되는 다음과 같은 두 가지 명령이 있습니다.

* **%%local** 다음 줄의 코드는 로컬로 실행됩니다. 코드는 유효한 Python 코드여야 합니다.
* **%% sql-o \<变量名 >** 针对 sqlContext 执行 Hive 查询。 -o 매개 변수가 전달된 경우 쿼리 결과가 %%local Python 컨텍스트에서 Pandas 데이터 프레임으로 유지됩니다.

Jupyter Notebook의 커널 및 제공되는 미리 정의된 "매직"에 대한 자세한 내용은 [HDInsight의 HDInsight Spark Linux 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md)을 참조하세요.

## <a name="data-ingestion-from-public-blob"></a>공용 blob에서 데이터 수집:
데이터 과학 프로세스의 첫 번째 단계는 원본에서 분석할 데이터를 수집하여 데이터 탐색 및 모델링 환경에 상주시키는 것입니다. 이 환경은 이 연습에서 Spark입니다. 이 섹션은 일련의 작업을 완료하는 코드를 포함합니다.

* 모델링할 데이터 샘플 수집
* 입력 데이터 세트 읽기(.tsv 파일로 저장됨)
* 데이터 포맷 및 정리
* 메모리에 개체 만들기 및 캐시(RDD 또는 데이터 프레임)
* SQL 컨텍스트에 임시 테이블로 등록

데이터 수집에 대한 코드는 다음과 같습니다.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT FILE FROM PUBLIC BLOB
    taxi_train_file = sc.textFile(taxi_train_file_loc)

    # GET SCHEMA OF THE FILE FROM HEADER
    schema_string = taxi_train_file.first()
    fields = [StructField(field_name, StringType(), True) for field_name in schema_string.split('\t')]
    fields[7].dataType = IntegerType() #Pickup hour
    fields[8].dataType = IntegerType() # Pickup week
    fields[9].dataType = IntegerType() # Weekday
    fields[10].dataType = IntegerType() # Passenger count
    fields[11].dataType = FloatType() # Trip time in secs
    fields[12].dataType = FloatType() # Trip distance
    fields[19].dataType = FloatType() # Fare amount
    fields[20].dataType = FloatType() # Surcharge
    fields[21].dataType = FloatType() # Mta_tax
    fields[22].dataType = FloatType() # Tip amount
    fields[23].dataType = FloatType() # Tolls amount
    fields[24].dataType = FloatType() # Total amount
    fields[25].dataType = IntegerType() # Tipped or not
    fields[26].dataType = IntegerType() # Tip class
    taxi_schema = StructType(fields)

    # PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
    taxi_header = taxi_train_file.filter(lambda l: "medallion" in l)
    taxi_temp = taxi_train_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
            .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                            float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                            float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))


    # CREATE DATA FRAME
    taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

    # CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
    taxi_df_train_cleaned = taxi_train_df.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
        .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
        .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
        .drop('direct_distance').drop('surcharge')\
        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )

    # CACHE & MATERIALIZE DATA-FRAME IN MEMORY. GOING THROUGH AND COUNTING NUMBER OF ROWS MATERIALIZES THE DATA-FRAME IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()

    # REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

위의 셀을 실행하는 데 걸린 시간: 276.62초

## <a name="data-exploration--visualization"></a>데이터 탐색 및 시각화
데이터를 Spark로 가져오면 데이터 과학 프로세스의 다음 단계에서 탐색 및 시각화를 통해 데이터를 더 잘 이해할 수 있습니다. 이 섹션에서는 SQL 쿼리를 사용하여 Taxi 데이터를 검사하고 시각적 조사에 대한 대상 변수 및 잠재 기능을 그립니다. 특히, Taxi Trip에서 승객 수의 빈도, 팁 금액의 빈도 및 지불 금액 및 형식에 따른 팁의 변화를 그립니다.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Taxi Trip의 샘플에서 승객 수 빈도의 히스토그램을 그립니다.
이 코드와 후속 코드 조각은 샘플을 쿼리하는 데 SQL 매직을 사용하고 데이터를 그리는 데 로컬 매직을 사용합니다.

* **SQL 매직(`%%sql`)** HDInsight PySpark 커널은 sqlContext에 대해 간편한 인라인 HiveQL 쿼리를 지원합니다. (-o VARIABLE_NAME) 인수는 Jupyter 서버에서 Pandas 데이터 프레임으로 SQL 쿼리의 출력을 유지합니다. 즉, 로컬 모드에서 사용할 수 있습니다.
* **`%%local` 매직** 은 HDInsight 클러스터의 헤드 노드인 Jupyter 서버에서 코드를 로컬로 실행하는 데 사용됩니다. 일반적으로 `%%sql -o` 매직을 사용하여 쿼리를 실행한 후에 `%%local`을 사용합니다. -o 매개 변수는 SQL 쿼리 출력을 로컬로 유지합니다. 그런 다음 `%%local` 매직은 로컬로 유지된 SQL 쿼리 출력에 대해 다음 코드 조각 집합이 로컬로 실행되도록 트리거합니다. 코드를 실행한 후 출력이 자동으로 시각화됩니다.

이 쿼리는 승객 수에 따라 여정을 검색합니다. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # SQL QUERY
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts FROM taxi_train WHERE passenger_count > 0 and passenger_count < 7 GROUP BY passenger_count


이 코드는 쿼리 출력에서 로컬 데이터 프레임을 만들고 데이터를 그립니다. `%%local` 매직은 로컬 데이터 프레임 `sqlResults`를 만드는데, 이것은 matplotlib로 그리는 데 사용할 수 있습니다. 

<!-- -->

> [!NOTE]
> 이 PySpark 매직은 이 연습에서 여러 번 사용됩니다. 데이터 양이 많은 경우 로컬 메모리에 맞게 데이터 프레임을 만들도록 샘플링해야 합니다.

<!-- -->

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

다음은 승객 수에 따라 여정을 그리는 코드입니다.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline

    # PLOT PASSENGER NUMBER VS TRIP COUNTS
    x_labels = sqlResults['passenger_count'].values
    fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
    fig.set_xticklabels(x_labels)
    fig.set_title('Counts of trips by passenger count')
    fig.set_xlabel('Passenger count in trips')
    fig.set_ylabel('Trip counts')
    plt.show()

**OUTPUT**

![승객 수에 따른 여정 빈도](./media/spark-advanced-data-exploration-modeling/frequency-of-trips-by-passenger-count.png)

Notebook의 **형식** 메뉴 버튼을 사용하여 다양한 시각화 형식(테이블, 원형, 꺾은선형, 영역 또는 막대) 중에서 선택할 수 있습니다. 막대 그리기는 다음과 같습니다.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>승객 수 및 요금 금액에 따라 팁 금액이 어떻게 달라지는지와 팁 금액에 대한 히스토그램을 그립니다.
SQL 쿼리를 사용하여 데이터를 샘플링합니다.

    # SQL SQUERY
    %%sql -q -o sqlResults
        SELECT fare_amount, passenger_count, tip_amount, tipped
        FROM taxi_train 
        WHERE passenger_count > 0 
        AND passenger_count < 7
        AND fare_amount > 0 
        AND fare_amount < 200
        AND payment_type in ('CSH', 'CRD')
        AND tip_amount > 0 
        AND tip_amount < 25


이 코드 셀에서는 SQL 쿼리를 사용하여 3가지로 데이터 그리기를 만듭니다.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline

    # TIP BY PAYMENT TYPE AND PASSENGER COUNT
    ax1 = resultsPDDF[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()

    # TIP BY PASSENGER COUNT
    ax2 = resultsPDDF.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount ($) by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()

    # TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
    ax = resultsPDDF.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(resultsPDDF.passenger_count))
    ax.set_title('Tip amount by Fare amount ($)')
    ax.set_xlabel('Fare Amount')
    ax.set_ylabel('Tip Amount')
    plt.axis([-2, 120, -2, 30])
    plt.show()


**출력:** 

![팁 금액 분포](./media/spark-advanced-data-exploration-modeling/tip-amount-distribution.png)

![승객 수에 따른 여정 수](./media/spark-advanced-data-exploration-modeling/tip-amount-by-passenger-count.png)

![금액에 따른 팁 금액](./media/spark-advanced-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>모델링에 대한 기능 엔지니어링, 변환 및 데이터 준비
이 섹션에서는 기계 학습 모델링에 사용할 데이터를 준비하는 데 사용되는 프로시저에 대한 코드를 설명하고 제공합니다. 다음 작업을 수행하는 방법을 보여줍니다.

* 시간을 트래픽 시간 bin으로 분할하여 새로운 기능 만들기
* 범주 기능 인덱스 및 원 핫 인코딩(one-hot encoding)
* 기계 학습 함수에 입력에 대한 레이블이 지정된 지점 개체 만들기
* 创建数据的随机采样，并将其拆分为定型集和测试集
* 기능 크기 조정
* 메모리에서 개체 캐시

### <a name="create-a-new-feature-by-partitioning-traffic-times-into-bins"></a>트래픽 시간을 bin으로 분할하여 새로운 기능 만들기
이 코드는 트래픽 시간을 bin으로 분할하여 새로운 기능을 만드는 방법 및 메모리에 결과 데이터 프레임을 캐시하는 방법을 보여 줍니다. RDD(복원력 있는 분산된 데이터 세트) 및 데이터 프레임을 반복해서 사용하는 경우 캐시하면 실행 시간이 향상됩니다. 따라서 RDD 및 데이터 프레임을 연습의 여러 단계에서 캐시합니다.

    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_train 
    """
    taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    # THE .COUNT() GOES THROUGH THE ENTIRE DATA-FRAME,
    # MATERIALIZES IT IN MEMORY, AND GIVES THE COUNT OF ROWS.
    taxi_df_train_with_newFeatures.cache()
    taxi_df_train_with_newFeatures.count()

**OUTPUT**

126050

### <a name="index-and-one-hot-encode-categorical-features"></a>범주 기능 인덱스 및 원 핫 인코딩(one-hot encoding)
이 섹션에는 모델링 기능에 입력에 대한 범주 기능을 인덱싱하거나 인코딩하는 방법을 보여줍니다. MLlib의 모델링 및 예측 함수는 사용하기 전에 범주 입력 데이터로 기능을 인덱싱 또는 인코딩해야 합니다. 

모델에 따라 다양한 방식에서 인덱싱하거나 인코드해야 합니다. 예를 들어 로지스틱 및 선형 회귀 모델은 원 핫 인코딩(one-hot encoding)이 필요하며 이런 경우, 예를 들어 3개의 범주가 있는 기능이 관찰 범주에 따라 각각 0 또는 1을 포함하는 3개의 기능 열로 확장될 수 있습니다. MLlib는 원 핫 인코딩 작업을 수행하는 [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) 함수를 제공합니다. 이 인코더는 레이블 인덱스의 열을 단 하나의 값을 가진 이진 벡터의 열에 매핑합니다. 이 인코딩을 사용하여 로지스틱 회귀 분석 등과 같은 숫자 값을 가진 기능을 예상하는 알고리즘을 범주 기능에 적용할 수 있습니다.

다음은 범주 기능을 인덱스 및 인코딩하는 코드입니다.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer

    # INDEX AND ENCODE VENDOR_ID
    stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
    model = stringIndexer.fit(taxi_df_train_with_newFeatures) # Input data-frame is the cleaned one from above
    indexed = model.transform(taxi_df_train_with_newFeatures)
    encoder = OneHotEncoder(dropLast=False, inputCol="vendorIndex", outputCol="vendorVec")
    encoded1 = encoder.transform(indexed)

    # INDEX AND ENCODE RATE_CODE
    stringIndexer = StringIndexer(inputCol="rate_code", outputCol="rateIndex")
    model = stringIndexer.fit(encoded1)
    indexed = model.transform(encoded1)
    encoder = OneHotEncoder(dropLast=False, inputCol="rateIndex", outputCol="rateVec")
    encoded2 = encoder.transform(indexed)

    # INDEX AND ENCODE PAYMENT_TYPE
    stringIndexer = StringIndexer(inputCol="payment_type", outputCol="paymentIndex")
    model = stringIndexer.fit(encoded2)
    indexed = model.transform(encoded2)
    encoder = OneHotEncoder(dropLast=False, inputCol="paymentIndex", outputCol="paymentVec")
    encoded3 = encoder.transform(indexed)

    # INDEX AND TRAFFIC TIME BINS
    stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
    model = stringIndexer.fit(encoded3)
    indexed = model.transform(encoded3)
    encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
    encodedFinal = encoder.transform(indexed)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

위의 셀을 실행하는 데 걸린 시간: 3.14초

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>기계 학습 함수에 입력에 대한 레이블이 지정된 지점 개체 만들기
이 섹션에는 범주별 텍스트 데이터를 레이블이 지정된 지점 데이터 형식으로 인덱싱하는 방법과 인코딩하는 방법을 보여 주는 코드가 포함되어 있습니다. 此转换准备要用于定型和测试 MLlib 逻辑回归和其他分类模型的文本数据。 레이블이 지정된 지점 개체는 대부분 MLlib의 기계 학습 알고리즘에서 입력 데이터로 필요한 방식으로 형식이 지정된 RDD(Resilient Distributed Datasets)입니다. [레이블이 지정된 지점](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) 은 레이블/응답과 연결된 로컬 벡터, 밀도 또는 스파스입니다.

다음은 이진 분류를 위해 텍스트 기능을 인덱싱 및 인코딩하는 코드입니다.

    # FUNCTIONS FOR BINARY CLASSIFICATION

    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingBinary(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.pickup_hour, line.weekday,
                             line.passenger_count, line.trip_time_in_secs, line.trip_distance, line.fare_amount])
        labPt = LabeledPoint(line.tipped, features)
        return  labPt

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                   line.vendorVec.toArray(), line.rateVec.toArray(), line.paymentVec.toArray()), axis=0)
        labPt = LabeledPoint(line.tipped, features)
        return  labPt


다음은 선형 회귀 분석을 위해 범주 텍스트 기능을 인코딩 및 인덱싱하는 코드입니다.

    # FUNCTIONS FOR REGRESSION WITH TIP AMOUNT AS TARGET VARIABLE

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingRegression(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        labPt = LabeledPoint(line.tip_amount, features)
        return  labPt

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
    def parseRowOneHotRegression(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        labPt = LabeledPoint(line.tip_amount, features)
        return  labPt


### <a name="create-a-random-subsampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>创建数据的随机采样，并将其拆分为定型集和测试集
이 코드는 데이터의 무작위 샘플링을 만듭니다(25%가 여기에서 사용됨). 데이터 세트의 크기로 인해 이 예제에 필요하지 않지만 여기서는 데이터를 샘플링하는 방법도 살펴봅니다. 그러면 필요할 때 자체 문제에 사용하는 방법을 알 수 있게 됩니다. 当样本较大时，采样可节省大量时间，同时训练模型。 다음 샘플을 교육 부분(여기서 75%)와 테스트 부분(여기서 25%)로 분할하여 분류 및 회귀 모델링에 사용합니다.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    from pyspark.sql.functions import rand

    samplingFraction = 0.25;
    trainingFraction = 0.75; testingFraction = (1-trainingFraction);
    seed = 1234;
    encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)

    # SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST, WITH A RANDOM COLUMN ADDED FOR DOING CV (SHOWN LATER)
    # INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS
    dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
    trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);

    # CACHE TRAIN AND TEST DATA
    trainData.cache()
    testData.cache()

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary = trainData.map(parseRowIndexingBinary)
    indexedTESTbinary = testData.map(parseRowIndexingBinary)
    oneHotTRAINbinary = trainData.map(parseRowOneHotBinary)
    oneHotTESTbinary = testData.map(parseRowOneHotBinary)

    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg = trainData.map(parseRowIndexingRegression)
    indexedTESTreg = testData.map(parseRowIndexingRegression)
    oneHotTRAINreg = trainData.map(parseRowOneHotRegression)
    oneHotTESTreg = testData.map(parseRowOneHotRegression)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTPUT**

执行以上单元格所花的时间：0.31 秒

### <a name="feature-scaling"></a>기능 크기 조정
데이터 정규화라고도 하는 기능 크기 조정은 폭 넓게 분배된 값을 가진 기능이 목적 함수에서 과도한 가중치를 부여하지 않도록 합니다. 기능 크기 조정에 대한 코드는 단위 분산에 대한 기능의 크기를 조정하는 [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) 를 사용합니다. 이러한 기능은 SGD(Stochastic Gradient Descent)와 함께 선형 회귀에 사용하기 위해 MLlib에서 제공합니다. SGD는 정칙 회귀 또는 SVM(support vector machine)과 같은 광범위한 다른 기계 학습 모델을 학습하기 위한 인기 있는 알고리입니다.   

> [!TIP]
> LinearRegressionWithSGD 알고리즘이 기능 크기 조정에 민감하다는 점을 발견했습니다.   
> 
> 

여기에 정칙 선형 SGD 알고리즘에 사용할 변수의 크기를 조정하는 코드가 있습니다.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from pyspark.mllib.linalg import Vectors
    from pyspark.mllib.feature import StandardScaler, StandardScalerModel
    from pyspark.mllib.util import MLUtils

    # SCALE VARIABLES FOR REGULARIZED LINEAR SGD ALGORITHM
    label = oneHotTRAINreg.map(lambda x: x.label)
    features = oneHotTRAINreg.map(lambda x: x.features)
    scaler = StandardScaler(withMean=False, withStd=True).fit(features)
    dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
    oneHotTRAINregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))

    label = oneHotTESTreg.map(lambda x: x.label)
    features = oneHotTESTreg.map(lambda x: x.features)
    scaler = StandardScaler(withMean=False, withStd=True).fit(features)
    dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
    oneHotTESTregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTPUT**

위의 셀을 실행하는 데 걸린 시간: 11.67초

### <a name="cache-objects-in-memory"></a>메모리에서 개체 캐시
분류, 회귀 및 확장된 기능에 사용되는 입력 데이터 프레임 개체를 캐시하여 ML(기계 학습) 알고리즘의 학습 및 테스트에 소요된 시간을 줄일 수 있습니다.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary.cache()
    indexedTESTbinary.cache()
    oneHotTRAINbinary.cache()
    oneHotTESTbinary.cache()

    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg.cache()
    indexedTESTreg.cache()
    oneHotTRAINreg.cache()
    oneHotTESTreg.cache()

    # SCALED FEATURES
    oneHotTRAINregScaled.cache()
    oneHotTESTregScaled.cache()

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTPUT** 

执行以上单元格所花的时间：0.13 秒

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>팁이 지불되었는지 여부를 이진 분류 모델로 예측합니다.
이 섹션에서는 Taxi Trip에 팁을 지불했는지 여부를 예측하는 이진 분류 작업에 대한 세 개의 모델을 사용하는 방법을 보여줍니다. 제공된 모델은 다음과 같습니다.

* 로지스틱 회귀 
* 임의 포리스트
* 그라데이션 향상 트리

코드 섹션을 빌드하는 각 모델은 다음과 같은 단계로 분할됩니다. 

1. **모델 교육** 데이터
2. **모델 평가**
3. **모델 저장**

매개 변수 비우기를 사용하여 CV(교차 유효성 검사)를 수행하는 두 가지 방법을 보여 줍니다.

1. 使用可应用于 MLlib 中的任何算法和算法中的任何参数集的**泛型**自定义代码。 
2. **pySpark CrossValidator 파이프라인 함수** 사용 对于 Spark 1.5.0 的 CrossValidator 有几个限制： 
   
   * 无法保存或保留管道模型以供将来使用。
   * 모델의 모든 매개 변수에 사용할 수 없습니다.
   * 모든 MLlib 알고리즘에 사용할 수 없습니다.

### <a name="generic-cross-validation-and-hyperparameter-sweeping-used-with-the-logistic-regression-algorithm-for-binary-classification"></a>이진 분류에 대한 로지스틱 회귀 분석 알고리즘과 함께 사용되는 일반적인 교차 유효성 검사 및 하이퍼 매개 변수 비우기
이 섹션의 코드에서는 NYC Taxi Trip 및 요금 데이터 세트에서 팁이 여정에 지불되었는지를 예측하는 [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) 로 로지스틱 회귀 분석 모델을 학습하고 평가하며 저장하는 방법을 보여 줍니다. MLlib 내의 모든 학습 알고리즘에 적용할 수 있는 사용자 지정 코드로 구현된 CV(교차 유효성 검사) 및 하이퍼 매개 변수 비우기를 사용하여 모델을 학습합니다.   

<!-- -->

> [!NOTE]
> 이 사용자 지정 CV 코드를 실행하는 데 몇 분 정도 걸릴 수 있습니다.

<!-- -->

**CV 및 하이퍼 매개 변수 비우기를 사용하여 로지스틱 회귀 모델 학습**

    # LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

    # GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
    from pyspark.mllib.evaluation import BinaryClassificationMetrics

    # CREATE PARAMETER GRID FOR LOGISTIC REGRESSION PARAMETER SWEEP
    from sklearn.grid_search import ParameterGrid
    grid = [{'regParam': [0.01, 0.1], 'iterations': [5, 10], 'regType': ["l1", "l2"], 'tolerance': [1e-3, 1e-4]}]
    paramGrid = list(ParameterGrid(grid))
    numModels = len(paramGrid)

    # SET NUM FOLDS AND NUM PARAMETER SETS TO SWEEP ON
    nFolds = 3;
    h = 1.0 / nFolds;
    metricSum = np.zeros(numModels);

    # BEGIN CV WITH PARAMETER SWEEP
    for i in range(nFolds):
        # Create training and x-validation sets
        validateLB = i * h
        validateUB = (i + 1) * h
        condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
        validation = trainData.filter(condition)
        # Create LabeledPoints from data-frames
        if i > 0:
            trainCVLabPt.unpersist()
            validationLabPt.unpersist()
        trainCV = trainData.filter(~condition)
        trainCVLabPt = trainCV.map(parseRowOneHotBinary)
        trainCVLabPt.cache()
        validationLabPt = validation.map(parseRowOneHotBinary)
        validationLabPt.cache()
        # For parameter sets compute metrics from x-validation
        for j in range(numModels):
            regt = paramGrid[j]['regType']
            regp = paramGrid[j]['regParam']
            iters = paramGrid[j]['iterations']
            tol = paramGrid[j]['tolerance']
            # Train logistic regression model with hypermarameter set
            model = LogisticRegressionWithLBFGS.train(trainCVLabPt, regType=regt, iterations=iters,  
                                                      regParam=regp, tolerance = tol, intercept=True)
            predictionAndLabels = validationLabPt.map(lambda lp: (float(model.predict(lp.features)), lp.label))
            # Use ROC-AUC as accuracy metrics
            validMetrics = BinaryClassificationMetrics(predictionAndLabels)
            metric = validMetrics.areaUnderROC
            metricSum[j] += metric

    avgAcc = metricSum / nFolds;
    bestParam = paramGrid[np.argmax(avgAcc)];

    # UNPERSIST OBJECTS
    trainCVLabPt.unpersist()
    validationLabPt.unpersist()

    # TRAIN ON FULL TRAIING SET USING BEST PARAMETERS FROM CV/PARAMETER SWEEP
    logitBest = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, regType=bestParam['regType'], 
                                                  iterations=bestParam['iterations'], 
                                                  regParam=bestParam['regParam'], tolerance = bestParam['tolerance'], 
                                                  intercept=True)


    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(logitBest.weights))
    print("Intercept: " + str(logitBest.intercept))

    # PRINT ELAPSED TIME    
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

Coefficients: [0.0082065285375, -0.0223675576104, -0.0183812028036, -3.48124578069e-05, -0.00247646947233, -0.00165897881503, 0.0675394837328, -0.111823113101, -0.324609912762, -0.204549780032, -1.36499216354, 0.591088507921, -0.664263411392, -1.00439726852, 3.46567827545, -3.51025855172, -0.0471341112232, -0.043521833294, 0.000243375810385, 0.054518719222]

Intercept: -0.0111216486893

위의 셀을 실행하는 데 걸린 시간: 14.43초

**표준 메트릭을 사용한 이진 분류 모델 평가**

이 섹션의 코드는 ROC 곡선 그림을 포함하는 테스트 데이터 집합에 대해 로지스틱 회귀 분석 모델을 평가하는 방법을 보여 줍니다.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT LIBRARIES
    from sklearn.metrics import roc_curve,auc
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics

    # PREDICT ON TEST DATA WITH BEST/FINAL MODEL
    predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitBest.predict(lp.features)), lp.label))

    # INSTANTIATE METRICS OBJECT
    metrics = BinaryClassificationMetrics(predictionAndLabels)

    # AREA UNDER PRECISION-RECALL CURVE
    print("Area under PR = %s" % metrics.areaUnderPR)

    # AREA UNDER ROC CURVE
    print("Area under ROC = %s" % metrics.areaUnderROC)
    metrics = MulticlassMetrics(predictionAndLabels)

    # OVERALL STATISTICS
    precision = metrics.precision()
    recall = metrics.recall()
    f1Score = metrics.fMeasure()
    print("Summary Stats")
    print("Precision = %s" % precision)
    print("Recall = %s" % recall)
    print("F1 Score = %s" % f1Score)

    # OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
    logitBest.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
    predictionAndLabelsDF = predictionAndLabels.toDF()
    predictionAndLabelsDF.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME    
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

Area under PR = 0.985336538462

Area under ROC = 0.983383274312

Summary Stats

Precision = 0.984174341679

Recall = 0.984174341679

F1 Score = 0.984174341679

위의 셀을 실행하는 데 걸린 시간: 2.67초

**ROC 곡선을 그립니다.**

*predictionAndLabelsDF*는 이전 셀에서 테이블 *tmp_results*로 등록되었습니다. *tmp_results*를 사용하면 쿼리를 수행하고 결과를 sqlResults 데이터 프레임으로 출력하여 그래프에 표시할 수 있습니다. 코드는 다음과 같습니다.

    # QUERY RESULTS                              
    %%sql -q -o sqlResults
    SELECT * from tmp_results


ROC 곡선을 그리고 예측을 수행하는 코드는 다음과 같습니다.

    # MAKE PREDICTIONS AND PLOT ROC-CURVE

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES                              
    %%local
    %matplotlib inline
    from sklearn.metrics import roc_curve,auc

    #PREDICTIONS
    predictions_pddf = sqlResults.rename(columns={'_1': 'probability', '_2': 'label'})
    prob = predictions_pddf["probability"] 
    fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    # PLOT ROC CURVES
    plt.figure(figsize=(5,5))
    plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
    plt.plot([0, 1], [0, 1], 'k--')
    plt.xlim([0.0, 1.0])
    plt.ylim([0.0, 1.05])
    plt.xlabel('False Positive Rate')
    plt.ylabel('True Positive Rate')
    plt.title('ROC Curve')
    plt.legend(loc="lower right")
    plt.show()


**OUTPUT**

![일반적인 접근 방식에 대한 로지스틱 회귀 분석 ROC 곡선](./media/spark-advanced-data-exploration-modeling/logistic-regression-roc-curve.png)

**나중에 사용할 Blob의 모델 유지**

이 섹션의 코드는 소비에 대한 로지스틱 회귀 분석 모델을 저장하는 방법을 보여 줍니다.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionModel

    # PERSIST MODEL
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
    dirfilename = modelDir + logisticregressionfilename;

    logitBest.save(sc, dirfilename);

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";


**OUTPUT**

위의 셀을 실행하는 데 걸린 시간: 34.57초

### <a name="use-mllibs-crossvalidator-pipeline-function-with-logistic-regression-elastic-regression-model"></a>로지스틱 회귀(탄력적 회귀) 모델과 함께 MLlib의 CrossValidator 파이프라인 함수 사용
이 섹션의 코드에서는 NYC Taxi Trip 및 요금 데이터 세트에서 팁이 여정에 지불되었는지를 예측하는 [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) 로 로지스틱 회귀 분석 모델을 학습하고 평가하며 저장하는 방법을 보여 줍니다. 매개 변수 비우기와 함께 CV에 대한 MLlib CrossValidator 파이프라인 함수로 구현되는 CV(교차 유효성 검사) 및 하이퍼 매개 변수 비우기를 사용하여 모델을 학습합니다.   

<!-- -->

> [!NOTE]
> 이 MLlib CV 코드를 실행하는 데 몇 분 정도 걸릴 수 있습니다.

<!-- -->

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml import Pipeline
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    from pyspark.ml.tuning import CrossValidator, ParamGridBuilder
    from sklearn.metrics import roc_curve,auc

    # DEFINE ALGORITHM / MODEL
    lr = LogisticRegression()

    # DEFINE GRID PARAMETERS
    paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                                  .addGrid(lr.maxIter, (5, 10))\
                                  .addGrid(lr.tol, (1e-4, 1e-5))\
                                  .addGrid(lr.elasticNetParam, (0.25,0.75))\
                                  .build()

    # DEFINE CV WITH PARAMETER SWEEP
    cv = CrossValidator(estimator= lr,
                        estimatorParamMaps=paramGrid,
                        evaluator=BinaryClassificationEvaluator(),
                        numFolds=3)

    # CONVERT TO DATA-FRAME: THIS DOES NOT RUN ON RDDs
    trainDataFrame = sqlContext.createDataFrame(oneHotTRAINbinary, ["features", "label"])

    # TRAIN WITH CROSS-VALIDATION
    cv_model = cv.fit(trainDataFrame)


    ## PREDICT AND EVALUATE ON TEST DATA-SET

    # USE TEST DATASET FOR PREDICTION
    testDataFrame = sqlContext.createDataFrame(oneHotTESTbinary, ["features", "label"])
    test_predictions = cv_model.transform(testDataFrame)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**OUTPUT**

위의 셀을 실행하는 데 걸린 시간: 107.98초

**ROC 곡선을 그립니다.**

*predictionAndLabelsDF*는 이전 셀에서 테이블 *tmp_results*로 등록되었습니다. *tmp_results*를 사용하면 쿼리를 수행하고 결과를 sqlResults 데이터 프레임으로 출력하여 그래프에 표시할 수 있습니다. 코드는 다음과 같습니다.

    # QUERY RESULTS
    %%sql -q -o sqlResults
    SELECT label, prediction, probability from tmp_results

다음은 ROC 곡선을 그리는 코드입니다.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES 
    %%local
    from sklearn.metrics import roc_curve,auc

    # ROC CURVE
    prob = [x["values"][1] for x in sqlResults["probability"]]
    fpr, tpr, thresholds = roc_curve(sqlResults['label'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    #PLOT
    plt.figure(figsize=(5,5))
    plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
    plt.plot([0, 1], [0, 1], 'k--')
    plt.xlim([0.0, 1.0])
    plt.ylim([0.0, 1.05])
    plt.xlabel('False Positive Rate')
    plt.ylabel('True Positive Rate')
    plt.title('ROC Curve')
    plt.legend(loc="lower right")
    plt.show()


**OUTPUT**

![MLlib의 CrossValidator를 사용하는 로지스틱 회귀 분석 ROC 곡선](./media/spark-advanced-data-exploration-modeling/mllib-crossvalidator-roc-curve.png)

### <a name="random-forest-classification"></a>임의 포리스트 분류
이 섹션의 코드에서는 NYC Taxi Trip 및 요금 데이터 세트에서 팁이 여정에 지불되었는지 여부를 예측하는 임의 포리스트 회귀를 학습, 평가, 저장하는 방법을 보여 줍니다.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics

    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

    # TRAIN RANDOMFOREST MODEL
    rfModel = RandomForest.trainClassifier(indexedTRAINbinary, numClasses=2, 
                                           categoricalFeaturesInfo=categoricalFeaturesInfo,
                                           numTrees=25, featureSubsetStrategy="auto",
                                           impurity='gini', maxDepth=5, maxBins=32)
    ## UN-COMMENT IF YOU WANT TO PRING TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())

    # PREDICT ON TEST DATA AND EVALUATE
    predictions = rfModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

    # AREA UNDER ROC CURVE
    metrics = BinaryClassificationMetrics(predictionAndLabels)
    print("Area under ROC = %s" % metrics.areaUnderROC)

    # PERSIST MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfclassificationfilename = "RandomForestClassification_" + datestamp;
    dirfilename = modelDir + rfclassificationfilename;

    rfModel.save(sc, dirfilename);

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

Area under ROC = 0.985336538462

위의 셀을 실행하는 데 걸린 시간: 26.72초

### <a name="gradient-boosting-trees-classification"></a>그라데이션 향상 트리 분류
이 섹션의 코드에서는 NYC Taxi Trip 및 요금 데이터 세트에서 팁이 여정에 지불되었는지 여부를 예측하는 그라데이션 향상 트리 모델을 학습, 평가, 저장하는 방법을 보여줍니다.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

    gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                    numIterations=10)
    ## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
    #print('Learned classification GBT model:')
    #print(bgtModel.toDebugString())

    # PREDICT ON TEST DATA AND EVALUATE
    predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

    # Area under ROC curve
    metrics = BinaryClassificationMetrics(predictionAndLabels)
    print("Area under ROC = %s" % metrics.areaUnderROC)

    # PERSIST MODEL IN A BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp;
    dirfilename = modelDir + btclassificationfilename;

    gbtModel.save(sc, dirfilename)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTPUT**

Area under ROC = 0.985336538462

위의 셀을 실행하는 데 걸린 시간: 28.13초

## <a name="predict-tip-amount-with-regression-models-not-using-cv"></a>회귀 모델로 CV를 사용하지 않고 팁 금액 예측
이 섹션에서는 다른 팁 기능에 따라 지불한 팁의 금액을 예측하는 회귀 작업에 대한 세 가지 모델을 사용하는 방법을 보여 줍니다. 제공된 모델은 다음과 같습니다.

* 정칙 선형 회귀
* 임의 포리스트
* 그라데이션 향상 트리

이러한 모델은 소개에서 설명했습니다. 코드 섹션을 빌드하는 각 모델은 다음과 같은 단계로 분할됩니다. 

1. **모델 교육** 데이터
2. **모델 평가**
3. **모델 저장**   

<!-- -->

> [!NOTE] 
> 교차 유효성 검사는 로지스틱 회귀 모델에 대한 세부 정보에 표시되므로 이 섹션의 3가지 회귀 모델에 사용되지 않습니다. 선형 회귀에 대한 탄력적 net과 함께 CV를 사용하는 방법을 보여 주는 예제를 이 토픽의 부록에 제공합니다.

<!-- -->

<!-- -->

> [!NOTE] 
> 경험에 따르면 LinearRegressionWithSGD 모델의 수렴과 관련된 문제가 발생할 수 있으며 매개 변수는 유효한 모델을 얻기 위해 신중하게 변경/최적화되어야 합니다. 변수의 크기를 조정하면 수렴에 큰 도움이 됩니다. 이 토픽의 부록에 나와 있는 바와 같이 LinearRegressionWithSGD 대신 탄력적 net 회귀를 사용할 수도 있습니다.

<!-- -->

### <a name="linear-regression-with-sgd"></a>SGD로 선형 회귀
이 섹션의 코드는 크기 조정된 기능을 사용하여 최적화를 위해 SGD(Stochastic Gradient Descent)를 사용하는 선형 회귀를 학습하는 방법 및 WASB(Azure Blob Storage)에서 모델의 점수를 매기고 평가하며 저장하는 방법을 보여줍니다.

> [!TIP]
> 경험에 따르면 LinearRegressionWithSGD 모델의 수렴과 관련된 문제가 발생할 수 있으며 매개 변수는 유효한 모델을 얻기 위해 신중하게 변경/최적화되어야 합니다. 변수의 크기를 조정하면 수렴에 큰 도움이 됩니다.
> 
> 

    # LINEAR REGRESSION WITH SGD 

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel
    from pyspark.mllib.evaluation import RegressionMetrics
    from scipy import stats

    # USE SCALED FEATURES TO TRAIN MODEL
    linearModel = LinearRegressionWithSGD.train(oneHotTRAINregScaled, iterations=100, step = 0.1, regType='l2', regParam=0.1, intercept = True)

    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(linearModel.weights))
    print("Intercept: " + str(linearModel.intercept))

    # SCORE ON SCALED TEST DATA-SET & EVALUATE
    predictionAndLabels = oneHotTESTregScaled.map(lambda lp: (float(linearModel.predict(lp.features)), lp.label))
    testMetrics = RegressionMetrics(predictionAndLabels)

    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
    dirfilename = modelDir + linearregressionfilename;

    linearModel.save(sc, dirfilename)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTPUT**

Coefficients: [0.0141707753435, -0.0252930927087, -0.0231442517137, 0.247070902996, 0.312544147152, 0.360296120645, 0.0122079566092, -0.00456498588241, -0.0898228505177, 0.0714046248793, 0.102171263868, 0.100022455632, -0.00289545676449, -0.00791124681938, 0.54396316518, -0.536293513569, 0.0119076553369, -0.0173039244582, 0.0119632796147, 0.00146764882502]

Intercept: 0.854507624459

RMSE = 1.23485131376

R-sqr = 0.597963951127

위의 셀을 실행하는 데 걸린 시간: 38.62초

### <a name="random-forest-regression"></a>임의 포리스트 회귀
이 섹션의 코드에서는 NYC Taxi Trip 데이터에서 팁 금액을 예측하는 임의 포리스트 모델을 학습, 평가, 저장하는 방법을 보여줍니다.   

<!-- -->

> [!NOTE]
> 사용자 지정 코드로 매개 변수 비우기를 사용하는 교차 유효성 검사는 부록에 제공됩니다.

<!-- -->

    #PREDICT TIP AMOUNTS USING RANDOM FOREST

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import RegressionMetrics


    # TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=25, featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=10, maxBins=32)
    # UN-COMMENT IF YOU WANT TO PRING TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())

    # PREDICT AND EVALUATE ON TEST DATA-SET
    predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)

    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfregressionfilename = "RandomForestRegression_" + datestamp;
    dirfilename = modelDir + rfregressionfilename;

    rfModel.save(sc, dirfilename);

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTPUT**

RMSE = 0.931981967875

R-sqr = 0.733445485802

위의 셀을 실행하는 데 걸린 시간: 25.98초

### <a name="gradient-boosting-trees-regression"></a>그라데이션 향상 트리 회귀
이 섹션의 코드에서는 NYC Taxi Trip 데이터에서 팁 금액을 예측하는 그라데이션 향상 트리 모델을 학습, 평가, 저장하는 방법을 보여줍니다.

**학습 및 평가**

    #PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    from pyspark.mllib.util import MLUtils

    # TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                    numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)

    # EVALUATE A TEST DATA-SET
    predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES
    test_predictions= sqlContext.createDataFrame(predictionAndLabels)
    test_predictions_pddf = test_predictions.toPandas()

    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
    dirfilename = modelDir + btregressionfilename;
    gbtModel.save(sc, dirfilename)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

RMSE = 0.928172197114

R-sqr = 0.732680354389

위의 셀을 실행하는 데 걸린 시간: 20.9초

**그림**

*tmp_results*는 이전 셀에서 Hive 테이블로 등록되어 있습니다. 테이블의 결과는 그래프로 나타내기 위해 *sqlResults* 데이터 프레임에 출력됩니다. 코드는 다음과 같습니다.

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results


다음은 Jupyter 서버를 사용하여 데이터를 그리는 코드입니다.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import numpy as np

    # PLOT
    ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(sqlResults['_1'], sqlResults['_2'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    ax.plot(sqlResults['_1'], fit[0] * sqlResults['_1'] + fit[1], color='magenta')
    plt.axis([-1, 15, -1, 15])
    plt.show(ax)

![Actual-vs-predicted-tip-amounts](./media/spark-advanced-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="appendix-additional-regression-tasks-using-cross-validation-with-parameter-sweeps"></a>부록: 매개 변수 비우기를 사용하는 교차 유효성 검사로 추가 회귀 작업
이 부록에는 선형 회귀에 대한 탄력적 net을 사용하여 CV를 수행하는 방법 및 임의 포리스트 회귀에 대한 사용자 지정 코드로 매개 변수 비우기를 사용하여 CV를 수행하는 방법을 보여 주는 코드가 있습니다.

### <a name="cross-validation-using-elastic-net-for-linear-regression"></a>선형 회귀에 대한 탄력적 net을 사용하여 교차 유효성 검사
이 섹션의 코드는 선형 회귀에 대한 탄력적 net을 사용하여 교차 유효성 검사를 실행하는 방법 및 테스트 데이터에 대한 모델을 평가하는 방법을 보여 줍니다.

    ###  CV USING ELASTIC NET FOR LINEAR REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.regression import LinearRegression
    from pyspark.ml import Pipeline
    from pyspark.ml.evaluation import RegressionEvaluator
    from pyspark.ml.tuning import CrossValidator, ParamGridBuilder

    # DEFINE ALGORITHM/MODEL
    lr = LinearRegression()

    # DEFINE GRID PARAMETERS
    paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                                  .addGrid(lr.maxIter, (5, 10))\
                                  .addGrid(lr.tol, (1e-4, 1e-5))\
                                  .addGrid(lr.elasticNetParam, (0.25,0.75))\
                                  .build() 

    # DEFINE PIPELINE 
    # SIMPLY THE MODEL HERE, WITHOUT TRANSFORMATIONS
    pipeline = Pipeline(stages=[lr])

    # DEFINE CV WITH PARAMETER SWEEP
    cv = CrossValidator(estimator= lr,
                        estimatorParamMaps=paramGrid,
                        evaluator=RegressionEvaluator(),
                        numFolds=3)

    # CONVERT TO DATA FRAME, AS CROSSVALIDATOR WON'T RUN ON RDDS
    trainDataFrame = sqlContext.createDataFrame(oneHotTRAINreg, ["features", "label"])

    # TRAIN WITH CROSS-VALIDATION
    cv_model = cv.fit(trainDataFrame)


    # EVALUATE MODEL ON TEST SET
    testDataFrame = sqlContext.createDataFrame(oneHotTESTreg, ["features", "label"])

    # MAKE PREDICTIONS ON TEST DOCUMENTS
    # cvModel uses the best model found (lrModel).
    predictionAndLabels = cv_model.transform(testDataFrame)

    # CONVERT TO DF AND SAVE REGISTER DF AS TABLE
    predictionAndLabels.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

위의 셀을 실행하는 데 걸린 시간: 161.21초

**Evaluate with R-SQR metric**

*tmp_results*는 이전 셀에서 Hive 테이블로 등록되어 있습니다. 테이블의 결과는 그래프로 나타내기 위해 *sqlResults* 데이터 프레임에 출력됩니다. 코드는 다음과 같습니다.

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT label,prediction from tmp_results


R-sqr을 계산하는 코드는 다음과 같습니다.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    from scipy import stats

    #R-SQR TEST METRIC
    corstats = stats.linregress(sqlResults['label'],sqlResults['prediction'])
    r2 = (corstats[2]*corstats[2])
    print("R-sqr = %s" % r2)


**OUTPUT**

R-sqr = 0.619184907088

### <a name="cross-validation-with-parameter-sweep-using-custom-code-for-random-forest-regression"></a>임의 포리스트 회귀에 대한 사용자 지정 코드로 교차 유효성 검사를 사용한 매개 변수 비우기
이 섹션의 코드는 임의 포리스트 회귀에 대한 사용자 지정 코드로 교차 유효성 검사를 사용하여 매개 변수 비우기를 수행하는 방법과 테스트 데이터에 대한 모델을 평가하는 방법을 보여 줍니다.

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    # GET ACCURARY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import RegressionMetrics
    from sklearn.grid_search import ParameterGrid

    ## CREATE PARAMETER GRID
    grid = [{'maxDepth': [5,10], 'numTrees': [25,50]}]
    paramGrid = list(ParameterGrid(grid))

    ## SPECIFY LEVELS OF CATEGORICAL VARIBLES
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

    # SPECIFY NUMFOLDS AND ARRAY TO HOLD METRICS
    nFolds = 3;
    numModels = len(paramGrid)
    h = 1.0 / nFolds;
    metricSum = np.zeros(numModels);

    for i in range(nFolds):
        # Create training and x-validation sets
        validateLB = i * h
        validateUB = (i + 1) * h
        condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
        validation = trainData.filter(condition)
        # Create labeled points from data-frames
        if i > 0:
            trainCVLabPt.unpersist()
            validationLabPt.unpersist()
        trainCV = trainData.filter(~condition)
        trainCVLabPt = trainCV.map(parseRowIndexingRegression)
        trainCVLabPt.cache()
        validationLabPt = validation.map(parseRowIndexingRegression)
        validationLabPt.cache()
        # For parameter sets compute metrics from x-validation
        for j in range(numModels):
            maxD = paramGrid[j]['maxDepth']
            numT = paramGrid[j]['numTrees']
            # Train logistic regression model with hypermarameter set
            rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=numT, featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=maxD, maxBins=32)
            predictions = rfModel.predict(validationLabPt.map(lambda x: x.features))
            predictionAndLabels = validationLabPt.map(lambda lp: lp.label).zip(predictions)
            # Use ROC-AUC as accuracy metrics
            validMetrics = RegressionMetrics(predictionAndLabels)
            metric = validMetrics.rootMeanSquaredError
            metricSum[j] += metric

    avgAcc = metricSum/nFolds;
    bestParam = paramGrid[np.argmin(avgAcc)];

    # UNPERSIST OBJECTS
    trainCVLabPt.unpersist()
    validationLabPt.unpersist()

    ## TRAIN FINAL MODL WIHT BEST PARAMETERS
    rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=bestParam['numTrees'], featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=bestParam['maxDepth'], maxBins=32)

    # EVALUATE MODEL ON TEST DATA
    predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

    #PRINT TEST METRICS
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

RMSE = 0.906972198262

R-sqr = 0.740751197012

위의 셀을 실행하는 데 걸린 시간: 69.17초

### <a name="clean-up-objects-from-memory-and-print-model-locations"></a>메모리에서 개체 정리 및 모델 위치 인쇄
`unpersist()` 를 사용하여 메모리에 캐시된 개체를 삭제합니다.

    # UNPERSIST OBJECTS CACHED IN MEMORY

    # REMOVE ORIGINAL DFs
    taxi_df_train_cleaned.unpersist()
    taxi_df_train_with_newFeatures.unpersist()
    trainData.unpersist()
    trainData.unpersist()

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary.unpersist()
    indexedTESTbinary.unpersist()
    oneHotTRAINbinary.unpersist()
    oneHotTESTbinary.unpersist()

    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg.unpersist()
    indexedTESTreg.unpersist()
    oneHotTRAINreg.unpersist()
    oneHotTESTreg.unpersist()

    # SCALED FEATURES
    oneHotTRAINregScaled.unpersist()
    oneHotTESTregScaled.unpersist()


**OUTPUT**

PythonRDD[122] at RDD at PythonRDD.scala: 43

\* * 要在使用笔记本中使用的模型文件的输出路径。 ** 독립적인 데이터 집합을 사용하고 점수를 매기려면 이러한 파일 이름을 복사하여 "Consumption notebook(소비 Notebook)"에 붙여넣어야 합니다.

    # PRINT MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**OUTPUT**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0316_47_30.096528"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0316_51_28.433670"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0316_50_17.454440"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0316_51_57.331730"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0316_50_40.138809"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0316_52_18.827237"

## <a name="whats-next"></a>다음은 무엇일까요?
Spark MlLib로 회귀 및 분류 모델을 만든 경우 이러한 모델의점수를  매기고 평가하는 방법을 배울 수 있습니다.

**모델 사용:** 이 토픽에서 만든 분류 및 회귀 모델의 점수를 매기고 평가하는 방법을 알아보려면 [Spark에서 만든 기계 학습 모델 점수 매기기 및 평가](spark-model-consumption.md)를 참조하세요.

