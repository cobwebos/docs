---
title: "适用于 Azure Data Lake 的 U-SQL可编程性指南 | Microsoft Docs"
description: "了解 Azure Data Lake 中可用于创建基于云的大数据平台的服务集。"
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
ms.assetid: 63be271e-7c44-4d19-9897-c2913ee9599d
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/30/2017
ms.author: saveenr
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: e4e298475d7be7d51c8bd55be498371ed6ce77a9
ms.contentlocale: zh-cn
ms.lasthandoff: 07/04/2017


---

# <a name="u-sql-programmability-guide"></a>U-SQL 可编程性指南

U-SQL 是为大数据类型的工作负荷设计的查询语言。 U-SQL 的一个独有特点是将类似 SQL 的声明性语言与 C# 提供的可扩展性和可编程性结合起来。 本指南着重介绍通过 C# 实现的 U-SQL 语言的可扩展性和可编程性。

## <a name="requirements"></a>要求

下载并安装[针对 Visual Studio 的 Azure Data Lake 工具](https://www.microsoft.com/download/details.aspx?id=49504)。

## <a name="get-started-with-u-sql"></a>U-SQL 入门  

请看以下 U-SQL 脚本：

```
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso",   1500.0, "2017-03-39"),
            ("Woodgrove", 2700.0, "2017-04-10")
        ) AS 
              D( customer, amount );
@results =
    SELECT
        customer,
    amount,
    date
    FROM @a;    
```

该脚本定义了 @a 行集并使用 @a 创建了 @results 行集。

## <a name="c-types-and-expressions-in-u-sql-script"></a>U-SQL 脚本中的 C# 类型及表达式

U-SQL 表达式是与 U-SQL 逻辑运算（如 `AND`、`OR` 和 `NOT`）配合使用的 C# 表达式。 U-SQL 表达式可与 SELECT、0EXTRACT、WHERE、HAVING、GROUP BY 和 DECLARE 配合使用。

例如，以下脚本将字符串分析为 SELECT 子句中的 DateTime 值。

```
@results =
    SELECT
        customer,
    amount,
    DateTime.Parse(date) AS date
    FROM @a;    
```

以下脚本将字符串分析为 DECLARE 语句中的 DateTime 值。

```
DECLARE @d DateTime = ToDateTime.Date("2016/01/01");
```

### <a name="use-c-expressions-for-data-type-conversions"></a>使用 C# 表达式进行数据类型转换
以下示例演示如何使用 C# 表达式执行日期时间数据转换。 在此特定方案中，字符串日期/时间数据转换为标准日期时间（采用午夜 00:00:00 时间表示法）。

```
DECLARE @dt String = "2016-07-06 10:23:15";

@rs1 =
    SELECT 
        Convert.ToDateTime(Convert.ToDateTime(@dt).ToString("yyyy-MM-dd")) AS dt,
        dt AS olddt
    FROM @rs0;
OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

### <a name="use-c-expressions-for-todays-date"></a>使用 C# 表达式显示今天的日期
可使用下面的 C# 表达式拉取今天的日期：

```
DateTime.Now.ToString("M/d/yyyy")
```

以下示例演示如何在脚本中使用此表达式：

```
@rs1 =
    SELECT
        MAX(guid) AS start_id,
        MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        user,
        des
    FROM @rs0
    GROUP BY user, des;
```



## <a name="using-net-assemblies"></a>使用 .NET 程序集
U-SQL 扩展性模型很大程度取决于添加自定义代码的能力。 目前，U-SQL 提供添加你自己的基于 Microsoft .NET 的代码的简单方法（具体而言是 C#）。 但是，你也可以添加用其他 .NET 语言编写的自定义代码，例如 VB.NET 或 F#。 

### <a name="register-a-net-assembly"></a>注册 .NET 程序集

使用 CREATE ASSEMBLY 语句将 .NET 程序集放入 U-SQL 数据库。 如果程序集位于数据库，则 U-SQL 脚本可以通过 REFERENCE ASSEMBLY 语句使用这些程序集。 

以下代码演示如何注册程序集：

```
CREATE ASSEMBLY MyDB.[MyAssembly]
    FROM "/myassembly.dll";
```

以下代码演示如何引用程序集：

```
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

请参阅[程序集注册说明](https://blogs.msdn.microsoft.com/azuredatalake/2016/08/26/how-to-register-u-sql-assemblies-in-your-u-sql-catalog/)，了解本主题的详细信息。


### <a name="use-assembly-versioning"></a>使用程序集版本控制
U-SQL 当前使用 .NET Framework 4.5 版本。 因此请确保自己的程序集与该版本的运行时兼容。

如上所述，U-SQL 以 64 位 (x64) 格式运行代码。 因此请确保将代码编译为在 x64 上运行。 否则将收到上面所示的不正确格式错误。

每个上传的程序集 DLL、资源文件（如不同的运行时、本机程序集或配置文件）最大可为 400 MB。 部署的资源（通过 DEPLOY RESOURCE 或引用程序集部署）的总大小及其附加文件不能超过 3 GB。

最后请注意，每个 U-SQL 数据库仅可包含任何给定程序集的一个版本。 例如，如果同时需要 NewtonSoft Json.Net 库的版本 7 和版本 8，则需要将它们注册到两个不同的数据库。 此外，每个脚本仅可引用给定程序集 DLL 的一个版本。 在这一方面，U-SQL 遵循 C# 程序集管理和版本控制语义。


## <a name="use-user-defined-functions-udf"></a>使用用户定义的函数 (UDF)
U-SQL 用户定义的函数或 UDF 是编程例程，可接受参数、执行操作（例如复杂计算）并将操作的结果以值的形式返回。 UDF 的返回值只能是单个标量。 与任何其他 C# 标量函数相似，U-SQL UDF可在 U-SQL 基本脚本中进行调用。

我们建议将 U-SQL 用户定义的函数初始化为**公共**和**静态**。

```
public static string MyFunction(string param1)
{
    return "my result";
}
```

首先来看一个创建 UDF 的简单示例。

在此用例方案中，需要确定会计时段，包括特定用户首次登录的会计季度和会计月份。 本方案中适用年份的第一个会计月份为 6 月。

为计算会计时段，引入以下 C# 函数：

```
public static string GetFiscalPeriod(DateTime dt)
{
    int FiscalMonth=0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter=0;
    if (FiscalMonth >=1 && FiscalMonth<=3)
    {
    FiscalQuarter = 1;
    }
    if (FiscalMonth >= 4 && FiscalMonth <= 6)
    {
    FiscalQuarter = 2;
    }
    if (FiscalMonth >= 7 && FiscalMonth <= 9)
    {
    FiscalQuarter = 3;
    }
    if (FiscalMonth >= 10 && FiscalMonth <= 12)
    {
    FiscalQuarter = 4;
    }

    return "Q" + FiscalQuarter.ToString() + ":P" + FiscalMonth.ToString();
}
```

它能简单地计算会计月份及季度并返回字符串值。 对于 6 月（第一个会计季度的第一个月），我们使用“Q1:P1”。 对于 7 月，我们使用“Q1:P2”，以此类推。

这是将用于 U-SQL 项目的一个常规 C# 函数。

下面是此方案中代码隐藏部分的样子：

```
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        public static string GetFiscalPeriod(DateTime dt)
        {
            int FiscalMonth=0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter=0;
            if (FiscalMonth >=1 && FiscalMonth<=3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return "Q" + FiscalQuarter.ToString() + ":" + FiscalMonth.ToString();
        }

    }

}
```

现在从基本 U-SQL 脚本调用此函数。 为此，必须提供函数的完全限定名称，包括命名空间（在本例中为 NameSpace.Class.Function(parameter)）。

```
USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

下面是实际的 U-SQL 基本脚本：

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt DateTime,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");

@rs1 =
    SELECT
        MAX(guid) AS start_id,
    MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

下面是脚本执行的输出文件：

```
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

本示例演示 U-SQL 中内联 UDF 的简单用法。

### <a name="keep-state-between-udf-invocations"></a>保持 UDF 调用之间的状态
在通过代码隐藏全局变量利用交互性时，U-SQL C# 可编程性对象会更加复杂。 来看看以下这个企业用例场景。

在大型组织中，用户可在多种内部应用程序之间切换。 其中可能包括 Microsoft Dynamics CRM、PowerBI 等。 客户希望对用户切换不同应用程序的方式、使用趋势等进行遥测分析。 企业的目标是优化应用程序的使用。 他们可能会合并不同的应用程序或特定的登录例程。

要实现此目标，必须确定会话 ID 和上次发生的会话之间的延迟时间。

需要找到上一次登录，并将其分配到正在生成到相同应用程序中的所有会话中。 第一个难题是 U-SQL 基本脚本将不允许使用 LAG 函数对已计算的列进行计算。 第二个难题是必须为同一时间段内的所有会话保留特定会话。

为解决此问题，我们使用了代码隐藏部分内部的全局变量：`static public string globalSession;`。

此全局变量在脚本执行期间将应用于整个行集。

下面是 U-SQL 程序的代码隐藏部分：

```
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQLApplication21
{
    public class UserSession
    {
        static public string globalSession;
        static public string StampUserSession(string eventTime, string PreviousRow, string Session)
        {

            if (!string.IsNullOrEmpty(PreviousRow))
            {
                double timeGap = Convert.ToDateTime(eventTime).Subtract(Convert.ToDateTime(PreviousRow)).TotalMinutes;
                if (timeGap <= 60) {return Session;}
                else {return Guid.NewGuid().ToString();}
            }
            else {return Guid.NewGuid().ToString();}

        }

        static public string getStampUserSession(string Session)
        {
            if (Session != globalSession && !string.IsNullOrEmpty(Session)) { globalSession = Session; }
            return globalSession;
        }

    }
}
```

本示例显示全局变量 `static public string globalSession;` 在 `getStampUserSession` 函数内部使用，并且会在每次更改会话参数时重新进行初始化。

U-SQL 基本脚本如下所示：

```
DECLARE @in string = @"\UserSession\test1.tsv";
DECLARE @out1 string = @"\UserSession\Out1.csv";
DECLARE @out2 string = @"\UserSession\Out2.csv";
DECLARE @out3 string = @"\UserSession\Out3.csv";

@records =
    EXTRACT DataId string,
            EventDateTime string,           
            UserName string,
            UserSessionTimestamp string

    FROM @in
    USING Extractors.Tsv();

@rs1 =
    SELECT 
        EventDateTime,
        UserName,
    LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,          
        string.IsNullOrEmpty(LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,           
        USQLApplication21.UserSession.StampUserSession
           (
            EventDateTime,
            LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC),
            LAG(UserSessionTimestamp, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)
           ) AS UserSessionTimestamp
    FROM @records;

@rs2 =
    SELECT 
        EventDateTime,
        UserName,
        LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,
        string.IsNullOrEmpty( LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,
        USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp) AS UserSessionTimestamp
    FROM @rs1
    WHERE UserName != "UserName";

OUTPUT @rs2
    TO @out2
    ORDER BY UserName, EventDateTime ASC
    USING Outputters.Csv();
```

`USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` 函数在第二次“内存行集”计算期间在此处进行调用。 它传递 `UserSessionTimestamp` 列，并在 `UserSessionTimestamp` 更改之前返回值。

输出文件如下所示：

```
"2016-02-19T07:32:36.8420000-08:00","User1",,True,"72a0660e-22df-428e-b672-e0977007177f"
"2016-02-17T11:52:43.6350000-08:00","User2",,True,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-17T11:59:08.8320000-08:00","User2","2016-02-17T11:52:43.6350000-08:00",False,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-11T07:04:17.2630000-08:00","User3",,True,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-11T07:10:33.9720000-08:00","User3","2016-02-11T07:04:17.2630000-08:00",False,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-15T21:27:41.8210000-08:00","User3","2016-02-11T07:10:33.9720000-08:00",False,"4d2bc48d-bdf3-4591-a9c1-7b15ceb8e074"
"2016-02-16T05:48:49.6360000-08:00","User3","2016-02-15T21:27:41.8210000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-16T06:22:43.6390000-08:00","User3","2016-02-16T05:48:49.6360000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-17T16:29:53.2280000-08:00","User3","2016-02-16T06:22:43.6390000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T16:39:07.2430000-08:00","User3","2016-02-17T16:29:53.2280000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T17:20:39.3220000-08:00","User3","2016-02-17T16:39:07.2430000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-19T05:23:54.5710000-08:00","User3","2016-02-17T17:20:39.3220000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T05:48:37.7510000-08:00","User3","2016-02-19T05:23:54.5710000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T06:40:27.4830000-08:00","User3","2016-02-19T05:48:37.7510000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T07:27:37.7550000-08:00","User3","2016-02-19T06:40:27.4830000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T19:35:40.9450000-08:00","User3","2016-02-19T07:27:37.7550000-08:00",False,"3f385f0b-3e68-4456-ac74-ff6cef093674"
"2016-02-20T00:07:37.8250000-08:00","User3","2016-02-19T19:35:40.9450000-08:00",False,"685f76d5-ca48-4c58-b77d-bd3a9ddb33da"
"2016-02-11T09:01:33.9720000-08:00","User4",,True,"9f0cf696-c8ba-449a-8d5f-1ca6ed8f2ee8"
"2016-02-17T06:30:38.6210000-08:00","User4","2016-02-11T09:01:33.9720000-08:00",False,"8b11fd2a-01bf-4a5e-a9af-3c92c4e4382a"
"2016-02-17T22:15:26.4020000-08:00","User4","2016-02-17T06:30:38.6210000-08:00",False,"4e1cb707-3b5f-49c1-90c7-9b33b86ca1f4"
"2016-02-18T14:37:27.6560000-08:00","User4","2016-02-17T22:15:26.4020000-08:00",False,"f4e44400-e837-40ed-8dfd-2ea264d4e338"
"2016-02-19T01:20:31.4800000-08:00","User4","2016-02-18T14:37:27.6560000-08:00",False,"2136f4cf-7c7d-43c1-8ae2-08f4ad6a6e08"
```

此示例演示一个更复杂的用例场景，其中将使用代码隐藏部分（应用于整个内存行集）内部的全局变量。

## <a name="use-user-defined-types-udt"></a>使用用户定义的类型 (UDT)
用户定义的类型 (UDT) 是 U-SQL 的另一个可编程性功能。 U-SQL UDT 的作用类似常规 C# 用户定义的类型。 C# 是一种强类型语言，允许使用内置的和自定义的用户定义的类型。

在行集中的顶点之间传递 UDT 时，U-SQL 无法隐式序列化或反序列化任意 UDT。 这意味着，用户必须使用 IFormatter 接口提供显式格式化程序。 这样，就为 U-SQL 提供了针对 UDT 的序列化和反序列化方法。

> [!NOTE]
> 即使设置了 IFormatter，U-SQL 的内置提取器和输出器目前也无法与文件来回序列化或反序列化 UDT 数据。 因此，在使用 OUTPUT 语句将 UDT 数据写入文件或者使用提取器读取 UDT 数据时，必须以字符串或字节数组的形式传递数据。 然后，显式调用序列化和反序列化代码（即，UDT 的 ToString() 方法）。 另一方面，用户定义的提取器和输出器可以读取和写入 UDT。

如果尝试在 EXTRACTOR 或 OUTPUTTER（在之前的 SELECT 以外）中使用 UDT，如下所示：

```
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

将收到以下错误：

```
Error   1   E_CSC_USER_INVALIDTYPEINOUTPUTTER: Outputters.Text was used to output column myfield of type
MyNameSpace.Myfunction_Returning_UDT.

Description:

Outputters.Text only supports built-in types.

Resolution:

Implement a custom outputter that knows how to serialize this type, or call a serialization method on the type in
the preceding SELECT.   C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\
USQL-Programmability\Types.usql 52  1   USQL-Programmability
```

若要在输出器中处理 UDT，则必须使用 ToString() 方法将其序列化，或创建一个自定义输出器。

目前不能在 GROUP BY 中使用 UDT。 如果在 GROUP BY 中使用 UDT，将引发以下错误：

```
Error   1   E_CSC_USER_INVALIDTYPEINCLAUSE: GROUP BY doesn't support type MyNameSpace.Myfunction_Returning_UDT
for column myfield

Description:

GROUP BY doesn't support UDT or Complex types.

Resolution:

Add a SELECT statement where you can project a scalar column that you want to use with GROUP BY.
C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\USQL-Programmability\Types.usql
62  5   USQL-Programmability
```

若要定义 UDT，则必须：

* 添加以下命名空间：

```
using Microsoft.Analytics.Interfaces
using System.IO;
```

* 添加 UDT 接口可能需要的 `Microsoft.Analytics.Interfaces`。 此外，可能需要使用 `System.IO` 定义 IFormatter 接口。

* 使用 SqlUserDefinedType 属性定义用户定义的类型。

**SqlUserDefinedType** 用于将程序集中的类型定义标记为 U-SQL 中的用户定义的类型 (UDT)。 特性上的属性反映 UDT 的物理特征。 此类不能继承。

SqlUserDefinedType 是 UDT 定义必需的特性。

类的构造函数：  

* SqlUserDefinedTypeAttribute（类型格式化程序）

* 类型格式化程序：定义 UDT 格式化程序所需的参数 -- 具体而言，`IFormatter` 接口的类型必须在此处传递。

```
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* 典型的 UDT 还需要 IFormatter 接口的定义，如以下示例中所示：

```
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

`IFormatter` 接口使用根类型 \<typeparamref name="T"> 序列化和反序列化对象图形。

\<typeparam name="T">要序列化和反序列化的对象图形的根类型。

* **反序列化**：对所提供的流上的数据进行反序列化，并重构对象的图形。

* **序列化**：使用所提供流的给定根对对象或对象的图形进行序列化。

`MyType` 实例：类型的实例。  
`IColumnWriter` 写入器/`IColumnReader` 读取器：基础列流。  
`ISerializationContext` 上下文：用于定义一组标志的枚举，这些标志在序列化期间指定流的源和定义上下文。

* **Intermediate**：指定源或定义上下文不是持久存储区。

* **Persistence**：指定源或定义上下文是持久存储区。

U-SQL UDT 定义是常规 C# 类型，可包括对运算符（如 +/==/!=）的重写。 它还可包括静态方法。 例如，如果将此 UDT 用作 U-SQL MIN 聚合函数的参数，则必须定义 < 运算符重写。

本指南先前演示了示例 - 从 Qn:Pn (Q1:P10) 格式的特定日期确定会计时段。 以下示例展示如何定义会计时段值的自定义类型。

以下示例演示了代码隐藏部分和用于该部分的自定义 UDT 和 IFormatter 接口：

```
[SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
public struct FiscalPeriod
{
    public int Quarter { get; private set; }

    public int Month { get; private set; }

    public FiscalPeriod(int quarter, int month):this()
    {
    this.Quarter = quarter;
    this.Month = month;
    }

    public override bool Equals(object obj)
    {
    if (ReferenceEquals(null, obj))
    {
        return false;
    }

    return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
    }

    public bool Equals(FiscalPeriod other)
    {
return this.Quarter.Equals(other.Quarter) && this.Month.Equals(other.Month);
    }

    public bool GreaterThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
    }

    public bool LessThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
    }

    public override int GetHashCode()
    {
    unchecked
    {
        return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
    }
    }

    public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
    {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
    }

    public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.Equals(c2);
    }

    public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
    {
    return !c1.Equals(c2);
    }
    public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.GreaterThan(c2);
    }
    public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.LessThan(c2);
    }
    public override string ToString()
    {
    return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
    }

}

public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
{
    public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
    {
    using (var binaryWriter = new BinaryWriter(writer.BaseStream))
    {
        binaryWriter.Write(instance.Quarter);
        binaryWriter.Write(instance.Month);
        binaryWriter.Flush();
    }
    }

    public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
    {
    using (var binaryReader = new BinaryReader(reader.BaseStream))
    {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
        return result;
    }
    }
}
```

定义的类型包括两个数字：季度和月份。 运算符 ==/!=/>/< 和静态方法 ToString() 在此处定义。

如前文所述，UDT 可在 SELECT 表达式中使用，但不能在未进行自定义序列化的情况下在 OUTPUTTER/EXTRACTOR 中使用。 必须使用 ToString() 将其序列化为字符串，或将其与 OUTPUTTER/EXTRACTOR 配合使用。

接下来讨论 UDT 的用法。 在代码隐藏部分中，已将 GetFiscalPeriod 函数更改为以下值：

```
public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
{
    int FiscalMonth = 0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter = 0;
    if (FiscalMonth >= 1 && FiscalMonth <= 3)
    {
    FiscalQuarter = 1;
    }
    if (FiscalMonth >= 4 && FiscalMonth <= 6)
    {
    FiscalQuarter = 2;
    }
    if (FiscalMonth >= 7 && FiscalMonth <= 9)
    {
    FiscalQuarter = 3;
    }
    if (FiscalMonth >= 10 && FiscalMonth <= 12)
    {
    FiscalQuarter = 4;
    }

    return new FiscalPeriod(FiscalQuarter, FiscalMonth);
}
```

可以看到，它返回 FiscalPeriod 类型的值。

以下示例演示如何在 U-SQL 基本脚本中进一步使用 UDT。 此示例演示从 U-SQL 脚本调用 UDT 的不同形式。

```
DECLARE @input_file string = @"c:\work\cosmos\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"c:\work\cosmos\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT 
        guid AS start_id,
        dt,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Quarter AS fiscalquarter,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Month AS fiscalmonth,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt) + new USQL_Programmability.CustomFunctions.FiscalPeriod(1,7) AS fiscalperiod_adjusted,
        user,
        des
    FROM @rs0;

@rs2 =
    SELECT 
           start_id,
           dt,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           fiscalquarter,
           fiscalmonth,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS fiscalperiod,

       // This user-defined type was created in the prior SELECT.  Passing the UDT to this subsequent SELECT would have failed if the UDT was not annotated with an IFormatter.
           fiscalperiod_adjusted.ToString() AS fiscalperiod_adjusted,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```

下面是完整的代码隐藏部分的示例：

```
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.IO;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        static public DateTime? ToDateTime(string dt)
        {
            DateTime dtValue;

            if (!DateTime.TryParse(dt, out dtValue))
                return Convert.ToDateTime(dt);
            else
                return null;
        }

        public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
        {
            int FiscalMonth = 0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter = 0;
            if (FiscalMonth >= 1 && FiscalMonth <= 3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return new FiscalPeriod(FiscalQuarter, FiscalMonth);
        }



        [SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
        public struct FiscalPeriod
        {
            public int Quarter { get; private set; }

            public int Month { get; private set; }

            public FiscalPeriod(int quarter, int month):this()
            {
                this.Quarter = quarter;
                this.Month = month;
            }

            public override bool Equals(object obj)
            {
                if (ReferenceEquals(null, obj))
                {
                    return false;
                }

                return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
            }

            public bool Equals(FiscalPeriod other)
            {
return this.Quarter.Equals(other.Quarter) &&    this.Month.Equals(other.Month);
            }

            public bool GreaterThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
            }

            public bool LessThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
            }

            public override int GetHashCode()
            {
                unchecked
                {
                    return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
                }
            }

            public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
            {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
            }

            public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.Equals(c2);
            }

            public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
            {
                return !c1.Equals(c2);
            }
            public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.GreaterThan(c2);
            }
            public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.LessThan(c2);
            }
            public override string ToString()
            {
                return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
            }

        }

        public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
        {
public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
            {
                using (var binaryWriter = new BinaryWriter(writer.BaseStream))
                {
                    binaryWriter.Write(instance.Quarter);
                    binaryWriter.Write(instance.Month);
                    binaryWriter.Flush();
                }
            }

public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
            {
                using (var binaryReader = new BinaryReader(reader.BaseStream))
                {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
                    return result;
                }
            }
        }
    }
}
```

## <a name="use-user-defined-aggregates-udagg"></a>使用用户定义的聚合 (UDAGG)
用户定义的聚合是非随时随附于 U-SQL 的任何与聚合相关的函数。 示例包括：用于执行自定义数学计算、字符串串联或字符串操作的聚合等。

用户定义的聚合基类定义如下所示：

```c#
    [SqlUserDefinedAggregate]
    public abstract class IAggregate<T1, T2, TResult> : IAggregate
    {
        protected IAggregate();

        public abstract void Accumulate(T1 t1, T2 t2);
        public abstract void Init();
        public abstract TResult Terminate();
    }
```

**SqlUserDefinedAggregate** 指示该类型应注册为用户定义的聚合。 此类不能继承。

SqlUserDefinedType 属性对于 UDAGG 定义是**可选**的。


基类允许传递三个抽象参数：两个输入参数和一个结果参数。 数据类型是可变的且应在类继承期间定义。

```
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    { … }

    public override void Accumulate(string guid, string user)
    { … }

    public override string Terminate()
    { … }
}
```

* **Init** 在计算期间针对每个组进行一次调用。 它为每个聚合组提供初始化例程。  
* **Accumulate** 针对每个值执行一次。 它提供聚合算法的主要功能。 可用于聚合在类继承期间定义的各种数据类型的值。 可接受两个可变数据类型的参数。
* **Terminate** 在处理结束时按每个聚合组执行一次，以输出每组的结果。

若要声明正确的输入或输出数据类型，请使用类定义，如下所示：

```
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1：Accumulate 的第一个参数
* T2：Accumulate 的第二个参数
* TResult：Terminate 的返回类型

例如：

```
public class GuidAggregate : IAggregate<string, int, int>
```

或

```
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>在 U-SQL 中使用 UDAGG
若要使用 UDAGG，需首先在代码隐藏中定义它，或从现有可编程性 DLL 对其进行引用，如前文所述。

然后使用以下语法：

```
AGG<UDAGG_functionname>(param1,param2)
```

下面是 UDAGG 的示例：

```
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    {
        guid_agg = "";
    }

    public override void Accumulate(string guid, string user)
    {
        if (user.ToUpper()== "USER1")
        {
        guid_agg += "{" + guid + "}";
        }
    }

    public override string Terminate()
    {
        return guid_agg;
    }

}
```

基本 U-SQL 脚本：

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @" \usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    SELECT
        user,
        AGG<USQL_Programmability.GuidAggregate>(guid,user) AS guid_list
    FROM @rs0
    GROUP BY user;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

在此用例场景中，将串联特定用户的类 GUID。

## <a name="use-user-defined-objects-udo"></a>使用用户定义的对象 (UDO)
U-SQL 可让你定义自定义可编程性对象，此类对象称为用户定义的对象 (UDO)。

下面是 U-SQL 中的 UDO 列表：

* 用户定义的提取器
    * 逐行提取
    * 用于实现数据提取窗体自定义结构化的文件

* 用户定义的输出器
    * 逐行输出
    * 用于输出自定义数据类型或用于自定义文件格式

* 用户定义的处理器
    * 提取一行并生成一行
    * 用于减少列数或生成新列，这些列具有从存在的列集派生的值

* 用户定义的应用器
    * 提取一行并生成 0 到 n 行
    * 与 OUTER/CROSS APPLY 配合使用

* 用户定义的合并器
    * 合并行集 -- 用户定义的联接

* 用户定义的化简器
    * 提取 n 行并生成一行
    * 用于减少行数

通常在 U-SQL 脚本中将 UDO 作为以下 U-SQL 语句的一部分进行显式调用：

* EXTRACT
* OUTPUT
* PROCESS
* COMBINE
* REDUCE

> [!NOTE]  
> UDO 被限制使用至多 0.5 Gb 内存。  此内存限制对本地执行不适用。

## <a name="use-user-defined-extractors"></a>使用用户定义的提取器
U-SQL 允许通过使用 EXTRACT 语句导入外部数据。 EXTRACT 语句可以使用内置 UDO 提取器：  

* *Extractors.Text()*：提供从不同编码的分隔文本文件中进行的提取。

* *Extractors.Csv()*：提供从不同编码的逗号分隔值 (CSV) 文件中进行的提取。

* *Extractors.Tsv()*：提供从不同编码的制表符分隔值 (TSV) 文件中进行的提取。

它可用于开发自定义提取器。 可在数据导入期间借助此语句执行以下任何任务：

* 通过拆分列、修改各个值来修改输入数据。 使用 PROCESSOR 功能可以更好地合并列。
* 分析非结构化数据（如网页和电子邮件）或半非结构化数据（如 XML/JSON）。
* 分析编码方式不受支持的数据。

若要定义用户定义的提取器 (UDE)，需要创建 `IExtractor` 接口。 提取器的所有输入参数（如列/行分隔符、编码）需要在类的构造函数中定义。 `IExtractor` 接口还应包含 `IEnumerable<IRow>` 重定的定义，如下所示：

```
[SqlUserDefinedExtractor]
public class SampleExtractor : IExtractor
{
     public SampleExtractor(string row_delimiter, char col_delimiter)
     { … }

     public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
     { … }
}
```

**SqlUserDefinedExtractor** 属性指示该类型应注册为用户定义的提取器。 此类不能继承。

SqlUserDefinedExtractor 是 UDE 定义的可选特性。 用于定义 UDE 对象的 AtomicFileProcessing 属性。

* bool     AtomicFileProcessing   

* **true** = 指示此提取器需要原子输入文件（JSON、XML 等）
* **false** = 指示此提取器可以处理拆分文件/分布式文件（CSV、SEQ 等）

主要 UDE 可编程性对象包括输入和输出。 输入对象用于将输入数据枚举为 `IUnstructuredReader`。 输出对象用于将输出数据设置为提取器活动的结果。

可通过 `System.IO.Stream` 和 `System.IO.StreamReader` 访问输入数据。

为枚举输入列，此处首先使用行分隔符拆分输入流。

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

然后进一步将输入行拆分为列部分。

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
    string[] parts = line.Split(my_column_delimiter);
    foreach (string part in parts)
    { … }
}
```

为了设置输出数据，此处使用 `output.Set` 方法。

请务必了解，自定义提取器仅返回使用输出定义的列和值。 所定义的列/值。

```
output.Set<string>(count, part);
```

通过调用 `yield return output.AsReadOnly();` 触发实际的提取器输出。

下面是提取器的示例：

```
[SqlUserDefinedExtractor(AtomicFileProcessing = true)]
public class FullDescriptionExtractor : IExtractor
{
     private Encoding _encoding;
     private byte[] _row_delim;
     private char _col_delim;

    public FullDescriptionExtractor(Encoding encoding, string row_delim = "\r\n", char col_delim = '\t')
    {
         this._encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
         this._row_delim = this._encoding.GetBytes(row_delim);
         this._col_delim = col_delim;

    }

    public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
    {
         string line;
         //Read the input line by line
         foreach (Stream current in input.Split(_encoding.GetBytes("\r\n")))
         {
        using (System.IO.StreamReader streamReader = new StreamReader(current, this._encoding))
         {
             line = streamReader.ReadToEnd().Trim();
             //Split the input by the column delimiter
             string[] parts = line.Split(this._col_delim);
             int count = 0; // start with first column
             foreach (string part in parts)
             {
    if (count == 0)
             {  // for column “guid”, re-generated guid
                 Guid new_guid = Guid.NewGuid();
                 output.Set<Guid>(count, new_guid);
             }
             else if (count == 2)
             {
                 // for column “user”, convert to UPPER case
                 output.Set<string>(count, part.ToUpper());

             }
             else
             {
                 // keep the rest of the columns as-is
                 output.Set<string>(count, part);
             }
             count += 1;
             }

         }
         yield return output.AsReadOnly();
         }
         yield break;
     }
}
```

在此用例场景中，提取器将重新生成“guid”列的 GUID，将“user”列的值转换为大写。 通过对输入数据进行分析和操作，自定义提取器可生成更复杂的结果。

下面是使用自定义提取器的基本 U-SQL 脚本：

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file
        USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 TO @output_file USING Outputters.Text();
```

## <a name="use-user-defined-outputters"></a>使用用户定义的输出器
用户定义的输出器是另一种 U-SQL UDO，允许扩展内置 U-SQL 功能。 与提取程序类似，内置输出器也有几种。

* *Outputters.Text()*：将数据写入不同编码的分隔文本文件中。
* *Outputters.Csv()*：将数据写入不同编码的逗号分隔值 (CSV) 文件中。
* *Outputters.Tsv()*：将数据写入不同编码的制表符分隔值 (TSV) 文件中。

自定义输出器允许以自定义格式编写数据。 这有助于完成以下任务：

* 将数据写入半结构化或非结构化文件。
* 编写编码方式不受支持的数据。
* 修改输出数据或添加自定义属性。

若要定义用户定义的输出器，需要创建 `IOutputter` 接口。

下面是基本 `IOutputter` 类的实现：

```
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

输出器的的所有输入参数（如列/行分隔符、编码等）需要在类的构造函数中定义。 `IOutputter` 接口还应包含 `void Output` 重写的定义。 （可选）可设置 `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` 属性以进行原子文件处理。 有关详细信息，请参阅以下详细信息。

```
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class MyOutputter : IOutputter
{

    public MyOutputter(myparam1, myparam2)
    {
      …
    }

    public override void Close()
    {
      …
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
      …
    }
}
```

* 为每个输入行调用 `Output`。 返回 `IUnstructuredWriter output` 行集。
* 构造函数类用于将参数传递到用户定义的输出器。
* `Close` 用于选择性地进行重写以发布开销状态或确定最后一行的写入时间。

**SqlUserDefinedOutputter** 属性指示该类型应注册为用户定义的输出器。 此类不能继承。

SqlUserDefinedOutputter 是用户定义的输出器定义的可选属性。 用于定义 AtomicFileProcessing 属性。

* bool     AtomicFileProcessing   

* **true** = 指示此输出器需要原子输出文件（JSON、XML 等）
* **false** = 指示此输出器可以处理拆分文件/分布式文件（CSV、SEQ 等）

主要可编程性对象是行和输出。 **row** 对象用于将输出数据枚举为 `IRow` 接口。 **Output** 用于将输出数据设置为目标文件。

可通过 `IRow` 接口访问输出数据。 一次将输出数据传递到一行。

通过调用 IRow 接口的 Get 方法枚举各个值。

```
row.Get<string>("column_name")
```

可通过调用 `row.Schema` 确定各列名称：

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

使用此方法可为任何元数据架构构建灵活的输出器。

使用 `System.IO.StreamWriter` 将输出数据写入文件。 流参数设置为 `output.BaseStrea`（作为 `IUnstructuredWriter output` 的一部分）。

每次行迭代后，必须将数据缓冲区刷新到文件中。 此外，在默认启用 Disposable 属性并使用 **using** 关键字的情况下，必须使用 `StreamWriter` 对象：

```
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

否则，需要在每次迭代后显式调用 Flush() 方法。 以下示例对此做了演示。

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>为用户定义的输出器设置页眉和页脚
若要设置页眉，请使用单个迭代执行流。

```
public override void Output(IRow row, IUnstructuredWriter output)
{
 …
if (isHeaderRow)
{
    …                
}

 …
if (isHeaderRow)
{
    isHeaderRow = false;
}
 …
}
}
```

第一个 `if (isHeaderRow)` 块中的代码仅执行一次。

若要设置页脚，请使用对 `System.IO.Stream` 对象的实例的引用 (`output.BaseStream`)。 在 `IOutputter` 接口的 Close() 方法中编写页脚。  （有关详细信息，请参阅以下示例。）

下面是用户定义的输出器示例：

```
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class HTMLOutputter : IOutputter
{
    // Local variables initialization
    private string row_delimiter;
    private char col_delimiter;
    private bool isHeaderRow;
    private Encoding encoding;
    private bool IsTableHeader = true;
    private Stream g_writer;

    // Parameters definition            
    public HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    this.isHeaderRow = isHeader;
    this.encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
    }

    // The Close method is used to write the footer to the file. It's executed only once, after all rows
    public override void Close().
    {
    //Reference to IO.Stream object - g_writer
    StreamWriter streamWriter = new StreamWriter(g_writer, this.encoding);
    streamWriter.Write("</table>");
    streamWriter.Flush();
    streamWriter.Close();
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
    System.IO.StreamWriter streamWriter = new StreamWriter(output.BaseStream, this.encoding);

    // Metadata schema initialization to enumerate column names
    ISchema schema = row.Schema;

    // This is a data-independent header--HTML table definition
    if (IsTableHeader)
    {
        streamWriter.Write("<table border=1>");
        IsTableHeader = false;
    }

    // HTML table attributes
    string header_wrapper_on = "<th>";
    string header_wrapper_off = "</th>";
    string data_wrapper_on = "<td>";
    string data_wrapper_off = "</td>";

    // Header row output--runs only once
    if (isHeaderRow)
    {
        streamWriter.Write("<tr>");
        for (int i = 0; i < schema.Count(); i++)
        {
        var col = schema[i];
        streamWriter.Write(header_wrapper_on + col.Name + header_wrapper_off);
        }
        streamWriter.Write("</tr>");
    }

    // Data row output
    streamWriter.Write("<tr>");                
    for (int i = 0; i < schema.Count(); i++)
    {
        var col = schema[i];
        string val = "";
        try
        {
        // Data type enumeration--required to match the distinct list of types from OUTPUT statement
        switch (col.Type.Name.ToString().ToLower())
        {
            case "string": val = row.Get<string>(col.Name).ToString(); break;
            case "guid": val = row.Get<Guid>(col.Name).ToString(); break;
            default: break;
        }
        }
        // Handling NULL values--keeping them empty
        catch (System.NullReferenceException)
        {
        }
        streamWriter.Write(data_wrapper_on + val + data_wrapper_off);
    }
    streamWriter.Write("</tr>");

    if (isHeaderRow)
    {
        isHeaderRow = false;
    }
    // Reference to the instance of the IO.Stream object for footer generation
    g_writer = output.BaseStream;
    streamWriter.Flush();
    }
}

// Define the factory classes
public static class Factory
{
    public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    return new HTMLOutputter(isHeader, encoding);
    }
}
```

U-SQL 基本脚本：

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.html";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
         FROM @input_file
         USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 
    TO @output_file 
    USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

这是一个 HTML 输出器，它使用表数据创建 HTML 文件。

### <a name="call-outputter-from-u-sql-base-script"></a>从 U-SQL 基本脚本调用输出器
若要从基的 U-SQL 脚本调用自定义输出器，则必须创建输出器对象的新实例。

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

为避免在基本脚本中创建对象的实例，可以创建函数包装器，如以下示例中所示：

```c#
        // Define the factory classes
        public static class Factory
        {
            public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                return new HTMLOutputter(isHeader, encoding);
            }
        }
```

在这种情况下，原始调用如下所示：

```
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="use-user-defined-processors"></a>使用用户定义的处理器
用户定义的处理器或称 UDP 是一种 U-SQL UDO，可让你通过应用可编程性功能处理传入的行。 使用 UDP 可以根据需要合并列、修改值和添加新列。 从基本意义上说，它有助于处理行集，以生成所需的数据元素。

若要定义 UDP，需使用 `SqlUserDefinedProcessor` 属性创建 `IProcessor` 接口，这对于 UDP 是可选的。

此接口应包含 `IRow` 接口行集重写的定义，如以下示例中所示：

```
[SqlUserDefinedProcessor]
public class MyProcessor: IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
        …
 }
}
```

**SqlUserDefinedProcessor** 指示该类型应注册为用户定义的处理器。 此类不能继承。

SqlUserDefinedProcessor 属性对于 UDP 定义是**可选**的。

主要可编程性对象是输入和输出。 输入对象用于枚举输入列，输出对象用于将输出数据设置为处理器活动的结果。

对于输入列枚举，此处使用 `input.Get` 方法。

```
string column_name = input.Get<string>("column_name");
```

`input.Get` 方法的参数是一个列，该列作为 U-SQL 基本脚本的 `PROCESS` 语句的 `PRODUCE` 子句的一部分传递。 此处需要使用正确的数据类型。

对于输出，请使用 `output.Set` 方法。

请务必了解，自定义生成器仅输出使用 `output.Set` 方法调用定义的列和值。

```
output.Set<string>("mycolumn", mycolumn);
```

通过调用 `return output.AsReadOnly();` 触发实际处理器输出。

下面是一个处理器示例：

```
[SqlUserDefinedProcessor]
public class FullDescriptionProcessor : IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
     string user = input.Get<string>("user");
     string des = input.Get<string>("des");
     string full_description = user.ToUpper() + "=>" + des;
     output.Set<string>("dt", input.Get<string>("dt"));
     output.Set<string>("full_description", full_description);
     output.Set<Guid>("new_guid", Guid.NewGuid());
     output.Set<Guid>("guid", input.Get<Guid>("guid"));
     return output.AsReadOnly();
 }
}
```

在此用例场景中，处理器通过合并现有列（在本例中为大写的“user”以及“des”）生成名为“full_description”的新列。 它还重新生成一个 GUID，并返回 GUID 的原始值和新值。

从上面的示例中看到，可在 `output.Set` 方法调用期间调用 C# 方法。

下面是使用自定义处理器的基本 U-SQL 脚本示例：

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
     PROCESS @rs0
     PRODUCE dt String,
             full_description String,
             guid Guid,
             new_guid Guid
     USING new USQL_Programmability.FullDescriptionProcessor();

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

## <a name="use-user-defined-appliers"></a>使用用户定义的应用器
使用 U-SQL 用户定义的应用器可为查询的外部表表达式返回的每一行调用自定义 C# 函数。 为左侧输入的每一行计算右侧输入，并合并生成的行以得到最终输出。 APPLY 运算符生成的数个列是左侧和右侧输入中的列集的组合。

用户定义的应用器将作为 USQL SELECT 表达式的一部分进行调用。

对用户定义的应用器的典型调用如下所示：

```
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

有关在 SELECT 表达式中使用应用器的详细信息，请参阅 [U-SQL SELECT：从 CROSS APPLY 和 OUTER APPLY 中选择](https://msdn.microsoft.com/library/azure/mt621307.aspx)。

用户定义的应用器基类定义如下所示：

```
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

若要定义用户定义的应用器，需使用 [`SqlUserDefinedApplier`] 属性创建 `IApplier` 接口，这对于用户定义的应用器定义是可选的。

```
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
    public ParserApplier()
    {
        …
    }

    public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
    {
        …
    }
}
```

* 针对外部表的每一行进行调用。 返回 `IUpdatableRow` 输出行集。
* 构造函数类用于将参数传递到用户定义的应用器。

**SqlUserDefinedApplier** 指示该类型应注册为用户定义的应用器。 此类不能继承。

**SqlUserDefinedApplier** 对于用户定义的应用器是**可选**的。


主要可编程性对象如下所示：

```
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

输入行集作为 `IRow` 输入进行传递。 输出行将生成为 `IUpdatableRow` 输出接口。

可通过调用 `IRow` 架构方法确定各列名称。

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

为从传入 `IRow` 获取实际数据值，需使用 `IRow` 接口的 Get() 方法。

```
mycolumn = row.Get<int>("mycolumn")
```

或使用架构列名称：

```
row.Get<int>(row.Schema[0].Name)
```

必须使用 `IUpdatableRow` 输出设置输出值：

```
output.Set<int>("mycolumn", mycolumn)
```

请务必了解，自定义应用器仅输出使用 `output.Set` 方法调用定义的列和值。

通过调用 `yield return output.AsReadOnly();` 触发实际输出。

可将用户定义的应用器参数传递到构造函数。 应用器可返回数量不等的列，这些列需要在基本 U-SQL 脚本中应用器调用期间进行定义。

```
new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

下面是用户定义的应用器示例：

```
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
private string parsingPart;

public ParserApplier(string parsingPart)
{
    if (parsingPart.ToUpper().Contains("ALL")
    || parsingPart.ToUpper().Contains("MAKE")
    || parsingPart.ToUpper().Contains("MODEL")
    || parsingPart.ToUpper().Contains("YEAR")
    || parsingPart.ToUpper().Contains("TYPE")
    || parsingPart.ToUpper().Contains("MILLAGE")
    )
    {
    this.parsingPart = parsingPart;
    }
    else
    {
    throw new ArgumentException("Incorrect parameter. Please use: 'ALL[MAKE|MODEL|TYPE|MILLAGE]'");
    }
}

public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
{

    string[] properties = input.Get<string>("properties").Split(',');

    //  only process with correct number of properties
    if (properties.Count() == 5)
    {

    string make = properties[0];
    string model = properties[1];
    string year = properties[2];
    string type = properties[3];
    int millage = -1;

    // Only return millage if it is number, otherwise, -1
    if (!int.TryParse(properties[4], out millage))
    {
        millage = -1;
    }

    if (parsingPart.ToUpper().Contains("MAKE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("make", make);
    if (parsingPart.ToUpper().Contains("MODEL") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("model", model);
    if (parsingPart.ToUpper().Contains("YEAR") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("year", year);
    if (parsingPart.ToUpper().Contains("TYPE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("type", type);
    if (parsingPart.ToUpper().Contains("MILLAGE") || parsingPart.ToUpper().Contains("ALL")) output.Set<int>("millage", millage);
    }
    yield return output.AsReadOnly();            
}
}
```

下面是用户定义的应用器的基本 U-SQL 脚本：

```
DECLARE @input_file string = @"c:\usql-programmability\car_fleet.tsv";
DECLARE @output_file string = @"c:\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        stocknumber int,
        vin String,
        properties String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        r.stocknumber,
        r.vin,
        properties.make,
        properties.model,
        properties.year,
        properties.type,
        properties.millage
    FROM @rs0 AS r
    CROSS APPLY
    new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

在此用例场景中，用户定义的应用器充当车队属性的逗号分隔值分析器。 输入文件行如下所示：

```
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Shevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

它是典型的制表符分隔 TSV 文件，具有包含制造商和型号等汽车属性的属性列。 这些属性必须解析为表中的列。 提供的应用器还可让你基于传递的参数在结果行集中生成动态数量的属性。 可以生成所有属性，或仅生成特定的属性集。

    …USQL_Programmability.ParserApplier ("all")
    …USQL_Programmability.ParserApplier ("make")
    …USQL_Programmability.ParserApplier ("make&model")

用户定义的应用器可作为应用器对象的新实例进行调用：

```
CROSS APPLY new MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

或使用包装器工厂方法的调用实现：

```c#
    CROSS APPLY MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

## <a name="use-user-defined-combiners"></a>使用用户定义的合并器
使用用户定义的合并器 (UDC) 可基于自定义逻辑合并左侧和右侧行集中的行。 用户定义的合并器与 COMBINE 表达式配合使用。

合并器通过 COMBINE 表达式进行调用，此表达式提供关于两个输入行集、分组列、预期结果架构的必要信息和其他信息。

若要在基本 U-SQL 脚本中调用合并器，需使用以下语法：

```
Combine_Expression :=
    'COMBINE' Combine_Input
    'WITH' Combine_Input
    Join_On_Clause
    Produce_Clause
    [Readonly_Clause]
    [Required_Clause]
    USING_Clause.
```

有关详细信息，请参阅 [COMBINE 表达式 (U-SQL)](https://msdn.microsoft.com/library/azure/mt621339.aspx)。

若要定义用户定义的合并器，需使用 [`SqlUserDefinedCombiner`] 属性创建 `ICombiner` 接口，这对于用户定义的合并器定义是可选的。

基本 `ICombiner` 类定义：

```
public abstract class ICombiner : IUserDefinedOperator
{
protected ICombiner();
public virtual IEnumerable<IRow> Combine(List<IRowset> inputs,
       IUpdatableRow output);
public abstract IEnumerable<IRow> Combine(IRowset left, IRowset right,
       IUpdatableRow output);
}
```

`ICombiner` 接口的自定义实现应包含 `IEnumerable<IRow>` 合并重写的定义。

```
[SqlUserDefinedCombiner]
public class MyCombiner : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    …
}
}
```

**SqlUserDefinedCombiner** 属性指示该类型应注册为用户定义的合并器。 此类不能继承。

**SqlUserDefinedCombiner** 用于定义合并器模式属性。 它也是用户定义的合并器定义的可选属性。

CombinerMode 模式

CombinerMode 枚举可采用以下值：

* Full  (0)：每个输出行可能依赖于左侧和右侧具有相同密钥值的所有输入行。

* Left  (1)：每个输出行依赖于左侧的单个输入行（还可能依赖于右侧具有相同密钥值的所有行）。

* Right (2)：每个输出行依赖于右侧的单个输入行（还可能依赖于左侧具有相同密钥值的所有行）。

* Inner  (3)：每个输出行依赖于左侧和右侧具有相同值的单个行。

示例：[`SqlUserDefinedCombiner(Mode=CombinerMode.Left)`]


主要可编程性对象是：

```c#
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

输入行集作为左侧和右侧 `IRowset` 类型的接口进行传递。 必须同时枚举这两个行集以进行处理。 由于只能枚举每个接口一次，因此必须在必要时对其进行枚举和缓存。

为进行缓存，可创建 List\<T\> 类型的内存结构，作为 LINQ 查询执行的结果，具体而言就是 List<`IRow`>。 还可在枚举期间使用匿名数据类型。

有关 LINQ 查询的详细信息，请参阅 [LINQ 查询 (C#) 简介](https://msdn.microsoft.com/library/bb397906.aspx)，有关 IEnumerable\<T\> 接口的详细信息，请参阅 [IEnumerable\<T\> 接口](https://msdn.microsoft.com/library/9eekhta0(v=vs.110).aspx)。

为从传入 `IRowset` 获取实际数据值，需使用 `IRow` 接口的 Get() 方法。

```
mycolumn = row.Get<int>("mycolumn")
```

可通过调用 `IRow` 架构方法确定各列名称。

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

或使用架构列名称：

```
c# row.Get<int>(row.Schema[0].Name)
```

使用 LINQ 实现的常规枚举如下所示：

```
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

枚举两个行集后，将循环访问所有行。 对于左侧行集中的每一行，将查找满足合并器条件的所有行。

必须使用 `IUpdatableRow` 输出设置输出值。

```
output.Set<int>("mycolumn", mycolumn)
```

通过调用 `yield return output.AsReadOnly();` 触发实际输出。

下面是一个合并器示例：

```
[SqlUserDefinedCombiner]
public class CombineSales : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    var internetSales =
    (from row in left.Rows
          select new
          {
              ProductKey = row.Get<int>("ProductKey"),
              OrderDateKey = row.Get<int>("OrderDateKey"),
              SalesAmount = row.Get<decimal>("SalesAmount"),
              TaxAmt = row.Get<decimal>("TaxAmt")
          }).ToList();

    var resellerSales =
    (from row in right.Rows
     select new
     {
     ProductKey = row.Get<int>("ProductKey"),
     OrderDateKey = row.Get<int>("OrderDateKey"),
     SalesAmount = row.Get<decimal>("SalesAmount"),
     TaxAmt = row.Get<decimal>("TaxAmt")
     }).ToList();

    foreach (var row_i in internetSales)
    {
    foreach (var row_r in resellerSales)
    {

        if (
        row_i.OrderDateKey > 0
        && row_i.OrderDateKey < row_r.OrderDateKey
        && row_i.OrderDateKey == 20010701
        && (row_r.SalesAmount + row_r.TaxAmt) > 20000)
        {
        output.Set<int>("OrderDateKey", row_i.OrderDateKey);
        output.Set<int>("ProductKey", row_i.ProductKey);
        output.Set<decimal>("Internet_Sales_Amount", row_i.SalesAmount + row_i.TaxAmt);
        output.Set<decimal>("Reseller_Sales_Amount", row_r.SalesAmount + row_r.TaxAmt);
        }

    }
    }
    yield return output.AsReadOnly();
}
}
```

在此用例场景中，将为零售商构建分析报表。 目的是查找所有此类产品：成本超过 $20,000，且在特定时间范围内在网站上的销售速度快于常规零售方式。

以下是基本 U-SQL 脚本。 可对比常规联接和合并器的逻辑：

```sql
DECLARE @LocalURI string = @"\usql-programmability\";

DECLARE @input_file_internet_sales string = @LocalURI+"FactInternetSales.txt";
DECLARE @input_file_reseller_sales string = @LocalURI+"FactResellerSales.txt";
DECLARE @output_file1 string = @LocalURI+"output_file1.tsv";
DECLARE @output_file2 string = @LocalURI+"output_file2.tsv";

@fact_internet_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    CustomerKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_internet_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@fact_reseller_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    ResellerKey int ,
    EmployeeKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_reseller_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@rs1 =
SELECT
    fis.OrderDateKey,
    fis.ProductKey,
    fis.SalesAmount+fis.TaxAmt AS Internet_Sales_Amount,
    frs.SalesAmount+frs.TaxAmt AS Reseller_Sales_Amount
FROM @fact_internet_sales AS fis
     INNER JOIN @fact_reseller_sales AS frs
     ON fis.ProductKey == frs.ProductKey
WHERE
    fis.OrderDateKey < frs.OrderDateKey
    AND fis.OrderDateKey == 20010701
    AND frs.SalesAmount+frs.TaxAmt > 20000;

@rs2 =
COMBINE @fact_internet_sales AS fis
WITH @fact_reseller_sales AS frs
ON fis.ProductKey == frs.ProductKey
PRODUCE OrderDateKey int,
        ProductKey int,
        Internet_Sales_Amount decimal,
        Reseller_Sales_Amount decimal
USING new USQL_Programmability.CombineSales();

OUTPUT @rs1 TO @output_file1 USING Outputters.Tsv();
OUTPUT @rs2 TO @output_file2 USING Outputters.Tsv();
```

用户定义的合并器可作为填充器对象的新实例进行调用：

```
USING new MyNameSpace.MyCombiner();
```


或使用包装器工厂方法的调用实现：

```
USING MyNameSpace.MyCombiner();
```

## <a name="use-user-defined-reducers"></a>使用用户定义的化简器

使用 U-SQL 可在 C# 中编写自定义行集化简器的功能，方法是通过实现 IReducer 接口来使用用户定义的运算符扩展性框架。

用户定义的化简器 (UDR) 可用于在数据提取（导入）期间消除不必要的行。 它还可用于操作和评估行与列。 它还可根据可编程性逻辑定义需要提取的行。

若要定义 UDR 类，需要使用可选的 `SqlUserDefinedReducer` 属性创建 `IReducer` 接口。

此类接口应包含 `IEnumerable` 接口行集重写的定义。

```
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
        …
    }

}
```

**SqlUserDefinedReducer** 属性指示该类型应注册为用户定义的化简器。 此类不能继承。
**SqlUserDefinedReducer** 是用户定义的化简器定义的可选属性。 可用于定义 IsRecursive 属性。

* bool     IsRecursive    
* **true** = 指示此减除器是否是幂等的

主要可编程性对象是输入和输出。 input 对象用于枚举输入行。 Output 用于将输出行设置为化简活动的结果。

对于输入行枚举，需使用 `Row.Get` 方法。

```
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

`Row.Get` 方法的参数是一个列，该列作为 U-SQL 基本脚本的 `REDUCE` 语句的 `PRODUCE` 类的一部分传递。 此处还需使用正确的数据类型。

对于输出，请使用 `output.Set` 方法。

请务必了解，自定义化简器仅输出使用 `output.Set` 方法调用定义的值。

```
output.Set<string>("mycolumn", guid);
```

通过调用 `yield return output.AsReadOnly();` 触发实际化简器输出。

下面是一个化简器示例：

```
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
    string guid;
    DateTime dt;
    string user;
    string des;

    foreach (IRow row in input.Rows)
    {
        guid = row.Get<string>("guid");
        dt = row.Get<DateTime>("dt");
        user = row.Get<string>("user");
        des = row.Get<string>("des");

        if (user.Length > 0)
        {
        output.Set<string>("guid", guid);
        output.Set<DateTime>("dt", dt);
        output.Set<string>("user", user);
        output.Set<string>("des", des);

        yield return output.AsReadOnly();
        }
    }
    }

}
```

在此用例场景中，化简器将跳过具有空用户名的行。 对于行集中的每一行，它读取所需的每一列，然后计算用户名的长度。 仅当用户名值长度大于 0 时，它才输出实际行。

下面是使用自定义化简器的基本 U-SQL 脚本：

```
DECLARE @input_file string = @"\usql-programmability\input_file_reducer.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    REDUCE @rs0 PRESORT guid
    ON guid  
    PRODUCE guid string, dt DateTime, user String, des String
    USING new USQL_Programmability.EmptyUserReducer();

@rs2 =
    SELECT guid AS start_id,
           dt AS start_time,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS start_fiscalperiod,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```

