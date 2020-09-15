---
title: 使用 Apache Spark for Azure Synapse Analytics 中的链接服务来保护访问凭据
description: 本文介绍有关如何使用链接服务和令牌库将 Apache Spark for Azure Synapse Analytics 与其他服务安全集成的概念
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 08/26/2020
ms.author: martinle
ms.reviewer: euang
ms.openlocfilehash: 21b571c859ec8ecc66c1c9a222e0648dc7f28f4f
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89422116"
---
# <a name="securing-your-credentials-through-linked-services-with-the-tokenlibrary"></a>使用 TokenLibrary 通过链接服务来保护凭据
访问来自外部源的数据是一种常见的模式。 除非外部数据源允许匿名访问，否则很可能需要使用凭据、机密或连接字符串来保护连接。  Azure Synapse Analytics 提供链接服务，将连接详细信息存储在链接服务或 Azure Key Vault 中，以简化集成过程。 创建链接服务后，Apache spark 可以引用该链接服务，以在代码中应用连接信息。 有关详细信息，请参阅[链接服务](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。
> [!NOTE]
> 从工作区中的 Azure Data Lake Storage 访问文件，可使用 AAD 传递进行身份验证，因此不需要使用 TokenLibrary。 


## <a name="prerequisite"></a>先决条件
* 链接服务 - 必须创建指向外部数据源的链接服务，并从令牌库引用该链接服务。 详细了解[链接服务](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。


## <a name="connecting-to-adls-gen2-outside-of-synapse-workspace"></a>连接到 Synapse 工作区外部的 ADLS Gen2

Synapse 提供 Azure Data Lake Storage Gen2 的集成链接服务体验。

```scala
// Scala code
val sc = spark.sparkContext
sc.hadoopConfiguration.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
sc.hadoopConfiguration.set("fs.azure.account.auth.type", "SAS")
sc.hadoopConfiguration.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>")

df.show()
```

```python
# Python code
sc._jsc.hadoopConfiguration().set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type", "SAS")
sc._jsc.hadoopConfiguration().set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>")

df.show()
```
## <a name="using-the-token-library"></a>使用令牌库

若要连接到其他链接服务，可以直接调用 TokenLibrary。

### <a name="getconnectionstring"></a>GetConnectionString
 若要检索连接字符串，请使用 getConnectionString 函数并传入链接服务名称<b></b><b></b>。

```scala
// Scala
// retrieve connectionstring from TokenLibrary

import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getConnectionString("<LINKED SERVICE NAME>")
println(connectionString)
```

```python
# Python
# retrieve connectionstring from TokenLibrary

from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
connection_string = token_library.getConnectionString("<LINKED SERVICE NAME>")
print(connection_string)
```
```csharp
// C#
// retrieve connectionstring from TokenLibrary

using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.GetConnectionString(<LINKED SERVICE NAME>);
Console.WriteLine(connectionString);
```

### <a name="getconnectionstringasmap"></a>GetConnectionStringAsMap
从连接字符串的键=值对中分析特定值，例如<i></i> 

<i>DefaultEndpointsProtocol=https;AccountName=\<AccountName>;AccountKey=\<AccountKey></i>

使用 getConnectionStringAsMap 函数，传递密钥以返回值<b></b>。
```scala
// Linked services can be used for storing and retreiving credentials (e.g, account key)
// Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val accountKey: String = TokenLibrary.getConnectionStringAsMap("<LINKED SERVICE NAME">).get("<KEY NAME>")
println(accountKey)
```

```python
# Linked services can be used for storing and retreiving credentials (e.g, account key)
# Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
accountKey = token_library.getConnectionStringAsMap("<LINKED SERVICE NAME>").get("<KEY NAME>")
print(accountKey)
```

## <a name="next-steps"></a>后续步骤

- [写入 SQL 池](./synapse-spark-sql-pool-import-export.md)

