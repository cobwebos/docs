---
title: "适用于 Azure Data Lake Aanlytics 的 U-SQL可编程性指南 | Microsoft Docs"
description: "U-SQL 可编程性指南"
services: data-lake-analytics
documentationcenter: 
author: MikeRys
manager: arindamc
ms.assetid: 63be271e-7c44-4d19-9897-c2913ee9599d
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/15/2016
ms.author: mrys
translationtype: Human Translation
ms.sourcegitcommit: 847081123123c849033c9de2b3c4359042d41359
ms.openlocfilehash: da29f6015502e4ce5a63ca1c47106dc346026803


---
# <a name="u-sql-programmability-guide"></a>U-SQL 可编程性指南
## <a name="azure-data-lake"></a>Azure Data Lake
Azure Data Lake 包括了所有所需的功能，使开发人员、数据专家和分析师可以更轻松地存储任何大小、形状和速度的数据以及跨平台和语言进行各种类型的处理和分析。 它消除了插入和存储所有数据的复杂性，同时启动更快，可与批量、流式、交互式分析一起运行。

Azure Data Lake 是一组服务，可协同工作以提供基于云的大数据平台：

- HDInsight
- Azure 数据湖存储
- Azure Data Lake Analytics

U-SQL 是一种查询语言，专为大数据类型的工作负荷设计。 U-SQL 的一个独有特点是将类似 SQL 的声明性语言与 C# 提供的可扩展性和可编程性结合起来。 它还允许访问和处理架构元数据，以及创建自定义组件，如数据处理器和化简器。

本指南着重介绍通过 C# 实现的 U-SQL 语言的可扩展性和可编程性。

## <a name="requirements"></a>要求
若要首先进行 ADL 开发，则需要下载并安装 [Azure Data Lake Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)（用于 Visual Studio 的 Azure Data Lake 工具）。

## <a name="starting-with-u-sql"></a>从 U-SQL 开始  
本文档不包含对 U-SQL 语言的描述。 但将介绍基本 U-SQL 构造，以逐渐引出 U-SQL 可编程性功能。 有关详细信息，请参阅 [U-SQL Language Reference](http://aka.ms/usql_reference)（U-SQL 语言参考）指南。

请看以下示例：

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        MAX(guid) AS start_id,
        MIN(dt) AS start_time,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

在上面的示例中，有一个由**本地变量** @input_file 定义的**输入文件** - 文件 input_file.tsv。

以下操作的执行基于以上 U-SQL 脚本的执行结果：

* 初始 **EXTRACT** 语句通过将“输入文件”转换为**内存行集**，将数据加载到内存中。
* **SELECT** 对数据行集执行运算以聚合数据和准备导出。
* **OUTPUT** 命令 - 将数据行集导出到**输出文件** - 外部文件。

首先，来了解一下可用于在 U-SQL 脚本中直接使用 C# 表达式的一些选项。

## <a name="c-types-and-expressions-in-u-sql-script"></a>U-SQL 脚本中的 C# 类型及表达式
与常规 C# 类似，U-SQL C# 表达式是一连串一个或多个运算符，可计算为单个值、对象、方法或命名空间。 表达式可由一个文本值、一个方法调用、一个运算符或一个简单名称构成。 简单名称可以是变量、类型成员、方法参数、命名空间或类型的名称。

谈及 U-SQL C# 表达式时，特指 U-SQL 基本脚本 C# 表达式。 基本的“C# 代码隐藏”部分（将在本文档的后面部分中讨论）也可能包含 C# 表达式，作为基于 C# 代码的常规元素。

表达式可使用运算符（将其他表达式用作参数）或使用方法调用（其参数也是其他方法调用）。 表达式的示例：  

```c#
    Convert.ToDateTime(Convert.ToDateTime(dt).ToString("yyyy-MM-dd"))
```

U-SQL 语言允许使用内置命名空间中的标准 C# 表达式。  
 
```c#
    Microsoft.Analytics.Interfaces;  
    Microsoft.Analytics.Types.Sql;  
    System;  
    System.Collections.Generic;  
    System.Linq;  
    System.Text;  
```

常规 C# 表达式可在 U-SQL SELECT、EXTRACT 中使用。

C# 表达式还可在 DECLARE 或 IF 语句中使用。 此类表达式的示例包括：   

```c#
    DateTime.Today.Day   
    Convert.ToDateTime
```

U-SQL 基本脚本示例：  

```sql
    DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");
```

将列作为行集的一部分进行操作时，C# 表达式可提供扩展的功能。 例如，若要将日期/时间列转换为具有&0; 小时的日期，则可以使用 U-SQL 基本脚本的以下 SELECT 部分：

```sql
@rs1 =
    SELECT
        MAX(guid) AS start_id,
     MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt).ToString("yyyy-MM-dd")))
AS start_zero_time,
        user,
        des
    FROM @rs0
    GROUP BY user, des;
```

可以看到，我们使用 `System.Convert.ToDateTime` 方法遍历转换。

以下是一个略复杂的用例场景，其中演示一些基本 C# 运算符的用法。 查看下面的表达式。

```sql
@rs1 =
    SELECT
        MAX(guid) AS start_id,
        MIN(dt) AS start_time,
          MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        user,
        des
    FROM @rs0
    GROUP BY user, des;
```

这里展示了 C# 条件运算符表达式的一个示例。

上面的示例演示 C# 表达式在基本 U-SQL 脚本中的用法。 但是，U-SQL 实现了扩展性更强的可编程性功能，本文档对此进行了深入介绍。  

完整脚本：

```sql
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
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

### <a name="using-c-expressions-for-data-type-conversions"></a>使用 C# 表达式进行数据类型转换
基于上面的示例，以下示例演示使用 C# 表达式转换日期/时间数据的技巧。 在此特定方案中，字符串日期/时间数据转换为标准日期时间（采用午夜 00:00:00 时间表示法）。

```sql
DECLARE @dt String = "2016-07-06 10:23:15";
@rs1 =
    SELECT Convert.ToDateTime(Convert.ToDateTime(@dt).ToString("yyyy-MM-dd")) AS dt,
           dt AS olddt
    FROM @rs0;
OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

### <a name="using-c-expressions-for-todays-date"></a>使用 C# 表达式显示今天的日期
可使用下面的 C# 表达式拉取今天的日期

```c#
DateTime.Now.ToString("M/d/yyyy")
```

在脚本中

```sql
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

## <a name="in-line-c-function-expressions"></a>内联 C# 函数表达式
U-SQL 允许将内联函数定义用作 C# 表达式的一部分。 这可实现将 C# 函数与输出引用参数结合使用的其他可能用法。

常规内联函数表达式定义

```c#
    (Func<type of param1, type of param2>)
    (param1 =>
         { … return param2}
    ) (Rowset Column)
```

示例：

```c#
    (Func<string, DateTime?>)
    (input_p =>
         {
            DateTime dt_result;
            return DateTime.TryParse(input_p, out dt_result)
                   ? (DateTime?) dt_result : (DateTime?) null;
         }
    )
    ) (dt)
```

在此示例中，使用字符串输入参数 input_p 定义内联函数。 在此函数中，将验证输入字符串是否是有效的日期/时间值。 如果是有效值，则返回该值；否则返回 null。

由于 DateTime.TryParse 函数包含输出参数 `out dt_result`，因此此方案中需要内联函数。 将其定义为 `DateTime dt_result`；


## <a name="verifying-data-type-values"></a>验证数据类型值
某些 C# 函数不能直接在 U-SQL 基本脚本中作为 C# 表达式使用。 尤其是需要使用输出引用参数的函数。 但如前文所述，可将这些函数定义为并用作内联函数表达式的一部分。

### <a name="using-inline-function-expressions"></a>使用内联函数表达式
要验证日期/时间值是否有效，可使用 `DateTime.TryParse`。

以下是使用内联函数表达式验证 `DateTime.TryParse` 的数据类型值的完整示例。

在此方案中，将验证日期/时间数据类型值

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt string,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        MAX(guid) AS start_id,
        MIN((
             (Func<string, DateTime?>)
             (input_parameter =>
                { DateTime dt_result;
                  return
                     DateTime.TryParse(input_parameter, out dt_result)
                       ?(DateTime?) dt_result : (DateTime?) null;
                }
             )
             ) (dt)) AS start_time,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

### <a name="using-code-behind"></a>使用代码隐藏
为使用 U-SQL 程序的代码隐藏中的相同功能，需将 C# 函数定义为日期/时间。

以下是基本 U-SQL 脚本部分，其中进行了必要的更改：

```sql
     @rs1 =
        SELECT
          MAX(guid) AS start_id,
          MIN(USQL_Programmability.CustomFunctions.ToDateTime(dt)) AS start_time,
          DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
          user,
          des
          FROM @rs0
        GROUP BY user, des;
```

和代码隐藏函数

```c#
        static public DateTime? ToDateTime(string dt)
        {
            DateTime dtValue;

            if (!DateTime.TryParse(dt, out dtValue))
                return Convert.ToDateTime(dt);
            else
                return null;
        }
```

## <a name="using-code-behind"></a>使用代码隐藏
代码隐藏是 U-SQL 项目的 C# 可编程性部分。 从概念上讲，代码隐藏是 U-SQL 脚本中引用的已编译程序集 (DLL)。 Visual Studio Tools 允许将 C# 可编程性部分作为 U-SQL 项目的一部分进行管理和调试。

在 Visual Studio 中创建典型的 U-SQL 项目后，项目将具有两个部分。 基本脚本 - 具有扩展名 **.usql** 的文件。

![a](./media/data-lake-analytics-u-sql-programmability-guide/base-script-file.png)


典型的解决方案项目   
![typical-solution-project](./media/data-lake-analytics-u-sql-programmability-guide/typical-solution-project.png)


项目的第二部分，称为“代码隐藏”- 文件 Script.usql.cs  
![code-behind](./media/data-lake-analytics-u-sql-programmability-guide/code-behind.png)

此文件包含可编程性对象的默认命名空间定义。

```c#
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQL_Programmability
{

}
```

上面的代码隐藏模板是自动生成的。 此文件包含可编程性对象的默认命名空间定义。 在项目执行期间，项目在基本 U-SQL 脚本中进行编译和引用。

要开始开发可编程性对象，需要创建“公共”类。

```c#
namespace USQL_Programmability
{
    public class MyClass
    {
        public static string MyFunction(string param1)
        {
            return "my result";
        }

    }

}
```

可编程性对象可以是“用户定义的函数”或称“UDF”、“用户定义的类型”或称“UDT”，以及“PROCESS”、“REDUCER”等。

## <a name="registering-u-sql-assemblies"></a>注册 U-SQL 程序集
U-SQL 扩展性模型很大程度取决于用户添加自定义代码的能力。 目前，U-SQL 为用户提供了添加自己的基于 .Net 代码的简单方法（尤其是 C#），但也可以添加用其他 .Net 语言编写的自定义代码，例如 VB.Net 或 F#。 甚至可以部署自己的用于其他语言的运行时，但仍需自己通过 .Net 层提供互操作性。 如果希望我们支持特定语言，请提出功能请求文件和/或在此处留下评论：http://aka.ms/adlfeedback

### <a name="difference-between-code-behind-and-assembly-registration-through-adl-tools-in-visual-studio"></a>通过 Visual Studio 中 ADL 工具实现的代码隐藏与程序集注册之间的不同之处
利用自定义代码的最简单方法是使用适用于 Visual Studio 的 ADL 工具的代码隐藏功能。

如前文所述，将脚本的自定义代码（例如，Script.usql）填入到其代码隐藏文件（例如，Script.usql.cs）中。

![code-behind-example](./media/data-lake-analytics-u-sql-programmability-guide/code-behind-example.jpg)
**图 1**：VS 中 ADL 工具中的代码隐藏示例（单击图像可放大，可从[此处](https://github.com/Azure/usql/tree/master/Examples/TweetAnalysis)获取示例代码）
<br />

代码隐藏的优点是，工具可在用户提交脚本时处理以下步骤：  

1. 生成代码隐藏文件的程序集  

1. 向脚本添加序言，此脚本使用 [CREATE ASSEMBLY](https://msdn.microsoft.com/library/azure/mt763293.aspx) 语句注册程序集文件，并使用 [REFERENCE ASSEMBLY] (https://msdn.microsoft.com/library/azure/mt763294.aspx) 将程序集加载到脚本的上下文中。

1. 向脚本添加结语，此脚本使用 [DROP ASSEMBLY](https://msdn.microsoft.com/library/azure/mt763295.aspx) 再次删除临时注册的程序集。

打开脚本时可看到生成的序言和结语：

![生成的序言](./media/data-lake-analytics-u-sql-programmability-guide/generated-prologue.png)

**图 2**：代码隐藏的序言和结语，属于自动生成的内容
<br />

代码隐藏的缺点包括

* 每次提交脚本时都会上传代码
* 不能与他人共享功能。

因此，可向解决方案中添加单独的 C# 类库（用于 U-SQL）（参见图 3）；开发代码或复制现有代码隐藏代码（所需的 C# 代码中无更改，参见图 4）；以及使用项目上的“注册程序集”菜单选项注册程序集（参见图 5 中的步骤 1）。

![creating-project](./media/data-lake-analytics-u-sql-programmability-guide/creating-project.png)
**图 3**：创建 U-SQL C# 代码项目。  
<br />

![class-library](./media/data-lake-analytics-u-sql-programmability-guide/class-library.png)
**图 4**：代码隐藏文件旁的 U-SQL C# 类库。  
<br />

![register-code](./media/data-lake-analytics-u-sql-programmability-guide/register-code.png)
**图 5**：如何注册 U-SQL C# 代码项目
<br />

注册对话框（参见图 5 中的步骤 2）提供以下方面的选项：注册程序集的位置（某个 Data Lake Analytics 帐户、某个数据库）和程序集的命名方式（本地程序集路径由工具填入）。 还提供用于重新注册某个已注册的程序集的选项，和两个用于添加其他依赖项的选项：

* *托管依赖项*：显示所需的其他托管程序集。 每个所选程序集单独注册并可在脚本中引用。 可将此用于其他 .Net 程序集

* *其他文件*：允许添加程序集所需的其他资源文件。 这些文件会一并注册程序集，并在程序集被引用时自动加载。 可将此用于配置文件、本机程序集、其他语言运行时及其资源等。

下面的示例中同时使用了这两个选项。 [关于图像处理的最新博客文章](https://blogs.msdn.microsoft.com/azuredatalake/2016/08/18/introducing-image-processing-in-u-sql/)是另一示例，展示可使用这些注册选项的预定义程序集的使用。

现可从任何对已注册的程序集的数据库具有权限的 U-SQL 脚本引用已注册的程序集（请参见图 4 U-SQL 脚本中的代码）。 必须为每个单独注册的程序集添加引用。 将自动部署其他资源文件。 该脚本不应再具有所引用程序集中代码的代码隐藏文件，但代码隐藏文件仍可以提供其他代码。

### <a name="registering-assemblies-via-adl-tools-in-visual-studio-and-in-u-sql-scripts"></a>通过 Visual Studio 和 U-SQL 脚本中的 ADL 工具注册程序集
虽然使用 Visual Studio 中的 ADL 工具可轻松注册程序集，但如果要在不同平台上进行开发，且已具有要上传和注册的编译程序集，则还可以使用脚本注册程序集（与使用工具注册的方法相同）。 基本按照以下步骤操作：

1. 将程序集 dll、所需的其他所有非系统 dll 以及资源文件上传到 Azure Data Lake 存储帐户中的所选位置，或上传到与 Azure Data Lake 帐户链接的 Microsoft Azure Blob 存储帐户中的所选位置。 可使用任何适用的上传工具（例如 Powershell 命令、Visual Studio 的 ADL Tool Data Lake Explorer 上传功能、最受欢迎的 SDK 上传命令或借助 Azure 门户）。

1. 上传 dll 后，使用 [CREATE ASSEMBLY](https://msdn.microsoft.com/library/azure/mt763293.aspx) 语句对其进行注册。

以下空间示例即使用此方法。

### <a name="registering-assemblies-that-use-other-net-assemblies-based-on-the-json-and-xml-sample-library"></a>注册使用其他 .Net 程序集的程序集（基于 JSON 和 XML 示例库）
[U-SQL Github 站点](https://github.com/Azure/usql/)提供一组可供使用的共享示例程序集。 其中，[Microsoft.Analytics.Samples.Formats](https://github.com/Azure/usql/tree/master/Examples/DataFormats) 程序集提供提取程序、函数和输出器，可用于处理 JSON 和 XML 文档。 Microsoft.Analytics.Samples.Formats 程序集依赖两个现有的特定于域的程序集来分别处理 JSON 和 XML。 它使用 [Newtonsoft Json.Net](http://www.newtonsoft.com/) 库处理 JSON 文档，使用 [System.Xml](https://msdn.microsoft.com/data/bb291078.aspx) 程序集处理 XML。 接下来通过它来演示如何注册并在脚本中使用程序集。

首先，将 [Visual Studio 项目](https://github.com/Azure/usql/tree/master/Examples/DataFormats)下载到本地开发环境（例如，通过使用适用于 Windows 的 GitHub 工具制作本地副本）。 然后在 Visual Studio 中打开解决方案，按上述右键单击该项目以注册程序集。 尽管此程序集具有两个依赖项，但只需包含 Newtonsoft 依赖项即可，因为 System.Xml 已在 Azure Data Lake 中可用（但必须显式引用）。 图 6 显示如何命名程序集（注意，也可选择不带点的其他名称）以及如何添加 Newtonsoft dll。 两个程序集现分别在指定的数据库（如 JSONBlog）中进行注册。

![注册程序集](./media/data-lake-analytics-u-sql-programmability-guide/register-assembly.png)

**图 6**：如何从 Visual Studio 中注册 Microsoft.Analytics.Samples.Formats 程序集
<br />

如果你或其他人（你曾通过向其授予数据库读取权限与之共享注册的程序集）现在希望在自己的脚本中使用 JSON 功能，则可向脚本中添加以下两个引用：

```
REFERENCE ASSEMBLY JSONBlog.[NewtonSoft.Json];
REFERENCE ASSEMBLY JSONBlog.[Microsoft.Analytics.Samples.Formats];
```

如果希望使用 XML 功能，请添加一个系统程序集引用以及对已注册程序集的引用：

```
REFERENCE SYSTEM ASSEMBLY [System.Xml];
REFERENCE ASSEMBLY JSONBlog.[Microsoft.Analytics.Samples.Formats];
```

有关如何使用 JSON 功能的详细信息，请参阅[此博客文章](https://blogs.msdn.microsoft.com/mrys/?p=755)。

### <a name="registering-assemblies-that-use-native-c-assemblies-using-the-sql-server-2016-spatial-type-assembly-from-the-feature-pack"></a>注册使用本机 C++ 程序集的程序集（使用功能包中的 SQL Server 2016 空间类型程序集）
接下来了解另一种略为不同的方案。 假设想使用的程序集在非基于 .Net 的代码上具有一个依赖项，具体点说，此程序集在本机 C++ 程序集上具有一个依赖项。 SQL Server 类型程序集 [Microsoft.SqlServer.Types.dll](https://www.microsoft.com/download/details.aspx?id=52676) 便是此类程序集的一个示例，它提供 SQL Server hierarchyID、geometry 和 geography 类型的基于 .Net 的实现，这些类型供 SQL Server 客户端应用程序用于处理 SQL Server 类型（在 SQL Server 2016 版本之前，此类型最初也是用于实现 SQL Server 空间类型的程序集）。

接下来了解如何在 U-SQL 中注册此程序集！

首先，从 [SQL Server 2016 功能包](https://www.microsoft.com/download/details.aspx?id=52676)中下载并安装程序集。 选择 64 位版本的安装程序 (ENU\x64\SQLSysClrTypes.msi)，因为需要确保具有 64 位版本的库。

安装程序将托管程序集 Microsoft.SqlServer.Types.dll 安装到 C:\Program Files (x86)\Microsoft SQL Server\130\SDK\Assemblies，并将本机程序集 SqlServerSpatial130.dll 安装到 \Windows\System32\。\.现在将程序集上传到 Azure Data Lake Store（例如上传到 /upload/asm/spatial 文件夹中）。

由于安装程序已将本机库安装到系统文件夹 c:\Windows\System32，因此必须确保在上传 SqlServerSpatial130.dll 前将其从文件夹中进行复制，或确保使用的工具不会执行系统文件夹的[文件系统重定向](https://msdn.microsoft.com/library/windows/desktop/aa384187(v=vs.85).aspx)。 例如，如果希望使用当前 Visual Studio ADL 文件资源管理器上传，则首先必须将该文件复制到另一个目录，否则（到此博文撰写时间为止）将上传 32 位版本，（因为 Visual Studio 是 32 位应用程序，会在其 ADL 上传文件选择窗口中执行“文件系统重定向”），并且在运行可调用本机程序集的 U-SQL 脚本时，会在运行时收到以下（内部）错误：

*用户表达式内部异常：曾尝试使用错误格式加载程序。（异常来自 HRESULT：0x8007000B）*

上传两个程序集文件后，现在使用以下脚本在数据库 SQLSpatial 中注册它们：

```sql
DECLARE @ASSEMBLY_PATH string = "/upload/asm/spatial/";
DECLARE @SPATIAL_ASM string = @ASSEMBLY_PATH+"Microsoft.SqlServer.Types.dll";
DECLARE @SPATIAL_NATIVEDLL string = @ASSEMBLY_PATH+"SqlServerSpatial130.dll";

CREATE DATABASE IF NOT EXISTS SQLSpatial;
USE DATABASE SQLSpatial;

DROP ASSEMBLY IF EXISTS SqlSpatial;
CREATE ASSEMBLY SqlSpatial
FROM @SPATIAL_ASM
WITH ADDITIONAL_FILES =
     (
         @SPATIAL_NATIVEDLL
     );
```

在这种情况下，仅注册一个 U-SQL 程序集，并含入本机程序集，作为 U-SQL 程序集的字符串依赖项。 若要使用空间程序集，则只需引用 U-SQL 程序集，其他文件将自动适用于程序集。 以下是使用空间程序集的简单示例脚本：

```sql
REFERENCE SYSTEM ASSEMBLY [System.Xml];
REFERENCE ASSEMBLY SQLSpatial.SqlSpatial;

USING Geometry = Microsoft.SqlServer.Types.SqlGeometry;
USING Geography = Microsoft.SqlServer.Types.SqlGeography;
USING SqlChars = System.Data.SqlTypes.SqlChars;

@spatial =
    SELECT * FROM (VALUES
                   // The following expression is not using the native DDL
                   ( Geometry.Point(1.0,1.0,0).ToString()),    
                   // The following expression is using the native DDL
                   ( Geometry.STGeomFromText(new SqlChars("LINESTRING (100 100, 20 180, 180 180)"), 0).ToString())
                  ) AS T(geom);

OUTPUT @spatial
TO "/output/spatial.csv"
USING Outputters.Csv();
```

SQL 类型库在 System.XML 程序集上具有依赖项，因此需要引用它。 此外，一些方法使用 System.Data.SqlTypes 类型而非内置 C# 类型。 由于已默认包含 System.Data，因此只需引用所需的 SQL 类型。 可从 [Github 站点](https://github.com/Azure/usql/tree/master/Examples/SQLSpatialExample)获取以上代码。


### <a name="assembly-versioning-and-other-points"></a>程序集版本控制和其他要点
U-SQL 当前使用 .Net Framework 4.5 版本。 因此请确保自己的程序集与该版本的运行时兼容！

如上所述，U-SQL 以 64 位 (x64) 格式运行代码。 因此请确保将代码编译为在 x64 上运行。 否则将收到上面所示的不正确格式错误！

每个上传的程序集 dll、资源文件（如不同的运行时、本机程序集或配置文件等）最大可为 400 MB，部署资源（通过 DEPLOY RESOURCE 或引用程序集部署）的总大小及其附加文件不能超过 3 GB。

最后，请注意，每个 U-SQL 数据库仅可包含任何给定程序集的一个版本。 例如，如果同时需要 NewtonSoft Json.Net 库的版本 7 和版本 8，则需要将它们注册到两个不同的数据库。 此外，每个脚本仅可引用给定程序集 dll 的一个版本。 在这一方面，U-SQL 遵循 C# 程序集管理和版本控制语义！


## <a name="user-defined-functions---udf"></a>用户定义的函数 - UDF
U-SQL 用户定义的函数或 UDF 是编程例程，可接受参数、执行操作（例如复杂计算）并将操作的结果以值的形式返回。 UDF 的返回值只能是单个标量。 与任何其他 C# 标量函数相似，U-SQL UDF可在 U-SQL 基本脚本中进行调用。

U-SQL 用户定义的函数应初始化为“公用”和“静态”。

```c#
        public static string MyFunction(string param1)
        {
            return "my result";
        }
```

首先来看一个创建 UDF 的简单示例。

在此用例方案中，需要确定会计时段 - 特定用户首次登录的会计季度和会计月份。 本方案中适用年份的第一个会计月份为六月。

为计算会计时段，引入以下 C# 函数

```c#
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

它能简单地计算会计月份及季度并返回字符串值。 6 月 - 第一个会计季度的第一个月 -“Q1:P1”，7 月则为 -“Q1:P2”，以此类推。

这是将用于 U-SQL 项目的一个常规 C# 函数。

以下是此方案中代码隐藏部分的样子

```c#
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

现在从基本 U-SQL 脚本调用此函数。 要执行此操作，必须提供函数的完全限定名称，包括命名空间 - NameSpace.Class.Function(参数)

```sql
    USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

以下是实际的 U-SQL 基本脚本

```sql
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

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

以下是脚本执行的输出文件

```
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

本示例演示 U-SQL 中内联 UDF 的简单用法。

### <a name="keeping-state-between-udf-invocations"></a>保持 UDF 调用之间的状态
在通过代码隐藏全局变量利用交互性时，U-SQL C# 可编程性对象会更加复杂。 来看看以下这个企业用例场景：

在大型组织中，用户可在多种内部应用程序之间切换。 其中包括 Microsoft Dynamics CRM、PowerBI 等。客户希望对用户切换不同应用程序的方式、使用趋势等进行遥测分析。 企业的最终目标是优化应用程序的使用。 可能会合并不同的应用程序或特定的登录例程。

要实现此目标，必须确定会话 ID 和上次发生的会话之间的延迟时间。

需要找到上一次登录，并将其分配到正在生成到相同应用程序中的所有会话中。 第一个难题是 U-SQL 基本脚本将不允许使用 LAG 函数对已计算的列进行计算。 第二个难题是必须为同一时间段内的所有会话保留特定会话。

为解决此问题，将使用“代码隐藏”部分内部的“全局变量”- `static public string globalSession;`。

此全局变量在脚本执行期间将应用于整个行集。

以下是 U-SQL 程序的代码隐藏部分

```c#
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
                if (timeGap <= 60)
                    return Session;
                else
                    return Guid.NewGuid().ToString();
            }
            else
                return Guid.NewGuid().ToString();

        }

        static public string getStampUserSession(string Session)
        {
            if (Session != globalSession && !string.IsNullOrEmpty(Session))
            {
                globalSession = Session;
            }
            return globalSession;
        }

    }
}
```

全局变量 `static public string globalSession;` 在 `getStampUserSession` 函数内部使用，并且会在每次更改会话参数时重新进行初始化。

U-SQL 基本脚本

```sql
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
    SELECT EventDateTime,
           UserName,
LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,          
           string.IsNullOrEmpty(LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,           
           USQLApplication21.UserSession.StampUserSession
           (
            EventDateTime,
            LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC),
            LAG(UserSessionTimestamp, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)
           )
           AS UserSessionTimestamp
    FROM @records;

@rs2 =
    SELECT EventDateTime,
           UserName,
           LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,
           string.IsNullOrEmpty(LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,
           USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp) AS UserSessionTimestamp
    FROM @rs1
    WHERE UserName != "UserName";

OUTPUT @rs2
TO @out2
ORDER BY UserName, EventDateTime ASC
USING Outputters.Csv();
```

`USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` 函数会在第二次“内存行集”计算期间在此处进行调用。 它传递 `UserSessionTimestamp` 列，并在 `UserSessionTimestamp` 更改之前返回值。

输出文件

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

## <a name="using-user-defined-types---udt"></a>使用用户定义的类型 - UDT
用户定义的类型或称 UDT 是 U-SQL 的另一个可编程性功能。 U-SQL UDT 的作用类似常规 C# 用户定义的类型。 C# 是一种强类型语言，允许使用内置的和自定义的用户定义的类型。

U-SQL 目前不能将 UDT 数据隐式序列化到外部文件或从外部文件进行隐式反序列化。 因此，必须使用序列化/反序列化方法将 IFormatter 接口定义为 UDT 定义的一部分。 ADLA V1 中仅支持中间序列化。 也就是说，尽管 IFormatter 对于内部 UDT 处理很重要，但它不能用于 EXTRACTOR 或 OUTPUTTER 中的持久序列化。 使用 OUTPUTTER 将数据写入文件或使用 EXTRACTOR 进行读取时，必须使用 UDT 实现的 ToString() 方法将 UDT 序列化为字符串。 或者，处理 UDT 时可使用自定义 EXTRACTOR/OUTPUTTER。  

如果尝试在 EXTRACTOR 或 OUTPUTTER（在之前的 SELECT 以外）中使用 UDT

```sql
@rs1 =
    SELECT MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

将收到以下错误

```
    Error   1   E_CSC_USER_INVALIDTYPEINOUTPUTTER: Outputters.Text was used to output column myfield of type
    MyNameSpace.Myfunction_Returning_UDT.

    Description:

    Outputters.Text only supports built-in types.

    Resolution:

    Implement a custom outputter that knows how to serialize this type or call a serialization method on the type in
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

* 添加以下命名空间

```c#
    using Microsoft.Analytics.Interfaces
    using System.IO;
```

* 可能需要 UDT 接口所需的 `Microsoft.Analytics.Interfaces` 和 `System.IO` 定义 IFormatter 接口

* 使用 SqlUserDefinedType 特性定义用户定义的类型

**SqlUserDefinedType** 用于将程序集中的类型定义标记为 U-SQL 中的用户定义的类型 (UDT)。 特性上的属性反映 UDT 的物理特征。 此类不能继承。

SqlUserDefinedType 是 UDT 定义必需的特性。

类的构造函数  

* SqlUserDefinedTypeAttribute(类型格式化程序)

* 类型格式化程序 - 定义 UDT 格式化程序所需的参数。 具体而言，`IFormatter` 接口的类型必须在此处传递。

```c#
    [SqlUserDefinedType(typeof(MyTypeFormatter))]
      public class MyType
           {
             …
           }
```

* 典型 UDT 还需要 IFormatter 接口的定义

```c#
       public class MyTypeFormatter : IFormatter<MyType>
        {
            public void Serialize(MyType instance, IColumnWriter writer,
                           ISerializationContext context)
            {
        …
            }

            public MyType Deserialize(IColumnReader reader,
                                  ISerializationContext context)
            {
        …
            }
        }
```

`IFormatter` 接口使用根类型 \<typeparamref name="T"> 序列化和反序列化对象图形。

\<typeparam name="T">要序列化和反序列化的对象图形的根类型。

* 反序列化 - 对所提供的流上的数据进行反序列化，并重构对象的图形。

* 序列化 - 使用所提供流的给定根对对象或对象的图形进行序列化。

`MyType` 实例 - 类型的实例  
`IColumnWriter` 编写器 / `IColumnReader` 读取者 - 基本列流。  
`ISerializationContext` 上下文 - 用于定义一组标志的枚举，这些标志在序列化期间指定流的源和定义上下文。 
 
   * *Intermediate* - 指定源或定义上下文不是持久存储区

   * *Persistence* - 指定源或定义上下文是持久存储区

U-SQL UDT 定义是常规 C# 类型，可能包括对运算符（如 +/==/!= 等）的替代。可能包括静态方法等。 例如，如果将此 UDT 用作 U-SQL MIN 聚合函数的参数，则必须定义 < 运算符替代。

本指南先前演示了示例 - 从 Qn:Pn (Q1:P10) 格式的特定日期确定会计时段。 以下示例展示如何定义会计时段值的自定义类型。

代码隐藏部分和用于该部分的自定义 UDT 和 IFormatter 接口：

```c#
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

定义的类型包括两个数字 - 季度和月份。 运算符 ==/!=/>/< 和静态方法 ToString() 在此处定义。

如前文所述，UDT 可在 SELECT 表达式中使用，但不能在未进行自定义序列化的情况下在 OUTPUTTER/EXTRACTOR 中使用。 必须使用 ToString() 将其序列化为字符串，或将其与 OUTPUTTER/EXTRACTOR 配合使用。

接下来讨论 UDT 的用法。 在代码隐藏部分中，已将 GetFiscalPeriod 函数更改为

```c#
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

以下介绍如何在 U-SQL 基本脚本中进一步使用 UDT。 此示例演示从 U-SQL 脚本调用 UDT 的不同方法。

```sql
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
    SELECT guid AS start_id,
           dt,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Quarter AS fiscalquarter,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Month AS fiscalmonth,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt) + new USQL_Programmability.CustomFunctions.FiscalPeriod(1,7) AS fiscalperiod_adjusted,
           user,
           des
    FROM @rs0;

@rs2 =
    SELECT start_id,
           dt,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           fiscalquarter,
           fiscalmonth,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS fiscalperiod,
           fiscalperiod_adjusted.ToString() AS fiscalperiod_adjusted,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 TO @output_file USING Outputters.Text();
```

完整的代码隐藏部分：

```c#
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

### <a name="udts-from-built-in-types"></a>内置类型中的 UDT
即将支持

## <a name="user-defined-aggregates--udagg"></a>用户定义的聚合 - UDAGG
用户定义的聚合是非随时随附于 U-SQL 的任何与聚合相关的函数。 示例包括：用于执行自定义数学计算、执行字符串串联或字符串操作的聚合等。

用户定义的聚合基类定义为

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

SqlUserDefinedType 特性对于 UDAGG 定义是**可选**的


基类允许传递三个抽象参数 - 两个输入参数和一个结果参数。 数据类型是可变的且应在类继承期间定义。

```c#
    public class GuidAggregate : IAggregate<string, string, string>
    {
        string guid_agg;

        public override void Init()
        {
        …
        }

        public override void Accumulate(string guid, string user)
        {
        …
        }

        public override string Terminate()
        {
        …
        }

    }
```

* Init 为每个聚合组提供初始化例程。 它在计算期间针对每个组进行一次调用。
* Accumulate - 针对每个值执行一次。 提供聚合算法的主要功能。 可用于聚合在类继承期间定义的各种数据类型的值。 可接受两个可变数据类型的参数。
* Terminate - 在处理结束时按每个聚合组执行一次，以输出每组的结果

若要声明正确的输入或输出数据类型，请使用类定义

```c#
    public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1 - Accumulate 的第一个参数
* T2 - Accumulate 的第二个参数
* TResult - Terminate 的返回类型

例如

```c#
    public class GuidAggregate : IAggregate<string, int, int>
```

或

```c#
    public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="using-udagg-in-u-sql"></a>在 U-SQL 中使用 UDAGG
若要使用 UDAGG，需首先在代码隐藏中定义它，或从现有可编程性 DLL 对其进行引用，如前文所述。

然后使用以下语法

```c#
    AGG<UDAGG_functionname>(param1,param2)
```

以下是 UDAGG

```c#
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

和基本 U-SQL 脚本的示例

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @" \usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        user,
        AGG<USQL_Programmability.GuidAggregate>(guid,user) AS guid_list
    FROM @rs0
    GROUP BY user;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

在此用例场景中，将串联特定用户的类 GUID。

## <a name="user-defined-objects--udo"></a>用户定义的对象 – UDO
U-SQL 提供定义自定义可编程性对象的功能，此类对象称为用户定义的对象或 UDO。

以下是 U-SQL 中的 UDO 列表

* 用户定义的提取程序
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
    * 合并行集 - 用户定义的联接

* 用户定义的减除器
    * 提取 n 行并生成一行
    * 用于减少行数

通常在 U-SQL 脚本中将 UDO 作为以下 U-SQL 语句的一部分进行显式调用：

* EXTRACT
* OUTPUT
* PROCESS
* COMBINE
* REDUCE

## <a name="user-defined-extractor"></a>用户定义的提取程序
U SQL 允许通过使用 EXTRACT 语句导入外部数据。 EXTRACT 语句可以使用内置 UDO 提取程序  

* *Extractors.Text()*：提供从不同编码的分隔文本文件中进行的提取。

* *Extractors.Csv()*：提供从不同编码的逗号分隔值 (CSV) 文件中进行的提取。

* *Extractors.Tsv()*：提供从不同编码的制表符分隔值 (TSV) 文件中进行的提取。

但是，它可用于开发自定义提取程序。 如果需要，该语句可在数据导入期间使用

* 通过拆分列、修改各个值来修改输入数据。 使用 PROCESSOR 功能可以更好地合并列。
* 分析非结构化的数据（如网页/电子邮件）或半非结构化的数据（如 XML/JSON）。
* 分析编码方式不受支持的数据

若要定义用户定义的提取程序或 UDE，则需要创建 `IExtractor` 接口。 提取程序的所有输入参数（如列/行分隔符、编码等）需要在类的构造函数中定义。 `IExtractor` 接口还应包含 `IEnumerable<IRow>` 替代的定义

```c#
     [SqlUserDefinedExtractor]
     public class SampleExtractor : IExtractor
     {
         public SampleExtractor(string row_delimiter, char col_delimiter)
         {
            …

         }

         public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
         {
             …
         }
     }
```

**SqlUserDefinedExtractor** 特性指示该类型应注册为用户定义的提取程序。 此类不能继承。

SqlUserDefinedExtractor 是 UDE 定义的可选特性。 用于定义 UDE 对象的 AtomicFileProcessing 属性。

* bool     AtomicFileProcessing   

* **true** = 指示此提取程序需要原子输入文件（JSON、XML 等）
* **false** = 指示此提取程序可以处理拆分文件/分布式文件（CSV、SEQ 等）

主要 UDE 可编程性对象包括输入和输出。 输入对象用于将输入数据枚举为 `IUnstructuredReader`，输出对象用于将输出数据设置为提取程序活动的结果。

可通过 `System.IO.Stream` 和 `System.IO.StreamReader` 访问输入数据。

为枚举输入列，此处首先使用行分隔符拆分输入流

```c#
    foreach (Stream current in input.Split(my_row_delimiter))
    {
    …
    }
```

然后进一步将输入行拆分为列部分。

```c#
    foreach (Stream current in input.Split(my_row_delimiter))
    {
    …
        string[] parts = line.Split(my_column_delimiter);
            foreach (string part in parts)
        {
        …
        }
    }
```

为了设置输出数据，此处使用 `output.Set` 方法

务必要了解：自定义提取程序将仅输出使用 output.Set 方法调用 所定义的列/值。

```c#
    output.Set<string>(count, part);
```

通过调用 `yield return output.AsReadOnly();` 触发实际的提取程序输出。

以下是提取程序示例

```c#
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

在此用例场景中，提取程序将重新生成“guid”列的 GUID，并将“user”列的值转换为大写。 通过对输入数据进行分析和操作，自定义提取程序可生成更复杂的结果。

使用自定义提取程序的基本 U-SQL 脚本

```sql
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

## <a name="user-defined-outputter"></a>用户定义的输出器
用户定义的输出器是另一种 U-SQL UDO，允许扩展内置 U-SQL 功能。 与提取程序类似，内置输出器也有几种。

* *Outputters.Text()*：将数据写入不同编码的分隔文本文件中。
* *Outputters.Csv()*：将数据写入不同编码的逗号分隔值 (CSV) 文件中。
* *Outputters.Tsv()*：将数据写入不同编码的制表符分隔值 (TSV) 文件中。

自定义输出器允许以自定义格式编写数据。 这有助于

* 将数据写入半结构化或非结构化文件
* 编写编码方式不受支持的数据
* 修改输出数据或添加自定义特性

若要定义用户定义的输出器，则需要创建 `IOutputter` 接口

基本 `IOutputter` 类实现

```c#
    public abstract class IOutputter : IUserDefinedOperator
    {
        protected IOutputter();

        public virtual void Close();
        public abstract void Output(IRow input, IUnstructuredWriter output);
    }
```

输出器的的所有输入参数（如列/行分隔符、编码等）需要在类的构造函数中定义。 `IOutputter` 接口还应包含 `void Output` 替代的定义。 （可选）可设置 `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` 特性以进行原子文件处理 - 请参阅下面的详细信息。

```c#
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

* 为每个输入行调用 `Output`。 返回 `IUnstructuredWriter output` 行集
* 构造函数类用于将参数传递到用户定义的输出器
* `Close` 选择性地进行重写以发布昂贵状态或了解最后一行的编写时间

**SqlUserDefinedOutputter** 属性指示该类型应注册为用户定义的输出器。 此类不能继承。

SqlUserDefinedReducer 是用户定义的输出器定义的可选特性。 用于定义 AtomicFileProcessing 属性。

* bool     AtomicFileProcessing   

* **true** = 指示此输出器需要原子输出文件（JSON、XML 等）
* **false** = 指示此输出器可以处理拆分文件/分布式文件（CSV、SEQ 等）

主要可编程性对象是行和输出。 行对象用于将输出数据枚举为 `IRow` 接口，输出对象用于将输出数据设置为目标文件。

可通过 `IRow` 接口访问输出数据。 一次将输出数据传递到一行。

通过调用 IRow 接口的 Get 方法枚举各个值。

```c#
    row.Get<string>("column_name")
```

可通过调用 `row.Schema` 确定各列名称

```c#
    ISchema schema = row.Schema;
    var col = schema[i];
    string val = row.Get<string>(col.Name)
```

此方法允许为任何元数据架构构建灵活的输出器。

通过配合使用 `System.IO.StreamWriter` 和设置为 `output.BaseStrea` 的流参数（作为 `IUnstructuredWriter output` 的一部分），可将输出数据写入文件

重要说明 - 若要在每次行迭代后将数据缓冲区刷新到文件中，必须在默认启用 Disposable 特性的情况下使用 `StreamWriter` 对象，且使用关键字

```c#
    using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
            {
        …
            }
```

否则，需在每次迭代后显式调用 Flush() 方法 - 以下示例对此进行了进一步演示：

### <a name="setting-header-and-footer-for-user-defined-outputter"></a>为用户定义的输出器设置页眉和页脚
若要设置页眉，请使用单个迭代执行流。

```c#
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

若要设置页脚，请使用对 `System.IO.Stream` 对象的实例的引用 (`output.BaseStream`)，并以 `IOutputter` 接口的 Close() 方法编写页脚 - 请参阅下面的示例。

以下是用户定义的输出器

```c#
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

            // Close method is used to write footer to file - executed only once after all rows
            public override void Close()
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

                // This is data independent header - HTML table definition
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

                // Header row output - runs only once
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
                        // Data type enumeration - require to match the distinct list of types form OUTPUT statement
                        switch (col.Type.Name.ToString().ToLower())
                        {
                            case "string": val = row.Get<string>(col.Name).ToString(); break;
                            case "guid": val = row.Get<Guid>(col.Name).ToString(); break;
                            default: break;
                        }
                    }
                    // Hnadling NULL values - keeping them empty
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

和 U-SQL 基本脚本的示例

```sql
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

OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

这是一个 HTML 输出器 - 使用表数据创建 HTML 文件。

### <a name="calling-outputter-from-u-sql-base-script"></a>从 U-SQL 基本脚本调用输出器
若要从基的 U-SQL 脚本调用自定义输出器，则必须创建输出器对象的新实例。

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

为避免在基本脚本中创建对象的实例，可以创建函数包装器。 从以上

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

在这种情况下，原始调用如下所示

```sql
OUTPUT @rs0 TO @output_file USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="user-defined-processor"></a>用户定义的处理器
用户定义的处理器或称 UDP 是一种 U-SQL UDO，允许通过应用可编程性功能处理传入的行。 （如有必要）UDP 允许合并列、修改值和添加新列。 从基本意义上说，它有助于处理行集，以生成所需的数据元素。

若要定义 UDP，需使用 `SqlUserDefinedProcessor` 特性创建 `IProcessor` 接口，这对于 UDP 是可选的。

此接口应包含 `IRow` 接口行集替代的定义

```c#
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

SqlUserDefinedProcessor 特性对于 UDP 定义是**可选**的

主要可编程性对象是输入和输出。 输入对象用于枚举输入列，输出对象用于将输出数据设置为处理器活动的结果。

对于输入列枚举，此处使用 `input.Get` 方法

```c#
    string column_name = input.Get<string>("column_name");
```

`input.Get` 方法的参数是一个列，该列作为 U-SQL 基本脚本的 ` PROCESS` 语句的 `PRODUCE` 子句的一部分传递。 此处需要使用正确的数据类型。

对于输出 - output.Set方法

务必要了解：自定义生成器将仅输出使用 output.Set 方法调用定义的列/值。

```c#
    output.Set<string>("mycolumn", mycolumn);
```

通过调用 `return output.AsReadOnly()` 触发实际处理器输出；

以下是一个处理器示例

```c#
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

在此用例场景中，处理器通过合并现有列“user”（大写）和“des”生成新列“full_description”。 它还会重新生成一个 guid，并返回 guid 的原始值和新值。

从上文可以看到，可在 output.Set 调用期间调用 C# 方法。

使用自定义处理器的基本 U-SQL 脚本

```sql
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

## <a name="user-defined-applier"></a>用户定义的应用器
USQL 用户定义的应用器允许为一个查询的外部表达式返回的每一行调用自定义 C# 函数。 为左侧输入的每一行计算右侧输入，并合并生成的行以得到最终输出。 APPLY 运算符生成的数个列是左侧和右侧输入中的列集的组合。

用户定义的应用器将作为 USQL SELECT 表达式的一部分进行调用。

对用户定义的应用器的典型调用如下所示

```sql
    SELECT …
    FROM …
    CROSS APPLY
    new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

请参阅 [U-SQL SELECT 从 CROSS APPLY 和 OUTER APPLY 中进行选择](https://msdn.microsoft.com/library/azure/mt621307.aspx)了解有关在 SELECT 表达式中使用应用器的详细信息。

用户定义的应用器的基类定义为

```c#
    public abstract class IApplier : IUserDefinedOperator
    {
        protected IApplier();

        public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
    }
```

若要定义用户定义的应用器，需使用 [`SqlUserDefinedCombiner`] 特性创建 `IApplier` 接口，这对于用户定义的应用器定义是可选的。

```c#
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

* 针对外部表的每一行进行调用。 返回 `IUpdatableRow` 输出行集
* 构造函数类用于将参数传递到用户定义的应用器

**SqlUserDefinedApplier** 指示该类型应注册为用户定义的应用器。 此类不能继承。

SqlUserDefinedApplier 特性对于用户定义的应用器是**可选**的


主要可编程性对象是

```c#
        public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

输入行集作为 `IRow` 输入进行传递。 输出行将生成为 `IUpdatableRow` 输出接口

可通过调用 `IRow` 架构方法确定各列名称。

```c#
    ISchema schema = row.Schema;
    var col = schema[i];
    string val = row.Get<string>(col.Name)
```

为从传入 `IRow` 获取实际数据值，需使用 `IRow` 接口的 Get() 方法

```c#
    mycolumn = row.Get<int>("mycolumn")
```

或使用架构列名称

```c#
    row.Get<int>(row.Schema[0].Name)
```

必须使用 `IUpdatableRow` 输出设置输出值

```c#
    output.Set<int>("mycolumn", mycolumn)
```

务必要了解：自定义应用器将仅输出使用 output.Set 方法调用定义的列/值。

通过调用 `yield return output.AsReadOnly()` 触发实际输出；

可将用户定义的应用器参数传递到构造函数。 应用器可返回数量不等的列，这些列需要在基本 U-SQL 脚本中应用器调用期间进行定义。

```c#
  new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

以下是用户定义的应用器示例：

```c#
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

和此用户定义的应用器的基本 U-SQL 脚本

```sql
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

它是典型的制表符分隔 TSV 文件，具有包含“制造”、“型号”等汽车属性的属性列。这些属性需解析为表中的列。 提供的应用器还允许基于传递的参数在结果行集中生成动态数量的属性 - 所有属性或仅特定的属性集。

    …USQL_Programmability.ParserApplier ("all")
    …USQL_Programmability.ParserApplier ("make")
    …USQL_Programmability.ParserApplier ("make&model")

用户定义的应用器可作为应用器对象的新实例进行调用

```c#
    CROSS APPLY new MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

或使用包装器工厂方法的调用实现

```c#
    CROSS APPLY MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

## <a name="user-defined-combiner"></a>用户定义的合并器
用户定义的合并器或称 UDC 允许基于自定义逻辑合并左侧和右侧行集中的行。 用户定义的合并器与 COMBINE 表达式配合使用。

合并器通过 COMBINE 表达式进行调用，此表达式提供关于两个输入行集、分组列、预期结果架构的必要信息和其他信息。

要在基本 U-SQL 脚本中调用合并器，需使用以下语法

```sql
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

若要定义用户定义的合并器，需使用 [`SqlUserDefinedCombiner`] 特性创建 `ICombiner` 接口，这对于用户定义的合并器定义是可选的。

基本 `ICombiner` 类定义

```c#
    public abstract class ICombiner : IUserDefinedOperator
    {
        protected ICombiner();
        public virtual IEnumerable<IRow> Combine(List<IRowset> inputs,
               IUpdatableRow output);
        public abstract IEnumerable<IRow> Combine(IRowset left, IRowset right,
               IUpdatableRow output);
    }
```

`ICombiner` 接口的自定义实现应包含 `IEnumerable<IRow>` 合并替代的定义。

```c#
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

SqlUserDefinedCombiner 是用户定义的合并器定义的可选特性。 它用于定义合并器模式属性。

CombinerMode 模式

CombinerMode 枚举可采用以下值：

* Full (0) 每个输出行可能依赖于左侧和右侧具有相同密钥值的所有输入行

* Left (1) 每个输出行依赖于左侧的单个输入行（还可能依赖于右侧具有相同密钥值的所有行）

* Right (2) 每个输出行依赖于右侧的单个输入行（还可能依赖于左侧具有相同密钥值的所有行）

* Inner (3) 每个输出行依赖于左侧和右侧具有相同值的单个行

示例：[`SqlUserDefinedCombiner(Mode=CombinerMode.Left)`]


主要可编程性对象是

```c#
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

输入行集作为左侧和右侧 `IRowset` 类型的接口进行传递。 必须同时枚举这两个行集以进行处理。 由于每个接口仅允许枚举一次，因此需要在必要时对其进行枚举和缓存。

为进行缓存，可创建 List\<T\> 类型的内存结构，作为 LINQ 查询执行的结果。 具体而言就是 List<`IRow`>。 还可在枚举期间使用匿名数据类型。

有关 LINQ 查询的详细信息，请参阅 [LINQ 查询 (C#) 简介](https://msdn.microsoft.com/library/bb397906.aspx)，有关 IEnumerable\<T\> 接口的详细信息，请参阅 [IEnumerable\<T\> 接口](https://msdn.microsoft.com/library/9eekhta0(v=vs.110).aspx)。

为从传入 `IRowset` 获取实际数据值，需使用 `IRow` 接口的 Get() 方法

```c#
    mycolumn = row.Get<int>("mycolumn")
```

可通过调用 `IRow` 架构方法确定各列名称。

```c#
    ISchema schema = row.Schema;
    var col = schema[i];
    string val = row.Get<string>(col.Name)
```

或使用架构列名称

```
    c# row.Get<int>(row.Schema[0].Name)
```

使用 LINQ 实现的常规枚举如下所示：

```c#
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

枚举两个行集后，将循环访问所有行和左侧行集中的每一行，并查找满足合并器条件的所有行。

必须使用 `IUpdatableRow` 输出设置输出值

```c#
    output.Set<int>("mycolumn", mycolumn)
```

通过调用 `yield return output.AsReadOnly()` 触发实际输出；

以下是一个合并器示例

```c#
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

在此用例场景中，将为零售商构建分析报表。 目的是查找所有此类产品：成本超过 $20000，且在特定时间范围内在 Internet 网站上的销售速度快于常规零售方式。

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

用户定义的合并器可作为填充器对象的新实例进行调用

```c#
    USING new MyNameSpace.MyCombiner();
```


或使用包装器工厂方法的调用实现

```c#
    USING MyNameSpace.MyCombiner();
```

## <a name="user-defined-reducer"></a>用户定义的减除器
U-SQL 还提供在 C# 中编写自定义行集减除器的功能，方法是通过实现 IReducer 接口来使用用户定义的运算符扩展性框架。

用户定义的减除器或称 UDR 可用于在数据提取（导入）期间消除不必要的行。 还可用于操作和计算行/列，以及基于可编程性逻辑定义需提取的行。

若要定义 UDR 类，需使用 `SqlUserDefinedReducer` 特性（可选）创建 `IReducer` 接口

此类接口应包含 `IEnumerable` 接口行集替代的定义

```c#
        [SqlUserDefinedReducer]
        public class EmptyUserReducer : IReducer
        {

            public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
            {
            …
            }

        }
```

**SqlUserDefinedReducer** 属性指示该类型应注册为用户定义的减除器。 此类不能继承。

SqlUserDefinedReducer 是用户定义的减除器定义的可选特性。 可用于定义 IsRecursive 属性。

* bool     IsRecursive    
* **true** = 指示此减除器是否是幂等的

主要可编程性对象是输入和输出。 输入对象用于枚举输入行，输出对象用于将输出行设置为减除活动的结果。

对于输入行枚举，需使用 `Row.Get` 方法

```c#
            foreach (IRow row in input.Rows)
            {
                        row.Get<string>("mycolumn");
            }
```

以下是一些要点。 `Row.Get` 方法的参数是一个列，该列作为 U-SQL 基本脚本的 `REDUCE` 语句的 `PRODUCE` 类的一部分传递。 此处还需使用正确的数据类型。

对于输出 - output.Set方法

务必要了解：自定义减除器将仅输出使用 output.Set 方法调用定义的值。

```c#
    output.Set<string>("mycolumn", guid);
```

通过调用 `yield return output.AsReadOnly()` 触发实际减除器输出；

以下是一个减除器示例

```c#
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

在此用例场景中，化简器将跳过具有空用户名的行。 对于行集中的每一行，它读取所需的每一列，然后计算用户名的长度，并仅在用户名值长度大于 0 时输出实际行。

使用自定义减除器的基本 U-SQL 脚本

```sql
DECLARE @input_file string = @"\usql-programmability\input_file_reducer.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

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

OUTPUT @rs2 TO @output_file USING Outputters.Text();
```



<!--HONumber=Feb17_HO1-->


