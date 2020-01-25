---
title: SQL Server 가상 머신에서 데이터 탐색 - Team Data Science Process
description: SQL 또는 Python과 같은 프로그래밍 언어를 사용하여 Azure에서 SQL Server VM에 저장된 데이터를 탐색하는 방법입니다.
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
ms.openlocfilehash: ae8c7c43ecbf9bc625e1e46be3e2c71c8d57b6f7
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720089"
---
# <a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Azure의 SQL Server Virtual Machine에서 데이터 탐색

이 문서에서는 Azure에서 SQL Server VM에 저장된 데이터를 탐색하는 방법을 다룹니다. 使用 SQL 或 Python 来检查数据。

이 작업은 [팀 데이터 과학 프로세스](overview.md)의 단계입니다.

> [!NOTE]
> 이 문서의 샘플 SQL 문에서는 데이터가 SQL Server에 있는 것으로 가정합니다. 그렇지 않은 경우 데이터를 SQL Server로 이동하는 방법은 클라우드 데이터 과학 프로세스 맵을 참조하세요.
> 
> 

## <a name="sql-dataexploration"></a>SQL 스크립트로 SQL 데이터 탐색
SQL Server에서 데이터 저장소를 탐색하는 데 사용할 수 있는 몇 가지 샘플 SQL 스크립트는 다음과 같습니다.

1. 일별 관찰 수 가져오기
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. 범주 열의 수준 가져오기
   
    `select  distinct <column_name> from <databasename>`
3. 두 범주 열 조합의 수준 수 가져오기 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. 숫자 열의 분포 가져오기
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [!NOTE]
> 실용적인 예제에는 [NYC Taxi 데이터 세트](https://www.andresmh.com/nyctaxitrips/)를 사용할 수 있으며, 엔드투엔드 연습에 [IPython Notebook 및 SQL Server를 사용한 NYC 데이터 랭글링](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb)이라는 IPNB를 참조할 수 있습니다.
> 
> 

## <a name="python"></a>Python으로 SQL 데이터 탐색
데이터가 SQL Server에 있는 경우 Python을 사용하여 데이터를 탐색하고 기능을 생성하는 작업은 [데이터 과학 환경에서 Azure Blob 데이터 처리](data-blob.md)에 설명된 대로 Python을 사용하여 Azure Blob의 데이터를 처리하는 것과 유사합니다. 将数据从数据库加载到 pandas 数据帧中，然后可以进一步处理。 데이터베이스에 연결하여 데이터 프레임으로 데이터를 로드하는 프로세스는 이 섹션에 설명되어 있습니다.

다음 연결 문자열 형식은 pyodbc를 사용(servername, dbname, username 및 password를 특정 값으로 대체)하여 Python에서 SQL Server 데이터베이스 연결하는 데 사용될 수 있습니다.

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

Python의 [Pandas 라이브러리](https://pandas.pydata.org/) 에서는 Python 프로그래밍용 데이터 조작을 위한 다양한 데이터 구조 및 데이터 분석 도구 집합을 제공합니다. 다음 코드는 SQL Server 데이터베이스에서 Pandas 데이터 프레임으로 반환되는 결과를 읽습니다.

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

이제 [데이터 과학 환경에서 Azure Blob 데이터 처리](data-blob.md)토픽에 설명된 대로 Pandas DataFrame으로 작업할 수 있습니다.

## <a name="the-team-data-science-process-in-action-example"></a>실행 중인 팀 데이터 과학 프로세스 예제
공용 데이터 세트를 사용하여 Cortana 분석 프로세스의 엔드투엔드 연습 예제는 [실행 중인 팀 데이터 과학 프로세스: SQL Server 사용](sql-walkthrough.md)을 참조하세요.

