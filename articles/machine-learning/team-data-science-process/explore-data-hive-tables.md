---
title: Hive 쿼리를 사용하여 Hive 테이블의 데이터 탐색 - Team Data Science Process
description: HDInsight Hadoop 클러스터의 Hive 테이블에서 데이터를 탐색하는 데 사용된 샘플 Hive 스크립트를 사용합니다.
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
ms.openlocfilehash: 75dce2b5a83d13fe4a7d166595456e9a8d6324ba
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722163"
---
# <a name="explore-data-in-hive-tables-with-hive-queries"></a>Hive 쿼리를 사용하여 Hive 테이블의 데이터 탐색

이 문서는 HDInsight Hadoop 클러스터의 Hive 테이블에서 데이터를 탐색하는 데 사용된 샘플 Hive 스크립트를 제공합니다.

이 작업은 [팀 데이터 과학 프로세스](overview.md)의 단계입니다.

## <a name="prerequisites"></a>필수 조건
이 문서에서는 사용자가 다음 작업을 수행한 것으로 가정합니다.

* Azure Storage 계정을 만들었습니다. 지침이 필요한 경우 [Azure Storage 계정 만들기](../../storage/common/storage-account-create.md)
* 사용자 지정된 Hadoop 클러스터에 HDInsight 서비스를 프로비전했습니다. 지침이 필요한 경우 [고급 분석을 위한 Azure HDInsight Hadoop 클러스터 사용자 지정](customize-hadoop-cluster.md)을 참조하세요.
* Azure HDInsight Hadoop 클러스터의 Hive 테이블에 데이터가 업로드되었습니다. 업로드되지 않은 경우 [데이터를 만들어서 Hive 테이블에 로드](move-hive-tables.md) 의 지침에 따라 먼저 Hive 테이블에 데이터를 업로드하세요.
* 클러스터에 대한 원격 액세스가 설정되었습니다. 지침이 필요한 경우 [Hadoop 클러스터의 헤드 노드에 액세스](customize-hadoop-cluster.md)를 참조하세요.
* Hive 쿼리를 제출하는 방법에 대한 지침이 필요한 경우 [Hive 쿼리를 제출하는 방법](move-hive-tables.md#submit)

## <a name="example-hive-query-scripts-for-data-exploration"></a>데이터 탐색에 대한 예제 Hive 쿼리 스크립트
1. 파티션당 관찰 수 가져오기 `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`
2. 일별 관찰 수 가져오기 `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`
3. 범주 열의 수준 가져오기  
    `SELECT  distinct <column_name> from <databasename>.<tablename>`
4. 두 범주 열 조합의 수준 수 가져오기 `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`
5. 숫자 열의 분포 가져오기  
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`
6. 두 조인 테이블의 레코드 추출
   
        SELECT
            a.<common_columnname1> as <new_name1>,
            a.<common_columnname2> as <new_name2>,
            a.<a_column_name1> as <new_name3>,
            a.<a_column_name2> as <new_name4>,
            b.<b_column_name1> as <new_name5>,
            b.<b_column_name2> as <new_name6>
        FROM
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <a_column_name1>,
                <a_column_name2>,
            FROM <databasename>.<tablename1>
            ) a
            join
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <b_column_name1>,
                <b_column_name2>,
            FROM <databasename>.<tablename2>
            ) b
            ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>택시 여정 데이터 시나리오에 대한 추가 쿼리 스크립트
또한 [NYC Taxi Trip Data](https://chriswhong.com/open-data/foil_nyc_taxi/) 시나리오에 대한 쿼리 예제가 [GitHub 리포지토리](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts)에 제공됩니다. 이러한 쿼리는 이미 데이터 스키마가 지정되어 있으며 바로 제출하여 실행할 수 있습니다.

