---
title: 在 1 TB 数据集上使用 Azure HDInsight Hadoop 群集-团队数据科学流程
description: HDInsight Hadoop 클러스터를 사용하는 엔드투엔드 시나리오에 팀 데이터 과학 프로세스를 사용하여 공개적으로 사용 가능한 1TB 데이터 세트로 모델을 빌드 및 배포합니다.
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
ms.openlocfilehash: 04bc29fb8a89f6e863f7c009e5299d1c702bf976
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721398"
---
# <a name="the-team-data-science-process-in-action---using-an-azure-hdinsight-hadoop-cluster-on-a-1-tb-dataset"></a>操作中的团队数据科学过程-使用 1 TB 数据集上的 Azure HDInsight Hadoop 群集

이 연습에서는 [Azure HDInsight Hadoop 클러스터](https://azure.microsoft.com/services/hdinsight/)를 사용하는 엔드투엔드 시나리오에서 팀 데이터 과학 프로세스(Team Data Science Process)를 사용하여 공개적으로 사용 가능한 [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) 데이터 세트 중 하나에서 데이터를 저장, 탐색, 기능 설계, 다운 샘플링하는 방법을 설명합니다. 본 문서에서는 Azure Machine Learning을 사용하여 이 데이터에 대한 이진 분류 모델을 빌드합니다. 또한 이러한 모델 중 하나를 웹 서비스로 게시하는 방법을 보여줍니다.

이 연습에서 IPython 노트북을 사용하여 작업을 수행할 수도 있습니다. 이 방법을 사용하려면 [Hive ODBC 연결을 사용하여 Criteo 연습](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) 토픽을 참조해야 합니다.

## <a name="dataset"></a>데이터 세트 설명
Criteo 数据是一个单击预测数据集，它是 370 GB （未压缩的） gzip 压缩 TSV 文件（超过 1.3 TB，其中包含超过4300000000个记录）。 [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/)에서 사용할 수 있는 24일간의 클릭 데이터를 가져옵니다. 데이터 과학자의 편의를 위해 실험에 사용할 수 있는 데이터의 압축을 해제해 두었습니다.

이 데이터 세트의 각 레코드에는 40개의 열이 있습니다.

* 첫 번째 열은 사용자가 **광고** 를 클릭했는지(값 1) 또는 클릭하지 않았는지(값 0)를 나타내는 레이블 열입니다.
* 다음 13개의 열은 숫자입니다.
* 마지막 26개는 범주 열입니다.

열은 익명으로 처리되고 일련의 열거된 이름, 즉 "Col1"(레이블 열)부터 'Col40"(마지막 범주 열)까지를 사용합니다.

다음은 이 데이터 세트에서 발췌한 두 관찰(행)의 처음 20개 열입니다.

    Col1    Col2    Col3    Col4    Col5    Col6    Col7    Col8    Col9    Col10    Col11    Col12    Col13    Col14    Col15            Col16            Col17            Col18            Col19        Col20

    0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb
    0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb

이 데이터 세트의 숫자 열과 범주 열 모두에 누락된 값이 있습니다. 누락된 값을 처리하는 간단한 방법을 설명합니다. 데이터에 대한 추가 정보는 Hive 테이블에 저장할 때 살펴봅니다.

**定义：** *点击链接速率（CTR）：* 此指标是在数据中单击的百分比。 이 Criteo 데이터 세트의 CTR은 약 3.3% 또는 0.033입니다.

## <a name="mltasks"></a>예측 작업의 예제
이 연습에서는 두 가지 샘플 예측 문제를 처리합니다.

1. **이진 분류**: 사용자가 광고를 클릭했는지 여부를 예측합니다.

   * 클래스 0: 클릭 안 함
   * 클래스 1: 클릭
2. **회귀**: 사용자 기능에서 광고 클릭 확률을 예측합니다.

## <a name="setup"></a>데이터 과학용 HDInsight Hadoop 클러스터 설정
**注意：** 此步骤通常是一种**管理**任务。

다음 3단계에 따라 HDInsight 클러스터를 사용하여 예측 분석 솔루션을 빌드하기 위한 Azure 데이터 과학 환경을 설정합니다.

1. [Storage 계정 만들기](../../storage/common/storage-account-create.md):이 Storage 계정은 Azure Blob Storage에 데이터를 저장하는 데 사용됩니다. HDInsight 클러스터에 사용되는 데이터는 여기에 저장됩니다.
2. [데이터 과학용 Azure HDInsight Hadoop 클러스터 사용자 지정](customize-hadoop-cluster.md):이 단계에서는 모든 노드에 설치된 64비트 Anaconda Python 2.7을 사용하여 Azure HDInsight Hadoop 클러스터를 만듭니다. HDInsight 클러스터를 사용자 지정할 때 두 가지 중요한 단계(이 항목에 설명)를 완료해야 합니다.

   * 创建时，将在步骤1中创建的存储帐户链接到 HDInsight 群集。 이 스토리지 계정은 클러스터 내에서 처리할 수 있는 데이터에 액세스하는 데 사용됩니다.
   * 创建群集的头节点后，启用对其头节点的远程访问。 请记住在此处指定的远程访问凭据（不同于在创建群集时指定的凭据）：完成以下过程。
3. [创建 Azure 机器学习 Studio （经典）工作区](../studio/create-workspace.md)：此 Azure 机器学习工作区用于在 HDInsight 群集上进行初始数据浏览和关闭采样后构建机器学习模型。

## <a name="getdata"></a>공용 원본에서 데이터 가져오기 및 사용
링크를 클릭하고 사용 약관에 동의한 후 이름을 제공하여 [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) 데이터 세트에 액세스할 수 있습니다. 快照如下所示：

![Criteo 조건 동의](./media/hive-criteo-walkthrough/hLxfI2E.png)

**다운로드 계속하기**를 클릭하여 데이터 세트 및 사용성을 읽어봅니다.

数据驻留在[Azure blob 存储](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)位置： wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/。 "wasb"는 Azure Blob Storage 위치를 나타냅니다.

1. 此 Azure blob 存储中的数据由三个已解压缩数据的子文件夹组成。

   1. 하위 폴더 *raw/count/* 는 처음 21일간의 데이터(day\_00~day\_20)를 포함합니다.
   2. 하위 폴더 *raw/train/* 은 단일 날짜의 데이터(day\_21)를 포함합니다.
   3. 하위 폴더 *raw/test/* 는 2일간의 데이터(day\_22 및 day\_23)를 포함합니다.
2. 原始的 gzip 数据也在主文件夹*raw/* as day_NN 中提供，其中 NN 从00到23。

로컬 다운로드가 필요 없는 이 데이터를 액세스, 탐색 및 모델링하는 다른 방법은 이 연습의 뒷부분에서 Hive 테이블을 만들 때 설명합니다.

## <a name="login"></a>클러스터 헤드 노드에 로그인
클러스터의 헤드 노드에 로그인하려면 [Azure Portal](https://ms.portal.azure.com) 을 사용하여 클러스터를 찾습니다. 왼쪽에 있는 HDInsight 코끼리 아이콘을 클릭하고 클러스터 이름을 두 번 클릭합니다. 导航到 "**配置**" 选项卡，双击页面底部的 "连接" 图标，并在出现提示时输入您的远程访问凭据，并转到群集的头节点。

典型的第一次群集头节点登录如下所示：

![클러스터에 로그인](./media/hive-criteo-walkthrough/Yys9Vvm.png)

왼쪽에는 데이터 탐색에 사용될 작업 도구인 "Hadoop 명령줄"이 있습니다. 두 가지 유용한 URL인 "Hadoop Yarn 상태" 및 "Hadoop 이름 노드"도 있습니다. Yarn Status URL은 작업 진행률을 표시하고, Name Node URL은 클러스터 구성에 대한 세부 정보를 표시합니다.

설정을 완료했으므로 이제 이 연습의 첫 번째 부분인 Hive를 사용하여 데이터 탐색 및 Azure Machine Learning용 데이터 준비를 시작할 수 있습니다.

## <a name="hive-db-tables"></a> Hive 데이터베이스 및 테이블 만들기
Criteo 데이터 세트에 대한 Hive 테이블을 만들려면 헤드 노드의 바탕 화면에서 ***Hadoop 명령줄***을 열고 명령을 입력하여 Hive 디렉터리를 입력합니다.

    cd %hive_home%\bin

> [!NOTE]
> 이 연습의 모든 Hive 명령은 Hive bin/ 디렉터리 프롬프트에서 실행합니다. 경로 문제가 자동으로 해결됩니다. "Hive 디렉터리 프롬프트", "Hive bin/ 디렉터리 프롬프트" 및 "Hadoop 명령줄"이라는 용어는 같은 의미로 사용할 수 있습니다.
>
> [!NOTE]
> Hive 쿼리를 실행하기 위해 항상 다음 명령을 사용할 수 있습니다.
>
>

        cd %hive_home%\bin
        hive

"hive >" 기호와 함께 Hive REPL이 표시되면 쿼리를 잘라내고 붙여넣어 실행하면 됩니다.

以下代码将创建一个数据库 "criteo"，然后生成四个表：

* *개수 생성을 위한 테이블* 한 개(day\_00~day\_20의 날짜를 기반으로 작성)
* *학습 데이터 세트로 사용하기 위한 테이블* 한 개(day\_21의 날짜를 기반으로 작성)
* *테스트 데이터 세트로 사용하기 위한 테이블* 두 개(각각 day\_22와 day\_23의 날짜를 기반으로 작성)

날짜 중 하나가 휴일이기 때문에 두 개의 다른 테이블로 테스트 데이터 세트를 분할합니다. 목표는 모델이 클릭 속도에서 공휴일과 평일 간의 차이점을 감지할 수 있는지를 확인하는 것입니다.

편의를 위해 다음에 [sample&#95;hive&#95;create&#95;criteo&#95;database&#95;and&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) 스크립트가 표시되어 있습니다.

    CREATE DATABASE IF NOT EXISTS criteo;
    DROP TABLE IF EXISTS criteo.criteo_count;
    CREATE TABLE criteo.criteo_count (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/count';

    DROP TABLE IF EXISTS criteo.criteo_train;
    CREATE TABLE criteo.criteo_train (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/train';

    DROP TABLE IF EXISTS criteo.criteo_test_day_22;
    CREATE TABLE criteo.criteo_test_day_22 (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_22';

    DROP TABLE IF EXISTS criteo.criteo_test_day_23;
    CREATE TABLE criteo.criteo_test_day_23 (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_23';

所有这些表都是外部的，因此可以指向其 Azure Blob 存储（wasb）位置。

**모든 Hive 쿼리를 실행할 수 있는 두 가지 방법이 있습니다.**

* **使用 HIVE 复制命令行**：第一种是发出 "hive" 命令，并在 Hive 复制命令行复制并粘贴查询：

        cd %hive_home%\bin
        hive

     现在，在复制命令行上，剪切并粘贴查询执行。
* **将查询保存到文件并执行命令**：第二种是将查询保存到 "hql" 文件（[示例&#95;hive&#95;创建&#95;criteo&#95;数据库&#95;和&#95;表](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)），然后发出以下命令来执行查询：

        hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql

### <a name="confirm-database-and-table-creation"></a>데이터베이스 및 테이블 만들기 확인
다음으로 Hive bin/ 디렉터리 프롬프트에서 다음 명령을 실행하여 데이터베이스 생성을 확인합니다.

        hive -e "show databases;"

다음 출력이 표시됩니다.

        criteo
        default
        Time taken: 1.25 seconds, Fetched: 2 row(s)

이는 "criteo"라는 새 데이터베이스의 생성을 확인합니다.

생성된 테이블을 확인하려면 Hive bin/ 디렉터리 프롬프트에서 다음 명령을 실행하기만 하면 됩니다.

        hive -e "show tables in criteo;"

그러면 다음 출력이 표시됩니다.

        criteo_count
        criteo_test_day_22
        criteo_test_day_23
        criteo_train
        Time taken: 1.437 seconds, Fetched: 4 row(s)

## <a name="exploration"></a> Hive에서 데이터 탐색
이제 Hive에서 몇 가지 기본 데이터 탐색을 수행할 준비가 완료되었습니다. 먼저 학습 및 테스트 데이터 테이블에서 예제 수를 계산합니다.

### <a name="number-of-train-examples"></a>train 예제 수
[sample&#95;hive&#95;count&#95;train&#95;table&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql)의 내용은 다음과 같습니다.

        SELECT COUNT(*) FROM criteo.criteo_train;

다음과 같은 결과가 산출됩니다.

        192215183
        Time taken: 264.154 seconds, Fetched: 1 row(s)

또는 Hive bin/ 디렉터리 프롬프트에서 다음 명령을 실행할 수도 있습니다.

        hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql

### <a name="number-of-test-examples-in-the-two-test-datasets"></a>두 가지 테스트 데이터 세트의 test 예제 수
이제 두 가지 테스트 데이터 세트의 예제 수를 계산합니다. [sample&#95;hive&#95;count&#95;criteo&#95;test&#95;day&#95;22&#95;table&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql)의 내용은 다음과 같습니다.

        SELECT COUNT(*) FROM criteo.criteo_test_day_22;

다음과 같은 결과가 산출됩니다.

        189747893
        Time taken: 267.968 seconds, Fetched: 1 row(s)

일반적으로 다음 명령을 실행하여 Hive bin/ 디렉터리 프롬프트에서 스크립트를 호출할 수도 있습니다.

        hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql

마지막으로 day\_23을 기준으로 테스트 데이터 세트의 데이터 예제 수를 알아봅니다.

执行此操作的命令类似于所示的命令（请参阅[示例&#95;hive&#95;count&#95;criteo&#95;test&#95;day&#95;23&#95;举例](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)）：

        SELECT COUNT(*) FROM criteo.criteo_test_day_23;

다음 출력이 표시됩니다.

        178274637
        Time taken: 253.089 seconds, Fetched: 1 row(s)

### <a name="label-distribution-in-the-train-dataset"></a>학습 데이터 세트의 레이블 분포
학습 데이터 세트의 레이블 분포를 알아보겠습니다. 여기에 사용되는 [sample&#95;hive&#95;criteo&#95;label&#95;distribution&#95;train&#95;table.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql)의 내용은 다음과 같습니다.

        SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;

다음과 같은 레이블 분포가 산출됩니다.

        1       6292903
        0       185922280
        Time taken: 459.435 seconds, Fetched: 2 row(s)

正值标签的百分比约为3.3% （与原始数据集一致）。

### <a name="histogram-distributions-of-some-numeric-variables-in-the-train-dataset"></a>학습 데이터 세트의 일부 숫자 변수에 대한 히스토그램 분포
Hive의 네이티브 "histogram\_numeric" 함수를 사용하여 숫자 변수의 배포가 어떤지 확인할 수 있습니다. [sample&#95;hive&#95;criteo&#95;histogram&#95;numeric.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql)의 내용은 다음과 같습니다.

        SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM
            (SELECT
            histogram_numeric(col2, 20) as col2_hist
            FROM
            criteo.criteo_train
            ) a
            LATERAL VIEW explode(col2_hist) exploded_table as hist;

다음과 같은 결과가 산출됩니다.

        26      155878415
        2606    92753
        6755    22086
        11202   6922
        14432   4163
        17815   2488
        21072   1901
        24113   1283
        27429   1225
        30818   906
        34512   723
        38026   387
        41007   290
        43417   312
        45797   571
        49819   428
        53505   328
        56853   527
        61004   160
        65510   3446
        Time taken: 317.851 seconds, Fetched: 20 row(s)

Hive의 LATERAL VIEW - explode 조합은 일반 목록 대신 SQL과 유사한 출력을 생성합니다. 在此表中，第一列对应于 bin 中心和第二列。

### <a name="approximate-percentiles-of-some-numeric-variables-in-the-train-dataset"></a>학습 데이터 세트의 일부 숫자 변수에 대한 백분위수 근사치
숫자 변수와 함께 백분위수 근사치도 알아보겠습니다. 여기에는 Hive의 기본 "percentile\_approx"가 사용됩니다. [sample&#95;hive&#95;criteo&#95;approximate&#95;percentiles.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql)의 내용은 다음과 같습니다.

        SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;

다음과 같은 결과가 산출됩니다.

        1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
        Time taken: 564.953 seconds, Fetched: 1 row(s)

일반적으로 백분위수 분포는 숫자 변수의 히스토그램 분포와 밀접한 관련이 있습니다.

### <a name="find-number-of-unique-values-for-some-categorical-columns-in-the-train-dataset"></a>학습 데이터 세트의 일부 범주 열에 대한 고유 값 수 확인
데이터 탐색을 계속하면서 일부 범주 열에서 고유한 값의 수를 확인합니다. 여기에 사용되는 [sample&#95;hive&#95;criteo&#95;unique&#95;values&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql)의 내용은 다음과 같습니다.

        SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;

다음과 같은 결과가 산출됩니다.

        19011825
        Time taken: 448.116 seconds, Fetched: 1 row(s)

Col15 具有19M 的唯一值！ "one-hot 인코딩"과 같은 네이티브 기술을 사용하여 이러한 고차원 범주 변수를 인코딩할 수는 없습니다. 따라서 여기에서는 이 문제를 효과적으로 해결하는 [통계로 알아보기](https://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx)라는 강력한 기술을 설명하고 알아봅니다.

마지막으로 일부 다른 범주 열에 대한 고유 값의 수도 알아봅니다. [sample&#95;hive&#95;criteo&#95;unique&#95;values&#95;multiple&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql)의 내용은 다음과 같습니다.

        SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)),
        COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
        FROM criteo.criteo_train;

다음과 같은 결과가 산출됩니다.

        30935   15200   7349    20067   3
        Time taken: 1933.883 seconds, Fetched: 1 row(s)

Col20을 제외하고 다른 모든 열에도 많은 고유 값이 있습니다.

### <a name="co-occurrence-counts-of-pairs-of-categorical-variables-in-the-train-dataset"></a>train 데이터 세트의 범주 변수 쌍에 대한 동시 발생 수

分类变量对的计数分布也是相关的。 [sample&#95;hive&#95;criteo&#95;paired&#95;categorical&#95;counts.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql)의 코드를 사용하여 확인할 수 있습니다.

        SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;

여기에서는 발생별 개수의 역순으로 상위 15개를 확인합니다. 다음 출력이 표시됩니다.

        ad98e872        cea68cd3        8964458
        ad98e872        3dbb483e        8444762
        ad98e872        43ced263        3082503
        ad98e872        420acc05        2694489
        ad98e872        ac4c5591        2559535
        ad98e872        fb1e95da        2227216
        ad98e872        8af1edc8        1794955
        ad98e872        e56937ee        1643550
        ad98e872        d1fade1c        1348719
        ad98e872        977b4431        1115528
        e5f3fd8d        a15d1051        959252
        ad98e872        dd86c04a        872975
        349b3fec        a52ef97d        821062
        e5f3fd8d        a0aaffa6        792250
        265366bf        6f5c7c41        782142
        Time taken: 560.22 seconds, Fetched: 15 row(s)

## <a name="downsample"></a> Azure 기계 학습에 대한 데이터 세트 다운 샘플링
데이터 세트를 탐색하고 모든 변수(조합 포함)에 대해 이 형식의 탐색을 수행하는 방법을 살펴보았으므로 이제 Azure Machine Learning에서 모델을 빌드할 수 있도록 데이터 세트를 다운 샘플링합니다. 문제의 중점은 지정된 예제 특성 집합(Col2~Col40의 기능 값)에 대해 Col1이 0(클릭 안 함)인지 1(클릭)인지 예측하는 것입니다.

학습 및 테스트 데이터 세트를 원래 크기의 1%로 다운 샘플링하려면 Hive의 네이티브 RAND() 함수를 사용합니다. [sample&#95;hive&#95;criteo&#95;downsample&#95;train&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) 스크립트는 학습 데이터 세트에 대해 이 작업을 수행합니다.

        CREATE TABLE criteo.criteo_train_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        ---Now downsample and store in this table

        INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;

다음과 같은 결과가 산출됩니다.

        Time taken: 12.22 seconds
        Time taken: 298.98 seconds

[sample&#95;hive&#95;criteo&#95;downsample&#95;test&#95;day&#95;22&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) 스크립트는 테스트 데이터 day\_22에 대해 이 작업을 수행합니다.

        --- Now for test data (day_22)

        CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;

다음과 같은 결과가 산출됩니다.

        Time taken: 1.22 seconds
        Time taken: 317.66 seconds


마지막으로 [sample&#95;hive&#95;criteo&#95;downsample&#95;test&#95;day&#95;23&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) 스크립트는 test 데이터 day\_23에 대해 이 작업을 수행합니다.

        --- Finally test data day_23
        CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 srical feature; tring)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;

다음과 같은 결과가 산출됩니다.

        Time taken: 1.86 seconds
        Time taken: 300.02 seconds

이제 다운 샘플링한 학습 및 테스트 데이터 세트를 사용하여 Azure Machine Learning에서 모델을 빌드할 준비가 완료되었습니다.

Azure Machine Learning을 계속 진행하기 전에 마지막 중요한 구성 요소는 통계(Count) 테이블과 관련됩니다. 在下一节中，将在下一节中讨论计数表。

## <a name="count"></a> count 테이블에 대한 간략한 설명
正如您所看到的，有几个分类变量的维数很高。 이 연습에서는 이러한 변수를 효율적이고 강력한 방식으로 인코딩하는 [통계로 알아보기](https://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx)라는 강력한 기술을 설명합니다. 이 기술에 대한 자세한 내용은 제공된 링크에서 확인할 수 있습니다.

>[!NOTE]
>이 연습에서는 통계 테이블을 사용하여 고차원 범주 기능의 압축된 표현을 생성하는 데 중점을 둡니다. 이 기술 외에도 범주 기능을 인코딩하는 여러 기술이 있습니다. 다른 기술에 대한 자세한 내용은 [one-hot-encoding](https://en.wikipedia.org/wiki/One-hot) 및 [기능 해싱](https://en.wikipedia.org/wiki/Feature_hashing)을 참조하세요.
>

통계 데이터에 대한 통계 테이블을 작성하려면 raw/count 폴더의 데이터를 사용합니다. 모델링 섹션에서는 처음부터 범주 기능에 이러한 통계 테이블을 작성하는 방법 또는 미리 작성된 통계 테이블을 사용하여 탐색하는 방법을 사용자에게 보여줍니다. 다음에서 "미리 작성된 통계 테이블"을 참조한다는 것은 Microsoft에서 제공한 통계 테이블을 사용하는 것을 의미합니다. 이러한 테이블에 액세스하는 방법에 대한 자세한 지침은 다음 섹션에 제공됩니다.

## <a name="aml"></a> Azure 기계 학습에서 모델 빌드
Azure Machine Learning의 모델 빌드 프로세스는 다음 단계를 따릅니다.

1. [Hive 테이블에서 Azure Machine Learning으로 데이터 가져오기](#step1)
2. [创建试验：清理数据并使其成为包含计数表的功能](#step2)
3. [모델 빌드, 학습 및 점수 매기기](#step3)
4. [모델 평가](#step4)
5. [모델을 웹 서비스로 게시](#step5)

이제 Azure Machine Learning Studio에서 모델을 빌드할 준비가 완료되었습니다. 다운 샘플링한 데이터는 클러스터에 Hive 테이블로 저장됩니다. Azure Machine Learning **데이터 가져오기** 모듈을 사용하여 이 데이터를 읽습니다. 이 클러스터의 스토리지 계정에 액세스하는 데 사용되는 자격 증명은 뒤에 제공됩니다.

### <a name="step1"></a> 1단계: Import Data 모듈을 사용하여 Hive 테이블에서 Azure Machine Learning으로 데이터를 가져와 기계 학습 실험용으로 선택
**+새로 만들기** -> **실험** -> **빈 실험**을 선택하여 시작합니다. 그런 다음 왼쪽 맨 위의 **검색** 상자에서 "Import Data"를 검색합니다. 실험 캔버스(화면 중간 부분)에 **Import Data** 모듈을 끌어다 놓고 데이터에 액세스하기 위해 모듈을 사용합니다.

Hive 테이블에서 데이터를 가져오는 동안의 **Import Data** 모양은 다음과 같습니다.

![데이터 가져오기로 데이터 가져오기](./media/hive-criteo-walkthrough/i3zRaoj.png)

**Import Data** 모듈의 경우 그래픽에서 제공되는 매개 변수의 값은 사용자가 제공해야 하는 값 종류에 해당하는 예입니다. 다음은 **Import Data** 모듈에 대해 설정된 매개 변수를 입력하는 방법에 대한 일반적인 지침입니다.

1. **데이터 원본**
2. **Hive 데이터베이스 쿼리** 상자에 SELECT * FROM <your\_database\_name.your\_table\_name> 정도만 입력합니다.
3. **Hcatalog 서버 URI**: 클러스터가 "abc"인 경우 간단하게 https://abc.azurehdinsight.net 입니다.
4. **Hadoop user account name**(Hadoop 사용자 계정 이름): 클러스터를 지정할 때 선택한 사용자 이름입니다. (원격 액세스 사용자 이름이 아님).
5. **Hadoop user account password**(Hadoop 사용자 계정 암호): 클러스터를 지정할 때 선택한 사용자 이름에 대한 암호입니다. (원격 액세스 암호가 아님).
6. **Location of output data**(출력 데이터 위치): "Azure"를 선택합니다.
7. **Azure 存储帐户名称**：与群集关联的存储帐户
8. **Azure 存储帐户密钥**：与群集关联的存储帐户的密钥。
9. **Azure container name**(Azure 컨테이너 이름): 클러스터 이름 "abc"의 경우 일반적으로 단순히 "abc"입니다.

**Import Data** 에서 데이터 가져오기를 완료하면(모듈에 녹색 틱이 표시됨) 이 데이터를 데이터 세트(선택한 이름 사용)로 저장합니다. 다음과 같이 표시됩니다.

![데이터 가져오기로 데이터 저장](./media/hive-criteo-walkthrough/oxM73Np.png)

**Import Data** 모듈의 출력 포트를 마우스 오른쪽 단추로 클릭합니다. **데이터 세트로 저장** 옵션 및 **시각화** 옵션이 표시됩니다. **Visualize** 옵션을 클릭하면 몇 가지 요약 통계에 유용한 오른쪽 패널과 함께 100개의 데이터 행이 표시됩니다. 데이터를 저장하려면 **Save as dataset** 을 클릭하고 지침을 따릅니다.

기계 학습 실험에서 사용하기 위해 저장된 데이터 세트를 선택하려면 다음 그림에 표시된 **검색** 상자를 사용하여 데이터 세트를 찾습니다. 그런 다음, 데이터 세트의 이름을 부분적으로 입력하여 데이터 세트에 액세스하고 주 패널로 끌어옵니다. 주 패널에 끌어다 놓으면 기계 학습 모델링에 사용하도록 선택됩니다.

![将数据集拖到主面板上](./media/hive-criteo-walkthrough/cl5tpGw.png)

> [!NOTE]
> 학습 및 테스트 데이터 세트 모두에 대해 이를 수행합니다. 또한 데이터베이스 이름 및 이 목적으로 지정한 테이블 이름을 사용해야 합니다. 그림에서 사용한 값은 오직 예제용입니다.\*\*
>
>

### <a name="step2"></a>步骤2：在 Azure 机器学习中创建实验，以预测单击/不单击
我们的 Azure 机器学习 Studio （经典）试验如下所示：

![기계 학습 실험](./media/hive-criteo-walkthrough/xRpVfrY.png)

이제 이 실험의 주요 구성 요소를 검사합니다. 首先将已保存的训练和测试数据集拖到试验画布上。

#### <a name="clean-missing-data"></a>누락 데이터 정리
**누락된 데이터 처리** 모듈은 이름에서 알 수 있듯이 사용자가 지정할 수 있는 방법으로 누락 데이터를 정리합니다. 이 모듈을 다음과 같습니다.

![누락 데이터 정리](./media/hive-criteo-walkthrough/0ycXod6.png)

在此处，选择将所有缺失值替换为0。 모듈의 드롭다운에서 확인할 수 있는 다른 옵션도 있습니다.

#### <a name="feature-engineering-on-the-data"></a>데이터에 대한 기능 엔지니어링
큰 데이터 세트의 범주 기능 중 일부에는 수백 만개의 고유 값이 있을 수 있습니다. 원 핫(one-hot) 인코딩과 같은 미숙한 방법을 사용하여 이러한 고차원 범주 기능을 나타내는 것은 전적으로 불가능합니다. 이 연습에서는 이러한 고차원 범주 변수를 압축해서 표현하기 위해 기본 제공 Azure Machine Learning 모듈로 통계 기능을 사용하는 방법을 보여줍니다. 最终结果是更小的模型大小、更快的定型时间和与使用其他技术相当的性能指标。

##### <a name="building-counting-transforms"></a>개수 변환 작성
통계 기능을 작성하기 위해 Azure Machine Learning에서 사용할 수 있는 **통계 변환 작성** 모듈을 사용합니다. 이 모듈은 다음과 같습니다.

![개수 변환 모듈 속성 빌드](./media/hive-criteo-walkthrough/e0eqKtZ.png)
![개수 변환 모듈 빌드](./media/hive-criteo-walkthrough/OdDN0vw.png)

> [!IMPORTANT]
> **통계 열** 상자에서 통계를 수행하려는 해당 열을 입력합니다. 이미 설명한 대로 이러한 열은 일반적으로 고차원 범주의 열입니다. Criteo 데이터 세트에 Col15에서 Col40까지 26개의 범주 열이 있습니다. 여기서 이 모든 범주 열의 개수를 세고 인덱스를 지정합니다(아래 나온 것 같이 15부터 40까지 쉼표로 구분).
>

MapReduce 모드에서 모듈을 사용하려면(큰 데이터 세트에 적합) HDInsight Hadoop 클러스터 액세스 권한(기능 탐색에 사용했던 액세스 권한도 이 용도에 재사용 가능) 및 해당 자격 증명이 필요합니다. 이전 그림은 값을 입력했을 때 보이는 모양을 나타냅니다. 이 그림의 표시된 값은 필요한 값으로 바꾸세요.

![모듈 매개 변수](./media/hive-criteo-walkthrough/05IqySf.png)

위의 그림은 입력 Blob 위치를 입력하는 방법을 보여줍니다. 이 위치에는 개수 테이블을 작성하기 위해 예약된 데이터가 있습니다.

이 모듈의 실행이 완료되면 모듈을 마우스 오른쪽 단추로 클릭하고 **변환으로 저장** 옵션을 선택하여 나중에 사용하기 위해 변환을 저장합니다.

!["변환으로 저장" 옵션](./media/hive-criteo-walkthrough/IcVgvHR.png)

위에 나와 있는 실험 아키텍처에서 데이터 세트 "ytransform2"는 저장된 개수 변환과 정확히 일치합니다. 이 실험의 나머지 부분에서는 독자가 일부 데이터에 대해 **통계 변환 작성** 모듈을 사용하여 통계를 생성한 다음, 해당 통계를 사용하여 학습 및 테스트 데이터 세트에 대해 통계 기능을 생성할 수 있다고 가정합니다.

##### <a name="choosing-what-count-features-to-include-as-part-of-the-train-and-test-datasets"></a>학습 및 테스트 데이터 세트의 일부로 포함할 개수 기능 선택
통계 변환이 준비되면 사용자는 **통계 테이블 매개 변수 수정** 모듈을 사용하여 학습 및 테스트 데이터 세트에 포함할 기능을 선택할 수 있습니다. 완성도를 위해 이 모듈을 아래에 표시했지만, 실험을 단순하게 하려면 이 모듈을 실제로 사용하지 마세요.

![개수 테이블 매개 변수를 수정합니다.](./media/hive-criteo-walkthrough/PfCHkVg.png)

이 경우에 볼 수 있듯이 로그 확률(log-odds)을 사용하고 백오프(back off) 열을 무시합니다. 휴지통 임계값(garbage bin threshold), 원활한 진행을 위해 추가할 의사 이전 예제(pseudo-prior examples) 수, 라플라스 노이즈(Laplacian noise) 사용 여부 같은 매개 변수도 설정할 수 있습니다. 이들은 모두 고급 기능으로, 이러한 종류의 기능 생성을 처음 시도하는 사용자는 기본값을 사용하는 것이 좋습니다.

##### <a name="data-transformation-before-generating-the-count-features"></a>개수 기능을 생성하기 전에 데이터 변환
이제 통계 기능을 실제로 생성하기 전에 학습 및 테스트 데이터를 변환하는 방법에서 중요한 부분에 초점을 맞추겠습니다. 在对数据应用计数转换之前，将使用两个**执行 R 脚本**模块。

![R 스크립트 모듈 실행](./media/hive-criteo-walkthrough/aF59wbc.png)

첫 번째 R 스크립트는 다음과 같습니다.

![첫 번째 R 스크립트](./media/hive-criteo-walkthrough/3hkIoMx.png)

이 R 스크립트는 열 이름을 "Col1"부터 "Col40"까지로 바꿉니다. 개수 변환에서 이 형식의 이름을 필요로 하기 때문입니다.

두 번째 R 스크립트에서는 음수 클래스를 다운샘플링하여 양수 클래스와 음수 클래스(각각 클래스 1과 클래스 0) 사이에서 분포의 균형을 맞춥니다. 다음 R 스크립트에서 이 작업을 수행하는 방법을 보여 줍니다.

![두 번째 R 스크립트](./media/hive-criteo-walkthrough/91wvcwN.png)

이 간단한 R 스크립트에서 "pos\_neg\_ratio"를 사용하여 양수 클래스와 음수 클래스 간의 균형량을 설정합니다. 클래스의 불균형을 개선하면 일반적으로 클래스 분포가 편향되는 분류 문제에 대해 성능 이점이 있으므로 이 작업이 중요합니다(이 경우에 양수 클래스가 3.3%이고 음수 클래스가 96.7%임).

##### <a name="applying-the-count-transformation-on-our-data"></a>개수 변환을 데이터에 적용
마지막으로 **변환 적용** 모듈을 사용하여 학습 및 테스트 데이터 세트에 통계 변환을 적용합니다. 이 모듈은 저장된 개수 변환을 하나의 입력으로 사용하고, 학습 또는 테스트 데이터 세트를 다른 입력으로 사용하여 개수 기능을 통해 데이터를 반환합니다. 다음과 같이 표시됩니다.

![변환 모듈 적용](./media/hive-criteo-walkthrough/xnQvsYf.png)

##### <a name="an-excerpt-of-what-the-count-features-look-like"></a>개수 기능이 보이는 모양 발췌
개수 기능이 어떻게 보이는지 보겠습니다. 발췌 내용은 다음과 같습니다.

![개수 기능](./media/hive-criteo-walkthrough/FO1nNfw.png)

이 발췌 내용에서는 계산된 열에서 통계 및 로그 확률뿐만 아니라 관련 백오프도 볼 수 있습니다.

이제 이렇게 변환된 데이터 세트를 사용하여 Azure Machine Learning 모델을 빌드할 준비가 되었습니다. 다음 섹션에서는 이 작업을 수행할 방법을 보여줍니다.

### <a name="step3"></a> 3단계: 모델 빌드, 학습 및 점수 매기기

#### <a name="choice-of-learner"></a>학습자의 선택
먼저 학습자를 선택해야 합니다. 2클래스 향상된 의사 결정 트리를 학습자로 사용합니다. 다음은 이 학습자에 대한 기본 옵션입니다.

![2 클래스 향상된 의사 결정 트리 매개 변수](./media/hive-criteo-walkthrough/bH3ST2z.png)

실험에서 기본값을 선택합니다. 默认值是有意义的，它是获得性能的快速基线的好方法。 기준이 있는 상태에서 원하는 경우 매개 변수를 스윕하여 성능을 향상시킬 수 있습니다.

#### <a name="train-the-model"></a>모델 교육
학습용으로 간단히 **모델 학습** 모듈을 호출합니다. 이 모듈에 대한 두 가지 입력이 2클래스 향상된 의사 결정 트리 학습자와 학습 데이터 세트입니다. 다음과 같습니다.

![모델 학습 모듈](./media/hive-criteo-walkthrough/2bZDZTy.png)

#### <a name="score-the-model"></a>모델 점수 매기기
학습된 모델이 있으면 테스트 데이터 세트에 대한 점수를 매기고 성능을 평가할 준비가 되었습니다. **평가 모델** 모듈과 함께 다음 그림에 있는 **점수 매기기 모델** 모듈을 사용하여 이 작업을 수행합니다.

![모델 점수 매기기 모듈](./media/hive-criteo-walkthrough/fydcv6u.png)

### <a name="step4"></a> 4단계: 모델 평가
마지막으로 모델 성능을 분석해야 합니다. 일반적으로 2클래스(이진) 분류 문제의 경우 AUC를 측정하는 것이 좋습니다. 若要可视化此曲线，请将 "**评分模型**" 模块连接到 "**评估模型**" 模块。 **모델 평가** 모듈에서 **시각화**를 클릭하면 다음과 같은 그림이 표시됩니다.

![Evaluate module BDT 모델](./media/hive-criteo-walkthrough/0Tl0cdg.png)

이진(또는 2클래스) 분류 문제에서 예측 정확도의 적절한 척도는 AUC(Area Under Curve)입니다. 다음 섹션에서는 테스트 데이터 세트에서 이 모델을 사용한 결과를 보여줍니다. 右键单击 "**评估模型**" 模块的输出端口，然后单击 "**可视化**"。

![Evaluate Model 모듈 시각화](./media/hive-criteo-walkthrough/IRfc7fH.png)

### <a name="step5"></a> 5단계: 모델을 웹 서비스로 게시
혼란을 최소화하면서 Azure 기계 학습 모델을 웹 서비스로 게시하는 기능은 이 기능의 광범위한 사용을 위한 유용한 기능입니다. 이 작업이 완료되면 누구나 예측이 필요한 입력 데이터를 사용하여 웹 서비스를 호출하고 웹 서비스는 모델을 사용하여 해당 예측을 반환할 수 있습니다.

首先，通过右键单击 "**训练模型**" 模块并使用 "**另存为定型模型**" 选项，将训练的模型另存为定型模型对象。

다음으로 웹 서비스에 입력 및 출력 포트를 만듭니다.

* 입력 포트는 예측이 필요한 데이터와 동일한 형식의 데이터를 사용합니다.
* 출력 포트는 점수가 매겨진 레이블 및 관련 확률을 반환합니다.

#### <a name="select-a-few-rows-of-data-for-the-input-port"></a>입력 포트에 대한 몇 가지 데이터 행 선택
**SQL 변환 적용** 모듈을 사용하여 입력 포트 데이터로 사용할 10개 행만 편리하게 선택할 수 있습니다. 다음에 나와 있는 SQL 쿼리를 사용하여 입력 포트에 대해 이러한 데이터 행을 선택합니다.

![입력 포트 데이터](./media/hive-criteo-walkthrough/XqVtSxu.png)

#### <a name="web-service"></a>웹 서비스
이제 웹 서비스를 게시하는 데 사용할 수 있는 소규모 실험을 실행할 준비가 완료되었습니다.

#### <a name="generate-input-data-for-webservice"></a>웹 서비스에 대한 입력 데이터 생성
통계 테이블이 크기 때문에 가장 먼저 테스트 데이터의 몇 줄을 가져와 통계 기능과 함께 출력 데이터를 생성합니다. 此输出可作为 web 服务的输入数据格式，如下所示：

![BDT 입력 데이터 만들기](./media/hive-criteo-walkthrough/OEJMmst.png)

> [!NOTE]
> 입력 데이터 서식에 대해 **통계 Featurizer** 모듈의 출력을 사용합니다. 이 실험의 실행이 완료되면 **Count Featurizer** 모듈의 출력을 Dataset으로 저장합니다. 이 Dataset은 웹 서비스의 입력 데이터로 사용됩니다.
>
>

#### <a name="scoring-experiment-for-publishing-webservice"></a>웹 서비스 게시를 위한 실험 점수 매기기
首先，基本结构是一个**评分模型**模块，该模块接受我们训练的模型对象和在前面步骤中使用**Count 特征化器**模块生成的几行输入数据。 "데이터 세트의 열 선택"을 사용하여 점수가 매겨진 레이블 및 점수 매기기 확률을 표시합니다.

![데이터 세트의 열 선택](./media/hive-criteo-walkthrough/kRHrIbe.png)

**데이터 세트의 열 선택** 모듈은 데이터 세트에서 데이터를 ‘필터링'할 때 사용할 수 있습니다. 콘텐츠는 다음과 같습니다.

![데이터 세트의 열 선택 모듈로 필터링](./media/hive-criteo-walkthrough/oVUJC9K.png)

파란색 입력 및 출력 포트를 가져오려면 오른쪽 아래에서 **prepare webservice** (웹 서비스 준비)를 클릭하면 됩니다. 이 실험을 실행하면 다음과 같이 오른쪽 아래에서 **PUBLISH WEB SERVICE** (웹 서비스 게시) 아이콘을 클릭하여 웹 서비스를 게시할 수도 있습니다.

![PUBLISH WEB SERVICE](./media/hive-criteo-walkthrough/WO0nens.png)

웹 서비스가 게시되면 다음과 같은 페이지로 리디렉션됩니다.

![웹 서비스 대시보드](./media/hive-criteo-walkthrough/YKzxAA5.png)

왼쪽에 웹 서비스에 대한 두 개의 링크가 있습니다.

* **REQUEST/RESPONSE** 서비스(또는 RRS)는 단일 예측에 사용되며 이 워크숍에서는 활용되었습니다.
* **BATCH EXECUTION** 서비스(또는 BES)는 배치 예측에 사용되며 이 서비스를 사용하려면 예측에 사용되는 입력 데이터가 Azure Blob Storage에 있어야 합니다.

**REQUEST/RESPONSE** 링크를 클릭하면 C#, python 및 R로 미리 만든 코드를 제공하는 페이지가 나타납니다. 이 코드를 사용하여 웹 서비스를 간편하게 호출할 수 있습니다. 此页面上的 API 密钥需要用于身份验证。

이 python 코드를 IPython 노트북의 새 셀에 복사하면 편리합니다.

올바른 API 키가 있는 Python 코드의 세그먼트는 다음과 같습니다.

![Python 코드](./media/hive-criteo-walkthrough/f8N4L4g.png)

默认 API 密钥已替换为 webservice 的 API 密钥。 IPython 노트북에서 이 셀에 대해 **Run** (실행)을 클릭하면 다음 응답이 나타납니다.

![IPython 응답](./media/hive-criteo-walkthrough/KSxmia2.png)

对于在 Python script JSON framework 中所询问的两个测试示例，您将以 "评分标签，评分概率" 的形式获取答案。 이 경우에 미리 만든 코드에서 제공하는 기본값(모든 숫자 열의 경우 0, 모든 범주 열의 경우 문자열 "값")을 선택했습니다.

总之，我们的演练演示如何使用 Azure 机器学习来处理大规模数据集。 이제 테라바이트의 데이터를 시작해서 예측 모델을 구성하고 클라우드의 웹 서비스로 배포했습니다.

