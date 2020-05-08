---
title: 使用 Azure Data Lake Storage 查询加速来筛选数据（预览版） |Microsoft Docs
description: 使用 "查询加速（预览版）" 检索存储帐户中的数据子集。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: normesta
ms.reviewer: jamsbak
ms.openlocfilehash: d7213bb44503fbe191a69683188bdea6976827ee
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82930074"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration-preview"></a>使用 Azure Data Lake Storage 查询加速来筛选数据（预览）

本文介绍如何使用查询加速（预览版）从存储帐户中检索数据的子集。 

查询加速（预览版）是 Azure Data Lake Storage 的一种新功能，它使应用程序和分析框架可以通过仅检索执行给定操作所需的数据来大幅优化数据处理。 若要了解详细信息，请参阅[Azure Data Lake Storage 查询加速（预览版）](data-lake-storage-query-acceleration.md)。

> [!NOTE]
> 查询加速功能处于公共预览阶段，在加拿大中部和法国中部区域提供。 若要查看限制，请参阅[已知问题](data-lake-storage-known-issues.md)一文。 若要注册预览版，请参阅[此窗体](https://aka.ms/adls/qa-preview-signup)。  

## <a name="prerequisites"></a>必备条件

### <a name="net"></a>[.NET](#tab/dotnet)

- 若要访问 Azure 存储，需要一个 Azure 订阅。 如果还没有订阅，请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- **常规用途 v2**存储帐户。 请参阅[创建存储帐户](../common/storage-quickstart-create-account.md)。

- [.NET SDK](https://dotnet.microsoft.com/download)。 

### <a name="java"></a>[Java](#tab/java)

- 若要访问 Azure 存储，需要一个 Azure 订阅。 如果还没有订阅，请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- **常规用途 v2**存储帐户。 请参阅[创建存储帐户](../common/storage-quickstart-create-account.md)。

- [Java 开发工具包（JDK）](/java/azure/jdk/?view=azure-java-stable)版本8或更高版本。

- [Apache Maven](https://maven.apache.org/download.cgi)。 

  > [!NOTE] 
  > 本文假设已使用 Apache Maven 创建了一个 Java 项目。 有关如何使用 Apache Maven 创建项目的示例，请参阅[设置](storage-quickstart-blobs-java.md#setting-up)。
  
---

## <a name="install-packages"></a>安装包 

### <a name="net"></a>[.NET](#tab/dotnet)

1. 下载查询加速包。 你可以使用以下链接获取包含这些包的压缩 .zip 文件： [https://aka.ms/adls/qqsdk/.net](https://aka.ms/adls/qqsdk/.net)。 

2. 将此文件的内容提取到您的项目目录。

3. 在文本编辑器中打开项目文件（*.csproj*），并在\<项目\>元素内添加这些包引用。

   ```xml
   <ItemGroup>
       <PackageReference Include="Azure.Storage.Blobs" Version="12.5.0-preview.1" />
       <PackageReference Include="Azure.Storage.Common" Version="12.4.0-preview.1" />
       <PackageReference Include="Azure.Storage.QuickQuery" Version="12.0.0-preview.1" />
   </ItemGroup>
   ```

4. 还原预览版 SDK 包。 此示例命令通过使用`dotnet restore`命令来还原预览版 SDK 包。 

   ```console
   dotnet restore --source C:\Users\contoso\myProject
   ```

5. 从公共 NuGet 存储库还原所有其他依赖项。

   ```console
   dotnet restore
   ```

### <a name="java"></a>[Java](#tab/java)

1. 在项目的根目录中创建目录。 根目录是包含**pom**文件的目录。

   > [!NOTE]
   > 本文中的示例假定目录的名称为**lib**。

2. 下载查询加速包。 你可以使用以下链接获取包含这些包的压缩 .zip 文件： [https://aka.ms/adls/qqsdk/java](https://aka.ms/adls/qqsdk/java)。 

3. 将此 .zip 文件中的文件提取到你创建的目录中。 在本示例中，该目录的名称为**lib**。 

4. 在文本编辑器中打开 pom.xml 文件  。 将以下依赖项元素添加到依赖项组。 

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

查询加速检索 CSV 和 Json 格式的数据。 因此，请确保为选择使用的任何 CSV 或 Json 分析库添加 using 语句。 本文中显示的示例使用 NuGet 上提供的[CsvHelper](https://www.nuget.org/packages/CsvHelper/)库分析 CSV 文件。 因此，我们会将这些`using`语句添加到代码文件的顶部。

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

若要编译本文中介绍的示例，还需要添加这些`using`语句。

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

您可以使用 SQL 来指定查询加速请求中的行筛选器谓词和列投影。 下面的代码查询存储中的 CSV 文件，并返回其中第三列与该值`Hemingway, Ernest`匹配的所有数据行。 

- 在 SQL 查询中，关键字`BlobStorage`用于表示正在查询的文件。

- 列引用被指定为`_N`第一列的位置`_1`。 如果源文件包含标题行，则可以按标题行中指定的名称来引用列。 

### <a name="net"></a>[.NET](#tab/dotnet)

Async 方法`BlobQuickQueryClient.QueryAsync`将查询发送到查询加速 API，然后将结果作为[流](https://docs.microsoft.com/dotnet/api/system.io.stream?view=netframework-4.8)对象传输回应用程序。

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
        Console.Error.WriteLine($"Error: {err.Name}:{ err.Description }");
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

方法`BlobQuickQueryClient.openInputStream()`将查询发送到查询加速 API，然后将结果作为`InputStream`对象流式传输到应用程序，该对象可像其他任何 InputStream 对象一样读取。

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

您可以将结果的范围限定为列的子集。 这样一来，只需检索执行给定计算所需的列。 这可以提高应用程序性能并降低成本，因为通过网络传输的数据较少。 

此代码仅检索数据`PublicationYear`集中所有书籍的列。 它还使用源文件中的标题行中的信息来引用查询中的列。


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

下面的代码将行筛选和列投影合并到同一个查询中。 

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

- [查询加速注册表单](https://aka.ms/adls/qa-preview-signup)    
- [Azure Data Lake Storage 查询加速（预览版）](data-lake-storage-query-acceleration.md)
- [查询加速 SQL 语言参考（预览版）](query-acceleration-sql-reference.md)
