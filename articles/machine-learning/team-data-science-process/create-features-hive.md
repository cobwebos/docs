---
title: 为 Azure HDInsight Hadoop 群集中的数据创建功能-团队数据科学过程
description: Azure HDInsight Hadoop 클러스터에 저장된 데이터의 기능을 생성하는 Hive 쿼리의 예입니다.
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
ms.openlocfilehash: c926aac3ea4360793ff52b616a55dc6198357c8a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721772"
---
# <a name="create-features-for-data-in-a-hadoop-cluster-using-hive-queries"></a>Hive 쿼리를 사용하여 Hadoop 클러스터의 데이터에 대한 기능 만들기
이 문서에는 Hive 쿼리를 사용하여 Azure HDInsight Hadoop 클러스터에 저장된 데이터에 대한 기능을 만드는 방법을 보여 줍니다. 이러한 Hive 쿼리는 제공된 스크립트인 포함된 Hive UDF(사용자 정의 함수)를 사용합니다.

기능을 만드는 데 필요한 작업은 메모리 집약적일 수 있습니다. 이 경우 Hive 쿼리의 성능이 더욱 중요해지며 특정 매개 변수를 조정하여 성능을 향상시킬 수 있습니다. 이러한 매개 변수를 조정하는 내용은 마지막 섹션에 설명되어 있습니다.

또한 [NYC Taxi Trip Data](https://chriswhong.com/open-data/foil_nyc_taxi/) 시나리오에 대해 제공되는 쿼리 예제가 [GitHub 리포지토리](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts)에도 제공됩니다. 이러한 쿼리는 이미 데이터 스키마가 지정되어 있으며 바로 제출하여 실행할 수 있습니다. 마지막 섹션에서는 사용자가 조정하여 Hive 쿼리 성능을 높일 수 있는 매개 변수에 대해서도 설명합니다.

이 작업은 [TDSP(팀 데이터 과학 프로세스)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)의 단계입니다.

## <a name="prerequisites"></a>필수 조건
이 문서에서는 사용자가 다음 작업을 수행한 것으로 가정합니다.

* Azure Storage 계정을 만들었습니다. 지침이 필요한 경우 [Azure Storage 계정 만들기](../../storage/common/storage-account-create.md)
* 사용자 지정된 Hadoop 클러스터에 HDInsight 서비스를 프로비전했습니다.  지침이 필요한 경우 [고급 분석을 위한 Azure HDInsight Hadoop 클러스터 사용자 지정](customize-hadoop-cluster.md)을 참조하세요.
* Azure HDInsight Hadoop 클러스터의 Hive 테이블에 데이터가 업로드되었습니다. 업로드되지 않은 경우 [데이터를 만들어서 Hive 테이블에 로드](move-hive-tables.md)의 지침에 따라 먼저 Hive 테이블에 데이터를 업로드합니다.
* 클러스터에 대한 원격 액세스가 설정되었습니다. 지침이 필요한 경우 [Hadoop 클러스터의 헤드 노드에 액세스](customize-hadoop-cluster.md)를 참조하세요.

## <a name="hive-featureengineering"></a>기능 생성
이 섹션에서는 Hive 쿼리를 사용하여 기능을 생성할 수 있는 방법의 몇 가지 예를 설명합니다. 추가 기능을 생성한 후 기존 테이블에 열로 추가하거나 추가 기능 및 기본 키를 사용하여 새 테이블을 만들어서 원래 테이블과 조인할 수 있습니다. 제시된 예는 다음과 같습니다.

1. [빈도 기반 기능 생성](#hive-frequencyfeature)
2. [이진 분류에서 범주 변수의 위험](#hive-riskfeature)
3. [날짜/시간 필드에서 기능 추출](#hive-datefeatures)
4. [텍스트 필드에서 기능 추출](#hive-textfeatures)
5. [GPS 좌표 사이의 거리 계산](#hive-gpsdistance)

### <a name="hive-frequencyfeature"></a>빈도 기반 기능 생성
범주 변수의 빈도 수준 또는 여러 범주 변수로 구성된 특정 조합의 빈도 수준을 계산하는 것이 매우 유용한 경우가 종종 있습니다. 사용자는 다음 스크립트를 사용하여 빈도를 계산할 수 있습니다.

        select
            a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
        from
        (
            select
                <column_name1>,<column_name2>, count(*) as sub_count
            from <databasename>.<tablename> group by <column_name1>, <column_name2>
        )a
        order by frequency desc;


### <a name="hive-riskfeature"></a>이진 분류에서 범주 변수의 위험
사용하는 모델에 숫자 기능만 허용되는 경우 이진 분류에서 숫자가 아닌 범주 변수를 숫자 기능으로 변환해야 합니다. 이렇게 변환하려면 숫자가 아닌 각 수준을 숫자 위험으로 바꾸면 됩니다. 이 섹션에서는 범주 변수의 위험 값(log odds)을 계산하는 몇 가지 일반적인 Hive 쿼리를 보여 줍니다.

        set smooth_param1=1;
        set smooth_param2=20;
        select
            <column_name1>,<column_name2>,
            ln((sum_target+${hiveconf:smooth_param1})/(record_count-sum_target+${hiveconf:smooth_param2}-${hiveconf:smooth_param1})) as risk
        from
            (
            select
                <column_nam1>, <column_name2>, sum(binary_target) as sum_target, sum(1) as record_count
            from
                (
                select
                    <column_name1>, <column_name2>, if(target_column>0,1,0) as binary_target
                from <databasename>.<tablename>
                )a
            group by <column_name1>, <column_name2>
            )b

이 예에서, 변수 `smooth_param1` 및 `smooth_param2`는 데이터에서 계산된 위험 값을 완화하도록 설정되었습니다. 위험 범위는 -Inf~Inf입니다. 0보다 큰 위험은 대상이 1일 확률이 0.5보다 크다는 것을 나타냅니다.

위험 테이블이 계산되면 사용자는 위험 값을 위험 테이블에 조인하여 위험 값을 할당할 수 있습니다. Hive 조인 쿼리는 이전 섹션에서 제공되었습니다.

### <a name="hive-datefeatures"></a>날짜/시간 필드에서 기능 추출
Hive는 날짜/시간 필드를 처리할 수 있는 UDF가 함께 제공됩니다. Hive의 기본 날짜/시간 형식은 'yyyy-MM-dd 00:00:00'입니다(예: '1970-01-01 12:21:32'). 이 섹션에서는 날짜/시간 필드에서 일 및 월을 추출하는 예제 및 기본 형식 이외의 형식으로 된 날짜/시간 문자열을 기본 형식의 날짜/시간 문자열로 변환하는 예제를 보여 줍니다.

        select day(<datetime field>), month(<datetime field>)
        from <databasename>.<tablename>;

此 Hive 查询假定 *\<日期时间字段 >* 采用默认日期时间格式。

날짜/시간 필드가 기본 형식이 아닌 경우 먼저 날짜/시간 필드를 Unix 타임스탬프로 변환한 후 Unix 타임스탬프를 기본 형식의 날짜/시간 문자열로 변환해야 합니다. 날짜/시간이 기본 형식으로 변환되면 포함된 날짜/시간 UDF를 적용하여 기능을 추출할 수 있습니다.

        select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
        from <databasename>.<tablename>;

在此查询中，如果 *> 的\<日期时间字段*的模式类似于*03/26/2015 12:04:39*，则应 `'MM/dd/yyyy HH:mm:ss'`*日期时间字段 > "的\<模式*。 이를 테스트하려면 다음 명령을 실행합니다.

        select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
        from hivesampletable limit 1;

이 쿼리에서 *hivesampletable* 은 기본적으로 클러스터가 프로비전될 때 모든 Azure HDInsight Hadoop 클러스터에 미리 설치된 상태로 제공됩니다.

### <a name="hive-textfeatures"></a>텍스트 필드에서 기능 추출
Hive 테이블에 텍스트 필드가 있고 이 텍스트 필드에 공백으로 구분된 단어 문자열이 포함되어 있으면 다음 쿼리는 문자열의 길이와 문자열에 포함된 단어 수를 추출합니다.

        select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
        from <databasename>.<tablename>;

### <a name="hive-gpsdistance"></a>GPS 좌표 사이의 거리 계산
이 섹션에 제공된 쿼리를 뉴욕시 택시 여행 데이터에 바로 적용할 수 있습니다. 이 쿼리의 목적은 Hive에 포함된 수학 함수를 적용하여 기능을 생성하는 방법을 보여 주는 것입니다.

이 쿼리에 사용된 필드는 승차 위치와 하차 위치의 GPS 좌표이며 이름은 *pickup\_longitude*, *pickup\_latitude*, *dropoff\_longitude* 및 *dropoff\_latitude*입니다. 태우는 좌표와 내리는 좌표 사이의 직접 거리를 계산하는 쿼리는 다음과 같습니다.

        set R=3959;
        set pi=radians(180);
        select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude,
            ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
            *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
            *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
            /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
            +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
            pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
        from nyctaxi.trip
        where pickup_longitude between -90 and 0
        and pickup_latitude between 30 and 90
        and dropoff_longitude between -90 and 0
        and dropoff_latitude between 30 and 90
        limit 10;

두 GPS 좌표 사이의 거리를 계산하는 수학 방정식은 <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">Movable Type Scripts</a> 사이트에서 찾을 수 있으며, 작성자는 Peter Lapisu입니다. 在此 Javascript 中，函数 `toRad()` 仅*lat_or_lon*pi/180，将度转换为弧度。 여기서 *lat_or_lon*은 위도 또는 경도입니다. Hive에서 `atan2` 함수를 제공하지 않고 `atan` 함수를 제공하므로 위의 Hive 쿼리에서는 <a href="https://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipedia</a>에서 제공하는 정의를 사용하여 `atan` 함수가 `atan2` 함수를 구현합니다.

![작업 영역 만들기](./media/create-features-hive/atan2new.png)

Hive에 포함된 UDF 전체 목록은 <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">Apache Hive wiki</a>의 **기본 제공 함수** 섹션에서 확인할 수 있습니다.  

## <a name="tuning"></a> 고급 항목: Hive 매개 변수를 조정하여 쿼리 속도 개선
Hive 클러스터의 기본 매개 변수 설정이 Hive 쿼리 및 쿼리에서 처리하는 데이터에 적합하지 않을 수 있습니다. 이 섹션에서는 사용자가 조정하여 Hive 쿼리 성능을 개선할 수 있는 일부 매개 변수에 대해 설명합니다. 사용자는 매개 변수 조정 쿼리를 먼저 추가한 후 데이터 처리 쿼리를 추가해야 합니다.

1. **Java 힙 공간**: 대용량 데이터 세트를 조인하거나 긴 레코드 처리하는 것과 관련된 쿼리에서 발생하는 일반적인 오류 중 하나는 **힙 공간 부족**입니다. *mapreduce.map.java.opts* 및 *mapreduce.task.io.sort.mb* 매개 변수를 원하는 값으로 설정하여 이 오류를 피할 수 있습니다. 다음은 예제입니다.
   
        set mapreduce.map.java.opts=-Xmx4096m;
        set mapreduce.task.io.sort.mb=-Xmx1024m;

    此参数将 4 GB 内存分配给 Java 堆空间，并通过为其分配更多内存来提高排序效率。 힙 공간과 관련된 작업 실패 오류가 발생할 경우 이러한 할당 방법을 사용하면 좋습니다.

1. **DFS 블록 크기**: 이 매개 변수는 파일 시스템에 저장되는 최소 데이터 단위를 설정합니다. 예를 들어 DFS 블록 크기가 128MB인 경우 128MB 이하의 모든 데이터는 단일 블록에 저장됩니다. 128MB보다 큰 데이터는 추가 블록에 할당됩니다. 
2. 블록 크기를 작게 할 경우 이름 노드에서 해당 파일과 관련된 블록을 찾으려면 훨씬 많은 요청을 처리해야 하므로 Hadoop에서 큰 오버헤드가 발생합니다. 기가바이트 단위(또는 그 이상)의 데이터를 처리할 때 권장되는 설정은 다음과 같습니다.

        set dfs.block.size=128m;

2. **Hive에서 조인 작업 최적화**: 맵/감소 프레임워크의 조인 작업은 일반적으로 감소 단계에서 수행되지만, 맵 단계("맵 조인"이라고도 함)에서 조인 작업을 예약하여 엄청난 이점을 얻을 수 있는 경우도 있습니다. 设置此选项：
   
       set hive.auto.convert.join=true;

3. **Hive에 매퍼 수 지정**: Hadoop을 사용하면 사용자가 리듀서 수를 설정할 수 있지만, 매퍼 수는 일반적으로 사용자가 설정하지 않습니다. 한 가지 팁으로, 맵 작업 각각의 크기가 다음과 같은 방법으로 결정되므로 *mapred.min.split.size* 및 *mapred.max.split.size* Hadoop 변수를 선택하여 매퍼 수를 어느 정도 조절할 수 있습니다.
   
        num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))
   
    일반적으로 기본값은 다음과 같습니다.
    
   - *mapred.min.split.size*: 0
   - *mapred.max.split.size*: **Long.MAX** 
   - *dfs.block.size*: 64MB

     보시는 것처럼 데이터 크기를 고려하려 이러한 매개 변수를 "설정"하면 사용되는 매퍼 수를 조정할 수 있습니다.

4. 아래에 Hive 성능을 최적화하는 **고급 옵션** 몇 가지가 추가로 설명되어 있습니다. 这些选项允许您设置分配的内存来映射和减少任务，并可用于调整性能。 주의할 사항으로, *mapreduce.reduce.memory.mb* 가 Hadoop 클러스터에 있는 각 작업자 노드의 실제 메모리 크기보다 크면 안 됩니다.
   
        set mapreduce.map.memory.mb = 2048;
        set mapreduce.reduce.memory.mb=6144;
        set mapreduce.reduce.java.opts=-Xmx8192m;
        set mapred.reduce.tasks=128;
        set mapred.tasktracker.reduce.tasks.maximum=128;

