---
title: 使用 Azure 机器学习数据准备 SDK 加载数据 - Python
description: 了解如何使用 Azure 机器学习数据准备 SDK 加载数据。 可以加载不同类型的输入数据，指定数据文件类型和参数，或使用 SDK 智能读取功能自动检测文件类型。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: 000870e3273799930f519ff32d6b072d8c2d1f10
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989679"
---
#<a name="load-data-with-the-azure-machine-learning-data-prep-sdk"></a>使用 Azure 机器学习数据准备 SDK 加载数据

[Azure 机器学习数据准备 SDK](https://docs.microsoft.com/python/api/overview/azure/dataprep?view=azure-dataprep-py) 允许加载不同类型的输入数据。 可以指定数据文件类型及其参数或使用 SDK 智能读取功能自动检测文件类型。

## <a name="read-lines"></a>读取行
加载数据的最简单方式之一是将其读取为文本行。

```
dataflow = dprep.read_lines(path='./data/text_lines.txt')
dataflow.head(5)
```
||折线图|
|----|-----|
|0|日期\|\|  最低温度\|\|  最高温度|
|1|2015-07-1 \|\|  -4.1 \|\|  10.0|
|2|2015-07-2 \|\|  -0.8 \|\|  10.8|
|3|2015-07-3 \|\|  -7.0 \|\|  10.5|
|4|2015-07-4 \|\|  -5.5 \|\|  9.3|

引入数据后，可以检索 pandas 数据帧的完整数据集。

```
df = dataflow.to_pandas_dataframe()
df
```

||折线图|
|----|-----|
|0|日期\|\| 最低温度\|\| 最高温度|
|1|2015-07-1\|\| 4.1\|\| 10.0|
|2|2015-07-2\|\| 0.8\|\| 10.8|
|3|2015-07-3\|\| 7.0\|\| 10.5|
|4|2015-07-4\|\| 5.5\|\| 9.3|

## <a name="read-csv"></a>读取 CSV
在读取带分隔符的文件时，可以让基础运行时推断分析参数（如分隔符、编码、是否使用标头等）而不是提供它们。 对于此示例，尝试仅通过指定文件的位置来读取文件。 

```
# SAS expires June 16th, 2019
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
dataflow.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0||stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|1|ALABAMA|1|101710|Hale County|10171002158| |
|2|ALABAMA|1|101710|Hale County|10171002162| |
|3|ALABAMA|1|101710|Hale County|10171002156| |
|4|ALABAMA|1|101710|Hale County|10171000588|2|

可以指定的参数之一是要在读取的文件中跳过的行数。 使用以下代码来筛选出重复行。
```
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1)
dataflow.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0|ALABAMA|1|101710|Hale County|10171002158|29|
|1|ALABAMA|1|101710|Hale County|10171002162|40 |
|2|ALABAMA|1|101710|Hale County|10171002156| 43|
|3|ALABAMA|1|101710|Hale County|10171000588|2|
|4|ALABAMA|1|101710|Hale County|10171000589|23 |

接下来，可以查看列的数据类型。
```
dataflow.head(1).dtypes

stnam                     object
fipst                     object
leaid                     object
leanm10                   object
ncessch                   object
schnam10                  object
MAM_MTH00numvalid_1011    object
dtype: object
```
遗憾的是，所有列均返回为字符串。 这是因为，默认情况下，Azure 机器学习数据准备 SDK 不会更改数据类型。 我们正在读取的数据源是一个文本文件，因此 SDK 将所有值读取为字符串。 但是，对于此示例，我们要将数值列分析为数字。 若要执行此操作，可以将 inference_arguments 参数设置为 current_culture。

```
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1,
                          inference_arguments=dprep.InferenceArguments.current_culture())
dataflow.head(1).dtypes

stnam                      object
fipst                     float64
leaid                     float64
leanm10                    object
ncessch                   float64
schnam10                   object
ALL_MTH00numvalid_1011    float64
dtype: object
```
多个列正确检测为数字且其类型设置为 float64。 引入完成后，可以检索 pandas 数据帧的完整数据集。

```
df = dataflow.to_pandas_dataframe()
df
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale County|1.017100e+10|49.0|
|1|ALABAMA|Hale County|1.017100e+10|40.0|
|2|ALABAMA|Hale County|1.017100e+10|43.0|
|3|ALABAMA|Hale County|1.017100e+10|2.0|
|4|ALABAMA|Hale County|1.017100e+10|23.0|

## <a name="read-excel"></a>读取 Excel
Azure 机器学习数据准备 SDK 包括用于加载 Excel 文件的 `read_excel` 函数。
```
dataflow = dprep.read_excel(path='./data/excel.xlsx')
dataflow.head(5)
```
||Column1|Column2|Column3|Column4|Column5|Column6|Column7|Column8|
|------|------|------|-----|------|-----|-------|----|-----|
|0|Hoba|Iron, IVB|60000000.0|已找到|1920.0|http://www.lpi.usra.edu/meteor/metbull.php?cod..。 |-19.58333|17.91667|
|1|Cape York|Iron, IIIAB|58200000.0|已找到|1818.0|http://www.lpi.usra.edu/meteor/metbull.php?cod..。 |76.13333|-64.93333|
|2|Campo del Cielo|Iron, IAB-MG|50000000.0|已找到|1576.0|http://www.lpi.usra.edu/meteor/metbull.php?cod..。 |-27.46667|-60.58333|
|3|Canyon Diablo|Iron, IAB-MG|30000000.0|已找到|1891.0|http://www.lpi.usra.edu/meteor/metbull.php?cod..。 |35.05000|-111.03333|
|4|Armanty|Iron, IIIE|28000000.0|已找到|1898.0|http://www.lpi.usra.edu/meteor/metbull.php?cod..。 |47.00000|88.00000|

已加载 Excel 文件的第一个表。 通过显式指定要加载的表的名称，可以实现相同的结果。 如果要改为加载第二个表，可以将其名称作为参数提供。
```
dataflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2')
dataflow.head(5)
```

||Column1|Column2|Column3|Column4|Column5|Column6|Column7|Column8|
|------|------|------|-----|------|-----|-------|----|-----|
|0|无|无|无|无|无|无|无|无|无|
|1|无|无|无|无|无|无|无|无|无|
|2|无|无|无|无|无|无|无|无|无|
|3|Rank|标题|工作室|全球|国内 / %|Column1|海外 / %|Column2|年份^|
|4|1|Avatar|Fox|2788|760.5|0.273|2027.5|0.727|2009^|5|

正如所看到的，第二个工作表中的表具有标头和三个空行。 需要相应地修改函数的参数。
```
dataflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2', use_header=True, skip_rows=3)
df = dataflow.to_pandas_dataframe()
df
```

||Rank|标题|工作室|全球|国内 / %|Column1|海外 / %|Column2|年份^|
|------|------|------|-----|------|-----|-------|----|-----|-----|
|0|1|Avatar|Fox|2788|760.5|0.273|2027.5|0.727|2009^|
|1|2|Titanic|Par.|2186.8|658.7|0.301|1528.1|0.699|1997^|
|2|3|Marvel's The Avengers|BV|1518.6|623.4|0.41|895.2|0.59|2012|
|3|4|Harry Potter and the Deathly Hallows Part 2（《哈利·波特与死亡圣器（下）》）|WB|1341.5|381|0.284|960.5|0.716|2011|
|4|5|Frozen（《冰雪奇缘》）|BV|1274.2|400.7|0.314|873.5|0.686|2013|

## <a name="read-fixed-width-files"></a>读取固定宽度文件
对于固定宽度文件，可以指定偏移量列表。 始终假定第一列从偏移量 0 处开始。

```
dataflow = dprep.read_fwf('./data/fixed_width_file.txt', offsets=[7, 13, 43, 46, 52, 58, 65, 73])
dataflow.head(5)
```
||010000|99999|BOGUS NORWAY|否|NO_1|ENRS|Column7|Column8|Column9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010003|99999|BOGUS NORWAY|否|否|ENSO||||
|1|010010|99999|JAN MAYEN（扬马延岛）|否|JN|ENJA|+70933|-008667|+00090|
|2|010013|99999|ROST|否|否|||||
|3|010014|99999|SOERSTOKKEN|否|否|ENSO|+59783|+005350|+00500|
|4|010015|99999|BRINGELAND|否|否|ENBL|+61383|+005867|+03270|


如果文件中没有标头，则需要将第一行视为数据。 通过将 `PromoteHeadersMode.NONE` 传递到标头关键字参数，可以避免标头检测并获取正确的数据。

```
dataflow = dprep.read_fwf('./data/fixed_width_file.txt',
                          offsets=[7, 13, 43, 46, 52, 58, 65, 73],
                          header=dprep.PromoteHeadersMode.NONE)

df = dataflow.to_pandas_dataframe()
df
```
||Column1|Column2|Column3|Column4|Column5|Column6|Column7|Column8|Column9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010000|99999|BOGUS NORWAY|否|NO_1|ENRS|Column7|Column8|Column9|
|1|010003|99999|BOGUS NORWAY|否|否|ENSO||||
|2|010010|99999|JAN MAYEN（扬马延岛）|否|JN|ENJA|+70933|-008667|+00090|
|3|010013|99999|ROST|否|否|||||
|4|010014|99999|SOERSTOKKEN|否|否|ENSO|+59783|+005350|+00500|
|5|010015|99999|BRINGELAND|否|否|ENBL|+61383|+005867|+03270|

## <a name="read-sql"></a>读取 SQL
Azure 机器学习数据准备 SDK 还可以从 SQL 服务器加载数据。 目前，仅支持 Microsoft SQL Server。
要从 SQL 服务器读取数据，请创建包含连接信息的数据源对象。

```
secret = dprep.register_secret("[SECRET-USERNAME]", "[SECRET-PASSWORD]")

ds = dprep.MSSQLDataSource(server_name="[SERVER-NAME]",
                           database_name="[DATABASE-NAME]",
                           user_name="[DATABASE-USERNAME]",
                           password=[DATABASE-PASSWORD])
```
正如所看到的，`MSSQLDataSource` 的密码参数接受机密对象。 可以通过两种方式来获取机密对象：
-   使用执行引擎注册机密及其值。 
-   仅使用 ID 创建机密（如果已在执行环境中注册机密值，则很有用）。

创建数据源对象后，可以继续读取数据。
```
dataflow = dprep.read_sql(ds, "SELECT top 100 * FROM [SalesLT].[Product]")
dataflow.head(5)
```

||ProductID|名称|ProductNumber|颜色|StandardCost|ListPrice|大小|重量|ProductCategoryID|ProductModelID|SellStartDate|SellEndDate|DiscontinuedDate|ThumbNailPhoto|ThumbnailPhotoFileName|rowguid|ModifiedDate|
|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|
|0|680|HL Road Frame - 黑色，58|FR-R92B-58|黑色|1059.3100|1431.50|58|1016.04|18|6|2002-06-01 00:00:00+00:00|无|无|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|43dd68d6-14a4-461f-9069-55309d90ea7e|2008-03-11 |0:01:36.827000+00:00|
|1|706|HL Road Frame - 红色，58|FR-R92R-58|红色|1059.3100|1431.50|58|1016.04|18|6|2002-06-01 00:00:00+00:00|无|无|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|9540ff17-2712-4c90-a3d1-8ce5568b2462|2008-03-11 |10:01:36.827000+00:00|
|2|707|Sport-100 Helmet，红色|HL-U509-R|红色|13.0863|34.99|无|无|35|33|2005-07-01 00:00:00+00:00|无|无|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|2e1ef41a-c08a-4ff6-8ada-bde58b64a712|2008-03-11 |10:01:36.827000+00:00|
|3|708|Sport-100 Helmet，黑色|HL-U509|黑色|13.0863|34.99|无|无|35|33|2005-07-01 00:00:00+00:00|无|无|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|a25a44fb-c2de-4268-958f-110b8d7621e2|2008-03-11 |10:01:36.827000+00:00|
|4|709|山地自行车袜，M|SO-B909-M|白色|3.3963|9.50|M|无|27|18|2005-07-01 00:00:00+00:00|2006-06-30 00:00:00+00:00|无|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|18f95f47-1540-4e02-8f1f-cc1bcb6828d0|2008-03-11 |10:01:36.827000+00:00|

```
df = dataflow.to_pandas_dataframe()
df.dtypes
```
```
ProductID                                     int64
Name                                         object
ProductNumber                                object
Color                                        object
StandardCost                                float64
ListPrice                                   float64
Size                                         object
Weight                                      float64
ProductCategoryID                             int64
ProductModelID                                int64
SellStartDate             datetime64[ns, UTC+00:00]
SellEndDate                                  object
DiscontinuedDate                             object
ThumbNailPhoto                               object
ThumbnailPhotoFileName                       object
rowguid                                      object
ModifiedDate              datetime64[ns, UTC+00:00]
dtype: object
```

## <a name="read-from-azure-data-lake-storage"></a>从 Azure Data Lake Storage 读取
SDK 可以通过两种方式获取访问 Azure Data Lake Storage 所需的 OAuth 令牌：
-   从用户的 Azure CLI 登录的最近登录会话中检索访问令牌
-   使用服务主体 (SP) 和证书作为机密

### <a name="use-an-access-token-from-a-recent-azure-cli-session"></a>使用最近 Azure CLI 会话中的访问令牌
在本地计算机上，运行以下命令：

> [!NOTE] 
> 如果用户帐户是多个 Azure 租户的成员，则需要在 AAD URL 主机名窗体中指定租户。


```
az login
az account show --query tenantId
dataflow = read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', tenant='microsoft.onmicrosoft.com')) head = dataflow.head(5) head
```
### <a name="create-a-service-principal-with-the-azure-cli"></a>使用 Azure CLI 创建服务主体
可以使用 Azure CLI 创建服务主体和相应的证书。 此特定服务主体配置为读取器，其范围仅限于 Azure Data Lake Storage 帐户“dpreptestfiles”
```
az account set --subscription "Data Wrangling development"
az ad sp create-for-rbac -n "SP-ADLS-dpreptestfiles" --create-cert --role reader --scopes /subscriptions/35f16a99-532a-4a47-9e93-00305f6c40f2/resourceGroups/dpreptestfiles/providers/Microsoft.DataLakeStore/accounts/dpreptestfiles
```
此命令发出 `appId` 和证书文件的路径（通常位于主文件夹中）。 .crt 文件包含 PEM 格式的公共证书和私钥。

提取指纹：
```
openssl x509 -in adls-dpreptestfiles.crt -noout -fingerprint
```

##### <a name="configure-an-azure-data-lake-storage-account-for-the-service-principal"></a>配置服务主体的 Azure Data Lake Storage 帐户
若要配置 Azure Data Lake Storage 文件系统的 ACL，请使用用户的 objectId，或者对于此示例，使用服务主体：
```
az ad sp show --id "8dd38f34-1fcb-4ff9-accd-7cd60b757174" --query objectId
```
##### <a name="configure-read-and-execute-access-for-the-azure-data-lake-storage-file-system"></a>配置 Azure Data Lake Storage 文件系统的读取和执行访问权限。
由于基础 HDFS ACL 模型不支持继承，需要单独配置文件夹和文件的 ACL。
```
az dls fs access set-entry --account dpreptestfiles --acl-spec "user:e37b9b1f-6a5e-4bee-9def-402b956f4e6f:r-x" --path /
az dls fs access set-entry --account dpreptestfiles --acl-spec "user:e37b9b1f-6a5e-4bee-9def-402b956f4e6f:r--" --path /farmers-markets.csv
```
```
certThumbprint = 'C2:08:9D:9E:D1:74:FC:EB:E9:7E:63:96:37:1C:13:88:5E:B9:2C:84'
certificate = ''
with open('./data/adls-dpreptestfiles.crt', 'rt', encoding='utf-8') as crtFile:
    certificate = crtFile.read()

servicePrincipalAppId = "8dd38f34-1fcb-4ff9-accd-7cd60b757174"
```
#### <a name="acquire-an-oauth-access-token"></a>获取 OAuth 访问令牌
使用 `adal` 包（通过：`pip install adal`）在 MSFT 租户上创建身份验证上下文，并获取 OAuth 访问令牌。 对于 ADLS，令牌请求中的资源必须为“https://datalake.azure.net”，这与大多数其他 Azure 资源不同。

```
import adal
from azureml.dataprep.api.datasources import DataLakeDataSource

ctx = adal.AuthenticationContext('https://login.microsoftonline.com/microsoft.onmicrosoft.com')
token = ctx.acquire_token_with_client_certificate('https://datalake.azure.net/', servicePrincipalAppId, certificate, certThumbprint)
dataflow = dprep.read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', accessToken=token['accessToken']))
dataflow.to_pandas_dataframe().head()
```

||FMID|MarketName|网站|street|city|县|
|----|------|-----|----|----|----|----|
|0|1012063|喀里多尼亚农贸市场协会 - 丹维尔|https://sites.google.com/site/caledoniafarmers..。 ||丹维尔|喀里多尼亚|
|1|1011871|斯特恩斯家园农贸市场|http://Stearnshomestead.com |6975 Ridge Road|帕尔马|凯霍加河|
|2|1011878|100 英里市场|http://www.pfcmarkets.com |507 哈里森街|卡拉马祖|卡拉马祖|
|3|1009364|106 S. 主要街道农贸市场|http://thetownofsixmile.wordpress.com/ |106 S. 主要街道|六英里|||
|4|1010691|第十街社区农贸市场|http://agrimissouri.com/mo-grown/grodetail.php..。 |第十街和波普拉区|拉马尔|巴顿|

