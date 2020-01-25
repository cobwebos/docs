---
title: 使用 R 和 SQL 数据类型和对象
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 了解如何使用机器学习服务（预览版）在 R 中使用 Azure SQL 数据库中的数据类型和数据对象，包括你可能会遇到的常见问题。
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: 7dfd12729c5697d1935d098cbd4ed863a4551acd
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719868"
---
# <a name="work-with-r-and-sql-data-in-azure-sql-database-machine-learning-services-preview"></a>使用 Azure SQL 数据库中的 R 和 SQL 数据机器学习服务（预览版）

本文介绍了在[AZURE SQL 数据库中机器学习服务（与 R）](sql-database-machine-learning-services-overview.md)中的 r 和 SQL 数据库之间移动数据时可能会遇到的一些常见问题。 이 연습은 고유 스크립트로 데이터 작업을 수행할 때 필요한 기본 배경 지식을 제공합니다.

你可能会遇到的常见问题包括：

- 경우에 따라 데이터 형식이 일치하지 않음
- 암시적 변환이 발생할 수 있음
- 경우에 따라 캐스트 및 변환 작업이 필요함
- R 및 SQL이 서로 다른 데이터 개체를 사용함

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>필수 조건

- Azure 구독이 없는 경우 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/).

- 이러한 연습에서 예제 코드를 실행하려면 먼저 Machine Learning Services(R 포함)를 사용하도록 설정된 Azure SQL 데이터베이스가 있어야 합니다. Microsoft는 공개 미리 보기 기간에는 사용자를 온보딩하고 기존 또는 새 데이터베이스에 기계 학습을 사용하도록 설정합니다. [미리 보기에 가입](sql-database-machine-learning-services-overview.md#signup) 단계를 수행하세요.

- 최신 [SSMS(SQL Server Management Studio)](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)를 설치했는지 확인합니다. 다른 데이터베이스 관리 또는 쿼리 도구를 사용하여 R 스크립트를 실행할 수 있지만, 이 빠른 시작에서는 SSMS를 사용합니다.

## <a name="working-with-a-data-frame"></a>使用数据帧

当脚本从 R 返回 SQL 返回结果时，它必须将数据作为**数据帧**返回。 스크립트에서 생성하는 다른 형식의 개체는 목록, 요소, 벡터 또는 이진 데이터이든 관계없이 저장 프로시저 결과의 일부로 출력하려면 데이터 프레임으로 변환되어야 합니다. 다행히도 기타 개체를 데이터 프레임으로 변경하는 기능을 지원하는 여러 R 함수가 있습니다. 您甚至可以序列化二进制模型，并在数据帧中返回它，稍后将在本文中进行此操作。

首先，让我们试验一些基本的 R 对象-矢量、矩阵和列表，并了解如何转换到数据帧会更改传递到 SQL 的输出。

R에서 두 가지 "Hello World" 스크립트를 비교합니다. 두 스크립트가 거의 동일하게 보이지만 첫 번째 스크립트는 3개 값의 단일 열을 반환하고, 두 번째 스크립트는 각각 하나의 단일 값이 포함된 세 개의 열을 반환합니다.

**예제 1**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
'
    , @input_data_1 = N'';
```

**예제 2**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' OutputDataSet<- data.frame(c("hello"), " ", c("world"));'
    , @input_data_1 = N'';
```

결과가 다른 이유는 무엇일까요?

일반적으로 R `str()` 명령을 사용하여 그 대답을 찾을 수 있습니다. R 스크립트에 `str(object_name)` 함수를 추가하여 지정된 R 개체의 데이터 스키마가 정보 메시지로 반환되도록 합니다. 可以在 SSMS 的 "**消息**" 选项卡中查看消息。

若要确定示例1和示例2的原因，请在每个语句的 `@script` 变量定义的末尾插入行 `str(OutputDataSet)`，如下所示：

**str 함수가 추가된 예제 1**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

**str 함수가 추가된 예제 2**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(c("hello"), " ", c("world"));
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

이제 **메시지**의 텍스트를 검토하여 출력이 다른 이유를 확인합니다.

**결과 - 예제 1**

```text
STDOUT message(s) from external script:
'data.frame':   3 obs. of  1 variable:
$ mytextvariable: Factor w/ 3 levels " ","hello","world": 2 1 3
```

**결과 - 예제 2**

```text
STDOUT message(s) from external script:
'data.frame':   1 obs. of  3 variables:
$ c..hello..: Factor w/ 1 level "hello": 1
$ X...      : Factor w/ 1 level " ": 1
$ c..world..: Factor w/ 1 level "world": 1
```

확인한 것처럼 R 구문을 약간 변경한 것이 결과의 스키마에 큰 영향을 미쳤습니다. 对于所有详细信息，R 数据类型中的差异在[Hadley Wickham 的 "高级 R" 的 "](http://adv-r.had.co.nz)*数据结构*" 部分的详细信息中进行了介绍。

우선은 R 개체를 데이터 프레임으로 강제 변환할 때 예상 결과를 확인해야 합니다.

> [!TIP]
> 你还可以使用 R 标识函数（如 `is.matrix``is.vector`）返回有关内部数据结构的信息。

## <a name="implicit-conversion-of-data-objects"></a>데이터 개체의 암시적 변환

如果两个数据对象具有相同的维数，或任何数据对象包含异类数据类型，则每个 R 数据对象都有其自己的规则，用于将值与其他数据对象结合使用。

例如，假设您要使用 R 执行矩阵乘法。您需要将包含三个值的单列矩阵乘以具有四个值的数组，并期望4x3 矩阵为结果。

먼저 작은 테스트 데이터 테이블을 만듭니다.

```sql
CREATE TABLE RTestData (col1 INT NOT NULL)

INSERT INTO RTestData
VALUES (1);

INSERT INTO RTestData
VALUES (10);

INSERT INTO RTestData
VALUES (100);
GO
```

现在，请运行以下脚本。

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:15);
OutputDataSet <- as.data.frame(x %*% y);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col1] INT
            , [Col2] INT
            , [Col3] INT
            , Col4 INT
            ));
```

내부적으로 3개 열이 포함된 열이 단일 열 행렬로 변환됩니다. 행렬은 R에서 특별한 경우인 배열이므로 두 인수가 일치하도록 `y`는 암시적으로 단일 열 행렬로 강제 변환됩니다.

**결과**

|Col1|Col2|Col3|Col4|
|---|---|---|---|
|12|13|14|15|
|120|130|140|150|
|1200|1300|1400|1500|

하지만 `y` 배열의 크기를 변경할 경우 발생하는 상황을 확인하세요.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:14);
OutputDataSet <- as.data.frame(y %*% x);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS(([Col1] INT));
```

이제 R는 결과로 단일 값을 반환합니다.

**결과**
    
|Col1|
|---|
|1542|

그 이유는 무엇입니까? 이 경우 두 인수가 동일한 길이의 벡터로 처리될 수 있기 때문에 R는 내부 곱을 행렬로 반환합니다.  这是预期的行为，具体取决于线性代数的规则。 但是，如果下游应用程序需要输出架构永远不会发生更改，则可能会导致问题！

## <a name="merge-or-multiply-columns-of-different-length"></a>다른 길이의 열 병합 또는 곱하기

R은 다양한 크기의 벡터를 사용하고 이러한 열 유사 구조를 데이터 프레임으로 결합하는 뛰어난 유연성을 제공합니다. 벡터 목록은 테이블처럼 보일 수 있지만 데이터 테이블에 적용되는 모든 규칙을 따르는 것은 아닙니다.

예를 들어 다음 스크립트는 길이가 6인 숫자 배열을 정의하고 R 변수 `df1`에 저장합니다. 然后，将该数值数组与包含三（3）个值的 RTestData 表的整数组合，以使新的数据帧 `df2`。

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
df1 <- as.data.frame( array(1:6) );
df2 <- as.data.frame( c( InputDataSet , df1 ));
OutputDataSet <- df2;
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col2] INT NOT NULL
            , [Col3] INT NOT NULL
            ));
```

데이터 프레임을 채우기 위해 R는 RTestData에서 검색된 요소를 `df1` 배열의 요소 수와 일치하도록 필요한 횟수만큼 반복합니다.

**결과**
    
|*Col2*|*Col3*|
|----|----|
|1|1|
|10|2|
|100|3|
|1|4|
|10|5|
|100|6|

请记住，数据帧只是看起来像是一个表，但实际上是一个向量列表。

## <a name="cast-or-convert-sql-data"></a>强制转换或转换 SQL 数据

R 和 SQL 不使用相同的数据类型，因此，在 SQL 中运行查询以获取数据，然后将其传递给 R 运行时，通常会发生某种类型的隐式转换。 在将数据从 R 返回到 SQL 时，会进行另一组转换。

- SQL 将数据从查询推送到 R 进程，并将其转换为内部表示形式以提高效率。
- R 런타임이 데이터를 data.frame 변수에 로드하고 데이터에서 고유한 작업을 수행합니다.
- 数据库引擎使用安全的内部连接将数据返回到 SQL，并以 SQL 数据类型表示数据。
- 您可以通过使用客户端或网络库（可以发出 SQL 查询并处理表格数据集）连接到 SQL 来获取数据。 이 클라이언트 애플리케이션은 다른 방식으로 데이터에 영향을 미칠 수 있습니다.

이 애플리케이션이 작동하는 방식을 확인하려면 [AdventureWorksDW](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks) 데이터 웨어하우스에서 이와 같은 쿼리를 실행합니다. 이 뷰는 예측 생성에 사용된 매출 데이터를 반환합니다.

```sql
USE AdventureWorksDW
GO

SELECT ReportingDate
    , CAST(ModelRegion AS VARCHAR(50)) AS ProductSeries
    , Amount
FROM [AdventureWorksDW].[dbo].[vTimeSeries]
WHERE [ModelRegion] = 'M200 Europe'
ORDER BY ReportingDate ASC
```

> [!NOTE]
> 모든 AdventureWorks 버전을 사용하거나 고유 데이터베이스를 사용해서 다른 쿼리를 만들 수 있습니다. 要点是尝试处理包含文本、日期时间和数字值的某些数据。

现在，请尝试使用此查询作为存储过程的输入。

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
str(InputDataSet);
OutputDataSet <- InputDataSet;
'
    , @input_data_1 = N'
           SELECT ReportingDate
         , CAST(ModelRegion as varchar(50)) as ProductSeries
         , Amount
           FROM [AdventureWorksDW].[dbo].[vTimeSeries]
           WHERE [ModelRegion] = ''M200 Europe''
           ORDER BY ReportingDate ASC ;'
WITH RESULT SETS undefined;
```

오류가 발생하면 쿼리 텍스트를 약간 편집해야 할 수 있습니다. 예를 들어 WHERE 절의 문자열 조건자는 두 개의 작은따옴표 집합으로 묶어야 합니다.

쿼리를 실행한 후 `str` 함수의 결과를 검토하여 R에서 입력 데이터가 어떻게 처리되는지 확인합니다.

**결과**

```text
STDOUT message(s) from external script: 'data.frame':    37 obs. of  3 variables:
STDOUT message(s) from external script: $ ReportingDate: POSIXct, format: "2010-12-24 23:00:00" "2010-12-24 23:00:00"
STDOUT message(s) from external script: $ ProductSeries: Factor w/ 1 levels "M200 Europe",..: 1 1 1 1 1 1 1 1 1 1
STDOUT message(s) from external script: $ Amount       : num  3400 16925 20350 16950 16950
```

- datetime 열은 R 데이터 형식 **POSIXct**를 사용하여 처리되었습니다.
- 텍스트 열 "ProductSeries"는 범주 변수를 의미하는 **요소**로 식별되었습니다. 문자열 값은 기본적으로 요소로 처리됩니다. R에 문자열을 전달하면 문자열은 내부에서 사용하도록 정수로 변환되고 다시 출력의 문자열에 매핑됩니다.

## <a name="summary"></a>요약

이러한 간단한 예제로부터 SQL 쿼리를 입력으로 전달할 때 데이터 변환으로 인한 효과를 확인할 필요성이 있다는 것을 알 수 있습니다. 由于 R 不支持某些 SQL 数据类型，因此请考虑使用以下方法来避免错误：

- 데이터를 미리 테스트하고 R 코드에 전달될 때 문제가 될 수 있는 스키마의 열 또는 값을 확인합니다.
- `SELECT *`를 사용하지 않고 입력 데이터 원본에서 열을 개별적으로 지정하고 각 열이 어떻게 처리되는지 알아봅니다.
- 문제를 방지하려면 입력 데이터를 준비할 때 필요에 따라 명시적 캐스트를 수행합니다.
- 오류를 일으키고 모델링에 유용하지 않은 데이터 열 전달(예: GUIDS 또는 rowguids)을 방지합니다.

有关支持的和不支持的 R 数据类型的详细信息，请参阅[r 库和数据类型](/sql/advanced-analytics/r/r-libraries-and-data-types)。
