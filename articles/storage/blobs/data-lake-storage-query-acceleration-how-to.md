---
title: 使用 Azure 数据湖存储查询加速（预览）筛选数据 |微软文档
description: 使用查询加速（预览）从存储帐户检索数据子集。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: normesta
ms.reviewer: jamsbak
ms.openlocfilehash: ae3dfc7681ef0d8ce3fcf679bddbd0ff195f4e3b
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771841"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration-preview"></a>使用 Azure 数据湖存储查询加速筛选数据（预览）

本文介绍如何使用查询加速（预览）从存储帐户检索数据子集。 

查询加速（预览）是 Azure 数据湖存储的新功能，它使应用程序和分析框架能够通过仅检索执行给定操作所需的数据来显著优化数据处理。 要了解更多信息，请参阅[Azure 数据湖存储查询加速（预览）。](data-lake-storage-query-acceleration.md)

> [!NOTE]
> 查询加速功能处于公共预览版中，并且在加拿大中部和法国中部区域可用。 要查看限制，请参阅[已知问题](data-lake-storage-known-issues.md)一文。 要在预览版中注册，请参阅[此窗体](https://aka.ms/adls/qa-preview-signup)。  

## <a name="prerequisites"></a>先决条件

### <a name="net"></a>[.NET](#tab/dotnet)

- 若要访问 Azure 存储，需要一个 Azure 订阅。 如果还没有订阅，请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- **通用 v2**存储帐户。 请参阅[创建存储帐户](../common/storage-quickstart-create-account.md)。

- [.NET SDK](https://dotnet.microsoft.com/download). 

### <a name="java"></a>[Java](#tab/java)

- 若要访问 Azure 存储，需要一个 Azure 订阅。 如果还没有订阅，请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- **通用 v2**存储帐户。 请参阅[创建存储帐户](../common/storage-quickstart-create-account.md)。

- [Java 开发工具包 (JDK)](/java/azure/jdk/?view=azure-java-stable) 8 或更高版本。

- [阿帕奇·马文](https://maven.apache.org/download.cgi)。 

  > [!NOTE] 
  > 本文假定您已使用 Apache Maven 创建了 Java 项目。 有关如何使用 Apache Maven 创建项目的示例，请参阅[设置](storage-quickstart-blobs-java.md#setting-up)。
  
---

## <a name="install-packages"></a>安装包 

### <a name="net"></a>[.NET](#tab/dotnet)

1. 下载查询加速包。 您可以使用此链接获取包含这些包的压缩 .zip 文件： [https://aka.ms/adls/qqsdk/.net](https://aka.ms/adls/qqsdk/.net)。 

2. 将此文件的内容提取到项目目录中。

3. 在文本编辑器中打开项目文件 *（.csproj*），并在\<Project\>元素中添加这些包引用。

   ```xml
   <ItemGroup>
       <PackageReference Include="Azure.Storage.Blobs" Version="12.5.0-preview.1" />
       <PackageReference Include="Azure.Storage.Common" Version="12.4.0-preview.1" />
       <PackageReference Include="Azure.Storage.QuickQuery" Version="12.0.0-preview.1" />
   </ItemGroup>
   ```

4. 还原预览 SDK 包。 此示例命令使用 命令`dotnet restore`还原预览 SDK 包。 

   ```console
   dotnet restore --source C:\Users\contoso\myProject
   ```

5. 从公共 NuGet 存储库还原所有其他依赖项。

   ```console
   dotnet restore
   ```

### <a name="java"></a>[Java](#tab/java)

1. 在项目的根目录中创建目录。 根目录是包含**pom.xml**文件的目录。

   > [!NOTE]
   > 本文中的示例假定目录的名称是**lib**。

2. 下载查询加速包。 您可以使用此链接获取包含这些包的压缩 .zip 文件： [https://aka.ms/adls/qqsdk/java](https://aka.ms/adls/qqsdk/java)。 

3. 将此 .zip 文件中的文件提取到您创建的目录。 在我们的示例中，该目录名为**lib**。 

4. 在文本编辑器中打开 pom.xml 文件**。 将以下依赖项元素添加到依赖项组。 

   ```xml
   <!-- Request static dependencies from Maven -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-core</artifactId>
       <version>1.3.0</version>
   </dependency>
   <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-core-http-netty</artifactId>
      <version>1.3.0</version>
   </dependency>
   <dependency>
      <groupId>org.apache.avro</groupId>
      <artifactId>avro</artifactId>
      <version>1.9.2</version>
   </dependency>
   <dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-csv</artifactId>
    <version>1.8</version>
   </dependency>
   <!-- Local dependencies -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-blob</artifactId>
       <version>12.5.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-blob-12.5.0-beta.1.jar</systemPath>
   </dependency>
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-common</artifactId>
       <version>12.5.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-common-12.5.0-beta.1.jar</systemPath>
   </dependency>
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-quickquery</artifactId>
       <version>12.0.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-quickquery-12.0.0-beta.1.jar</systemPath>
   </dependency>
   ```

---

## <a name="add-statements"></a>添加语句


### <a name="net"></a>[.NET](#tab/dotnet)

将这些`using`语句添加到代码文件的顶部。

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
using Azure.Storage.QuickQuery;
using Azure.Storage.QuickQuery.Models;
```

查询加速检索 CSV 和 Json 格式化的数据。 因此，请确保为选择使用的任何 CSV 或 Json 解析库添加 using 语句。 本文中显示的示例使用 NuGet 上可用的[CsvHelper](https://www.nuget.org/packages/CsvHelper/)库解析 CSV 文件。 因此，我们将这些`using`语句添加到代码文件的顶部。

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

要编译本文中介绍的示例，您还需要添加这些`using`语句。

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
using System.Threading;
using System.Linq;
```

### <a name="java"></a>[Java](#tab/java)

将这些`import`语句添加到代码文件的顶部。

```java
import com.azure.storage.blob.*;
import com.azure.storage.blob.models.*;
import com.azure.storage.common.*;
import com.azure.storage.quickquery.*;
import com.azure.storage.quickquery.models.*;
import java.io.*;
import org.apache.commons.csv.*;
```

---

## <a name="retrieve-data-by-using-a-filter"></a>使用筛选器检索数据

可以使用 SQL 在查询加速请求中指定行筛选器谓词和列投影。 以下代码查询存储中的 CSV 文件，并返回第三列与 值`Hemingway, Ernest`匹配的所有数据行。 

- 在 SQL 查询中，`BlobStorage`关键字用于表示要查询的文件。

- 列引用指定为`_N`第一列的位置。 `_1` 如果源文件包含标题行，则可以按头行中指定的名称引用列。 

### <a name="net"></a>[.NET](#tab/dotnet)

异步方法`BlobQuickQueryClient.QueryAsync`将查询发送到查询加速 API，然后将结果作为[Stream](https://docs.microsoft.com/dotnet/api/system.io.stream?view=netframework-4.8)对象流式传输回应用程序。

```cs
static async Task QueryHemingway(BlockBlobClient blob)
{
    string query = @"SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest'";
    await DumpQueryCsv(blob, query, false);
}

private static async Task DumpQueryCsv(BlockBlobClient blob, string query, bool headers)
{
    try
    {
        using (var reader = new StreamReader((await blob.GetQuickQueryClient().QueryAsync(query,
                new CsvTextConfiguration() { HasHeaders = headers }, 
                new CsvTextConfiguration() { HasHeaders = false }, 
                new ErrorHandler(),
                new BlobRequestConditions(), 
                new ProgressHandler(),
                CancellationToken.None)).Value.Content))
        {
            using (var parser = new CsvReader(reader, new CsvConfiguration(CultureInfo.CurrentCulture) 
            { HasHeaderRecord = false }))
            {
                while (await parser.ReadAsync())
                {
                    parser.Context.Record.All(cell =>
                    {
                        Console.Out.Write(cell + "  ");
                        return true;
                    });
                    Console.Out.WriteLine();
                }
            }
        }
    }
    catch (Exception ex)
    {
        Console.Error.WriteLine("Exception: " + ex.ToString());
    }
}

class ErrorHandler : IBlobQueryErrorReceiver
{
    public void ReportError(BlobQueryError err)
    {
        Console.Error.WriteLine(String.Format("Error: {1}:{2}", err.Name, err.Description));
    }
}

class ProgressHandler : IProgress<long>
{
    public void Report(long value)
    {
        Console.Error.WriteLine("Bytes scanned: " + value.ToString());
    }
}

```

### <a name="java"></a>[Java](#tab/java)

该方法`BlobQuickQueryClient.openInputStream()`将查询发送到查询加速 API，然后将结果流式传输回应用程序，作为对象，可以像任何其他`InputStream`InputStream 对象一样读取。

```java
static void QueryHemingway(BlobClient blobClient) {
    String expression = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest'";
    DumpQueryCsv(blobClient, expression, false);
}

static void DumpQueryCsv(BlobClient blobClient, String query, Boolean headers) {
    try {
    
        BlobQuickQueryDelimitedSerialization input = new BlobQuickQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(headers)
            .setFieldQuote('\0')
            .setEscapeChar('\\');

        BlobQuickQueryDelimitedSerialization output = new BlobQuickQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(false)
            .setFieldQuote('\0')
            .setEscapeChar('\n');
                
        BlobRequestConditions requestConditions = null;
        /* ErrorReceiver determines what to do on errors. */
        ErrorReceiver<BlobQuickQueryError> errorReceiver = System.out::println;

        /* ProgressReceiver details how to log progress*/
        com.azure.storage.common.ProgressReceiver progressReceiver = System.out::println;
    
        /* Create a query acceleration client to the blob. */
        BlobQuickQueryClient qqClient = new BlobQuickQueryClientBuilder(blobClient)
            .buildClient();
        /* Open the query input stream. */
        InputStream stream = qqClient.openInputStream(query, input, output, requestConditions, errorReceiver, progressReceiver);
            
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(stream))) {
            /* Read from stream like you normally would. */
            for (CSVRecord record : CSVParser.parse(reader, CSVFormat.EXCEL)) {
                System.out.println(record.toString());
            }
        }
    } catch (Exception e) {
        System.err.println("Exception: " + e.toString());
    }
}
```

---

## <a name="retrieve-specific-columns"></a>检索特定列

您可以将结果范围限定为列的子集。 这样，您只能检索执行给定计算所需的列。 这提高了应用程序性能并降低了成本，因为通过网络传输的数据更少。 

此代码仅检索数据集中`PublicationYear`所有书籍的列。 它还使用源文件中的标头行中的信息来引用查询中的列。


### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryPublishDates(BlockBlobClient blob)
{
    string query = @"SELECT PublicationYear FROM BlobStorage";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryPublishDates(BlobClient blobClient)
{
    String expression = "SELECT PublicationYear FROM BlobStorage";
    DumpQueryCsv(blobClient, expression, true);
}
```

---

以下代码将行筛选和列投影合并到同一查询中。 

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryMysteryBooks(BlockBlobClient blob)
{
    string query = @"SELECT BibNum, Title, Author, ISBN, Publisher FROM BlobStorage WHERE Subjects LIKE '%Mystery%'";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryMysteryBooks(BlobClient blobClient)
{
    String expression = "SELECT BibNum, Title, Author, ISBN, Publisher FROM BlobStorage WHERE Subjects LIKE '%Mystery%'";
    DumpQueryCsv(blobClient, expression, true);
}
```

---

## <a name="next-steps"></a>后续步骤

- [查询加速注册表单](https://aka.ms/adls/queryaccelerationpreview)    
- [Azure 数据湖存储查询加速（预览）](data-lake-storage-query-acceleration.md)
- [查询加速 SQL 语言参考（预览）](query-acceleration-sql-reference.md)
- 查询加速 REST API 参考
