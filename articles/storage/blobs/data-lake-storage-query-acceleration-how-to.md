---
title: 使用 Azure Data Lake Storage 查询加速来筛选数据 |Microsoft Docs
description: 使用查询加速来检索存储帐户中的数据子集。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 09/09/2020
ms.author: normesta
ms.reviewer: jamsbak
ms.custom: devx-track-csharp
ms.openlocfilehash: 72602e1e74074f21c93950bdb779758e784ce171
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89659870"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration"></a>使用 Azure Data Lake Storage 查询加速来筛选数据

本文介绍如何使用查询加速从存储帐户检索数据的子集。 

查询加速使应用程序和分析框架可以通过仅检索执行给定操作所需的数据来大幅优化数据处理。 若要了解详细信息，请参阅 [Azure Data Lake Storage 查询加速](data-lake-storage-query-acceleration.md)。

## <a name="prerequisites"></a>先决条件

- 若要访问 Azure 存储，需要一个 Azure 订阅。 如果还没有订阅，请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- **常规用途 v2**存储帐户。 请参阅 [创建存储帐户](../common/storage-quickstart-create-account.md)。

- 选择一个选项卡以查看任何特定于 SDK 的必备组件。

  ### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

  不适用

  ### <a name="net"></a>[.NET](#tab/dotnet)

  [.NET SDK](https://dotnet.microsoft.com/download) 

  ### <a name="java"></a>[Java](#tab/java)

  - [Java 开发工具包 (JDK)](/java/azure/jdk/?view=azure-java-stable&preserve-view=true) 8 或更高版本

  - [Apache Maven](https://maven.apache.org/download.cgi) 

    > [!NOTE] 
    > 本文假设已使用 Apache Maven 创建了一个 Java 项目。 有关如何使用 Apache Maven 创建项目的示例，请参阅 [设置](storage-quickstart-blobs-java.md#setting-up)。
  
  ### <a name="python"></a>[Python](#tab/python)

  [Python](https://www.python.org/downloads/) 3.8 或更高版本。

  ### <a name="nodejs"></a>[Node.js](#tab/nodejs)

  使用 Node.js SDK 不需要额外的先决条件。

---

## <a name="enable-query-acceleration"></a>启用查询加速

若要使用查询加速，你必须将查询加速功能注册到你的订阅。 验证功能注册后，必须注册 Azure 存储资源提供程序。 

### <a name="step-1-register-the-query-acceleration-feature"></a>步骤1：注册查询加速功能

若要使用查询加速，必须先将查询加速功能注册到订阅。 

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

1. 打开 Windows PowerShell 命令窗口。

1. 运行 `Connect-AzAccount` 命令以登录 Azure 订阅，并按照屏幕上的说明操作。

   ```powershell
   Connect-AzAccount
   ```

2. 如果你的标识与多个订阅相关联，请设置你的活动订阅。

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   将 `<subscription-id>` 占位符值替换为你的订阅 ID。

3. 使用 [AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) 命令注册 query 加速功能。

   ```powershell
   Register-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
   ```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 打开 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)，或者，如果已在本地[安装](https://docs.microsoft.com/cli/azure/install-azure-cli) Azure CLI，请打开命令控制台应用程序，如 Windows PowerShell。

2. 如果你的标识与多个订阅相关联，请将你的活动订阅设置为订阅存储帐户。

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   将 `<subscription-id>` 占位符值替换为你的订阅 ID。

3. 使用 [az feature register](/cli/azure/feature#az-feature-register) 命令注册 query 加速功能。

   ```azurecli
   az feature register --namespace Microsoft.Storage --name BlobQuery
   ```

---

### <a name="step-2-verify-that-the-feature-is-registered"></a>步骤2：验证功能是否已注册

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

若要验证注册是否完成，请使用 [AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) 命令。

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要验证注册是否已完成，请使用 [az feature](/cli/azure/feature#az-feature-show) 命令。

```azurecli
az feature show --namespace Microsoft.Storage --name BlobQuery
```

---

### <a name="step-3-register-the-azure-storage-resource-provider"></a>步骤3：注册 Azure 存储资源提供程序

批准注册后，你必须重新注册 Azure 存储资源提供程序。 

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

若要注册资源提供程序，请使用 [AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) 命令。

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要注册资源提供程序，请使用 [az provider register](/cli/azure/provider#az-provider-register) 命令。

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

---

## <a name="set-up-your-environment"></a>设置你的环境

### <a name="step-1-install-packages"></a>步骤1：安装包 

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

安装 Az module version 4.6.0 或更高版本。

```powershell
Install-Module -Name Az -Repository PSGallery -Force
```

若要从 Az 的较早版本进行更新，请运行以下命令：

```powershell
Update-Module -Name Az
```

#### <a name="net"></a>[.NET](#tab/dotnet)

1. 打开命令提示符并将目录 () 更改为 `cd` 项目文件夹，例如：

   ```console
   cd myProject
   ```

2. `12.5.0-preview.6`使用命令安装适用于 .net 的 Azure Blob 存储客户端库包的版本 `dotnet add package` 。 

   ```console
   dotnet add package Azure.Storage.Blobs -v 12.6.0
   ```

3. 本文中显示的示例使用 [CsvHelper](https://www.nuget.org/packages/CsvHelper/) 库分析 CSV 文件。 若要使用该库，请使用以下命令。

   ```console
   dotnet add package CsvHelper
   ```

#### <a name="java"></a>[Java](#tab/java)

1. 在文本编辑器中打开项目的 *pom.xml* 文件。 将以下依赖项元素添加到依赖项组。 

   ```xml
   <!-- Request static dependencies from Maven -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-core</artifactId>
       <version>1.6.0</version>
   </dependency>
    <dependency>
        <groupId>org.apache.commons</groupId>
        <artifactId>commons-csv</artifactId>
        <version>1.8</version>
    </dependency>    
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-storage-blob</artifactId>
      <version>12.8.0-beta.1</version>
    </dependency>
   ```

#### <a name="python"></a>[Python](#tab/python)

使用 [pip](https://pypi.org/project/pip/) 安装适用于 Python 的 Azure Data Lake Storage 客户端库。

```
pip install azure-storage-blob==12.4.0
```

#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

打开一个终端窗口，然后键入以下命令来安装适用于 JavaScript 的 Data Lake 客户端库。

```javascript
    npm install @azure/storage-blob
    npm install @fast-csv/parse
```

---

### <a name="step-2-add-statements"></a>步骤2：添加语句

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

不适用

#### <a name="net"></a>[.NET](#tab/dotnet)

将这些 `using` 语句添加到代码文件的顶部。

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
```

查询加速检索 CSV 和 Json 格式的数据。 因此，请确保为选择使用的任何 CSV 或 Json 分析库添加 using 语句。 本文中显示的示例使用 NuGet 上提供的 [CsvHelper](https://www.nuget.org/packages/CsvHelper/) 库分析 CSV 文件。 因此，我们会将这些 `using` 语句添加到代码文件的顶部。

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

若要编译本文中介绍的示例，还需要添加这些 `using` 语句。

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
```

#### <a name="java"></a>[Java](#tab/java)

将这些 `import` 语句添加到代码文件的顶部。

```java
import com.azure.storage.blob.*;
import com.azure.storage.blob.options.*;
import com.azure.storage.blob.models.*;
import com.azure.storage.common.*;
import java.io.*;
import java.util.function.Consumer;
import org.apache.commons.csv.*;
```

#### <a name="python"></a>[Python](#tab/python)

将这些 import 语句添加到代码文件的顶部。

```python
import sys, csv
from azure.storage.blob import BlobServiceClient, ContainerClient, BlobClient, DelimitedTextDialect, BlobQueryError
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

将 `storage-blob` 此语句放置在代码文件的顶部，以包含模块。 

```javascript
const { BlobServiceClient } = require("@azure/storage-blob");
```

查询加速检索 CSV 和 Json 格式的数据。 因此，请确保为选择使用的任何 CSV 或 Json 分析模块添加语句。 本文中显示的示例通过使用 [fast-csv](https://www.npmjs.com/package/fast-csv) 模块分析 CSV 文件。 因此，我们将此语句添加到代码文件的顶部。

```javascript
const csv = require('@fast-csv/parse');
```

---

## <a name="retrieve-data-by-using-a-filter"></a>使用筛选器检索数据

您可以使用 SQL 来指定查询加速请求中的行筛选器谓词和列投影。 下面的代码查询存储中的 CSV 文件，并返回其中第三列与该值匹配的所有数据行 `Hemingway, Ernest` 。 

- 在 SQL 查询中，关键字 `BlobStorage` 用于表示正在查询的文件。

- 列引用被指定为 `_N` 第一列的位置 `_1` 。 如果源文件包含标题行，则可以按标题行中指定的名称来引用列。 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$blob = "csv/csv-general/seattle-library.csv"
Get-QueryCsv $ctx $container $blob "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'" $false

```

### <a name="net"></a>[.NET](#tab/dotnet)

Async 方法 `BlobQuickQueryClient.QueryAsync` 将查询发送到查询加速 API，然后将结果作为 [流](https://docs.microsoft.com/dotnet/api/system.io.stream) 对象传输回应用程序。

```cs
static async Task QueryHemingway(BlockBlobClient blob)
{
    string query = @"SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    await DumpQueryCsv(blob, query, false);
}

private static async Task DumpQueryCsv(BlockBlobClient blob, string query, bool headers)
{
    try
    {
        var options = new BlobQueryOptions() {
            InputTextConfiguration = new BlobQueryCsvTextOptions() { HasHeaders = headers },
            OutputTextConfiguration = new BlobQueryCsvTextOptions() { HasHeaders = true },
            ProgressHandler = new Progress<long>((finishedBytes) => Console.Error.WriteLine($"Data read: {finishedBytes}"))
        };
        options.ErrorHandler += (BlobQueryError err) => {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.Error.WriteLine($"Error: {err.Position}:{err.Name}:{err.Description}");
            Console.ResetColor();
        };
        // BlobDownloadInfo exposes a Stream that will make results available when received rather than blocking for the entire response.
        using (var reader = new StreamReader((await blob.QueryAsync(
                query,
                options)).Value.Content))
        {
            using (var parser = new CsvReader(reader, new CsvConfiguration(CultureInfo.CurrentCulture) { HasHeaderRecord = true }))
            {
                while (await parser.ReadAsync())
                {
                    Console.Out.WriteLine(String.Join(" ", parser.Context.Record));
                }
            }
        }
    }
    catch (Exception ex)
    {
        Console.Error.WriteLine("Exception: " + ex.ToString());
    }
}

```

### <a name="java"></a>[Java](#tab/java)

方法将 `BlobQuickQueryClient.openInputStream()` 查询发送到查询加速 API，然后将结果作为对象流式传输到应用程序， `InputStream` 该对象可像其他任何 InputStream 对象一样读取。

```java
static void QueryHemingway(BlobClient blobClient) {
    String expression = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    DumpQueryCsv(blobClient, expression, true);
}

static void DumpQueryCsv(BlobClient blobClient, String query, Boolean headers) {
    try {
        BlobQuerySerialization input = new BlobQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(headers)
            .setFieldQuote('\0')
            .setEscapeChar('\\');
        BlobQuerySerialization output = new BlobQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(true)
            .setFieldQuote('\0')
            .setEscapeChar('\n');
        Consumer<BlobQueryError> errorConsumer = System.out::println;
        Consumer<BlobQueryProgress> progressConsumer = progress -> System.out.println("total bytes read: " + progress.getBytesScanned());
        BlobQueryOptions queryOptions = new BlobQueryOptions(query)
            .setInputSerialization(input)
            .setOutputSerialization(output)
            .setErrorConsumer(errorConsumer)
            .setProgressConsumer(progressConsumer);            

        /* Open the query input stream. */
        InputStream stream = blobClient.openQueryInputStream(queryOptions).getValue();
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(stream))) {
            /* Read from stream like you normally would. */
            for (CSVRecord record : CSVParser.parse(reader, CSVFormat.EXCEL.withHeader())) {
                System.out.println(record.toString());
            }
        }
    } catch (Exception e) {
        System.err.println("Exception: " + e.toString());
        e.printStackTrace(System.err);
    }
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_hemingway(blob: BlobClient):
    query = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'"
    dump_query_csv(blob, query, False)

def dump_query_csv(blob: BlobClient, query: str, headers: bool):
    qa_reader = blob.query_blob(query, blob_format=DelimitedTextDialect(has_header=headers), on_error=report_error, encoding='utf-8')
    # records() returns a generator that will stream results as received. It will not block pending all results.
    csv_reader = csv.reader(qa_reader.records())
    for row in csv_reader:
        print("*".join(row))
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

此示例将查询发送到查询加速 API，然后对结果进行流式处理。

```javascript
async function queryHemingway(blob)
{
    const query = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    await dumpQueryCsv(blob, query, false);
}

async function dumpQueryCsv(blob, query, headers)
{
    var response = await blob.query(query, {
        inputTextConfiguration: {
            kind: "csv",
            recordSeparator: '\n',
            hasHeaders: headers
        },
        outputTextConfiguration: {
            kind: "csv",
            recordSeparator: '\n',
            hasHeaders: true
        },
        onProgress: (progress) => console.log(`Data read: ${progress.loadedBytes}`),
        onError: (err) => console.error(`Error: ${err.position}:${err.name}:${err.description}`)});
    return new Promise(
        function (resolve, reject) {
            csv.parseStream(response.readableStreamBody)
                .on('data', row => console.log(row))
                .on('error', error => {
                    console.error(error);
                    reject(error);
                })
                .on('end', rowCount => resolve());
    });
}
```

---

## <a name="retrieve-specific-columns"></a>检索特定列

您可以将结果的范围限定为列的子集。 这样一来，只需检索执行给定计算所需的列。 这可以提高应用程序性能并降低成本，因为通过网络传输的数据较少。 

此代码仅检索 `BibNum` 数据集中所有书籍的列。 它还使用源文件中的标题行中的信息来引用查询中的列。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$blob = "csv/csv-general/seattle-library-with-headers.csv"
Get-QueryCsv $ctx $container $blob "SELECT BibNum FROM BlobStorage" $true

```

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryBibNum(BlockBlobClient blob)
{
    string query = @"SELECT BibNum FROM BlobStorage";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryBibNum(BlobClient blobClient)
{
    String expression = "SELECT BibNum FROM BlobStorage";
    DumpQueryCsv(blobClient, expression, true);
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_bibnum(blob: BlobClient):
    query = "SELECT BibNum FROM BlobStorage"
    dump_query_csv(blob, query, True)
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
async function queryBibNum(blob)
{
    const query = "SELECT BibNum FROM BlobStorage";
    await dumpQueryCsv(blob, query, true);
}
```

---

下面的代码将行筛选和列投影合并到同一个查询中。 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Get-QueryCsv $ctx $container $blob $query $true

Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType 
            FROM BlobStorage 
            WHERE ItemType IN 
                ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')"

```

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryDvds(BlockBlobClient blob)
{
    string query = @"SELECT BibNum, Title, Author, ISBN, Publisher, ItemType 
        FROM BlobStorage 
        WHERE ItemType IN 
            ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryDvds(BlobClient blobClient)
{
    String expression = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType " +
                        "FROM BlobStorage " +
                        "WHERE ItemType IN " +
                        "   ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    DumpQueryCsv(blobClient, expression, true);
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_dvds(blob: BlobClient):
    query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType "\
        "FROM BlobStorage "\
        "WHERE ItemType IN "\
        "   ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')"
    dump_query_csv(blob, query, True)
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
async function queryDvds(blob)
{
    const query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType " +
                  "FROM BlobStorage " +
                  "WHERE ItemType IN " + 
                  " ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    await dumpQueryCsv(blob, query, true);
}
```

---

## <a name="next-steps"></a>后续步骤

- [Azure Data Lake Storage 查询加速](data-lake-storage-query-acceleration.md)
- [查询加速 SQL 语言参考](query-acceleration-sql-reference.md)
