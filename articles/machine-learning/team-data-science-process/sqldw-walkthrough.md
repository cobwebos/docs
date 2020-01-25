---
title: 使用 Azure Synapse Analytics 构建和部署模型-团队数据科学流程
description: 使用 Azure Synapse Analytics 和公开可用的数据集，生成和部署机器学习模型。
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
ms.openlocfilehash: e64b951a8bb96b25a6ef917b4cebe077d6dd6657
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718440"
---
# <a name="the-team-data-science-process-in-action-using-azure-synapse-analytics"></a>工作中的团队数据科学过程：使用 Azure Synapse Analytics
在本教程中，我们将逐步介绍如何使用 Azure Synapse Analytics 对公开提供的数据集（ [NYC 出租车行程](https://www.andresmh.com/nyctaxitrips/)数据集）生成和部署机器学习模型。 构造的二元分类模型预测是否为行程支付了提示。  模型包括多类分类（无论是否有提示）和回归（支付的刀尖数量的分布）。

이 절차에서는 [TDSP(팀 데이터 과학 프로세스)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) 워크플로를 따릅니다. 本文介绍如何设置数据科学环境，如何将数据加载到 Azure Synapse Analytics 中，以及如何使用 Azure Synapse Analytics 或 IPython 笔记本浏览要建模的数据和工程功能。 그런 다음 Azure Machine Learning으로 모델을 빌드하고 배포하는 방법을 보여 줍니다.

## <a name="dataset"></a>NYC Taxi Trips 데이터 세트
NYC Taxi Trip 데이터는 1억 7,300만 개가 넘는 개별 여정 및 각 여정의 요금으로 기록된 약 20GB의 압축된 CSV 파일(압축되지 않은 경우 약 48GB)로 구성됩니다. 각 여정 레코드는 승차 및 하차 위치, 익명 처리된 hack(기사) 면허증 번호 및 medallion(택시의 고유 ID) 번호를 포함합니다. 데이터는 2013년의 모든 여정을 포괄하며, 매월 다음 두 개의 데이터 세트로 제공됩니다.

1. **trip_data.csv** 파일에는 승객 수, 승차 및 하차 지점, 여정 기간, 여정 거리 등 여정 세부 정보가 포함됩니다. 다음은 몇 가지 샘플 레코드입니다.

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. **trip_fare.csv** 파일에는 지불 유형, 금액, 추가 요금 및 세금, 팁 및 통행료, 총 지불 금액 등 각 여정의 요금에 대한 세부 정보가 포함됩니다. 다음은 몇 가지 샘플 레코드입니다.

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

trip\_data 및 trip\_fare를 조인하는 데 사용된 **고유 키**는 다음 세 필드로 구성됩니다.

* medallion,
* hack\_license 및
* pickup\_datetime.

## <a name="mltasks"></a>세 가지 유형의 예측 작업 처리
*tip\_amount*를 기반으로 예측 문제를 작성하여 세 종류의 모델링 작업을 보여 줍니다.

1. **二元分类**：要预测是否为行程支付了提示（即，大于 $0 的*tip\_量*）为正示例，而*tip\_$0 量*为负的示例。
2. **다중 클래스 분류**: 여정에 대해 지불된 팁의 범위를 예측합니다. *tip\_amount*를 5개의 bin 또는 클래스로 나눕니다.

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. **회귀 작업**: 여정에 대해 지불된 팁의 금액을 예측합니다.

## <a name="setup"></a>고급 분석을 위한 Azure 데이터 과학 환경 설정
Azure 데이터 과학 환경을 설정하려면 다음 단계를 수행합니다.

A: 모든 지역에서 Blob 및 File Storage에 고객 관리 키 및 SSE를 사용할 수 있습니다.

* 이 문서에서는 Azure CLI에서 SMB 탑재를 사용하여 Linux VM에서 Azure File 스토리지 서비스를 사용하는 방법을 보여 줍니다. Azure File Storage를 사용하여 파일 공유에 의존하는 레거시 응용 프로그램을 비경제적인 다시 쓰기 작업 없이 빠르게 Azure로 마이그레이션할 수 있습니다. File Storage를 사용하여 세상에 공개적으로 표시하거나 응용 프로그램 데이터를 비공개적으로 저장할 수 있습니다.
* 若要创建自己的 Azure 存储帐户，请按照[关于 Azure 存储帐户](../../storage/common/storage-create-storage-account.md)中所述的步骤进行操作。 이 연습의 뒷부분에서 필요하므로 다음 스토리지 계정 자격 증명에 대한 값을 적어두어야 합니다.

  * **Storage 계정 이름**
  * **Storage 계정 키**
  * 2단계: Blob 및 File Storage에 SSE 사용

**预配 Azure Synapse Analytics 实例。**
请参阅在[Azure 门户中创建和查询 AZURE SQL 数据仓库中](../../sql-data-warehouse/create-data-warehouse-portal.md)的文档，预配 Azure Synapse Analytics 实例。 请确保在以下步骤中将用到的以下 Azure Synapse Analytics 凭据上进行了注释。

* **服务器名称**： \<server name >
* **SQLDW(데이터베이스) 이름**
* **사용자 이름**
* **암호**

**Visual Studio 및 SQL Server 데이터 도구 설치** 有关说明，请参阅[Visual Studio 2019 FOR SQL 数据仓库](../../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md)入门。

**通过 Visual Studio 连接到 Azure Synapse Analytics。** 有关说明，请参阅[连接到 AZURE SQL 数据仓库](../../sql-data-warehouse/sql-data-warehouse-connect-overview.md)中的步骤 1 & 2。

> [!NOTE]
> 在 Azure Synapse Analytics 中创建的数据库上运行以下 SQL 查询（而不是在连接主题的步骤3中提供的查询）来**创建主密钥**。
>
>

    BEGIN TRY
           --Try to create the master key
        CREATE MASTER KEY
    END TRY
    BEGIN CATCH
           --If the master key exists, do nothing
    END CATCH;

**Azure 구독에서 Azure Machine Learning 작업 영역을 만듭니다.** 자세한 지침은 [Azure Machine Learning 작업 영역 만들기](../studio/create-workspace.md)에 요약된 단계를 수행합니다.

## <a name="getdata"></a>将数据加载到 Azure Synapse Analytics
Windows PowerShell 명령 콘솔을 엽니다. 다음 PowerShell 명령을 실행하여 GitHub에서 *-DestDir* 매개 변수를 사용하여 지정한 로컬 디렉터리에 공유하는 예제 SQL 스크립트 파일을 다운로드합니다. 매개 변수 *-DestDir* 의 값을 로컬 디렉터리로 변경할 수 있습니다. *-DestDir* 이 존재하지 않는 경우 PowerShell 스크립트를 통해 생성됩니다.

> [!NOTE]
> 다음 PowerShell 스크립트를 실행할 때 **DestDir** 디렉터리에 만들거나 작성하는 데 관리자 권한이 필요한 경우 *관리자 권한으로 실행* 해야 할 수 있습니다.
>
>

    $source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
    $ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
    $wc = New-Object System.Net.WebClient
    $wc.DownloadFile($source, $ps1_dest)
    .\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'

성공적으로 실행된 후에 현재 작업 디렉터리를 *-DestDir*로 변경합니다. 아래와 같은 화면을 볼 수 있어야 합니다.

![현재 작업 디렉터리 변경 내용][19]

*-DestDir*의 관리자 모드에서 다음 PowerShell 스크립트를 실행합니다.

    ./SQLDW_Data_Import.ps1

第一次运行 PowerShell 脚本时，系统会要求你输入 Azure Synapse Analytics 和 Azure blob 存储帐户中的信息。 이 PowerShell 스크립트가 처음 실행을 완료하는 경우 입력한 자격 증명은 현재 작업 디렉터리의 SQLDW.conf 구성 파일에 작성됩니다. 이 PowerShell 스크립트 파일을 나중에 실행하는 경우 이 구성 파일에서 필요한 매개 변수를 모두 읽는 옵션이 있습니다. 일부 매개 변수를 변경해야 할 경우 표시되는 메시지에 따라 이 구성 파일을 삭제하고 매개 변수 값을 입력하여 화면에 매개 변수를 입력하거나 *-DestDir* 디렉터리의 SQLDW.conf 파일을 편집하여 매개 변수 값을 변경하도록 선택할 수 있습니다.

> [!NOTE]
> 若要避免架构名称与 Azure Azure Synapse Analytics 中已存在的架构名称冲突，请在直接从 SQLDW 文件中读取参数时，将 SQLDW 文件中的架构名称作为默认架构添加到架构名称中每个运行的名称。 PowerShell 스크립트에서 스키마 이름을 지정하라는 메시지가 표시될 수 있습니다. 이 이름은 사용자가 임의로 지정할 수 있습니다.
>
>

이 **PowerShell 스크립트** 파일은 다음 작업을 완료합니다.

* AzCopy가 설치되지 않은 경우 **AzCopy 다운로드 및 설치**

        $AzCopy_path = SearchAzCopy
        if ($AzCopy_path -eq $null){
               Write-Host "AzCopy.exe is not found in C:\Program Files*. Now, start installing AzCopy..." -ForegroundColor "Yellow"
            InstallAzCopy
            $AzCopy_path = SearchAzCopy
        }
            $env_path = $env:Path
            for ($i=0; $i -lt $AzCopy_path.count; $i++){
                if ($AzCopy_path.count -eq 1){
                    $AzCopy_path_i = $AzCopy_path
                } else {
                    $AzCopy_path_i = $AzCopy_path[$i]
                }
                if ($env_path -notlike '*' +$AzCopy_path_i+'*'){
                    Write-Host $AzCopy_path_i 'not in system path, add it...'
                    [Environment]::SetEnvironmentVariable("Path", "$AzCopy_path_i;$env_path", "Machine")
                    $env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine")
                    $env_path = $env:Path
                }
* AzCopy를 사용하여 공용 Blob에서 **프라이빗 Blob 스토리지 계정에 데이터 복사**

        Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
        $start_time = Get-Date
        AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
        $end_time = Get-Date
        $time_span = $end_time - $start_time
        $total_seconds = [math]::Round($time_span.TotalSeconds,2)
        Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
        Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"
* 使用以下命令将数据从专用 blob 存储帐户中的**Polybase （通过执行 LoadDataToSQLDW）加载到 Azure Synapse Analytics**中。

  * 스키마 만들기

          EXEC (''CREATE SCHEMA {schemaname};'');
  * 데이터베이스 범위 자격 증명 만들기

          CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
          WITH IDENTITY = ''asbkey'' ,
          Secret = ''{StorageAccountKey}''
  * 为 Azure 存储 blob 创建外部数据源

          CREATE EXTERNAL DATA SOURCE {nyctaxi_trip_storage}
          WITH
          (
              TYPE = HADOOP,
              LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
              CREDENTIAL = {KeyAlias}
          )
          ;

          CREATE EXTERNAL DATA SOURCE {nyctaxi_fare_storage}
          WITH
          (
              TYPE = HADOOP,
              LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
              CREDENTIAL = {KeyAlias}
          )
          ;
  * csv 파일에 대한 외부 파일 형식을 만듭니다. 데이터가 압축되지 않으며 필드는 파이프 문자로 분리됩니다.

          CREATE EXTERNAL FILE FORMAT {csv_file_format}
          WITH
          (
              FORMAT_TYPE = DELIMITEDTEXT,
              FORMAT_OPTIONS
              (
                  FIELD_TERMINATOR ='','',
                  USE_TYPE_DEFAULT = TRUE
              )
          )
          ;
  * AzCopy는 최적의 성능을 내는 간단한 명령을 사용하여 데이터를 Microsoft Azure Blob 및 File Storage에 복사하거나 이들 스토리지에서 복사하기 위한 명령줄 유틸리티입니다.

          CREATE EXTERNAL TABLE {external_nyctaxi_fare}
          (
              medallion varchar(50) not null,
              hack_license varchar(50) not null,
              vendor_id char(3),
              pickup_datetime datetime not null,
              payment_type char(3),
              fare_amount float,
              surcharge float,
              mta_tax float,
              tip_amount float,
              tolls_amount float,
              total_amount float
          )
          with (
              LOCATION    = ''/nyctaxifare/'',
              DATA_SOURCE = {nyctaxi_fare_storage},
              FILE_FORMAT = {csv_file_format},
              REJECT_TYPE = VALUE,
              REJECT_VALUE = 12
          )

            CREATE EXTERNAL TABLE {external_nyctaxi_trip}
            (
                   medallion varchar(50) not null,
                   hack_license varchar(50)  not null,
                   vendor_id char(3),
                   rate_code char(3),
                   store_and_fwd_flag char(3),
                   pickup_datetime datetime  not null,
                   dropoff_datetime datetime,
                   passenger_count int,
                   trip_time_in_secs bigint,
                   trip_distance float,
                   pickup_longitude varchar(30),
                   pickup_latitude varchar(30),
                   dropoff_longitude varchar(30),
                   dropoff_latitude varchar(30)
            )
            with (
                LOCATION    = ''/nyctaxitrip/'',
                DATA_SOURCE = {nyctaxi_trip_storage},
                FILE_FORMAT = {csv_file_format},
                REJECT_TYPE = VALUE,
                REJECT_VALUE = 12
            )

    - 将数据从 Azure blob 存储中的外部表加载到 Azure Synapse Analytics

            CREATE TABLE {schemaname}.{nyctaxi_fare}
            WITH
            (
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_fare}
            ;

            CREATE TABLE {schemaname}.{nyctaxi_trip}
            WITH
            (
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_trip}
            ;

    - 샘플 데이터 테이블(NYCTaxi_Sample)을 만들고 여정 및 요금 테이블에 SQL 쿼리를 선택하여 데이터를 삽입합니다. （本演练的一些步骤需要使用此示例表。）

            CREATE TABLE {schemaname}.{nyctaxi_sample}
            WITH
            (
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            (
                SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount,
                tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
                tip_class = CASE
                        WHEN (tip_amount = 0) THEN 0
                        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                        ELSE 4
                    END
                FROM {schemaname}.{nyctaxi_trip} t, {schemaname}.{nyctaxi_fare} f
                WHERE datepart("mi",t.pickup_datetime) = 1
                AND t.medallion = f.medallion
                AND   t.hack_license = f.hack_license
                AND   t.pickup_datetime = f.pickup_datetime
                AND   pickup_longitude <> ''0''
                AND   dropoff_longitude <> ''0''
            )
            ;

스토리지 계정의 지리적 위치는 로드 시간을 영향을 줍니다.

> [!NOTE]
> 根据专用 blob 存储帐户的地理位置，将数据从公共 blob 复制到专用存储帐户的过程可能需要大约15分钟甚至更长时间，并将数据从存储帐户加载到 AzureAzure Synapse 分析可能需要20分钟或更长时间。
>
>

중복된 원본 및 대상 파일이 있는 경우 수행할 작업을 결정해야 합니다.

> [!NOTE]
> 공용 Blob Storage에서 프라이빗 Blob 스토리지 계정으로 복사할 .csv 파일이 개인 Blob Storage 계정에 이미 있으면 AzCopy는 덮어쓸 것인지를 묻습니다. 덮어쓰지 않으려는 경우 메시지가 표시되면 **n** 을 입력합니다. **모두** 덮어쓰려는 경우 메시지가 표시되면 **a**를 입력합니다. 또한 **y** 를 입력하여 개별적으로 .csv 파일을 덮어쓸 수 있습니다.
>
>

![AzCopy의 출력][21]

사용자 고유의 데이터를 사용할 수 있습니다. 데이터가 실제 애플리케이션의 온-프레미스 머신에 있으면 AzCopy을 사용하여 프라이빗 Azure Blob 스토리지에 온-프레미스 데이터를 업로드할 수 있습니다. PowerShell 스크립트 파일의 AzCopy 명령에서 **원본** 위치인 `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"`를 데이터가 있는 로컬 디렉터리로 변경해야만 합니다.

> [!TIP]
> 如果你的数据已在实际应用程序中的专用 Azure blob 存储中，则可以跳过 PowerShell 脚本中的 AzCopy 步骤，并直接将数据上传到 Azure Azure Synapse Analytics。 데이터 형식에 맞추려면 스크립트를 추가로 편집해야 합니다.
>
>

此 PowerShell 脚本还将 Azure Synapse Analytics 信息插入到数据浏览示例文件中 SQLDW_Explorations .sql、SQLDW_Explorations ipynb SQLDW_Explorations_Scripts 和 py，以便可以试用这三个文件PowerShell 脚本完成后立即进行。

성공적으로 실행한 후에 다음과 같이 화면에 표시됩니다.

![성공적인 스크립트 실행의 출력][20]

## <a name="dbexplore"></a>Azure Synapse 分析中的数据浏览和功能设计
在本部分中，我们将通过使用**Visual Studio Data Tools**直接对 Azure Synapse ANALYTICS 运行 SQL 查询来执行数据浏览和功能生成。 이 섹션에서 사용되는 모든 SQL 쿼리는 *SQLDW_Explorations.sql*이라는 샘플 스크립트에서 찾을 수 있습니다. 이 파일은 이미 PowerShell 스크립트에 의해 로컬 디렉터리에 다운로드되었습니다. 또한 [GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql)에서 검색할 수 있습니다. 但 GitHub 中的文件并未插入 Azure Synapse Analytics 信息。

使用 Visual Studio 和 Azure Synapse Analytics 登录名和密码连接到 Azure Synapse Analytics，并打开**SQL 对象资源管理器**以确认已导入数据库和表。 *SQLDW_Explorations.sql* 파일을 검색합니다.

> [!NOTE]
> PDW(병렬 데이터 웨어하우스) 쿼리 편집기를 열려면 **SQL 개체 탐색기**에서 PDW를 선택하여 **새 쿼리** 명령을 사용합니다. 표준 SQL 쿼리 편집기는 PDW에서 지원되지 않습니다.
>
>

以下是本部分中执行的数据浏览和功能生成任务的类型：

* 다양한 기간에 걸쳐 몇몇 필드의 데이터 분포를 탐색합니다.
* 경도 및 위도 필드의 데이터 품질을 조사합니다.
* **tip\_amount**에 따라 이진 및 다중 클래스 분류 레이블을 생성합니다.
* 기능을 생성하고 여정 거리를 계산/비교합니다.
* 두 테이블을 조인하고 모델을 빌드하는 데 사용할 무작위 샘플을 추출합니다.

### <a name="data-import-verification"></a>데이터 가져오기 확인
다음 쿼리를 실행하면 이전에 Polybase 병렬 대량 가져오기를 사용하여 채운 테이블에서 행 및 열 수를 신속하게 확인할 수 있습니다.

    -- Report number of rows in table <nyctaxi_trip> without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')

    -- Report number of columns in table <nyctaxi_trip>
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'

**출력:** 173,179,759행과 14개의 열이 표시됩니다.

### <a name="exploration-trip-distribution-by-medallion"></a>탐색: medallion별 여정 분포
이 예제 쿼리에서는 지정된 기간 내의 100개가 넘는 여정을 완료한 medallion(택시 번호)을 식별합니다. 쿼리는 **pickup\_datetime** 파티션 구성표를 조건으로 하므로 분할된 테이블 액세스를 활용합니다. 전체 데이터 세트를 쿼리할 때도 분할된 테이블 및/또는 인덱스 검색을 사용합니다.

    SELECT medallion, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

**输出：** 查询应返回一个表，其中的行指定13369徽章（出租车）和在2013中完成的行程数。 마지막 열에는 완료된 여정 수가 포함됩니다.

### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>탐색: medallion 및 hack_license별 여정 분포
이 예제에서는 지정된 기간 내의 100개가 넘는 여정을 완료한 medallion(택시 번호) 및 hack_license 번호(드라이버)를 식별합니다.

    SELECT medallion, hack_license, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

**출력:** 쿼리는 2013년에 100개가 넘는 여정을 완료한 13,369개의 차/드라이버 ID가 지정된 13,369개 행의 테이블을 반환합니다. 마지막 열에는 완료된 여정 수가 포함됩니다.

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>데이터 품질 평가: 경도 및/또는 위도가 잘못된 레코드 확인
이 예제에서는 경도 및/또는 위도 필드에 유효하지 않은 값(라디안이 -90도~90도에 속해야 함)이 포함되거나 (0, 0) 좌표가 있는지 조사합니다.

    SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

**출력:** 쿼리는 잘못된 경도 및/또는 위도 필드가 있는 837,467개의 여정을 반환합니다.

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>탐색: 왕복 여정과 비왕복 여정 분포
이 예제에서는 지정된 기간 동안(또는 여기 설정된 대로 전체 연도를 포함하는 경우 전체 데이터 세트에서) 왕복 여정 수와 비왕복 여정 수를 확인합니다. 이 분포는 나중에 이진 분류 모델링에 사용할 이진 레이블 분포를 반영합니다.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM <schemaname>.<nyctaxi_fare>
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

**출력:** 쿼리는 2013년 팁 빈도(왕복 여정 90,447,622 및 비왕복 여정 82,264,709)를 반환합니다.

### <a name="exploration-tip-classrange-distribution"></a>탐색: 팁 클래스/범위 분포
이 예제에서는 지정된 기간 동안(또는 전체 연도를 포괄하는 경우 전체 데이터 세트에서) 팁 범위 분포를 계산합니다. 此标签类的分布稍后将用于多类分类建模。

    SELECT tip_class, COUNT(*) AS tip_freq FROM (
        SELECT CASE
            WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tip_class

**출력:**

| tip_class | tip_freq |
| --- | --- |
| 1 |82230915 |
| 2 |6198803 |
| 3 |1932223 |
| 0 |82264625 |
| 4 |85765 |

### <a name="exploration-compute-and-compare-trip-distance"></a>탐색: 여정 거리 컴퓨팅 및 비교
이 예제에서는 승차 및 하차 경도/위도를 SQL 지리 지점으로 변환하고, SQL 지리 지점 차이를 사용하여 여정 거리를 계산한 다음, 비교를 위해 무작위 결과 샘플을 반환합니다. 앞서 설명한 데이터 품질 평가 쿼리를 사용하여 유효한 좌표로만 결과가 제한됩니다.

    /****** Object:  UserDefinedFunction [dbo].[fnCalculateDistance] ******/
    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function to calculate the direct distance  in mile between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
          DECLARE @distance decimal(28, 10)
          -- Convert to radians
          SET @Lat1 = @Lat1 / 57.2958
          SET @Long1 = @Long1 / 57.2958
          SET @Lat2 = @Lat2 / 57.2958
          SET @Long2 = @Long2 / 57.2958
          -- Calculate distance
          SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
          --Convert to miles
          IF @distance <> 0
          BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
          END
          RETURN @distance
    END
    GO

    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

### <a name="feature-engineering-using-sql-functions"></a>SQL 함수를 사용하는 기능 엔지니어링
때때로 SQL 함수는 기능 엔지니어링에 대한 효율적인 옵션일 수 있습니다. 이 연습에서는 승차와 하차 위치 간의 직접 거리를 계산하는 SQL 함수를 정의했습니다. **Visual Studio Data Tools**에서 다음 SQL 스크립트를 실행할 수 있습니다.

거리 함수를 정의하는 SQL 스크립트는 다음과 같습니다.

    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function calculate the direct distance between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
          DECLARE @distance decimal(28, 10)
          -- Convert to radians
          SET @Lat1 = @Lat1 / 57.2958
          SET @Long1 = @Long1 / 57.2958
          SET @Lat2 = @Lat2 / 57.2958
          SET @Long2 = @Long2 / 57.2958
          -- Calculate distance
          SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
          --Convert to miles
          IF @distance <> 0
          BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
          END
          RETURN @distance
    END
    GO

SQL 쿼리에서 기능을 생성하는 이 함수를 호출하는 예는 다음과 같습니다.

    -- Sample query to call the function to create features
    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

**출력:** 이 쿼리는 승차 및 하차 위도 및 경도와 해당 직선 거리(마일)가 포함된 테이블(2,803,538개 행)을 생성합니다. 下面是前三行的结果：

|  | pickup_latitude | pickup_longitude | dropoff_latitude | dropoff_longitude | DirectDistance |
| --- | --- | --- | --- | --- | --- |
| 1 |40.731804 |-74.001083 |40.736622 |-73.988953 |.7169601222 |
| 2 |40.715794 |-74,010635 |40.725338 |-74.00399 |.7448343721 |
| 3 |40.761456 |-73.999886 |40.766544 |-73.988228 |0.7037227967 |

### <a name="prepare-data-for-model-building"></a>모델 구축에 사용할 데이터를 준비합니다.
다음 쿼리는 **nyctaxi\_trip** 및 **nyctaxi\_fare** 테이블을 조인하고, 이진 분류 레이블 **tipped**와 다중 클래스 분류 레이블 **tip\_class**를 생성하며, 조인된 전체 데이터 세트에서 샘플을 추출합니다. 샘플링은 승차 시간에 따라 여정의 하위 집합을 검색하여 수행됩니다.  可以复制此查询，然后将其直接粘贴到[Azure 机器学习 Studio （经典）](https://studio.azureml.net) [导入数据][导入数据模块，]，以便从 Azure 中的 SQL 数据库实例进行直接数据引入。 잘못된 (0, 0) 좌표가 있는 레코드는 쿼리에서 제외됩니다.

    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,     f.total_amount, f.tip_amount,
        CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
        CASE WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
    WHERE datepart("mi",t.pickup_datetime) = 1
    AND   t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'

Azure Machine Learning을 진행할 준비가 되었으면 다음을 수행할 수 있습니다.

1. 保存最终的 SQL 查询，以提取和采样数据，并直接将查询复制并粘贴到 Azure 机器学习中的[导入数据模块，]或
2. 保留计划用于在新的 Azure Synapse 分析表中进行建模的抽样和工程数据，并使用 Azure 机器学习中的 "[导入数据模块，][导入数据模块，]" 模块中的新表。 之前步骤中的 PowerShell 脚本已为你执行此任务。 데이터 가져오기 모듈의 이 테이블에서 직접 읽을 수 있습니다.

## <a name="ipnb"></a>IPython Notebook에서 데이터 탐색 및 기능 엔지니어링
在本部分中，我们将使用 Python 和 SQL 查询对先前创建的 Azure Synapse Analytics 执行数据浏览和功能生成。 **SQLDW_Explorations.ipynb**라는 샘플 IPython Notebook 및 Python 스크립트 파일 **SQLDW_Explorations_Scripts.py**는 로컬 디렉터리에 다운로드되었습니다. 또한 [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW)에서 사용할 수 있습니다. 이 두 파일은 Python 스크립트에서 동일합니다. IPython Notebook 서버가 없는 경우 Python 스크립트 파일이 제공됩니다. 이 두 샘플 Python 파일은 **Python 2.7**에서 디자인됩니다.

PowerShell 脚本已将示例 IPython 笔记本中所需的 Azure Synapse 分析信息以及下载到本地计算机的 Python 脚本文件预先插入到你的本地计算机。 수정 없이 실행할 수 있습니다.

如果已设置 Azure 机器学习工作区，则可以直接将示例 IPython 笔记本上载到 AzureML IPython 笔记本服务，并开始运行它。 以下是上传到 AzureML IPython 笔记本服务的步骤：

1. 登录到 Azure 机器学习工作区，单击顶部的 **"工作室"** ，并单击网页左侧的 "**笔记本**"。

    ![Studio를 클릭한 다음, NOTEBOOKS 클릭][22]
2. 单击网页左下角的 "**新建**"，然后选择 " **Python 2**"。 그런 다음 노트북에 이름을 제공하고 확인 표시를 클릭하여 새 비어 있는 IPython Notebook을 만듭니다.

    ![NEW를 클릭한 다음, Python 2 클릭][23]
3. 单击新 IPython 笔记本的左上角的**Jupyter**符号。

    ![Jupyter 기호 클릭][24]
4. 샘플 IPython Notebook을 AzureML IPython Notebook 서비스의 **트리** 페이지로 끌어서 놓고 **업로드**를 클릭합니다. 그런 다음 샘플 IPython Notebook은 AzureML IPython Notebook 서비스에 업로드됩니다.

    ![[업로드] 클릭][25]

이 샘플 IPython Notebook 또는 Python 스크립트 파일을 실행하기 위해 다음 Python 패키지가 필요합니다. AzureML IPython Notebook 서비스를 사용하는 경우 이러한 패키지는 미리 설치되었습니다.

- pandas
- numpy
- matplotlib
- pyodbc
- PyTables

使用大型数据 Azure 机器学习生成高级分析解决方案时，建议使用以下顺序：

* 소량의 데이터 샘플을 메모리 내 데이터 프레임으로 읽습니다.
* 샘플링된 데이터를 사용하여 일부 시각화 및 탐색을 수행합니다.
* 샘플링된 데이터를 사용하여 기능 엔지니어링을 실험합니다.
* 对于大型数据浏览、数据操作和功能设计，请使用 Python 直接针对 Azure Synapse Analytics 发出 SQL 查询。
* Azure Machine Learning 모델 빌드에 적합한 샘플 크기를 결정합니다.

다음은 데이터 탐색, 데이터 시각화 및 기능 엔지니어링에 대한 몇 가지 예제입니다. 더 많은 데이터 탐색은 샘플 IPython Notebook 및 샘플 Python 스크립트 파일에 있을 수 있습니다.

### <a name="initialize-database-credentials"></a>데이터베이스 자격 증명 초기화
다음 변수에서 데이터베이스 연결 설정을 초기화합니다.

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database driver>

### <a name="create-database-connection"></a>데이터베이스 연결 만들기
데이터베이스에 연결을 만드는 연결 문자열은 다음과 같습니다.

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a><nyctaxi_trip> 테이블의 행 및 열 수 보고
    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_trip>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

* 총 행 수 = 173179759
* 총 열 수 = 14

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_fare"></a><nyctaxi_fare> 테이블의 행 및 열 수 보고
    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_fare>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_fare>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

* 총 행 수 = 173179759
* 총 열 수 = 11

### <a name="read-in-a-small-data-sample-from-the-azure-synapse-analytics-database"></a>从 Azure Synapse 分析数据库读入小型数据样本
    t0 = time.time()

    query = '''
        SELECT TOP 10000 t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
            f.tolls_amount, f.total_amount, f.tip_amount
        FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
        WHERE datepart("mi",t.pickup_datetime) = 1
        AND   t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
    '''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

샘플 테이블을 읽은 시간 = 14.096495초
검색된 행 및 열 수 = (1000, 21)

### <a name="descriptive-statistics"></a>기술 통계
이제 샘플링된 데이터를 탐색할 준비가 되었습니다. **trip\_distance**(또는 지정하도록 선택한 다른 모든 필드)에 대한 기술 통계부터 살펴봅니다.

    df1['trip_distance'].describe()

### <a name="visualization-box-plot-example"></a>시각화: 상자 그림 예제
다음으로 여정 거리에 대한 상자 그림을 확인하여 사분위수를 시각화합니다.

    df1.boxplot(column='trip_distance',return_type='dict')

![상자 그림 출력][1]

### <a name="visualization-distribution-plot-example"></a>시각화: 분포 그림 예제
샘플링된 여정 거리에 대한 분포 및 히스토그램을 시각화하는 그림입니다.

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![분포 그림 출력][2]

### <a name="visualization-bar-and-line-plots"></a>시각화: 가로 막대형 차트 및 꺾은선형 그림
이 예에서는 여정 거리를 5개의 bin으로 범주화하고 범주화 결과를 시각화합니다.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

위의 bin 분포를 다음과 같은 가로 막대형 차트 또는 꺾은선형 그림으로 그릴 수 있습니다.

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![가로 막대형 그림 출력][3]

및

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![꺾은선형 그림 출력][4]

### <a name="visualization-scatterplot-examples"></a>시각화: 산점도 예제
**trip\_time\_in\_secs** 및 **trip\_distance** 사이의 산점도를 표시하여 상관관계가 있는지 확인합니다.

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![시간과 거리의 사이의 관계에 대한 산점도 출력][6]

마찬가지로 **rate\_code** 및 **trip\_distance** 간의 관계를 확인할 수 있습니다.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![코드와 거리의 사이의 관계에 대한 산점도 출력][8]

### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>IPython Notebook에서 SQL 쿼리를 사용하여 샘플링된 데이터에서 데이터 탐색
在本部分中，我们将使用在前面创建的新表中保存的抽样数据来浏览数据分布。 可以使用原始表执行类似的探索。

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>탐색: 샘플링된 테이블의 행과 열 개수를 보고합니다.
    nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
    print 'Number of rows in sample = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
    print 'Number of columns in sample = %d' % ncols.iloc[0,0]

#### <a name="exploration-tippednot-tripped-distribution"></a>탐색: 왕복/비왕복 분포
    query = '''
        SELECT tipped, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tipped
        '''

    pd.read_sql(query, conn)

#### <a name="exploration-tip-class-distribution"></a>탐색: 팁 클래스 분포
    query = '''
        SELECT tip_class, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tip_class
    '''

    tip_class_dist = pd.read_sql(query, conn)

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>탐색: 클래스에 의해 팁 분포 그리기
    tip_class_dist['tip_freq'].plot(kind='bar')

![그림 #26][26]

#### <a name="exploration-daily-distribution-of-trips"></a>탐색: 일일 여정 분포
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>탐색: medallion당 여정 분포
    query = '''
        SELECT medallion,count(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>탐색: medallion 및 hack license별 여정 분포
    query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
    pd.read_sql(query,conn)


#### <a name="exploration-trip-time-distribution"></a>탐색: 여정 시간 분포
    query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-trip-distance-distribution"></a>탐색: 여정 거리 분포
    query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-payment-type-distribution"></a>탐색: 지불 형식 분포
    query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
    pd.read_sql(query,conn)

#### <a name="verify-the-final-form-of-the-featurized-table"></a>기능화한 테이블의 최종 양식 확인
    query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
    pd.read_sql(query,conn)

## <a name="mlmodel"></a>Azure 기계 학습에서 모델 빌드
이제 [Azure Machine Learning](https://studio.azureml.net)에서 모델 빌드 및 모델 배포를 진행할 준비가 완료되었습니다. 이전에 식별된 다음과 같은 예측 문제에 데이터를 사용할 준비가 되었습니다.

1. **이진 분류**: 여정에 대해 팁이 지불되었는지 여부를 예측합니다.
2. **다중 클래스 분류**: 이전에 정의한 클래스에 따라 지불된 팁의 범위를 예측합니다.
3. **회귀 작업**: 여정에 대해 지불된 팁의 금액을 예측합니다.

若要开始建模练习，请登录到**Azure 机器学习（经典）** 工作区。 如果尚未创建机器学习工作区，请参阅[创建 Azure 机器学习 Studio （经典）工作](../studio/create-workspace.md)区。

1. 若要开始 Azure 机器学习，请参阅[什么是 Azure 机器学习 Studio （经典）？](../studio/what-is-ml-studio.md)
2. 登录到[Azure 机器学习 Studio （经典）](https://studio.azureml.net)。
3. 机器学习 Studio （经典）主页提供丰富的信息、视频、教程、指向模块参考的链接和其他资源。 有关 Azure 机器学习的详细信息，请参阅[Azure 机器学习文档中心](https://azure.microsoft.com/documentation/services/machine-learning/)。

일반적인 학습 실험은 다음 단계로 구성됩니다.

1. **+새** 실험 만들기
2. 将数据导入 Azure 机器学习 Studio （经典）。
3. 根据需要预先处理、转换和处理数据。
4. 필요에 따라 기능을 생성합니다.
5. 데이터를 학습/유효성 검사/테스트 데이터 세트로 분할하거나, 각각에 대한 별도의 데이터 세트를 만듭니다.
6. 해결할 학습 문제에 따라 하나 이상의 기계 학습 알고리즘을 선택하세요. 例如，二元分类、多类分类和回归。
7. 학습 데이터 세트를 사용하여 하나 이상의 모델을 학습합니다.
8. 학습된 모델을 사용하여 유효성 검사 데이터 세트의 점수를 매깁니다.
9. 모델을 평가하여 학습 문제에 대한 관련 메트릭을 컴퓨팅합니다.
10. 优化模型并选择要部署的最佳模型。

在此练习中，我们已在 Azure Synapse Analytics 中探索并设计了数据，并决定了要在 Azure 机器学习 Studio （经典）中引入的示例大小。 예측 모델 중 하나 이상을 빌드하는 절차는 다음과 같습니다.

1. 使用 "**数据输入和输出**" 部分提供的 "[导入数据模块，][导入数据模块，]" 模块，将数据导入 Azure 机器学习 Studio （经典）。 자세한 내용은 [데이터 가져오기][导入数据模块，] 참조 페이지를 참조하세요.

    ![Azure ML 데이터 가져오기][17]
2. **속성** 패널에서 **Azure SQL Database**를 **데이터 원본**으로 선택합니다.
3. **데이터베이스 서버 이름** 필드에 데이터베이스 DNS 이름을 입력합니다. 형식: `tcp:<your_virtual_machine_DNS_name>,1433`
4. **데이터베이스 이름** 을 해당 필드에 입력합니다.
5. **서버 사용자 계정 이름**에 *SQL 사용자 이름*을 입력하고, **서버 사용자 계정 암호**에 *암호*를 입력합니다.
7. 在 "**数据库查询**" 编辑文本区域中，粘贴用于提取必需的数据库字段（包括所有计算字段，如标签）的查询，然后向下采样数据，使数据达到所需的样本大小。

下图列出了直接从 Azure Synapse Analytics 数据库读取数据的二进制分类试验的示例（请记住，将表名称替换 nyctaxi_trip，nyctaxi_fare 然后按在演练）。 다중 클래스 분류 및 회귀 문제에 대한 유사한 실험을 생성할 수 있습니다.

![Azure 기계 학습][10]

> [!IMPORTANT]
> 이전 섹션에 제공된 모델링 데이터 추출 및 샘플링 쿼리 예제에서는 **세 가지 모델링 연습에 대한 모든 레이블이 쿼리에 포함되어 있습니다**. 각 모델링 연습의 중요한(필수) 단계는 다른 두 문제에 대한 필요 없는 레이블 및 다른 모든 **목표 누설**을 **제외**하는 것입니다. 예를 들어 이진 분류를 사용할 때는 레이블 **tipped**를 사용하고, **tip\_class**, **tip\_amount** 및 **total\_amount** 필드를 제외합니다. 이러한 필드는 지불된 팁을 의미하므로 목표 누설입니다.
>
> 若要排除任何不必要的列或目标泄漏，可以使用 "[选择数据集中的列][select-columns]" 模块或[编辑元数据][edit-metadata]。 有关详细信息，请参阅 "[选择数据集中的列][select-columns]" 和 "[编辑元数据][edit-metadata]引用页"。
>
>

## <a name="mldeploy"></a>Azure 기계 학습에서 모델 배포
모델이 준비된 경우 실험에서 직접 웹 서비스로 쉽게 배포할 수 있습니다. Azure ML 웹 서비스 배포에 대한 자세한 내용은 [Azure Machine Learning 웹 서비스 배포](../studio/deploy-a-machine-learning-web-service.md)를 참조하세요.

새 웹 서비스를 배포하려면 다음을 수행해야 합니다.

1. 점수 매기기 실험을 만듭니다.
2. 웹 서비스를 배포합니다.

**완료된** 학습 실험에서 점수 매기기 실험을 만들려면 아래쪽 작업 모음에서 **점수 매기기 실험 만들기**를 클릭합니다.

![Azure 점수 매기기][18]

Azure Machine Learning에서는 학습 실험의 구성 요소를 기반으로 점수 매기기 실험을 만듭니다. 특히 다음 작업을 수행합니다.

1. 학습된 모델을 저장하고 모델 학습 모듈을 제거합니다.
2. 필요한 입력 데이터 스키마를 나타내는 논리적 **입력 포트** 를 식별합니다.
3. 필요한 웹 서비스 출력 스키마를 나타내는 논리적 **출력 포트** 를 식별합니다.

创建评分实验后，请检查结果，并根据需要进行调整。 典型的调整是将输入数据集或查询替换为排除标签字段的数据集，因为在调用服务时，这些标签字段将不会映射到架构。 更好的做法是，将输入数据集和/或查询的大小减小到一些记录，足以指示输入架构。 对于输出端口，通常使用 "[选择数据集中的列][select-columns]" 模块排除所有输入字段并仅在输出中包括**评分标签**和**评分概率**。

샘플 점수 매기기 실험은 아래 그림에서 제공됩니다. 배포할 준비가 되면 아래쪽 작업 모음에서 **웹 서비스 게시** 단추를 클릭합니다.

![Azure ML 게시][11]

## <a name="summary"></a>요약
이 연습 자습서에서 수행한 작업을 설명하면 Azure 데이터 과학 환경을 만들고 대용량 공용 데이터 세트로 작업하며, 팀 데이터 과학 프로세스를 통해 가져오고, 데이터 취득에서 모델 학습, Azure Machine Learning 웹 서비스의 배포에 이르는 모든 과정을 수행했습니다.

### <a name="license-information"></a>라이선스 정보
이 샘플 연습 및 이와 함께 제공되는 스크립트와 IPython Notebook은 MIT 라이선스에 따라 Microsoft에서 공유한 것입니다. 자세한 내용은 GitHub의 샘플 코드 디렉터리에 있는 LICENSE.txt 파일을 참조하세요.

## <a name="references"></a>참조
- [Andrés Monroy NYC 出租车行程下载页](https://www.andresmh.com/nyctaxitrips/)
- [丽丽 Whong) FOILing NYC 的出租车行程数据](https://chriswhong.com/open-data/foil_nyc_taxi/)
- [NYC 出租车和礼车委员会研究和统计信息](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[1]: ./media/sqldw-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/sqldw-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/sqldw-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/sqldw-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/sqldw-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/sqldw-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/sqldw-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/sqldw-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/sqldw-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/sqldw-walkthrough/azuremltrain.png
[11]: ./media/sqldw-walkthrough/azuremlpublish.png
[12]: ./media/sqldw-walkthrough/ssmsconnect.png
[13]: ./media/sqldw-walkthrough/executescript.png
[14]: ./media/sqldw-walkthrough/sqlserverproperties.png
[15]: ./media/sqldw-walkthrough/sqldefaultdirs.png
[16]: ./media/sqldw-walkthrough/bulkimport.png
[17]: ./media/sqldw-walkthrough/amlreader.png
[18]: ./media/sqldw-walkthrough/amlscoring.png
[19]: ./media/sqldw-walkthrough/ps_download_scripts.png
[20]: ./media/sqldw-walkthrough/ps_load_data.png
[21]: ./media/sqldw-walkthrough/azcopy-overwrite.png
[22]: ./media/sqldw-walkthrough/ipnb-service-aml-1.png
[23]: ./media/sqldw-walkthrough/ipnb-service-aml-2.png
[24]: ./media/sqldw-walkthrough/ipnb-service-aml-3.png
[25]: ./media/sqldw-walkthrough/ipnb-service-aml-4.png
[26]: ./media/sqldw-walkthrough/tip_class_hist_1.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[导入数据模块，]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
