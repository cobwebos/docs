---
title: Azure Data Lake를 사용한 확장성 있는 데이터 과학 - Team Data Science Process
description: Azure Data Lake를 사용하여 데이터 세트에 대해 데이터 탐색 및 이진 분류 작업을 수행하는 방법입니다.
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
ms.openlocfilehash: 9409f14b20684afa1a39d45e663ff316f405cc97
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717922"
---
# <a name="scalable-data-science-with-azure-data-lake-an-end-to-end-walkthrough"></a>Azure Data Lake를 사용한 확장성 있는 데이터 과학: 엔드투엔드 연습
이 연습에서는 팁을 요금으로 지급할지 여부를 예측하기 위해 NYC Taxi Trip 및 요금 데이터 세트 샘플에서 데이터 탐색 및 이진 분류 작업을 수행하는 데 Azure Data Lake를 사용하는 방법을 보여줍니다. [팀 데이터 과학 프로세스](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), 엔드투엔드, 데이터 획득에서 모델 학습한 다음, 모델을 게시하는 웹 서비스 배포 단계까지 안내합니다.

## <a name="technologies"></a>기술

本演练中使用了这些技术。
* Azure 데이터 레이크 분석
* U-SQL 및 Visual Studio
* Python
* Azure Machine Learning
* 스크립트


### <a name="azure-data-lake-analytics"></a>Azure 데이터 레이크 분석
[Microsoft Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/) 는 데이터 과학자가 임의 크기, 모양 및 속도로 데이터를 저장하고 데이터 처리, 고급 분석 및 기계 학습 모델링을 높은 확장성과 함께 비용 효율적으로 수행하는 데 필요한 모든 기능을 포함합니다.   데이터가 실제로 처리되는 경우에만 작업 단위로 비용을 지불합니다. Azure Data Lake 분석에는 SQL의 선언적 특성을 C#의 표현 능력으로 혼합하여 확장성 있는 분산 쿼리 기능을 제공하는 언어인 U-SQL을 포함합니다. 이를 통해, 읽기에 스키마를 적용하여 구조화되지 않은 데이터를 처리하고, 사용자 지정 논리 및 UDF(사용자 정의 함수)를 삽입하고, 대규모 실행 방법을 정교하게 세분화하여 제어할 수 있도록 확장성을 포함할 수 있습니다. U-SQL의 디자인 원리에 대해 자세히 알아보려면 [Visual Studio 블로그 게시물](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/)을 참조하세요.

Data Lake Analytics는 또한 Cortana Analytics Suite에서 핵심적인 부분으로, Azure SQL Data Warehouse, Power BI 및 Data Factory와 함께 사용할 수 있습니다. 这种组合提供了一个完整的云大数据和高级分析平台。

이 연습에서는 데이터 과학 프로세스 작업을 완료하는 데 필요한 필수 조건 및 리소스를 설치하는 방법을 설명하는 것으로 시작합니다. 然后，它将使用 U SQL 概述数据处理步骤，并通过显示如何将 Python 和 Hive 与 Azure 机器学习 Studio （经典）结合使用来构建和部署预测模型。

### <a name="u-sql-and-visual-studio"></a>U-SQL 및 Visual Studio
이 연습에서는 Visual Studio를 사용하여 데이터 세트를 처리하도록 U-SQL 스크립트를 편집할 것을 권장합니다. U-SQL 스크립트는 여기서 설명하고 별도의 파일로 제공됩니다. 이 프로세스에는 데이터 수집, 탐색 및 샘플링이 포함됩니다. 또한 Azure 포털에서 U-SQL 스크립트 작업을 실행하는 방법을 보여 줍니다. Azure Machine Learning Studio에서 이진 분류 모델의 빌드 및 배포를 용이하게 하기 위해 연결된 HDInsight 클러스터에 데이터에 대한 Hive 테이블이 생성됩니다.

### <a name="python"></a>Python
이 연습에는 Azure Machine Learning Studio에서 Python을 사용하여 예측 모델을 빌드 및 배포하는 방법을 보여 주는 섹션도 포함되어 있습니다. 이 프로세스의 이러한 단계에 대해 Python 스크립트와 함께 Jupyter Notebook을 제공합니다. Notebook에는 여기에 설명된 이진 분류 모델 외에도 다중 클래스 분류 및 회귀 모델링과 같은 추가 기능 엔지니어링 단계 및 모델 생성을 위한 코드가 포함됩니다. 회귀 작업은 다른 팁 기능을 기반으로 하는 팁의 금액을 예측합니다.

### <a name="azure-machine-learning"></a>Azure Machine Learning 
Azure 机器学习 Studio （经典）用于使用以下两种方法生成和部署预测模型：首先使用 Python 脚本，然后使用 HDInsight （Hadoop）群集上的 Hive 表。

### <a name="scripts"></a>스크립트
이 연습에는 주요 단계만 나와 있습니다. [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough)에서 전체 **U-SQL 스크립트** 및 **Jupyter Notebook**을 다운로드할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
이 토픽을 시작하기 전에 다음이 있어야 합니다.

* Azure 구독 아직 가지고 있지 않은 경우 [Azure 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
* [권장] Visual Studio 2013 이상. 아직 이러한 버전이 설치되지 않은 경우 [Visual Studio Community](https://www.visualstudio.com/vs/community/)에서 무료로 다운로드할 수 있습니다.

> [!NOTE]
> Visual Studio 대신 Azure Portal을 사용하여 Azure Data Lake 쿼리를 제출할 수도 있습니다. Visual Studio 및 포털에서 이 작업을 수행하는 방법에 대한 지침은 **U-SQL로 데이터 처리** 섹션에서 설명합니다.
>
>


## <a name="prepare-data-science-environment-for-azure-data-lake"></a>Azure Data Lake에 대한 데이터 과학 환경 준비
이 연습에 대한 데이터 과학 환경을 준비하려면 다음 리소스를 만듭니다.

* Azure Data Lake Storage （ADLS）
* Azure Data Lake 분석(ADLA)
* Azure Blob Storage 계정
* Azure 机器学习 Studio （经典）帐户
* Visual Studio용 Azure Data Lake 도구(권장)

이 섹션에서는 이러한 각 리소스를 만드는 방법에 대한 지침을 제공합니다. Azure Machine Learning에서 Python 대신 Hive 테이블을 사용하여 모델을 작성하려는 경우 HDInsight(Hadoop) 클러스터를 프로비전해야 합니다. 이러한 대체 절차는 옵션 2 섹션에 설명되어 있습니다.


> [!NOTE]
> **Azure Data Lake Store**는 별도로 만들거나 **Azure Data Lake Analytics**을 만들 때 기본 스토리지로 만들 수 있습니다. 이러한 각 리소스를 만들기 위한 지침이 별도로 참조되지만 Data Lake 스토리지 계정을 별도의 단계로 만들지 않아도 됩니다.
>
>

### <a name="create-an-azure-data-lake-storage"></a>创建 Azure Data Lake Storage


[Azure Portal](https://portal.azure.com)에서 ADLS를 만듭니다. 자세한 내용은 [Azure Portal을 사용하여 Data Lake Store로 HDInsight 클러스터 만들기](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)를 참조하세요. 여기 설명된 **옵션 구성** 블레이드의 **DataSource** 블레이드에서 클러스터 AAD ID를 설정해야 합니다.

 ![3](./media/data-lake-walkthrough/3-create-ADLS.PNG)

### <a name="create-an-azure-data-lake-analytics-account"></a>Azure Data Lake 분석 계정 만들기
[Azure Portal](https://portal.azure.com)에서 ADLA 계정을 만듭니다. 자세한 내용은 [자습서: Azure Portal을 사용하여 Azure Data Lake Analytics 시작](../../data-lake-analytics/data-lake-analytics-get-started-portal.md)을 참조하세요.

 ![4](./media/data-lake-walkthrough/4-create-ADLA-new.PNG)

### <a name="create-an-azure-blob-storage-account"></a>Azure Blob Storage 계정 만들기
[Azure Portal](https://portal.azure.com)에서 Azure Blob Storage 계정을 만듭니다. 有关详细信息，请参阅[关于 Azure 存储帐户](../../storage/common/storage-create-storage-account.md)中的创建存储帐户部分。

 ![5](./media/data-lake-walkthrough/5-Create-Azure-Blob.PNG)

### <a name="set-up-an-azure-machine-learning-studio-classic-account"></a>设置 Azure 机器学习 Studio （经典）帐户
从[Azure 机器学习 studio](https://azure.microsoft.com/services/machine-learning/)页注册/进入 Azure 机器学习 studio （经典）。 **지금 시작** 단추를 클릭한 후 "무료 작업 영역" 또는 "표준 작업 영역"을 선택합니다. 이제 Azure Machine Learning Studio에서 실험을 만들 준비가 되었습니다.

### <a name="install-azure-data-lake-tools-recommended"></a>Azure Data Lake 도구 설치[권장]
[Visual Studio용 Azure Data Lake 도구](https://www.microsoft.com/download/details.aspx?id=49504)에서 사용 중인 Visual Studio 버전에 대한 Azure Data Lake 도구를 설치합니다.

 ![6](./media/data-lake-walkthrough/6-install-ADL-tools-VS.PNG)

安装完成后，打开 Visual Studio。 위쪽 메뉴에 Data Lake 탭이 표시됩니다. Azure 리소스는 Azure 계정에 로그인하면 왼쪽 패널에 표시됩니다.

 ![7](./media/data-lake-walkthrough/7-install-ADL-tools-VS-done.PNG)

## <a name="the-nyc-taxi-trips-dataset"></a>NYC Taxi Trips 데이터 세트
여기에 사용된 데이터 세트는 공개적으로 제공되는 데이터 세트인 [NYC Taxi Trips 데이터 세트](https://www.andresmh.com/nyctaxitrips/)입니다. NYC Taxi Trip 데이터는 1억 7,300만 개가 넘는 개별 여정 및 각 여정의 요금으로 기록된 약 20GB의 압축된 CSV 파일(압축되지 않은 경우 약 48GB)로 구성됩니다. 각 여정 레코드는 승차 및 하차 위치, 익명 처리된 hack(기사) 면허증 번호 및 medallion(택시의 고유 ID) 번호를 포함합니다. 데이터는 2013년의 모든 여정을 포괄하며, 매월 다음 두 개의 데이터 세트로 제공됩니다.

'trip_data' CSV는 승객 수, 승차 및 하차 지점, 여정 기간, 여정 거리 등 여정 세부 정보를 포함합니다. 다음은 몇 가지 샘플 레코드입니다.

       medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868



'trip_fare' CSV는 지불 유형, 금액, 추가 요금 및 세금, 팁 및 통행료, 총 지불 금액 등 각 여정의 요금에 대한 세부 정보를 포함합니다. 다음은 몇 가지 샘플 레코드입니다.

       medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

trip\_data와 trip\_fare를 조인할 고유 키는 medallion, hack\_licence 및 pickup\_datetime이라는 세 가지 필드로 구성됩니다. 可从 Azure 存储 blob 访问原始 CSV 文件。 이 조인에 대한 U-SQL 스크립트는 [trip 및 fare 테이블 조인](#join) 섹션에 나와 있습니다.

## <a name="process-data-with-u-sql"></a>U-SQL로 데이터 처리
이 섹션에서 설명하는 데이터 처리 작업에는 데이터 수집, 품질 검사, 탐색 및 샘플링이 포함됩니다. 또한 trip 및 fare 테이블을 조인하는 방법도 보여줍니다. 마지막 섹션에서는 Azure 포털에서 U-SQL 스크립트 작업을 실행하는 방법을 보여 줍니다. 각 하위 섹션에 대한 링크는 다음과 같습니다.

* [데이터 수집: 공용 Blob에서 데이터 읽기](#ingest)
* [데이터 품질 검사](#quality)
* [데이터 탐색](#explore)
* [trip 및 fare 테이블 조인](#join)
* [데이터 샘플링](#sample)
* [U-SQL 작업 실행](#run)

U-SQL 스크립트는 여기서 설명하고 별도의 파일로 제공됩니다. **GitHub** 에서 전체 [U-SQL 스크립트](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough)를 다운로드할 수 있습니다.

U-SQL을 실행하려면 Visual Studio를 열고 **파일 --> 새로 만들기 --> 프로젝트**를 클릭한 후 **U-SQL 프로젝트**를 선택하고 이름을 지정하고 폴더에 저장합니다.

![8](./media/data-lake-walkthrough/8-create-USQL-project.PNG)

> [!NOTE]
> Visual Studio 대신 Azure Portal을 사용하여 U-SQL을 실행할 수 있습니다. 포털에서 Azure Data Lake Analytics 리소스로 이동한 후 다음 그림과 같이 쿼리를 직접 제출할 수 있습니다.
>
>

![9](./media/data-lake-walkthrough/9-portal-submit-job.PNG)

### <a name="ingest"></a>데이터 수집: 공용 Blob에서 데이터 읽기

Azure blob 中数据的位置引用为**wasb://container\_名称\@blob\_存储\_帐户\_name.blob.core.windows.net/blob_name** ，可使用**提取器（）** 进行提取。 在以下脚本中，用您自己的容器名称和存储帐户名称替换容器\_名称\@blob\_存储\_帐户\_名称 wasb 地址。 由于文件名的格式相同，因此可以使用**行程\_数据\_\{\*\}** ，以读取所有12个行程文件。

    ///Read in Trip data
    @trip0 =
        EXTRACT
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count string,
        trip_time_in_secs string,
        trip_distance string,
        pickup_longitude string,
        pickup_latitude string,
        dropoff_longitude string,
        dropoff_latitude string
    // This is reading 12 trip data from blob
    FROM "wasb://container_name@blob_storage_account_name.blob.core.windows.net/nyctaxitrip/trip_data_{*}.csv"
    USING Extractors.Csv();

첫 번째 행에 헤더가 있으므로 헤더를 제거하고 열 형식을 적절하게 변경해야 합니다. 您可以使用 swebhdfs：/ **data_lake_storage_name/azuredatalakestorage/folder_name/file_name**_ 将已处理的数据保存到 Azure Data Lake Storage，还可以使用 wasb：/ **/container_name\@blob_storage_account_name/** 。

    // change data types
    @trip =
        SELECT
        medallion,
        hack_license,
        vendor_id,
        rate_code,
        store_and_fwd_flag,
        DateTime.Parse(pickup_datetime) AS pickup_datetime,
        DateTime.Parse(dropoff_datetime) AS dropoff_datetime,
        Int32.Parse(passenger_count) AS passenger_count,
        Double.Parse(trip_time_in_secs) AS trip_time_in_secs,
        Double.Parse(trip_distance) AS trip_distance,
        (pickup_longitude==string.Empty ? 0: float.Parse(pickup_longitude)) AS pickup_longitude,
        (pickup_latitude==string.Empty ? 0: float.Parse(pickup_latitude)) AS pickup_latitude,
        (dropoff_longitude==string.Empty ? 0: float.Parse(dropoff_longitude)) AS dropoff_longitude,
        (dropoff_latitude==string.Empty ? 0: float.Parse(dropoff_latitude)) AS dropoff_latitude
    FROM @trip0
    WHERE medallion != "medallion";

    ////output data to ADL
    OUTPUT @trip
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_trip.csv"
    USING Outputters.Csv();

    ////Output data to blob
    OUTPUT @trip
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_trip.csv"
    USING Outputters.Csv();

마찬가지로 fare 데이터 집합에서 읽을 수 있습니다. 右键单击 Azure Data Lake Storage，可以选择在 Visual Studio 中的**Azure 门户--> 数据资源管理器**或**文件资源管理器**中查看数据。

 ![10](./media/data-lake-walkthrough/10-data-in-ADL-VS.PNG)

 ![11](./media/data-lake-walkthrough/11-data-in-ADL.PNG)

### <a name="quality"></a>데이터 품질 검사
Trip 및 fare 테이블을 읽은 후 다음과 같은 방식으로 데이터 품질 검사를 수행할 수 있습니다. 生成的 CSV 文件可以输出到 Azure Blob 存储或 Azure Data Lake Storage。

medallion 수와 medallion의 고유 번호를 찾습니다.

    ///check the number of medallions and unique number of medallions
    @trip2 =
        SELECT
        medallion,
        vendor_id,
        pickup_datetime.Month AS pickup_month
        FROM @trip;

    @ex_1 =
        SELECT
        pickup_month,
        COUNT(medallion) AS cnt_medallion,
        COUNT(DISTINCT(medallion)) AS unique_medallion
        FROM @trip2
        GROUP BY pickup_month;
        OUTPUT @ex_1
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_1.csv"
    USING Outputters.Csv();

여정(trip)이 100개가 넘는 medallion을 찾습니다.

    ///find those medallions that had more than 100 trips
    @ex_2 =
        SELECT medallion,
               COUNT(medallion) AS cnt_medallion
        FROM @trip2
        //where pickup_datetime >= "2013-01-01t00:00:00.0000000" and pickup_datetime <= "2013-04-01t00:00:00.0000000"
        GROUP BY medallion
        HAVING COUNT(medallion) > 100;
        OUTPUT @ex_2
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_2.csv"
    USING Outputters.Csv();

pickup_longitude 조건으로 유효하지 않은 레코드를 찾습니다.

    ///find those invalid records in terms of pickup_longitude
    @ex_3 =
        SELECT COUNT(medallion) AS cnt_invalid_pickup_longitude
        FROM @trip
        WHERE
        pickup_longitude <- 90 OR pickup_longitude > 90;
        OUTPUT @ex_3
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_3.csv"
    USING Outputters.Csv();

일부 변수에 대해 누락된 값을 찾습니다.

    //check missing values
    @res =
        SELECT *,
               (medallion == null? 1 : 0) AS missing_medallion
        FROM @trip;

    @trip_summary6 =
        SELECT
            vendor_id,
        SUM(missing_medallion) AS medallion_empty,
        COUNT(medallion) AS medallion_total,
        COUNT(DISTINCT(medallion)) AS medallion_total_unique
        FROM @res
        GROUP BY vendor_id;
    OUTPUT @trip_summary6
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_16.csv"
    USING Outputters.Csv();



### <a name="explore"></a>데이터 탐색
데이터를 잘 이해하기 위해 다음 스크립트를 사용하여 데이터를 탐색해 봅니다.

왕복 및 비왕복 여정의 분포를 찾습니다.

    ///tipped vs. not tipped distribution
    @tip_or_not =
        SELECT *,
               (tip_amount > 0 ? 1: 0) AS tipped
        FROM @fare;

    @ex_4 =
        SELECT tipped,
               COUNT(*) AS tip_freq
        FROM @tip_or_not
        GROUP BY tipped;
        OUTPUT @ex_4
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_4.csv"
    USING Outputters.Csv();

0, 5, 10, 20달러의 제한 값을 가진 팁 금액의 분포를 찾습니다.

    //tip class/range distribution
    @tip_class =
        SELECT *,
               (tip_amount >20? 4: (tip_amount >10? 3:(tip_amount >5 ? 2:(tip_amount > 0 ? 1: 0)))) AS tip_class
        FROM @fare;
    @ex_5 =
        SELECT tip_class,
               COUNT(*) AS tip_freq
        FROM @tip_class
        GROUP BY tip_class;
        OUTPUT @ex_5
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_5.csv"
    USING Outputters.Csv();

여정 거리의 기본 통계를 찾습니다.

    // find basic statistics for trip_distance
    @trip_summary4 =
        SELECT
            vendor_id,
            COUNT(*) AS cnt_row,
            MIN(trip_distance) AS min_trip_distance,
            MAX(trip_distance) AS max_trip_distance,
            AVG(trip_distance) AS avg_trip_distance
        FROM @trip
        GROUP BY vendor_id;
    OUTPUT @trip_summary4
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_14.csv"
    USING Outputters.Csv();

여정 거리의 백분위수를 찾습니다.

    // find percentiles of trip_distance
    @trip_summary3 =
        SELECT DISTINCT vendor_id AS vendor,
                        PERCENTILE_DISC(0.25) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
                        PERCENTILE_DISC(0.5) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
                        PERCENTILE_DISC(0.75) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc
        FROM @trip;
       // group by vendor_id;
    OUTPUT @trip_summary3
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_13.csv"
    USING Outputters.Csv();


### <a name="join"></a>trip 및 fare 테이블 조인
medallion, hack_license 및 pickup_time으로 trip 및 fare 테이블을 조인할 수 있습니다.

    //join trip and fare table

    @model_data_full =
    SELECT t.*,
    f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
    (f.tip_amount > 0 ? 1: 0) AS tipped,
    (f.tip_amount >20? 4: (f.tip_amount >10? 3:(f.tip_amount >5 ? 2:(f.tip_amount > 0 ? 1: 0)))) AS tip_class
    FROM @trip AS t JOIN  @fare AS f
    ON   (t.medallion == f.medallion AND t.hack_license == f.hack_license AND t.pickup_datetime == f.pickup_datetime)
    WHERE   (pickup_longitude != 0 AND dropoff_longitude != 0 );

    //// output to blob
    OUTPUT @model_data_full
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_full_data.csv"
    USING Outputters.Csv();

    ////output data to ADL
    OUTPUT @model_data_full
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_7_full_data.csv"
    USING Outputters.Csv();


각 승객 수 수준에 대해 레코드 수, 평균 팁 금액, 팁 금액의 분산, 왕복 여정의 백분율을 계산합니다.

    // contingency table
    @trip_summary8 =
        SELECT passenger_count,
               COUNT(*) AS cnt,
               AVG(tip_amount) AS avg_tip_amount,
               VAR(tip_amount) AS var_tip_amount,
               SUM(tipped) AS cnt_tipped,
               (float)SUM(tipped)/COUNT(*) AS pct_tipped
        FROM @model_data_full
        GROUP BY passenger_count;
        OUTPUT @trip_summary8
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_17.csv"
    USING Outputters.Csv();


### <a name="sample"></a>데이터 샘플링
먼저 조인된 테이블에서 0.1%의 데이터를 임의로 선택합니다.

    //random select 1/1000 data for modeling purpose
    @addrownumberres_randomsample =
    SELECT *,
            ROW_NUMBER() OVER() AS rownum
    FROM @model_data_full;

    @model_data_random_sample_1_1000 =
    SELECT *
    FROM @addrownumberres_randomsample
    WHERE rownum % 1000 == 0;

    OUTPUT @model_data_random_sample_1_1000
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_random_1_1000.csv"
    USING Outputters.Csv();

다음으로 이진 변수 tip_class로 계층화된 샘플링을 수행합니다.

    //stratified random select 1/1000 data for modeling purpose
    @addrownumberres_stratifiedsample =
    SELECT *,
            ROW_NUMBER() OVER(PARTITION BY tip_class) AS rownum
    FROM @model_data_full;

    @model_data_stratified_sample_1_1000 =
    SELECT *
    FROM @addrownumberres_stratifiedsample
    WHERE rownum % 1000 == 0;
    //// output to blob
    OUTPUT @model_data_stratified_sample_1_1000
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_9_stratified_1_1000.csv"
    USING Outputters.Csv();
    ////output data to ADL
    OUTPUT @model_data_stratified_sample_1_1000
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_9_stratified_1_1000.csv"
    USING Outputters.Csv();


### <a name="run"></a>U-SQL 작업 실행
编辑完脚本后，可以使用 Azure Data Lake Analytics 帐户将它们提交给服务器。 **Data Lake**, **작업 제출**을 클릭하고 **분석 계정**, **병렬 처리**를 선택하고 **제출** 단추를 클릭합니다.

 ![12](./media/data-lake-walkthrough/12-submit-USQL.PNG)

작업이 성공적으로 컴파일되면 모니터링을 위해 작업 상태가 Visual Studio에 표시됩니다. 作业完成后，您甚至可以重播作业执行过程并找出瓶颈步骤来提高您的工作效率。 또한 Azure Portal로 이동하여 U-SQL 작업의 상태를 확인할 수도 있습니다.

 ![13](./media/data-lake-walkthrough/13-USQL-running-v2.PNG)

 ![14](./media/data-lake-walkthrough/14-USQL-jobs-portal.PNG)

이제 Azure Blob Storage 또는 Azure Portal에서 출력 파일을 확인할 수 있습니다. 다음 단계에서 모델링을 위한 계층화된 샘플 데이터를 사용합니다.

 ![15](./media/data-lake-walkthrough/15-U-SQL-output-csv.PNG)

 ![16](./media/data-lake-walkthrough/16-U-SQL-output-csv-portal.PNG)

## <a name="build-and-deploy-models-in-azure-machine-learning"></a>Azure Machine Learning에서 모델 빌드 및 배포
Azure Machine Learning으로 데이터를 끌어와 빌드 및 배포하기 위한 두 가지 옵션에 대해 설명합니다.

* 첫 번째 옵션에서는 Azure Blob에 기록된 샘플링된 데이터를 사용하고(위의 **데이터 샘플링** 단계에서) 모델을 빌드하고 Azure Machine Learning에 배포하는 데 Python을 사용합니다.
* 두 번째 옵션에서는 Hive 쿼리를 사용하여 Azure Data Lake의 데이터를 직접 쿼리할 수 있습니다. 이 옵션에서는 새 HDInsight 클러스터를 만들거나 Hive 테이블이 Azure Data Lake Storage의 NY 택시 데이터를 가리키는 기존 HDInsight 클러스터를 사용해야 합니다.  다음 섹션에서는 두 옵션을 모두 설명합니다.

## <a name="option-1-use-python-to-build-and-deploy-machine-learning-models"></a>옵션 1: Python을 사용하여 기계 학습 모델 빌드 및 배포
Python을 사용하여 기계 학습 모델을 빌드 및 배포하려면 로컬 컴퓨터에서 또는 Azure Machine Learning Studio에서 Jupyter Notebook을 만듭니다. [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough)上提供的 Jupyter Notebook 包含用于浏览、可视化数据、功能设计、建模和部署的完整代码。 이 문서에서는 모델링 및 배포 단계만 보여줍니다.

### <a name="import-python-libraries"></a>Python 라이브러리 가져오기
샘플 Jupyter Notebook 또는 Python 스크립트 파일을 실행하기 위해 다음 Python 패키지가 필요합니다. Azure Machine Learning Notebook 서비스를 사용하는 경우 이러한 패키지는 미리 설치되어 있는 상태입니다.

    import pandas as pd
    from pandas import Series, DataFrame
    import numpy as np
    import matplotlib.pyplot as plt
    from time import time
    import pyodbc
    import os
    from azure.storage.blob import BlobService
    import tables
    import time
    import zipfile
    import random
    import sklearn
    from sklearn.linear_model import LogisticRegression
    from sklearn.cross_validation import train_test_split
    from sklearn import metrics
    from __future__ import division
    from sklearn import linear_model
    from azureml import services


### <a name="read-in-the-data-from-blob"></a>Blob에서 데이터 읽기
* 연결 문자열

        CONTAINERNAME = 'test1'
        STORAGEACCOUNTNAME = 'XXXXXXXXX'
        STORAGEACCOUNTKEY = 'YYYYYYYYYYYYYYYYYYYYYYYYYYYY'
        BLOBNAME = 'demo_ex_9_stratified_1_1000_copy.csv'
        blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
* 텍스트로 읽기

        t1 = time.time()
        data = blob_service.get_blob_to_text(CONTAINERNAME,BLOBNAME).split("\n")
        t2 = time.time()
        print(("It takes %s seconds to read in "+BLOBNAME) % (t2 - t1))

  ![17](./media/data-lake-walkthrough/17-python_readin_csv.PNG)
* 열 이름 추가 및 열 구분

        colnames = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime',
        'passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'payment_type', 'fare_amount', 'surcharge', 'mta_tax', 'tolls_amount',  'total_amount', 'tip_amount', 'tipped', 'tip_class', 'rownum']
        df1 = pd.DataFrame([sub.split(",") for sub in data], columns = colnames)
* 일부 열을 숫자로 변경

        cols_2_float = ['trip_time_in_secs','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'fare_amount', 'surcharge','mta_tax','tolls_amount','total_amount','tip_amount', 'passenger_count','trip_distance'
        ,'tipped','tip_class','rownum']
        for col in cols_2_float:
            df1[col] = df1[col].astype(float)

### <a name="build-machine-learning-models"></a>기계 학습 모델 빌드
여기서는 여정에서 팁을 받는지 여부를 예측하는 이진 분류 모델을 빌드합니다. Jupyter Notebook에서 다중 클래스 분류 및 회귀 모델이라는 다른 두 모델을 확인할 수 있습니다.

* 먼저 scikit-learn 모델에 사용할 수 있는 더미 변수를 만들어야 합니다.

        df1_payment_type_dummy = pd.get_dummies(df1['payment_type'], prefix='payment_type_dummy')
        df1_vendor_id_dummy = pd.get_dummies(df1['vendor_id'], prefix='vendor_id_dummy')
* 모델링을 위한 데이터 프레임 만들기

        cols_to_keep = ['tipped', 'trip_distance', 'passenger_count']
        data = df1[cols_to_keep].join([df1_payment_type_dummy,df1_vendor_id_dummy])

        X = data.iloc[:,1:]
        Y = data.tipped
* 학습 및 테스트 60-40분할

        X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.4, random_state=0)
* 학습 집합에서 로지스틱 회귀

        model = LogisticRegression()
        logit_fit = model.fit(X_train, Y_train)
        print ('Coefficients: \n', logit_fit.coef_)
        Y_train_pred = logit_fit.predict(X_train)

       ![c1](./media/data-lake-walkthrough/c1-py-logit-coefficient.PNG)
* 테스트 데이터 집합 점수 매기기

        Y_test_pred = logit_fit.predict(X_test)
* 평가 메트릭 계산

        fpr_train, tpr_train, thresholds_train = metrics.roc_curve(Y_train, Y_train_pred)
        print fpr_train, tpr_train, thresholds_train

        fpr_test, tpr_test, thresholds_test = metrics.roc_curve(Y_test, Y_test_pred)
        print fpr_test, tpr_test, thresholds_test

        #AUC
        print metrics.auc(fpr_train,tpr_train)
        print metrics.auc(fpr_test,tpr_test)

        #Confusion Matrix
        print metrics.confusion_matrix(Y_train,Y_train_pred)
        print metrics.confusion_matrix(Y_test,Y_test_pred)

       ![c2](./media/data-lake-walkthrough/c2-py-logit-evaluation.PNG)

### <a name="build-web-service-api-and-consume-it-in-python"></a>웹 서비스 API 구축 및 Python에서 사용
기계 학습 모델을 빌드한 후 운영하려고 합니다. 여기서는 예로 이진 로지스틱 모델을 사용합니다. 请确保本地计算机中的 scikit-learn 版本为0.15.1 （Azure 机器学习 Studio 已至少在此版本中）。

* 从 Azure 机器学习 Studio （经典）设置查找工作区凭据。 Azure Machine Learning 스튜디오에서 **설정** --> **이름** --> **권한 부여 토큰**을 클릭합니다.

    ![c3](./media/data-lake-walkthrough/c3-workspace-id.PNG)

        workspaceid = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
        auth_token = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'

* 웹 서비스 만들기

        @services.publish(workspaceid, auth_token)
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float, payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(int) #0, or 1
        def predictNYCTAXI(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            inputArray = [trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH, payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS]
            return logit_fit.predict(inputArray)
* 웹 서비스 자격 증명 가져오기

        url = predictNYCTAXI.service.url
        api_key =  predictNYCTAXI.service.api_key

        print url
        print api_key

        @services.service(url, api_key)
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float,payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(float)
        def NYCTAXIPredictor(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            pass
* 웹 서비스 API를 호출합니다. 通常，请在上一步之后等待5-10 秒。

        NYCTAXIPredictor(1,2,1,0,0,0,0,0,1)

       ![c4](./media/data-lake-walkthrough/c4-call-API.PNG)

## <a name="option-2-create-and-deploy-models-directly-in-azure-machine-learning"></a>옵션 2: Azure Machine Learning에서 모델을 만들고 직접 배포
Azure 机器学习 Studio （经典）可以直接从 Azure Data Lake Storage 读取数据，然后将其用于创建和部署模型。 此方法使用指向 Azure Data Lake Storage 的 Hive 表。 需要为 Hive 表预配单独的 Azure HDInsight 群集。 

### <a name="create-an-hdinsight-linux-cluster"></a>HDInsight Linux 클러스터 만들기
[Azure Portal](https://portal.azure.com)에서 HDInsight 클러스터(Linux)를 만듭니다. 有关详细信息，请参阅[使用 Azure 门户创建具有 Data Lake Store 的 hdinsight 群集](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)中的**创建具有访问权限的 hdinsight 群集 Azure Data Lake Storage**部分。

 ![18](./media/data-lake-walkthrough/18-create_HDI_cluster.PNG)

### <a name="create-hive-table-in-hdinsight"></a>HDInsight에서 Hive 테이블 만들기
现在，使用上一步中 Azure Data Lake Storage 中存储的数据，创建要在 HDInsight 群集中的 Azure 机器学习 Studio （经典）中使用的 Hive 表。 방금 만든 HDInsight 클러스터로 이동합니다. 单击 **"设置**" -->  ** --> 群集 AAD 标识** --> **ADLS 访问**"的"**属性**"，确保 Azure Data Lake Storage 帐户添加到具有" 读取 "、" 写入 "和" 执行 "权限的列表中。

 ![19](./media/data-lake-walkthrough/19-HDI-cluster-add-ADLS.PNG)

그런 다음 **설정** 단추 옆의 **대시보드**를 클릭하면 팝업 창이 나타납니다. 페이지의 오른쪽 위 모서리에서 **Hive 보기**를 클릭하면 **쿼리 편집기**가 표시됩니다.

 ![20](./media/data-lake-walkthrough/20-HDI-dashboard.PNG)

 ![21](./media/data-lake-walkthrough/21-Hive-Query-Editor-v2.PNG)

다음 Hive 스크립트를 붙여 넣어 테이블을 만듭니다. 数据源的位置以这种方式 Azure Data Lake Storage 引用中： **adl：/data_lake_store_name/azuredatalakestore： 443/folder_name/file_name**。

    CREATE EXTERNAL TABLE nyc_stratified_sample
    (
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count string,
        trip_time_in_secs string,
        trip_distance string,
        pickup_longitude string,
        pickup_latitude string,
        dropoff_longitude string,
        dropoff_latitude string,
      payment_type string,
      fare_amount string,
      surcharge string,
      mta_tax string,
      tolls_amount string,
      total_amount string,
      tip_amount string,
      tipped string,
      tip_class string,
      rownum string
      )
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    LOCATION 'adl://data_lake_storage_name.azuredatalakestore.net:443/nyctaxi_folder/demo_ex_9_stratified_1_1000_copy.csv';


查询完成后，应会看到如下所示的结果：

 ![22](./media/data-lake-walkthrough/22-Hive-Query-results.PNG)

### <a name="build-and-deploy-models-in-azure-machine-learning-studio"></a>Azure Machine Learning Studio에서 모델 빌드 및 배포
이제 팁이 Azure Machine Learning에서 유료인지 여부를 예측하는 모델을 빌드 및 배포할 준비가 되었습니다. 계층화된 샘플 데이터는 이 이진 분류(팁인지 아닌지) 문제에서 사용할 준비가 되었습니다. 다중 클래스 분류(tip_class) 및 회귀(tip_amount)를 사용하는 예측 모델은 Azure Machine Learning Studio를 사용하여 빌드 및 배포할 수도 있지만 여기에서는 이진 분류 모델을 사용하여 사례를 처리하는 방법만 살펴보겠습니다.

1. 使用 "**数据输入和输出**" 部分中的 "**导入数据**" 模块，将数据导入 Azure 机器学习 Studio （经典）。 자세한 내용은 [데이터 가져오기](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) 참조 페이지를 참조하세요.
2. **속성** 패널에서 **데이터 원본**으로 **Hive 쿼리**를 선택합니다.
3. 다음 Hive 스크립트를 **Hive 데이터베이스 쿼리** 편집기에 붙여 넣습니다.

        select * from nyc_stratified_sample;
4. 输入 HDInsight 群集的 URI （可在 Azure 门户中找到此 URI）、Hadoop 凭据、输出数据的位置，以及 Azure 存储帐户名称/密钥/容器名称。

   ![23](./media/data-lake-walkthrough/23-reader-module-v3.PNG)

다음 그림은 Hive 테이블에서 데이터를 읽는 이진 분류 실험 예제입니다.

 ![24](./media/data-lake-walkthrough/24-AML-exp.PNG)

실험을 만든 후 **웹 서비스 설정** --> **예측 웹 서비스**를 클릭합니다.

 ![25](./media/data-lake-walkthrough/25-AML-exp-deploy.PNG)

자동으로 생성된 점수 매기기 실험을 실행하고 완료되었으면 **웹 서비스 배포**

 ![26](./media/data-lake-walkthrough/26-AML-exp-deploy-web.PNG)

웹 서비스 대시보드가 바로 표시됩니다.

 ![27](./media/data-lake-walkthrough/27-AML-web-api.PNG)

## <a name="summary"></a>요약
完成本演练后，你已经创建了一个用于在 Azure Data Lake 中生成可缩放的端到端解决方案的数据科学环境。 이 환경은 모델 학습을 통한 데이터 획득부터 웹 서비스로 모델 배포에 이르는 데이터 과학 프로세스의 정식 단계를 통해 가져온 대형 공용 데이터 세트를 분석하는 데 사용되었습니다. 使用了 U-SQL 处理、浏览和采样数据。 Python 和 Hive 与 Azure 机器学习 Studio （经典）结合使用，可生成和部署预测模型。

## <a name="whats-next"></a>다음은 무엇일까요?
[TDSP(팀 데이터 과학 프로세스)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) 에 대한 학습 경로는 고급 분석 프로세스의 각 단계를 설명하는 토픽에 대한 링크를 제공합니다. 다양한 예측 분석 시나리오에서 리소스 및 서비스를 사용하는 방법을 소개하는 [팀 데이터 과학 프로세스 연습](walkthroughs.md) 페이지에는 일련의 연습 과정이 항목별로 정리되어 있습니다.

* [실행 중인 팀 데이터 과학 프로세스: SQL Data Warehouse 사용](sqldw-walkthrough.md)
* [실행 중인 팀 데이터 과학 프로세스: HDInsight Hadoop 클러스터 사용](hive-walkthrough.md)
* [팀 데이터 과학 프로세스: SQL Server 사용](sql-walkthrough.md)
* [Azure HDInsight에서 Spark를 사용하는 데이터 과학 프로세스 개요](spark-overview.md)
