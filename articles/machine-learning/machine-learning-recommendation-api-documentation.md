---
title: "机器学习建议 API 文档 | Microsoft Docs"
description: "用于建议引擎的 Azure 机器学习建议 API 文档在 Microsoft Azure 应用商店中提供。"
services: machine-learning
documentationcenter: 
author: LuisCabrer
manager: jhubbard
editor: cgronlun
ms.assetid: 32c3ab2f-fdd7-48cc-b501-ad55c79b87dc
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: LuisCa
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d9c7f3c11e534f20bae157430a00724a2c839e8e


---
# <a name="azure-machine-learning-recommendations-api-documentation"></a>Azure 机器学习建议 API 文档
> [!NOTE]
> 应开始使用建议 API 认知服务，而非此版本。 建议认知服务将替代此服务，并且所有新功能都将在该处开发。 它具有新功能，如支持批处理、更好用的 API 资源管理器、更简洁的 API 图面、更一致的注册/计费体验等。
> 了解有关[迁移到新认知服务](http://aka.ms/recomigrate)的详细信息
> 
> 

本文档介绍 Microsoft Azure 机器学习建议 API。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="1-general-overview"></a>1.一般概述
本文档是 API 参考。 应从“Azure 机器学习建议 - 快速入门”文档开始。

Azure 机器学习建议 API 可分为以下逻辑组：

* <ins>限制</ins> - 建议 API 限制。
* <ins>常规信息</ins> - 有关身份验证、服务 URI 和版本控制的信息。
* <ins>模型基本</ins> - 支持在模型上执行基本操作（例如创建、更新和删除模型）的 API。
* <ins>模型高级</ins> - 支持深入了解模型上高级数据的 API。
* <ins>模型业务规则</ins> - 支持管理模型建议结果上的业务规则的 API。
* <ins>目录</ins> - 支持在模型目录上执行基本操作的 API。 目录包含使用数据项的元数据信息。
* <ins>功能</ins> - 支持深入了解目录项目和如何使用此信息生成更好的建议的 API。
* <ins>使用数据</ins> - 支持在模型使用数据上执行基本操作的 API。 基本窗体中的使用数据由包括 &#60;userId&#62;、&#60;itemId&#62; 对的行组成。
* <ins>生成</ins> - 支持触发模型生成并执行与此生成相关的基本操作的 API。 具有有价值的使用数据时可触发模型生成。
* <ins>建议</ins> - 支持在模型生成后使用建议的 API。
* <ins>用户数据</ins> - 支持提取有关用户使用数据信息的 API。
* <ins>通知</ins> - 支持接收与 API 操作相关的问题通知的 API。 （例如，当你通过数据采集报告使用数据，但大多数事件处理均失败时， 将引发错误通知。）

## <a name="2-limitations"></a>2.限制
* 每个订阅的最大模型数是 10 个。
* 每个模型的最大生成数是 20 个。
* 目录可容纳的最大项目数是 100000 个。
* 保留的最大使用点数约为 5000000 个。 如果上传或报告新使用点，将删除最老的使用点。
* 邮件可发送的最大数据大小（例如导入目录数据、导入使用数据）是 200MB。
* 获取建议时可要求的最大项目数是 150 个。

## <a name="3-apis---general-information"></a>3.API - 常规信息
### <a name="31-authentication"></a>3.1. 身份验证
请遵循有关身份验证的 Microsoft Azure 应用商店指南。 应用商店支持基本或 OAuth 身份验证方法。

### <a name="32-service-uri"></a>3.2. 服务 URI
Azure 机器学习建议 API 的服务根 URI 在[此处](https://api.datamarket.azure.com/amla/recommendations/v3/)。

完整服务 URI 使用 OData 规范的元素表示。  

### <a name="33-api-version"></a>3.3. API 版本
最终，每个 API 调用都将拥有名为“apiVersion”的查询参数，该参数应设为 1.0。

### <a name="34-ids-are-case-sensitive"></a>3.4. ID 区分大小写
任何 API 返回的 ID 均区分大小写，并且应在后续 API 调用中传递为参数时使用。 例如，模型 ID 和目录 ID 区分大小写。

## <a name="4-recommendations-quality-and-cold-items"></a>4.建议质量和冷项
### <a name="41-recommendation-quality"></a>4.1. 建议质量
创建建议模型通常足够支持系统提供建议。 然后，建议质量因处理的使用情况和目录覆盖范围而异。 例如，如果有许多冷项（没有重要使用情况的项目），系统将难以为此类项目提供建议或将此类项目用作建议项目。 为了解决冷项问题，系统允许使用项目元数据以增强建议。 此元数据称为功能。 典型功能是书籍作者或电影演员。 功能通过密钥/值字符串形式的目录提供。 有关目录文件的完整格式，请参阅[导入目录部分](#81-import-catalog-data)。 

### <a name="42-rank-build"></a>4.2. 排名生成
功能可增强建议模型，但这样做要求使用有意义的功能。 为此引入了新生成：排名生成。 此生成将对功能有用性进行排名。 有意义的功能是排名分数在 2 分和更高分的功能。
在了解哪些功能是有意义的功能后，触发包含有意义功能列表（子列表）的建议生成。 可将这些功能用于增强暖项和冷项。 为了将它们用于暖项，应设置 `UseFeatureInModel` 生成参数。 为了将功能用于冷项，应启用 `AllowColdItemPlacement` 生成参数。
注意：不启用 `UseFeatureInModel` 则无法启用 `AllowColdItemPlacement`。

### <a name="43-recommendation-reasoning"></a>4.3. 建议推理
建议推理是功能使用的另一个方面。 事实上，Azure 机器学习建议引擎可使用功能提供建议解释（也称为 推理），这使建议使用者对建议项目更有信心。
若要启用推理，在请求建议生成前，应设置 `AllowFeatureCorrelation` 和 `ReasoningFeatureList` 参数。

## <a name="5-model-basic"></a>5.模型基本
### <a name="51-create-model"></a>5.1. 创建模型
创建“创建模型”请求。

| HTTP 方法 | URI |
|:--- |:--- |
| POST |`<rootURI>/CreateModel?modelName=%27<model_name>%27&apiVersion=%271.0%27`<br><br>示例：<br>`<rootURI>/CreateModel?modelName=%27MyFirstModel%27&apiVersion=%271.0%27` |

| 参数名称 | 有效值 |
|:--- |:--- |
| modelName |仅允许使用字母（A-Z、a-z）、数字 (0-9)、连字符 (-) 和下划线 (_)。<br>最大长度：20 |
| apiVersion |1.0 |
|  | |
| 请求正文 |无 |

**响应**：

HTTP 状态代码：200

* `feed/entry/content/properties/id` - 包含模型 ID。
  **注意**：模型 ID 区分大小写。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
      <subtitle type="text">Create A New Model</subtitle>
      <id>https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'</id>
      <rights type="text" />
      <updated>2014-10-05T06:35:21Z</updated>
      <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'" />
      <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">CreateANewModelEntity2</title>
    <updated>2014-10-05T06:35:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">a658c626-2baa-43a7-ac98-f6ee26120a12</d:Id>
        <d:Name m:type="Edm.String">MyFirstModel</d:Name>
        <d:Date m:type="Edm.String">10/5/2014 6:35:19 AM</d:Date>
        <d:Status m:type="Edm.String">Created</d:Status>
        <d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
        <d:BuildId m:type="Edm.String">-1</d:BuildId>
        <d:Mpr m:type="Edm.String">0</d:Mpr>
        <d:UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:UserName>
        <d:Description m:type="Edm.String"></d:Description>
      </m:properties>
    </content>
      </entry>
    </feed>

### <a name="52-get-model"></a>5.2. 获取模型
创建“获取模型”请求。

| HTTP 方法 | URI |
|:--- |:--- |
| GET |`<rootURI>/GetModel?id=%27<model_id>%27&apiVersion=%271.0%27`<br>示例：<br>`<rootURI>/GetModel?id=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27` |

| 参数名称 | 有效值 |
|:--- |:--- |
| id |模型的唯一标识符（区分大小写） |
| apiVersion |1.0 |
|  | |
| 请求正文 |无 |

**响应**：

HTTP 状态代码：200

模型数据可在以下元素中找到：

* `feed/entry/content/properties/Id` - 模型唯一 ID。
* `feed/entry/content/properties/Name` - 模型名称。
* `feed/entry/content/properties/Date` - 模型创建日期。
* `feed/entry/content/properties/Status` - 模型状态。 下列类型作之一：
  * 已创建 - 模型已创建，并且不包含目录和使用情况。
  * ReadyForBuild - 模型已创建，并且包含目录和使用情况。
* `feed/entry/content/properties/HasActiveBuild` - 指示模型是否创建成功。
* `feed/entry/content/properties/BuildId` - 模型活动生成 ID。
* `feed/entry/content/properties/Mpr` - 模型平均百分位排名（MPR - 有关详细信息，请参阅 ModelInsight）。
* `feed/entry/content/properties/UserName` - 模型内部用户名称。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
      <subtitle type="text">Get A List Of All Models</subtitle>
      <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'</id>
      <rights type="text" />
      <updated>2014-10-28T14:35:51Z</updated>
      <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'" />
      <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetAListOfAllModelsEntity</title>
    <updated>2014-10-28T14:35:51Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">68b232f2-1037-45f7-8f49-af822695ee63</d:Id>
        <d:Name m:type="Edm.String">vah-11111</d:Name>
        <d:Date m:type="Edm.String">10/28/2014 2:16:07 PM</d:Date>
        <d:Status m:type="Edm.String">ReadyForBuild</d:Status>
        <d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
        <d:BuildId m:type="Edm.String">-1</d:BuildId>
        <d:Mpr m:type="Edm.String">0</d:Mpr>
        <d:UsageFileNames m:type="Edm.String">ImplicitMatrix10_Guid_small.txt, ImplicitMatrix11_Guid_small.txt</d:UsageFileNames>
        <d:CatalogId m:type="Edm.String">626babdb-cab6-43a6-82ef-4fb86345700c</d:CatalogId>
        <d:UserName m:type="Edm.String">89dc4722-03ba-4f90-8821-b1db388408b5@dm.com</d:UserName>
        <d:Description m:type="Edm.String">short description</d:Description>
        <d:CatalogFileName m:type="Edm.String">catalog10_small.txt</d:CatalogFileName>
      </m:properties>
    </content>
      </entry>
    </feed>

### <a name="53-get-all-models"></a>5.3.    获取所有模型
检索当前用户的所有模型。

| HTTP 方法 | URI |
|:--- |:--- |
| GET |`<rootURI>/GetAllModels?apiVersion=%271.0%27`<br>示例：<br>`<rootURI>/GetAllModels?apiVersion=%271.0%27` |

| 参数名称 | 有效值 |
|:--- |:--- |
| apiVersion |1.0 |
|  | |
| 请求正文 |无 |

**响应**：

HTTP 状态代码：200

* `feed/entry/content/properties/Id` - 模型唯一 ID。
* `feed/entry/content/properties/Name` - 模型名称。
* `feed/entry/content/properties/Date` - 模型创建日期。
* `feed/entry/content/properties/Status` - 模型状态。 下列类型作之一：
  * 已创建 - 模型已创建，并且不包含目录和使用情况。
  * ReadyForBuild - 模型已创建，并且包含目录和使用情况。
* `feed/entry/content/properties/HasActiveBuild` - 指示模型是否创建成功。
* `feed/entry/content/properties/BuildId` - 模型活动生成 ID。
* `feed/entry/content/properties/Mpr` - 模型 MPR（有关详细信息，请参阅 ModelInsight）。
* `feed/entry/content/properties/UserName` - 模型内部用户名称。
* `feed/entry/content/properties/UsageFileNames` - 用逗号分隔的模型使用情况文件列表。
* `feed/entry/content/properties/CatalogId` - 模型目录 ID。
* `feed/entry/content/properties/Description` - 模型说明。
* `feed/entry/content/properties/CatalogFileName` - 模型目录文件名称。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get A List Of All Models</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'</id>
        <rights type="text" />
        <updated>2014-10-28T14:35:51Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'" />
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
            <title type="text">GetAListOfAllModelsEntity</title>
    <updated>2014-10-28T14:35:51Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Id m:type="Edm.String">68b232f2-1037-45f7-8f49-af822695ee63</d:Id>
                    <d:Name m:type="Edm.String">vah-11111</d:Name>
                    <d:Date m:type="Edm.String">10/28/2014 2:16:07 PM</d:Date>
                    <d:Status m:type="Edm.String">ReadyForBuild</d:Status>
                    <d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
                    <d:BuildId m:type="Edm.String">-1</d:BuildId>
                    <d:Mpr m:type="Edm.String">0</d:Mpr>
                    <d:UsageFileNames m:type="Edm.String">ImplicitMatrix10_Guid_small.txt, ImplicitMatrix11_Guid_small.txt</d:UsageFileNames>
                    <d:CatalogId m:type="Edm.String">626babdb-cab6-43a6-82ef-4fb86345700c</d:CatalogId>
                    <d:UserName m:type="Edm.String">89dc4722-03ba-4f90-8821-b1db388408b5@dm.com</d:UserName>
                    <d:Description m:type="Edm.String">short description</d:Description>
                    <d:CatalogFileName m:type="Edm.String">catalog10_small.txt</d:CatalogFileName>
                </m:properties>
            </content>
        </entry>
    </feed>

### <a name="54-update-model"></a>5.4.    更新模型
可更新模型说明或活动生成 ID。<br>
<ins>活动生成 ID</ins> - 每个模型的每个生成均具有生成 ID。 活动生成 ID 是每个新模型的第一个成功生成。 如果拥有活动生成 ID 并且为相同模型执行其他生成，则在需要时要显式将其设置为默认生成 ID。 使用建议时，如果未指定要使用的生成 ID，将自动使用默认生成 ID。<br>
如果在生产中拥有建议模型，这套机制支持先生成和测试新模型，然后推动它们进入生产。

| HTTP 方法 | URI |
|:--- |:--- |
| PUT |`<rootURI>/UpdateModel?id=%27<modelId>%27&apiVersion=%271.0%27`<br>示例：<br>`<rootURI>/UpdateModel?id=%279559872f-7a53-4076-a3c7-19d9385c1265%27&apiVersion=%271.0%27` |

| 参数名称 | 有效值 |
|:--- |:--- |
| id |模型的唯一标识符（区分大小写） |
| apiVersion |1.0 |
|  | |
| 请求正文 |`<ModelUpdateParams xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">`<br>`<Description>New Description</Description>`<br>`<ActiveBuildId>-1</ActiveBuildId>`<br>` </ModelUpdateParams>`<br><br>请注意，XML 标记说明和 ActiveBuildId 为选填项。 如果不想设置说明或 ActiveBuildId，请删除整个标记。 |

**响应**：

HTTP 状态代码：200

### <a name="55-delete-model"></a>5.5.    删除模型
按 ID 删除现有模型。

| HTTP 方法 | URI |
|:--- |:--- |
| 删除 |`<rootURI>/DeleteModel?id=%27<model_id>%27&apiVersion=%271.0%27`<br>示例：<br>`<rootURI>/DeleteModel?id=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27` |

| 参数名称 | 有效值 |
|:--- |:--- |
| id |模型的唯一标识符（区分大小写） |
| apiVersion |1.0 |
|  | |
| 请求正文 |无 |

**响应**：

HTTP 状态代码：200

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
      <subtitle type="text">Delete Model by Id</subtitle>
      <id>https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'</id>
      <rights type="text" />
      <updated>2014-10-28T10:39:33Z</updated>
      <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'" />
      <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">DeleteModelByIdEntity</title>
    <updated>2014-10-28T10:39:33Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:string m:type="Edm.String"></d:string>
      </m:properties>
    </content>
      </entry>
    </feed>

## <a name="6-model-advanced"></a>6.高级模型
### <a name="61-model-data-insight"></a>6.1.    模型数据见解
返回此模型用以生成的使用数据的统计数据。

仅可用于建议生成。

| HTTP 方法 | URI |
|:--- |:--- |
| GET |`<rootURI>/GetDataInsight?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>示例：<br>`<rootURI>/GetDataInsight?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27` |

| 参数名称 | 有效值 |
|:--- |:--- |
| modelId |模型的唯一标识符 |
| apiVersion |1.0 |
|  | |
| 请求正文 |无 |

**响应**：

HTTP 状态代码：200

数据返回为属性集合。

* `feed/entry/id/content/properties/key` - 包含属性名称。
* `feed/entry/id/content/properties/value` - 包含属性值。

下表介绍了每个密钥表示的值。

| 键 | 说明 |
|:--- |:--- |
| AvgItemLength |每个项目的平均不同用户数。 |
| AvgUserLength |每个用户的平均不同项目数。 |
| DensificationNumberOfItems |删除无法建模的项目后的项目数。 |
| DensificationNumberOfUsers |删除无法建模的用户和项目后的使用点数。 |
| DensificationNumberOfRecords |删除无法建模的用户和项目后的使用点数。 |
| MaxItemLength |最热门项目的不同用户数。 |
| MaxUserLength |用户的最大不同项目数。 |
| MinItemLength |项目的最大不同用户数。 |
| MinUserLength |用户的最小不同项目数。 |
| RawNumberOfItems |使用情况文件中的项目数。 |
| RawNumberOfUsers |删除前的使用点数。 |
| RawNumberOfRecords |删除前的使用点数。 |
| SamplingNumberOfItems |不适用 |
| SamplingNumberOfRecords |不适用 |
| SamplingNumberOfUsers |不适用 |

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Get data insight statistics</subtitle>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">AvgItemLength</d:Key>
        <d:Value m:type="Edm.String">18.936</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">AvgUserLength</d:Key>
        <d:Value m:type="Edm.String">51.879</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">DensificationNumberOfItems</d:Key>
        <d:Value m:type="Edm.String">2,000</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">DensificationNumberOfRecords</d:Key>
        <d:Value m:type="Edm.String">37,872</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
    <content type="application/xml">
        <m:properties>
            <d:Key m:type="Edm.String">DensificationNumberOfUsers</d:Key>
            <d:Value m:type="Edm.String">730</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">MaxItemLength</d:Key>
                <d:Value m:type="Edm.String">4,704</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">MaxUserLength</d:Key>
                <d:Value m:type="Edm.String">190</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">MinItemLength</d:Key>
                <d:Value m:type="Edm.String">8</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">MinUserLength</d:Key>
                <d:Value m:type="Edm.String">20</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">RawNumberOfItems</d:Key>
                <d:Value m:type="Edm.String">2,000</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">RawNumberOfRecords</d:Key>
                <d:Value m:type="Edm.String">72,022</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">RawNumberOfUsers</d:Key>
                <d:Value m:type="Edm.String">9,044</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">SampelingNumberOfItems</d:Key>
                <d:Value m:type="Edm.String">2,000</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=13&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=13&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">SampelingNumberOfRecords</d:Key>
                <d:Value m:type="Edm.String">72,022</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=14&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=14&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">SampelingNumberOfUsers</d:Key>
                <d:Value m:type="Edm.String">9,044</d:Value>
            </m:properties>
        </content>
    </entry>
    </feed>

### <a name="62-model-insight"></a>6.2.    模型见解
返回活动生成或特定生成（如提供）的模型见解。

仅可用于建议生成。

| HTTP 方法 | URI |
|:--- |:--- |
| GET |对于活动生成 ID：<br>`<rootURI>/GetModelInsight?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>示例：<br>`<rootURI>/GetModelInsight?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`<br><br>对于特定生成 ID：<br>`<rootURI>/GetModelInsight?modelId=%27<model_id>%27&buildId=%27<build_id>%27&apiVersion=%271.0%27` |

| 参数名称 | 有效值 |
|:--- |:--- |
| modelId |模型的唯一标识符 |
| buildId |可选 - 标识成功生成的数字。 |
| apiVersion |1.0 |
|  | |
| 请求正文 |无 |

**响应**：

HTTP 状态代码：200

数据返回为属性集合。

* `feed/entry/id/content/properties/key`
* `feed/entry/id/content/properties/value`

下表介绍了每个密钥表示的值。

| 键 | 说明 |
|:--- |:--- |
| CatalogCoverage |可通过使用模式建模的目录部分。 项目的其余部分需要基于内容的功能。 |
| Mpr |模型的平均百分位排名。 排名越低越好。 |
| NumberOfDimensions |矩阵因子分解算法使用的维度数。 |

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Get model insight statistics</subtitle>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-27T14:27:11Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'" />
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
        <title type="text">GetModelInsightStatisticsEntity</title>
        <updated>2014-10-27T14:27:11Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">CatalogCoverage</d:Key>
                <d:Value m:type="Edm.String">1.000</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
        <title type="text">GetModelInsightStatisticsEntity</title>
        <updated>2014-10-27T14:27:11Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">Mpr</d:Key>
                <d:Value m:type="Edm.String">0.367</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
        <title type="text">GetModelInsightStatisticsEntity</title>
        <updated>2014-10-27T14:27:11Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">NumberOfDimensions</d:Key>
                <d:Value m:type="Edm.String">20</d:Value>
            </m:properties>
        </content>
    </entry>
    </feed>

### <a name="63-get-model-sample"></a>6.3.    获取模型示例
获取建议模型示例。

| HTTP 方法 | URI |
|:--- |:--- |
| GET |`<rootURI>/GetModelSample?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>示例：<br>`<rootURI>/GetModelSample?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`<br><br>对于特定生成 ID：<br>`<rootURI>/GetModelSample?modelId=%27<model_id>%27&buildId=%27<build_id>%27&apiVersion=%271.0%27`<br>示例：<br>`<rootURI>/GetModelSample?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&buildId=%271500068%27&apiVersion=%271.0%27` |

| 参数名称 | 有效值 |
|:--- |:--- |
| modelId |模型的唯一标识符 |
| apiVersion |1.0 |
|  | |
| 请求正文 |无 |

**响应**：

HTTP 状态代码：200

OData XML

响应按原始文本格式返回：

<pre>
级别 1 --------------- 655fc955-a5a3-4a26-9723-3090859cb27b，Prey: A Novel 655fc955-a5a3-4a26-9723-3090859cb27b，Prey: A Novel 分级：0.5215 3f471802-f84f-44a0-99c8-6d2e7418eec1，Black Hawk Down: A Story of Modern War 分级：0.5151 07b10e28-9e7c-4032-90b7-10acab7f2460，Cryptonomicon 分级：0.5148 6afc18e4-8c2a-43d1-9021-57543d6b11d8，Imajica 分级：0.5146 e4cc5e69-3567-43ab-b00f-f0d8d0506870，Hit List 分级：0.514 56b61441-0eed-46cc-a8f6-112775b81892，Life and Death in Shanghai 56b61441-0eed-46cc-a8f6-112775b81892，Life and Death in Shanghai 分级：0.5218 53156702-cc0c-443d-b718-6fb74b2491d3，Son of \ 分级：0.5212 fb8cf7a6-8719-46ee-97d4-92f931d77a3a，Smoke and Mirrors: Short Fictions and Illusions 分级：0.5188 8f5fe006-79e4-4679-816b-950989d1db4b，A Place I've Never Been (Contemporary American Fiction) 分级：0.5156 d8db4583-cc0f-49ce-bc95-b7fa3491623f，Happiness: A Novel 分级：0.5156 50471eec-9aeb-4900-84d7-21567ab18546，If the Buddha Dated: A Handbook for Finding Love on a Spiritual Path cfe922a1-7ca0-4f8d-ad9d-b7cc87bfe0ef， Divine Secrets of the Ya-Ya Sisterhood: A Novel 分级：0.5266 ff91a483-1ce5-4b37-a6fd-5ffcf21f8745，The Poisonwood Bible: A Novel 分级：0.5252 973f8cbd-0846-4f6b-9d28-4dd0d7dc3a19，Pigs in Heaven 分级：0.5244 e2cbf7ad-0636-4117-8b30-298da6df7077，Animal Dreams 分级：0.5227 6c818fd3-5a09-417d-9ab4-7ffe090f0fef，Confessions of an Ugly Stepsister: A Novel 分级：0.5222 5e97148f-defb-4d74-af2d-80f4763bf531，The Deep End of the Ocean (Oprah's Book Club) 5e97148f-defb-4d74-af2d-80f4763bf531，The Deep End of the Ocean (Oprah's Book Club) 分级：0.537 5dcbac37-2946-4f2a-a0b3-bbe710f9409a，, Up Island：小说 分级：0.5277 bc5b69db-733b-4346-adde-3927544258f7，Downtown 分级：0.5275 31fe5c63-3e5a-48d0-802b-d3b0f989a634，Have a Nice Day: A Tale of Blood and Sweatsocks 分级：0.5252 0adf981a-b65b-4c11-b36b-78aca2f948a2，The Perfect Storm: A True Story of Men Against the Sea 分级：0.5238 68f97068-ae1a-4163-9e94-396b800b743d，Modoc: The True Story of the Greatest Elephant That Ever Lived 68f97068-ae1a-4163-9e94-396b800b743d，Modoc: The True Story of the Greatest Elephant That Ever Lived 分级：0.5379 6724862e-e4e7-4022-9614-1468d8b902ff，Little House on the Prairie 分级：0.5345 cdedb837-1620-496d-94c4-6ccfed888320，Little House in the Big Woods 分级：0.5325 382164ba-406b-4187-b726-d7a54b9d790d，The Tao of Pooh 分级：0.5309 6a068d6a-bb74-4ba3-b3f2-a956c4f9d1b5，On the Banks of Plum Creek 分级：0.5285 37ef8e74-e348-44e5-aabc-1d7f9efcb25b，Men Are from Mars Women Are from Venus: A Practical Guide for Improving Communication and Getting What You Want in Your Relationships 37ef8e74-e348-44e5-aabc-1d7f9efcb25b，Men Are from Mars, Women Are from Venus: A Practical Guide for Improving Communication and Getting What You Want in Your Relationships 分级：0.5397 f2be16d4-5faf-4d32-ab83-7ba74d29261e，, Politically Correct Bedtime Stories: Modern Tales for Our Life and Times 分级：0.5207 ef732c5c-334b-4d6b-ab82-7255eb7286d0，Honor Among Thieves 分级：0.5195 0b209b8c-7cdd-47fd-b940-05c7ff7c60fc，The Giving Tree 分级：0.5194 883b360f-8b42-407f-b977-2f44ad840877，Scary Stories to Tell in the Dark: Collected from American Folklore (Scary Stories) 分级：0.5184 ff51b67e-fa8e-4c5e-8f4d-02a928de735d，Men at Work: The Craft of Baseball d008dae9-c73a-40a1-9a9b-96d5cf546f36，The Gulag Archipelago 1918-1956: An Experiment in Literary Investigation I-II 分级：0.5416 ff51b67e-fa8e-4c5e-8f4d-02a928de735d，Men at Work: The Craft of Baseball 分级：0.5403 49dec30e-0adb-411a-b186-48eaabf6f8bc，Fatherland 分级：0.5394 cc7964fd-d30f-478e-a425-93ddbdf094ed，Magic the Gathering: Arena Vol. 1 分级：0.5379 8a1e9f36-97af-4614-bed9-24e3940a05f3，More Sniglets: Any Word That Doesn't Appear in the Dictionary but Should 分级：0.5377 12a6d988-be21-4a09-8143-9d5f4261ba16，A Dream of Eagles 07b10e28-9e7c-4032-90b7-10acab7f2460，Cryptonomicon 分级：0.5417 e4cc5e69-3567-43ab-b00f-f0d8d0506870，Hit List 分级：0.5416 1f1a34c4-9781-49f5-a3cc-acec3ae3c71d，The Family 分级：0.5371 56daeffe-7d48-43cd-8ef8-7dffd0c103d3，Kilo Class 分级：0.5366 b2fe511e-5cb9-4a56-b823-2801e63e6a96，Legal Tender 分级：0.5366 df87525b-e435-4bd6-8701-4e60ad344e28，Finding Fish 56d33036-dfda-46b9-8e2a-76cb03921bb0， The X-Files: Ground Zero 分级：0.5417 0780cde8-6529-4e1d-b6c6-082c1b80e596，Twelve Red Herrings 分级：0.5416 df87525b-e435-4bd6-8701-4e60ad344e28，Finding Fish 分级：0.5408 400fe331-2c35-490c-adbc-b28b4b73d56c，Shall We Tell the President? 分级：0.5383 f86ad7d0-5c03-42b3-aebf-13d44aec8b30，Shades of Grace 分级：0.5358 de1f62a4-89e6-44d2-aaee-992a4bf093f1，The Map That Changed the World: William Smith and the Birth of Modern Geology de1f62a4-89e6-44d2-aaee-992a4bf093f1，The Map That Changed the World: William Smith and the Birth of Modern Geology 分级：0.5422 b303538f-e2c6-4a2c-b425-8d21e684fc3e，My Uncle Oswald 分级：0.5385 34b84627-48af-4a4c-96c4-b26fb3863f56，Midnight In the Garden of Good and Evil 分级：0.5379 306cbaa7-b1a8-4142-9d55-e11b5018a7a8，The Street Lawyer 分级：0.5376 e53b4baa-8c09-45c4-95c0-b6a26b98770b，Miss Smillas Feeling for Snow 分级：0.5367

<a name="level-2"></a>级别 2
---------------
352aaea1-6b12-454d-a3d5-46379d9e4eb2，The Sinister Pig (Hillerman Tony) 352aaea1-6b12-454d-a3d5-46379d9e4eb2，The Sinister Pig (Hillerman Tony) 分级：0.5425 74c49398-bc10-4af5-a658-a996a1201254，Children of the Storm (Peters Elizabeth) 分级：0.5387 9ba80080-196e-43fd-8025-391d963f77e7，The Floating Girl 分级：0.5372 e68f81d5-7745-4cc7-b943-fedb8fcc2ced，Killer Smile (Scottoline Lisa) 分级：0.5353 b2fe511e-5cb9-4a56-b823-2801e63e6a96，Legal Tender 分级：0.5332 c65c3995-abf7-4c7b-bb3c-8eb5aa9be7a5，Lake Wobegon days 0adf981a-b65b-4c11-b36b-78aca2f948a2，The Perfect Storm: A True Story of Men Against the Sea 分级：0.5433 c65c3995-abf7-4c7b-bb3c-8eb5aa9be7a5，Lake Wobegon days 分级：0.543 a00ae6ad-4a7f-4211-9836-75ce8834eb11，Sniglets (Snig'lit: Any Word That Doesn't Appear in the Dictionary But Should) 分级：0.5327 6f6e192e-0d64-49ca-9b63-f09413ea1ee6，Politically Correct Holiday Stories: For an Enlightened Yuletide Season 分级：0.5307 798051a8-147d-4d46-b0dc-e836325029e6，AGE OF INNOCENCE (MOVIE TIE-IN) 分级：0.5301 73f3e25a-e996-4162-9ed8-ff3d34075650，O Pioneers! (Penguin Twentieth-Century Classics) cba8163f-6536-436b-8130-47b4a43c827f，Trust No One (The Official Guide to the X-Files Vol. 2) 分级：0.5434 5708e4cb-2492-49c0-94a8-cc413eec5d89，Small Gods (Discworld Novels (Paperback) 分级：0.5406 73f3e25a-e996-4162-9ed8-ff3d34075650，O Pioneers! (Penguin Twentieth-Century Classics) 分级：0.5403 d885b0bd-ae4b-452d-bdf2-faa90197dbc9，The Color of Magic 分级：0.539 b133a9c4-4784-4db3-b100-d0d6dffb94d2，The Truth Is Out There (The Official Guide to the X-Files Vol. 1) 分级：0.5367 271700a5-854a-4d5a-8409-6b57a5ee4de4, Fluke: Or I Know Why the Winged Whale Sings 271700a5-854a-4d5a-8409-6b57a5ee4de4，Fluke: Or I Know Why the Winged Whale Sings 分级：0.5445 2de1c354-90ff-47c5-a0db-1bad7d88ef94，The Salaryman's Wife (Children of Violence Series) 分级：0.5329 d279416e-19c0-43f8-9ec9-a585947879ca，Zen Attitude 分级：0.5316 c8f854d7-3de3-4b23-8217-f4f851670fd4，Revenge of the Cootie Girls: A Robin Hudson Mystery (Robin Hudson Mysteries (Paperback)) 分级：0.5305 8ef4751c-7074-409e-a3ac-d49b222fc864，Where the Wild Things Are 分级：0.5289 9ad1b620-0a7b-4543-8673-66d4c3bcb2f1，Their Eyes Were Watching God 9ad1b620-0a7b-4543-8673-66d4c3bcb2f1，Their Eyes Were Watching God 分级：0.5446 da45c4d5-aba1-413b-a9bd-50df98b1e1d2，The Bean Trees 分级：0.5389 65ecbdd1-131c-40c3-a3d6-d86ca281377a，The God of Small Things 分级：0.5387 c78743bf-7947-4a0c-8db7-8a3bfe69ba70，The Stone Diaries 分级：0.5355 973f8cbd-0846-4f6b-9d28-4dd0d7dc3a19，Pigs in Heaven 分级：0.5344 5f17d90a-2604-4fe8-8977-1a280b9098b1，One for the Money (Stephanie Plum Novels (Paperback)) 5f17d90a-2604-4fe8-8977-1a280b9098b1，One for the Money (Stephanie Plum Novels (Paperback)) 分级：0.5446 57169b2b-9a8a-486b-9aac-1ed98ce57168，Final Appeal 分级：0.5332 efcb1bc4-7278-4a8f-b491-befde02070d6，Moment of Truth 分级：0.5329 1efa91a2-993b-4c43-9f5c-3454fc12612d，Burn Factor 分级：0.5309 24c59962-458a-4ec8-b95d-d694e861919c，At Home in Mitford (The Mitford Years) 分级：0.5303 4fd48c46-1a20-4c57-bc7f-a02ef123dc52，As Nature Made Him: The Boy Who Was Raised As a Girl 4fd48c46-1a20-4c57-bc7f-a02ef123dc52，As Nature Made Him: The Boy Who Was Raised As a Girl 分级：0.5449 cd5f2c03-20cb-43be-a1fb-3b4233e63222，Pigs in Heaven 分级：0.5329 19985fdb-d07a-4a25-ae4a-97b9cb61e5d1，Love in the Time of Cholera (Penguin Great Books of the 20th Century) 分级：0.5267 15689d09-c711-4844-84d8-130a90237b26，Bel Canto 分级：0.5245 ff91a483-1ce5-4b37-a6fd-5ffcf21f8745，The Poisonwood Bible: A Novel 分级：0.5235 98df28ec-41e7-4fca-b77f-8b0d3109085d，Star Trek Memories f874b5a3-5d40-4436-94ff-0fa1c090ddf5，The Sun Also Rises (A Scribner classic) 分级：0.5451 98df28ec-41e7-4fca-b77f-8b0d3109085d，Star Trek Memories 分级：0.5442 0ce0014a-9a48-4013-a08a-7f2c11877930，H.M.S. 未看到的分级：0.5421 15316ca6-1e38-425f-893d-691944a47000，More Scary Stories To Tell In The Dark 分级：0.5409 329d5682-3dc3-4206-8aa2-eef4b1032258，Letters from the Earth 分级：0.54 5b9445d5-c072-419c-8d49-6f669bb1b0a9，Daughter of Fortune: A Novel (Oprah's Book Club (Hardcover)) 5b9445d5-c072-419c-8d49-6f669bb1b0a9，Daughter of Fortune: A Novel (Oprah's Book Club (Hardcover)) 分级：0.5462 ff91a483-1ce5-4b37-a6fd-5ffcf21f8745，The Poisonwood Bible: A Novel 分级：0.5372 604eb3bd-6026-4f51-bffd-9fb54f180400，Family Pictures: A Novel 分级：0.5341 8d06d01d-31cd-4678-b6b1-140a67987ce9，Songs in Ordinary Time (Oprah's Book Club (Paperback)) 分级：0.5334 da45c4d5-aba1-413b-a9bd-50df98b1e1d2，The Bean Trees 分级：0.5319 d5358189-d70f-4e35-8add-34b83b4942b3，Pigs in Heaven d5358189-d70f-4e35-8add-34b83b4942b3，Pigs in Heaven 分级：0.5491 ff91a483-1ce5-4b37-a6fd-5ffcf21f8745，The Poisonwood Bible: A Novel 分级：0.5401 c78743bf-7947-4a0c-8db7-8a3bfe69ba70，The Stone Diaries 分级：0.5393 8d06d01d-31cd-4678-b6b1-140a67987ce9，Songs in Ordinary Time (Oprah's Book Club (Paperback)) 分级：0.5382 973f8cbd-0846-4f6b-9d28-4dd0d7dc3a19，Pigs in Heaven 分级：0.5367

</pre>


## <a name="7-model-business-rules"></a>7.模型业务规则
下面是受支持的规则类型：

* <strong>BlockList</strong> - BlockList 支持提供不想在建议结果中返回的项目列表。 
* <strong>FeatureBlockList</strong> - FeatureBlockList 支持根据功能值阻止项目。

*请勿在单个阻止列表规则中发送超过 1000 个项目，否则调用可能会超时。如果需要阻止超过 1000 个项目，可以生成多个阻止列表调用。*

* <strong>Upsale</strong> - Upsale 支持强制在建议结果中返回项目。
* <strong>WhiteList</strong> - WhiteList 支持仅提示使用项目列表中的建议。
* <strong>FeatureWhiteList</strong> - FeatureWhiteList 支持仅建议使用具有特定功能值的项目。
* <strong>PerSeedBlockList</strong> - PerSeedBlockList 支持为每个项目提供无法返回为建议结果的项目列表。

### <a name="71-get-model-rules"></a>7.1.    获取模型规则
| HTTP 方法 | URI |
|:--- |:--- |
| GET |`<rootURI>/GetModelRules?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>示例：<br>`<rootURI>/GetModelRules?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27` |

| 参数名称 | 有效值 |
|:--- |:--- |
| modelId |模型的唯一标识符 |
| apiVersion |1.0 |
|  | |
| 请求正文 |无 |

**响应**：

HTTP 状态代码：200

* `feed/entry/content/properties/Id` - 此规则的唯一标识符。
* `feed/entry/content/properties/Type` - 规则类型。
* `feed/entry/content/properties/Parameter` - 规则参数。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Get a list of rules for a model</subtitle>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-11-05T12:58:57Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'" />
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
        <title type="text">GetAListOfRulesForAModelEntity</title>
        <updated>2014-11-05T12:58:57Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Id m:type="Edm.String">1000043</d:Id>
                <d:Type m:type="Edm.String">BlockList</d:Type>
                <d:Parameters m:type="Edm.String">{"ItemsToExclude":["1000"]}</d:Parameters>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
        <title type="text">GetAListOfRulesForAModelEntity</title>
        <updated>2014-11-05T12:58:57Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Id m:type="Edm.String">1000044</d:Id>
                <d:Type m:type="Edm.String">BlockList</d:Type>
                <d:Parameters m:type="Edm.String">{"ItemsToExclude":["1001"]}</d:Parameters>
            </m:properties>
        </content>
    </entry>
    </feed>

### <a name="72-add-rule"></a>7.2.    添加规则
| HTTP 方法 | URI |
|:--- |:--- |
| POST |`<rootURI>/AddRule?apiVersion=%271.0%27` |
| 标头 |`"Content-Type", "text/xml"` |

| 参数名称 | 有效值 |
|:--- |:--- |
| apiVersion |1.0 |
|  | |
| 请求正文 | |

<ins>无论什么时候提供业务规则的项目 ID，请确保使用项目的外部 ID（在目录文件中使用的相同 ID）</ins><br>
<ins>若要添加 BlockList 规则：</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>BlockList</Type><Value>{"ItemsToExclude":["2406E770-769C-4189-89DE-1C9283F93A96","3906E110-769C-4189-89DE-1C9283F98888"]}</Value></ApiFilter>`<br><br><ins>
<ins>若要添加 FeatureBlockList 规则：</ins><br>
<br>
`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>FeatureBlockList</Type><Value>{"Name":"Movie_category","Values":["Adult","Drama"]}</Value></ApiFilter>`<br><br><ins>若要添加 Upsale 规则：</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>Upsale</Type><Value>{"ItemsToUpsale":["2406E770-769C-4189-89DE-1C9283F93A96"],"NumberOfItemsToUpsale":5}</Value></ApiFilter>`<br><br>
<ins>若要添加 WhiteList 规则：</ins><br>
`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>WhiteList</Type><Value>{"ItemsToInclude":["2406E770-769C-4189-89DE-1C9283F93A96","1116E770-769C-4189-89DE-1C9283F88888"]}</Value></ApiFilter>`<br><br><ins>
<ins>若要添加 FeatureWhiteList 规则：</ins><br>
<br>
`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>FeatureWhiteList</Type><Value>{"Name":"Movie_rating","Values":["PG13"]}</Value></ApiFilter>`<br><br><ins>若要添加 PerSeedBlockList 规则：</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>PerSeedBlockList</Type><Value>{"SeedItems":["9949"],"ItemsToExclude":["9862","8158","8244"]}</Value></ApiFilter>`|

**响应**：

HTTP 状态代码：200

API 返回新创建的规则及其详细信息。 规则属性可从以下路径检索：

* `feed/entry/content/properties/Id` - 此规则的唯一标识符。
* `feed/entry/content/properties/Type` - 规则类型：BlockList 或 Upsale。
* `feed/entry/content/properties/Parameter` - 规则参数。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/AddRule" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Add A Rule</subtitle>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-11-05T11:13:28Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'" />
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
        <title type="text">AddARuleEntity</title>
        <updated>2014-11-05T11:13:28Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Id m:type="Edm.String">1000041</d:Id>
                <d:Type m:type="Edm.String">BlockList</d:Type>
                <d:Parameters m:type="Edm.String">{"ItemsToExclude":["1002"]}</d:Parameters>
            </m:properties>
        </content>
    </entry>
    </feed>

### <a name="73-delete-rule"></a>7.3.    删除规则
| HTTP 方法 | URI |
|:--- |:--- |
| 删除 |`<rootURI>/DeleteRule?modelId=%27<model_id>%27&filterId=%27<filter_Id>%27&apiVersion=%271.0%27`<br><br>示例：<br>`DeleteRule?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&filterId=%271000011%27&apiVersion=%271.0%27` |

| 参数名称 | 有效值 |
|:--- |:--- |
| modelId |模型的唯一标识符 |
| filterId |筛选器的唯一标识符 |
| apiVersion |1.0 |
|  | |
| 请求正文 |无 |

**响应**：

HTTP 状态代码：200

### <a name="74-delete-all-rules"></a>7.4.    删除所有规则
| HTTP 方法 | URI |
|:--- |:--- |
| 删除 |`<rootURI>/DeleteAllRules?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>示例：<br>`DeleteAllRules?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&apiVersion=%271.0%27` |

| 参数名称 | 有效值 |
|:--- |:--- |
| modelId |模型的唯一标识符 |
| apiVersion |1.0 |
|  | |
| 请求正文 |无 |

**响应**：

HTTP 状态代码：200

## <a name="8-catalog"></a>8.目录
### <a name="81-import-catalog-data"></a>8.1.    导入目录数据
如果将多个目录文件上载到具有多个调用的同一模型，则只能插入新目录项目。 现有项目将保留原始值。 不能使用此方法更新目录数据。

目录数据应遵循以下格式：

* 没有特征 - `<Item Id>,<Item Name>,<Item Category>[,<Description>]`
* 有特征 - `<Item Id>,<Item Name>,<Item Category>,[<Description>],<Features list>`

注意：文件大小上限为 200MB。

**格式详细信息**

| Name | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| 项目 ID |是 |[A-z]、[a-z]、[0-9]、[_] &#40;下划线&#41;、[-] &#40;短划线&#41;<br> 最大长度：50 |项目的唯一标识符。 |
| 项目名称 |是 |任何字母数字字符<br> 最大长度：255 |项目名称。 |
| 项目类别 |是 |任何字母数字字符 <br> 最大长度：255 |此项目所属的类别（例如烹饪书籍、剧本...）；可为空。 |
| 说明 |否，除非有特征（但可为空） |任何字母数字字符 <br> 最大长度：4000 |此项目的说明。 |
| 特征列表 |否 |任何字母数字字符 <br> 最大长度：4000；最大特征数量：20 |功能名称的逗号分隔列表 = 可用于增强模型建议的功能值；请参阅[高级主题](#2-advanced-topics)部分。 |

| HTTP 方法 | URI |
|:--- |:--- |
| POST |`<rootURI>/ImportCatalogFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>示例：<br>`<rootURI>/ImportCatalogFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27` |
| 标头 |`"Content-Type", "text/xml"` |

| 参数名称 | 有效值 |
|:--- |:--- |
| modelId |模型的唯一标识符 |
| filename |目录的文本标识符。<br>仅允许使用字母（A-Z、a-z）、数字 (0-9)、连字符 (-) 和下划线 (_)。<br>最大长度：50 |
| apiVersion |1.0 |
|  | |
| 请求正文 |示例（附有功能）：<br/>2406e770-769c-4189-89de-1c9283f93a96,Clara Callan,Book,the book  description,author=Richard Wright,publisher=Harper Flamingo Canada,year=2001<br>21bf8088-b6c0-4509-870c-e1c7ac78304a,The Forgetting Room: A Fiction (Byzantium Book),Book,,author=Nick Bantock,publisher=Harpercollins,year=1997<br>3bb5cb44-d143-4bdd-a55c-443964bf4b23,Spadework,Book,,author=Timothy Findley, publisher=HarperFlamingo Canada, year=2001<br>552a1940-21e4-4399-82bb-594b46d7ed54,Restraint of Beasts,Book,the book description,author=Magnus Mills, publisher=Arcade Publishing, year=1998</pre> |

**响应**：

HTTP 状态代码：200

API 返回导入报告。

* `feed\entry\content\properties\LineCount` - 接受的行数。
* `feed\entry\content\properties\ErrorCount` - 由于错误而未插入的行数。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Import catalog file</subtitle>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T06:58:04Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'" />
    <entry>
       <id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
        <title type="text">ImportCatalogFileEntity2</title>
        <updated>2014-10-05T06:58:04Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:LineCount m:type="Edm.String">4</d:LineCount>
                <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
            </m:properties>
        </content>
    </entry>
    </feed>

### <a name="82-get-catalog"></a>8.2.    获取目录
检索所有目录项。
一次检索一页目录。 如果要获取特定索引处的项目，可使用 $skip odata 参数。 例如，如果要获取从位置 100 开始的项目，请将参数 $skip=100 添加到请求。

| HTTP 方法 | URI |
|:--- |:--- |
| GET |`<rootURI>/GetCatalog?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>示例：<br>`GetCatalog?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&apiVersion=%271.0%27` |

| 参数名称 | 有效值 |
|:--- |:--- |
| modelId |模型的唯一标识符 |
| apiVersion |1.0 |
|  | |
| 请求正文 |无 |

**响应**：

HTTP 状态代码：200

响应包括每个目录项目中的一个条目。 每个条目均具有以下数据：

* `feed/entry/content/properties/ExternalId` - 目录项目外部 ID，由客户提供。
* `feed/entry/content/properties/InternalId` - 目录项目内部 ID，由 Azure 机器学习建议生成。
* `feed/entry/content/properties/Name` - 目录项目名称。
* `feed/entry/content/properties/Category` - 目录项目类别。
* `feed/entry/content/properties/Description` - 目录项目描述。
* `feed/entry/content/properties/Metadata` - 目录项目元数据。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get All Catalog Items</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'</id>
        <rights type="text" />
        <updated>2014-10-29T11:13:26Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'" />
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
        <title type="text">AllCatalogItemsEntity</title>
        <updated>2014-10-29T11:13:26Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:ExternalId m:type="Edm.String">552A1940-21E4-4399-82BB-594B46D7ED54</d:ExternalId>
                <d:InternalId m:type="Edm.String">060db26a-e6a6-464e-bb52-436d2da82a50</d:InternalId>
                <d:Name m:type="Edm.String">Restraint of Beasts</d:Name>
                <d:Category m:type="Edm.String">Book</d:Category>
                <d:Description m:type="Edm.String"></d:Description>
                <d:Metadata m:type="Edm.String"></d:Metadata>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
        <title type="text">AllCatalogItemsEntity</title>
        <updated>2014-10-29T11:13:26Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:ExternalId m:type="Edm.String">2406E770-769C-4189-89DE-1C9283F93A96</d:ExternalId>
                <d:InternalId m:type="Edm.String">209d7bfe-2eb9-4455-92a3-7c867a41a74a</d:InternalId>
                <d:Name m:type="Edm.String">Clara Callan</d:Name>
                <d:Category m:type="Edm.String">Book</d:Category>
                <d:Description m:type="Edm.String"></d:Description>
                <d:Metadata m:type="Edm.String"></d:Metadata>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
        <title type="text">AllCatalogItemsEntity</title>
        <updated>2014-10-29T11:13:26Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:ExternalId m:type="Edm.String">3BB5CB44-D143-4BDD-A55C-443964BF4B23</d:ExternalId>
                <d:InternalId m:type="Edm.String">913ff79b-059b-4d4d-8042-6fa4cc1391dd</d:InternalId>
                <d:Name m:type="Edm.String">Spadework</d:Name>
                <d:Category m:type="Edm.String">Book</d:Category>
                <d:Description m:type="Edm.String"></d:Description>
                <d:Metadata m:type="Edm.String"></d:Metadata>
            </m:properties>
        </content>
    </entry>
    <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
        <title type="text">AllCatalogItemsEntity</title>
        <updated>2014-10-29T11:13:26Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:ExternalId m:type="Edm.String">21BF8088-B6C0-4509-870C-E1C7AC78304A</d:ExternalId>
                <d:InternalId m:type="Edm.String">ea65e4fa-768c-40b4-92c3-69d3e8178691</d:InternalId>
                <d:Name m:type="Edm.String">The Forgetting Room: A Fiction (Byzantium Book)</d:Name>
                <d:Category m:type="Edm.String">Book</d:Category>
                <d:Description m:type="Edm.String"></d:Description>
                <d:Metadata m:type="Edm.String"></d:Metadata>
            </m:properties>
        </content>
    </entry>
    </feed>

### <a name="83-get-catalog-items-by-token"></a>8.3.    根据令牌获取目录项目
| HTTP 方法 | URI |
|:--- |:--- |
| GET |`<rootURI>/GetCatalogItemsByToken?modelId=%27<modelId>%27&token=%27<token>%27&apiVersion=%271.0%27`<br><br>示例：<br>`GetCatalogItemsByToken?modelId=%270dbb55fa-7f11-418d-8537-8ff2d9d1d9c6%27&token=%27Cla%27&apiVersion=%271.0%27` |

| 参数名称 | 有效值 |
|:--- |:--- |
| modelId |模型的唯一标识符 |
| 令牌 |目录项目名称的令牌。 应包含至少 3 个字符。 |
| apiVersion |1.0 |
|  | |
| 请求正文 |无 |

**响应**：

HTTP 状态代码：200

响应包括每个目录项目中的一个条目。 每个条目均具有以下数据：

* `feed/entry/content/properties/InternalId` - 目录项目内部 ID，由 Azure 机器学习建议生成。
* `feed/entry/content/properties/Name` - 目录项目名称。
* `feed/entry/content/properties/Rating` -（供将来使用）
* `feed/entry/content/properties/Reasoning` -（供将来使用）
* `feed/entry/content/properties/Metadata` -（供将来使用）
* `feed/entry/content/properties/FormattedRating` -（供将来使用）

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get Catalog items that contain a token</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'</id>
        <rights type="text" />
        <updated>2014-10-29T11:48:19Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'" />
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
            <title type="text">CatalogItemsThatContainATokenEntity</title>
            <updated>2014-10-29T11:48:19Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
            <content type="application/xml">
                  <m:properties>
                    <d:Id m:type="Edm.String">2406E770-769C-4189-89DE-1C9283F93A96</d:Id>
                    <d:Name m:type="Edm.String">Clara Callan</d:Name>
                    <d:Rating m:type="Edm.Double">0</d:Rating>
                    <d:Reasoning m:type="Edm.String"></d:Reasoning>
                    <d:Metadata m:type="Edm.String"></d:Metadata>
                    <d:FormattedRating m:type="Edm.Double" m:null="true"></d:FormattedRating>
                  </m:properties>
            </content>
        </entry>
    </feed>

## <a name="9-usage-data"></a>9.使用情况数据
### <a name="91-import-usage-data"></a>9.1.    导入使用情况数据
#### <a name="911-uploading-file"></a>9.1.1. 上传文件
本部分介绍如何使用文件上传使用情况数据。 通过使用情况数据可调用此 API 几次。 所有调用的全部使用情况数据都将保存。

| HTTP 方法 | URI |
|:--- |:--- |
| POST |`<rootURI>/ImportUsageFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>示例：<br>`<rootURI>/ImportUsageFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27ImplicitMatrix10_Guid_small.txt%27&apiVersion=%271.0%27` |

| 参数名称 | 有效值 |
|:--- |:--- |
| modelId |模型的唯一标识符 |
| filename |目录的文本标识符。<br>仅允许使用字母（A-Z、a-z）、数字 (0-9)、连字符 (-) 和下划线 (_)。<br>最大长度：50 |
| apiVersion |1.0 |
|  | |
| 请求正文 |使用情况数据。 格式：<br>`<User Id>,<Item Id>[,<Time>,<Event>]`<br><br><table><tr><th>Name</th><th>必需</th><th>类型</th><th>说明</th></tr><tr><td>用户 ID</td><td>是</td><td>[A-z]、[a-z]、[0-9]、[_] &#40;下划线&#41;、[-] &#40;短划线&#41;<br> 最大长度：255 </td><td>用户的唯一标识符。</td></tr><tr><td>项目 ID</td><td>是</td><td>[A-z]、[a-z]、[0-9]、[&#95;]、[_] &#40;下划线&#41;、[-] &#40;短划线&#41;<br> 最大长度：50</td><td>项目的唯一标识符。</td></tr><tr><td>时间</td><td>否</td><td>日期格式：YYYY/MM/DDTHH:MM:SS（例如 2013/06/20T10:00:00）</td><td>数据的时间。</td></tr><tr><td>事件</td><td>无；如果提供，还必须加上日期</td><td>下列类型作之一：<br>• Click<br>• RecommendationClick<br>•    AddShopCart<br>• RemoveShopCart<br>• Purchase</td><td></td></tr></table><br>文件大小上限：200MB<br><br>示例：<br><pre>149452,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>6360,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>50321,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>71285,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>224450,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>236645,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>107951,1b3d95e2-84e4-414c-bb38-be9cf461c347</pre> |

**响应**：

HTTP 状态代码：200

* `Feed\entry\content\properties\LineCount` - 接受的行数。
* `Feed\entry\content\properties\ErrorCount` - 由于错误而未插入的行数。
* `Feed\entry\content\properties\FileId` - 文件标识符。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
      <subtitle type="text">Add bulk usage data (usage file)</subtitle>
      <id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'</id>
      <rights type="text" />
      <updated>2014-10-05T07:21:44Z</updated>
      <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'" />
      <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">AddBulkUsageDataUsageFileEntity2</title>
    <updated>2014-10-05T07:21:44Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">33</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
        <d:FileId m:type="Edm.String">fead7c1c-db01-46c0-872f-65bcda36025d</d:FileId>
      </m:properties>
    </content>
      </entry>
    </feed>


#### <a name="912-using-data-acquisition"></a>9.1.2. 使用数据获取
本部分介绍如何将事件实时发送到 Azure 机器学习建议（通常从网站发送）。

| HTTP 方法 | URI |
|:--- |:--- |
| POST |`<rootURI>/AddUsageEvent?apiVersion=%271.0%27` |
| 标头 |`"Content-Type", "text/xml"` |

| 参数名称 | 有效值 |
|:--- |:--- |
| apiVersion |1.0 |
| 请求正文 |要发送的每个事件的事件数据条目。 应在 SessionId 字段中为相同用户或浏览器会话发送相同 ID。 （请参见以下事件正文示例。） |

* 事件“Click”的示例：
  
        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
        <Name>Click</Name>
        <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        </EventData>
        </EventData>
        </Event>
* 事件“RecommendationClick”的示例：
  
        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
          <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
          <SessionId>11112222</SessionId>
          <EventData>
        <EventData>
          <Name>RecommendationClick</Name>
          <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        </EventData>
          </EventData>
        </Event>
* 事件“AddShopCart”的示例：
  
        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
          <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
          <SessionId>11112222</SessionId>
          <EventData>
        <EventData>
          <Name>AddShopCart</Name>
          <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        </EventData>
          </EventData>
        </Event>
* 事件“RemoveShopCart”的示例：
  
        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
          <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
          <SessionId>11112222</SessionId>
          <EventData>
              <EventData>
                      <Name>RemoveShopCart</Name>
                      <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
                </EventData>
          </EventData>
        </Event>
* 事件“Purchase”的示例：
  
        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
            <EventData>
                <Name>Purchase</Name>
                <PurchaseItems>
                    <PurchaseItem>
                        <ItemId>ABBF8081-C5C0-4F09-9701-E1C7AC78304A</ItemId>
                        <Count>1</Count>
                    </PurchaseItem>
                    <PurchaseItem>
                        <ItemId>21BF8088-B6C0-4509-870C-11C0AC7F304B</ItemId>
                        <Count>3</Count>
                    </PurchaseItem>
                </PurchaseItems>
            </EventData>
        </EventData>
        </Event>
* 发送“Click”和“AddShopCart”2 个事件的示例：
  
        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
          <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
          <SessionId>11112222</SessionId>
          <EventData>
        <EventData>
          <Name>Click</Name>
          <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
          <ItemName>itemName</ItemName>
          <ItemDescription>item description</ItemDescription>
          <ItemCategory>category</ItemCategory>
        </EventData>
        <EventData>
          <Name>AddShopCart</Name>
          <ItemId>552A1940-21E4-4399-82BB-594B46D7ED54</ItemId>
        </EventData>
          </EventData>
        </Event>

**响应**：HTTP 状态代码：200

### <a name="92-list-model-usage-files"></a>9.2.    列出模型使用情况文件
检索所有模型使用情况文件的元数据。
一次检索一页使用情况文件。 每页包含 100 个项目。 如果要获取特定索引处的项目，可使用 $skip odata 参数。 例如，如果要获取从位置 100 开始的项目，请将参数 $skip=100 添加到请求。

| HTTP 方法 | URI |
|:--- |:--- |
| GET |`<rootURI>/ListModelUsageFiles?forModelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>示例：<br>`<rootURI>/ListModelUsageFiles?forModelId=%270dbb55fa-7f11-418d-8537-8ff2d9d1d9c6%27&apiVersion=%271.0%27` |

| 参数名称 | 有效值 |
|:--- |:--- |
| forModelId |模型的唯一标识符 |
| apiVersion |1.0 |
|  | |
| 请求正文 |无 |

**响应**：

HTTP 状态代码：200

响应包括每个使用情况文件中的一个条目。 每个条目均具有以下数据：

* `feed\entry\content\properties\Id` - 使用情况文件 ID。
* `feed\entry\content\properties\Length` - 使用情况文件长度（以 MB 为单位）。
* `feed\entry\content\properties\DateModified` - 创建使用情况文件的日期。
* `feed\entry\content\properties\UseInModel` - 使用情况文件是否已在模型中使用。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get a list of model's usage files info</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'</id>
        <rights type="text" />
        <updated>2014-10-30T09:40:25Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'" />
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
            <title type="text">GetAListOfModelsUsageFilesInfoEntity</title>
            <updated>2014-10-30T09:40:25Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Id m:type="Edm.String">4c067b42-e975-4cb2-8c98-a6ab80ed6d63</d:Id>
                    <d:Length m:type="Edm.Double">0</d:Length>
                    <d:DateModified m:type="Edm.String">10/30/2014 9:19:57 AM</d:DateModified>
                    <d:UseInModel m:type="Edm.String">true</d:UseInModel>
                </m:properties>
            </content>
        </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
        <title type="text">GetAListOfModelsUsageFilesInfoEntity</title>
        <updated>2014-10-30T09:40:25Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Id m:type="Edm.String">3126d816-4e80-4248-8339-1ebbdb9d544d</d:Id>
                <d:Length m:type="Edm.Double">0.001</d:Length>
                <d:DateModified m:type="Edm.String">10/30/2014 9:21:44 AM</d:DateModified>
                <d:UseInModel m:type="Edm.String">true</d:UseInModel>
              </m:properties>
        </content>
    </entry>
</feed>

### <a name="93-get-usage-statistics"></a>9.3.    获取使用情况统计
获取使用情况统计。

| HTTP 方法 | URI |
|:--- |:--- |
| GET |`<rootURI>/GetUsageStatistics?modelId=%27<modelId>%27& startDate=%27<date>%27&endDate=%27<date>%27&eventTypes=%27<types>%27&apiVersion=%271.0%27`<br><br>示例：<br>`<rootURI>/GetUsageStatistics?modelId=%271d20c34f-dca1-4eac-8e5d-f299e4e4ad66%27&startDate=%272014%2F10%2F17T00%3A00%3A00%27&endDate=%272014%2F11%2F16T00%3A00%3A00%27&eventTypes=%271%2C2%2C3%2C4%2C5%27&apiVersion=%271.0%27` |

| 参数名称 | 有效值 |
|:--- |:--- |
| modelId |模型的唯一标识符 |
| startDate |开始日期。 格式：yyyy/MM/ddTHH:mm:ss |
| endDate |结束日期。 格式：yyyy/MM/ddTHH:mm:ss |
| eventTypes |事件类型逗号分隔的字符串，或是 null 以获取所有事件 |
| apiVersion |1.0 |
|  | |
| 请求正文 |无 |

**响应**：

HTTP 状态代码：200

密钥/值元素集合。 每个集合包含按小时分组的特定事件类型的事件总数。

* `feed\entry[i]\content\properties\Key` - 包含时间（按小时分组）和事件类型。
* `feed\entry[i]\content\properties\Value` - 事件总数。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get usage statistics</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-11-18T11:39:16Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'" />
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
        <title type="text">GetUsageStatisticsEntity</title>
        <updated>2014-11-18T11:39:16Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Event m:type="Edm.String">11/9/2014 8:00:00 AM;Click</d:Event>
                <d:Value m:type="Edm.String">5</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
        <title type="text">GetUsageStatisticsEntity</title>
        <updated>2014-11-18T11:39:16Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Event m:type="Edm.String">11/9/2014 8:00:00 AM;RecommendationClick</d:Event>
                <d:Value m:type="Edm.String">10</d:Value>
              </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
        <title type="text">GetUsageStatisticsEntity</title>
        <updated>2014-11-18T11:39:16Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Event m:type="Edm.String">11/1/2014 8:00:00 AM;RemoveShopCart</d:Event>
                <d:Value m:type="Edm.String">10</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
        <title type="text">GetUsageStatisticsEntity</title>
        <updated>2014-11-18T11:39:16Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Event m:type="Edm.String">11/5/2014 8:00:00 AM;RemoveShopCart</d:Event>
                <d:Value m:type="Edm.String">10</d:Value>
            </m:properties>
        </content>
    </entry>
    </feed>

### <a name="94-get-usage-file-sample"></a>9.4.    获取使用情况文件示例
检索前 2KB 的使用情况文件内容。

| HTTP 方法 | URI |
|:--- |:--- |
| GET |`<rootURI>/GetUsageFileSample?modelId=%27<modelId>%27& fileId=%27<fileId>%27&apiVersion=%271.0%27`<br><br>示例：<br>`<rootURI>/GetUsageFileSample?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&fileId=%274c067b42-e975-4cb2-8c98-a6ab80ed6d63%27&apiVersion=%271.0%27` |

| 参数名称 | 有效值 |
|:--- |:--- |
| modelId |模型的唯一标识符 |
| fileId |模型使用情况文件的唯一标识符 |
| apiVersion |1.0 |
|  | |
| 请求正文 |无 |

**响应**：

HTTP 状态代码：200

响应按原始文本格式返回：

<pre>
85526,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 210926,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 116866,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 177458,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 274004,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 123883,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 37712,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 152249,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 250948,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 235588,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 158254,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 271195,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 141157,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 171118,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 225087,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1
</pre>


### <a name="95-get-model-usage-file"></a>9.5.    获取模型使用情况文件
检索使用情况文件的全部内容。

| HTTP 方法 | URI |
|:--- |:--- |
| GET |`<rootURI>/GetModelUsageFile?mid=%27<modelId>%27& fid=%27<fileId>%27&download=%27<download_value>%27&apiVersion=%271.0%27`<br><br>示例：<br>`<rootURI>/GetModelUsageFile?mid=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&fid=%273126d816-4e80-4248-8339-1ebbdb9d544d%27&download=%271%27&apiVersion=%271.0%27` |

| 参数名称 | 有效值 |
|:--- |:--- |
| mid |模型的唯一标识符 |
| fid |模型使用情况文件的唯一标识符 |
| 下载 |1 |
| apiVersion |1.0 |
|  | |
| 请求正文 |无 |

**响应**：

HTTP 状态代码：200

响应按原始文本格式返回：

<pre>
85526,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 210926,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 116866,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 177458,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 274004,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 123883,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 37712,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 152249,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 250948,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 235588,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 158254,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 271195,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 141157,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 171118,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 225087,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 244881,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 50547,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 213090,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 260655,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 72214,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 189334,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 36326,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 189336,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 189334,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 260655,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 162100,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 54946,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 260965,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 102758,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 112602,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 163925,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 262998,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 144717,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1
</pre>

### <a name="96-delete-usage-file"></a>9.6.    删除使用情况文件
删除指定的模型使用情况文件。

| HTTP 方法 | URI |
|:--- |:--- |
| 删除 |`<rootURI>/DeleteUsageFile?modelId=%27<modelId>%27&fileId=%27<fileId>%27&apiVersion=%271.0%27`<br><br>示例：<br>`<rootURI>/DeleteUsageFile?modelId=%270f86d698-d0f4-4406-a684-d13d22c47a73%27&fileId=%27f2e0b09d-be5c-46b2-9ac2-c7f622e5e1a5%27&apiVersion=%271.0%27` |

| 参数名称 | 有效值 |
|:--- |:--- |
| modelId |模型的唯一标识符 |
| fileId |要删除的文件的唯一标识符 |
| apiVersion |1.0 |
|  | |
| 请求正文 |无 |

**响应**：

HTTP 状态代码：200

### <a name="97-delete-all-usage-files"></a>9.7.    删除所有使用情况文件
删除所有模型使用情况文件。

| HTTP 方法 | URI |
|:--- |:--- |
| 删除 |`<rootURI>/DeleteAllUsageFiles?modelId=%27<modelId>%27&apiVersion=%271.0%27`<br><br>示例：<br>`<rootURI>/DeleteAllUsageFiles?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&apiVersion=%271.0%27` |

| 参数名称 | 有效值 |
|:--- |:--- |
| modelId |模型的唯一标识符 |
| apiVersion |1.0 |
|  | |
| 请求正文 |无 |

**响应**：

HTTP 状态代码：200

## <a name="10-features"></a>10.功能
本部分介绍如何检索功能信息，例如导入的功能及其值、排名和分配排名的时间。 功能导入为目录数据的一部分，并且在排名生成后关联排名。
特征排名可根据使用情况数据和项目类型而更改。 但是，对于一致的使用情况/项目，排名只能有小幅的波动。
特征的排名是一个非负数字。 数字 0 表示功能未排名（如果在第一个排名生成完成之前调用此 API，则会发生此情况）。 决定排名的日期称为分数有效时间。

### <a name="101-get-features-info-for-last-rank-build"></a>10.1. 获取功能信息（对于最后一个排名生成）
为最后一个成功的排名生成检索功能信息，包括排名。

| HTTP 方法 | URI |
|:--- |:--- |
| GET |`<rootURI>/GetModelFeatures?modelId=%27<modelId>%27&samplingSize=%27<samplingSize>%27&apiVersion=%271.0%27`<br><br>示例：<br>`<rootURI>/GetModelFeatures?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&samplingSize=10%27&apiVersion=%271.0%27` |

| 参数名称 | 有效值 |
|:--- |:--- |
| modelId |模型的唯一标识符 |
| samplingSize |根据在目录中显示的数据，每项功能包含的值数。 <br/>可能的值包括：<br> -1 - 所有示例。 <br>0 - 没有示例。 <br>N - 为每个功能名称返回 N 个示例。 |
| apiVersion |1.0 |
|  | |
| 请求正文 |无 |

**响应**：

HTTP 状态代码：200

响应包含功能信息条目列表。 每个条目包含：

* `feed/entry/content/m:properties/d:Name` - 功能名称。
* `feed/entry/content/m:properties/d:RankUpdateDate` - 为此功能（也称为 分数新鲜度功能）分配排名的日期。 历史日期（“0001-01-01T00:00:00”）表示没有执行任何排名生成。
* `feed/entry/content/m:properties/d:Rank` - 功能排名 (float)。 2.0 和以上的排名被认为是正常功能。
* `feed/entry/content/m:properties/d:SampleValues` - 要求最高到示例大小的值的逗号分隔列表。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Get the features of a model</subtitle>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2015-01-08T13:15:02Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'" />
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
        <title type="text">ModelFeaturesEntity</title>
        <updated>2015-01-08T13:15:02Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Name m:type="Edm.String">Author</d:Name>
                <d:RankUpdateDate m:type="Edm.String">0001-01-01T00:00:00</d:RankUpdateDate>
                <d:Rank m:type="Edm.String">0</d:Rank>
                <d:SampleValues m:type="Edm.String">A. A. Attanasio, A. A. Milne, A. Bates, A. C. Bhaktivedanta Swami Prabhupada et al., A. C. Crispin, A. C. Doyle, A. C. H. Smith, A. E. Parker, A. J. Holt, A. J. Matthews</d:SampleValues>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
        <title type="text">ModelFeaturesEntity</title>
        <updated>2015-01-08T13:15:02Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Name m:type="Edm.String">Publisher</d:Name>
                <d:RankUpdateDate m:type="Edm.String">0001-01-01T00:00:00</d:RankUpdateDate>
                <d:Rank m:type="Edm.String">0</d:Rank>
                <d:SampleValues m:type="Edm.String">A. Mondadori, Abacus, Abacus Press, Abacus Uk, Abstract Studio, Acacia Press, Academy Chicago Publishers, Ace Books, ACE Charter, Actar</d:SampleValues>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
        <title type="text">ModelFeaturesEntity</title>
        <updated>2015-01-08T13:15:02Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1"/>
        <contenttype="application/xml">
        <m:properties>
            <d:Name m:type="Edm.String">Year</d:Name>
            <d:RankUpdateDate m:type="Edm.String">0001-01-01T00:00:00</d:RankUpdateDate>
            <d:Rank m:type="Edm.String">0</d:Rank>
            <d:SampleValues m:type="Edm.String">0, 1920, 1926, 1927, 1929, 1930, 1932, 1942, 1943, 1946</d:SampleValues>
        </m:properties>
        </content>
    </entry>
</feed>

### <a name="102-get-features-info-for-specific-rank-build"></a>10.2. 获取功能信息（对于特定排名生成）
为特定排名生成检索功能信息，包括排名。

| HTTP 方法 | URI |
|:--- |:--- |
| GET |`<rootURI>/GetModelFeatures?modelId=%27<modelId>%27&samplingSize=%27<samplingSize>%27&rankBuildId=<rankBuildId>&apiVersion=%271.0%27`<br><br>示例：<br>`<rootURI>/GetModelFeatures?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&samplingSize=10%27&rankBuildId=1000551&apiVersion=%271.0%27` |

| 参数名称 | 有效值 |
|:--- |:--- |
| modelId |模型的唯一标识符 |
| samplingSize |根据在目录中显示的数据，每项功能包含的值数。<br/> 可能的值包括：<br> -1 - 所有示例。 <br>0 - 没有示例。 <br>N - 为每个功能名称返回 N 个示例。 |
| rankBuildId |排名生成的唯一标识符，或是 -1，表示最后一个排名生成 |
| apiVersion |1.0 |
|  | |
| 请求正文 |无 |

**响应**：

HTTP 状态代码：200

响应包含功能信息条目列表。 每个条目包含：

* `feed/entry/content/m:properties/d:Name` - 功能名称。
* `feed/entry/content/m:properties/d:RankUpdateDate` - 为此功能（也称为 分数新鲜度功能）分配排名的日期。 历史日期（“0001-01-01T00:00:00”）表示没有执行任何排名生成。
* `feed/entry/content/m:properties/d:Rank` - 功能排名 (float)。 2.0 和以上的排名被认为是正常功能。
* `feed/entry/content/m:properties/d:SampleValues` - 要求最高到示例大小的值的逗号分隔列表。

OData

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get the features of a model</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'</id>
        <rights type="text" />
        <updated>2015-01-08T13:54:22Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'" />
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
            <title type="text">ModelFeaturesEntity</title>
            <updated>2015-01-08T13:54:22Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Name m:type="Edm.String">Author</d:Name>
                    <d:RankUpdateDate m:type="Edm.String">2015-01-08T13:52:14.673</d:RankUpdateDate>
                    <d:Rank m:type="Edm.String">3.38867426</d:Rank>
                    <d:SampleValues m:type="Edm.String">A. A. Attanasio, A. A. Milne, A. Bates, A. C. Bhaktivedanta Swami Prabhupada et al., A. C. Crispin, A. C. Doyle, A. C. H. Smith, A. E. Parker, A. J. Holt, A. J. Matthews</d:SampleValues>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
            <title type="text">ModelFeaturesEntity</title>
            <updated>2015-01-08T13:54:22Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Name m:type="Edm.String">Publisher</d:Name>
                    <d:RankUpdateDate m:type="Edm.String">2015-01-08T13:52:14.673</d:RankUpdateDate>
                    <d:Rank m:type="Edm.String">1.67839336</d:Rank>
                    <d:SampleValues m:type="Edm.String">A. Mondadori, Abacus, Abacus Press, Abacus Uk, Abstract Studio, Acacia Press, Academy Chicago Publishers, Ace Books, ACE Charter, Actar</d:SampleValues>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
            <title type="text">ModelFeaturesEntity</title>
            <updated>2015-01-08T13:54:22Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Name m:type="Edm.String">Year</d:Name>
                    <d:RankUpdateDate m:type="Edm.String">2015-01-08T13:52:14.673</d:RankUpdateDate>
                    <d:Rank m:type="Edm.String">1.12352145</d:Rank>
                    <d:SampleValues m:type="Edm.String">0, 1920, 1926, 1927, 1929, 1930, 1932, 1942, 1943, 1946</d:SampleValues>
                </m:properties>
            </content>
        </entry>
    </feed>


## <a name="11-build"></a>11.构建
  本部分介绍与生成相关的不同 API。 有 3 种类型的生成：建议生成、排名生成和 FBT（经常一起购买）生成。

建议生成的目的在于生成用于预测的建议模型。 预测（对于此种类型的生成）分为两种类型：

* I2I， 也称为 项目到项目建议，若提供一个项目或项目列表，此选项将预测可能受到高度关注的项目列表。
* U2I，也称为 用户到项目建议，若提供用户 ID（也可以选择性地提供项目列表），此选项将预测可能受到指定用户高度关注的项目列表（及其其他项目选项）。 U2I 建议基于用户到模型生成时关注的项目的历史记录。

排名生成是技术生成，可让用户了解功能的效用。 通常为了获取涉及功能的建议模型的最佳结果，应采取以下步骤：

* 触发排名生成（除非功能得分稳定），然后等到获取功能得分。
* 调用 [Get Features Info](#101-get-features-info-for-last-rank-build) API，检索功能排名。
* 使用以下参数配置建议生成：
  * `useFeatureInModel` - 设为 True。
  * `ModelingFeatureList` - 将逗号分隔的功能列表设为 2.0 或更高得分（根据在上一步中检索的排名而定）。
  * `AllowColdItemPlacement` - 设为 True。
  * 可选择将 `EnableFeatureCorrelation` 设为 True，将 `ReasoningFeatureList` 设为要用于介绍的功能的列表（通常是在建模中使用的相同功能列表或子列表）。
* 使用配置的参数触发建议生成。

注意：如果没有配置任何参数（例如不使用参数调用建议生成）或者没有显式禁用功能（例如 `UseFeatureInModel` 设为 False），则如果存在排名生成，系统将把与功能相关的参数设置为上面介绍的值。

没有限制不能为相同模型同时运行排名生成和建议生成。 但是无法在相同模型上平行运行相同类型的两个生成。

FBT（经常一起购买）生成是有时称为“保守”推荐器的其他建议算法，适用于本质上不同类的目录（同类：书籍、电影、一些食物、时尚；非同类：计算机和设备、跨域，多种多样）。

注意：如果上传的使用情况文件包含选填字段“事件类型”，则 FBT 建模将仅使用“Purchase”事件。 如果未提供事件类型，则所有事件都将视为购买事件。

#### <a name="111-build-parameters"></a>11.1 生成参数
每种生成类型均可通过参数集配置（如下所述）。 如果没有配置参数，系统将自动根据触发生成时显示的信息将参数值设为属性。

##### <a name="1111-usage-condenser"></a>11.1.1. 使用情况冷凝器
用户或带有少数使用点的项目可能比信息包含更多噪音。 系统尝试预测每个用户/项目在模型中使用的最少使用点。 此数字将在 ItemCutoffLowerBound 和 ItemCutoffUpperBound 参数为项目定义的范围中，也会在 UserCutOffLowerBound 和 UserCutoffUpperBound 参数为用户定义的范围中。 将至少一个相应边界设为零可最大程度降低冷凝器对项目或用户的影响。

##### <a name="1112-rank-build-parameters"></a>11.1.2. 排名生成参数
下表介绍了排名生成的生成参数。

| 键 | 说明 | 类型 | 有效值 |
|:--- |:--- |:--- |:--- |
| NumberOfModelIterations |总体计算时间和模型精确度反映模型执行迭代的次数。 数字越高，得到的精确度就越高，但需要较长的计算时间。 |Integer |10-50 |
| NumberOfModelDimensions |维度的数目与“特征”的数目相关，模型尝试查找数据中的数目。 增加维度的数目将允许结果进一步微调成较小的群集。 但是，太多维度将使模型无法查找项目之间的相关性。 |Integer |10-40 |
| ItemCutOffLowerBound |定义冷凝器的项目下限。 请参阅上述使用情况冷凝器。 |Integer |2 或更高（0 禁用冷凝器） |
| ItemCutOffUpperBound |定义冷凝器的项目上限。 请参阅上述使用情况冷凝器。 |Integer |2 或更高（0 禁用冷凝器） |
| UserCutOffLowerBound |定义冷凝器的用户下限。 请参阅上述使用情况冷凝器。 |Integer |2 或更高（0 禁用冷凝器） |
| UserCutOffUpperBound |定义冷凝器的用户上限。 请参阅上述使用情况冷凝器。 |Integer |2 或更高（0 禁用冷凝器） |

##### <a name="1113-recommendation-build-parameters"></a>11.1.3. 建议生成参数
下表介绍了建议生成的生成参数。

| 键 | 说明 | 类型 | 有效值 |
|:--- |:--- |:--- |:--- |
| NumberOfModelIterations |总体计算时间和模型精确度反映模型执行迭代的次数。 数字越高，得到的精确度就越高，但需要较长的计算时间。 |Integer |10-50 |
| NumberOfModelDimensions |维度的数目与“特征”的数目相关，模型尝试查找数据中的数目。 增加维度的数目将允许结果进一步微调成较小的群集。 但是，太多维度将使模型无法查找项目之间的相关性。 |Integer |10-40 |
| ItemCutOffLowerBound |定义冷凝器的项目下限。 请参阅上述使用情况冷凝器。 |Integer |2 或更高（0 禁用冷凝器） |
| ItemCutOffUpperBound |定义冷凝器的项目上限。 请参阅上述使用情况冷凝器。 |Integer |2 或更高（0 禁用冷凝器） |
| UserCutOffLowerBound |定义冷凝器的用户下限。 请参阅上述使用情况冷凝器。 |Integer |2 或更高（0 禁用冷凝器） |
| UserCutOffUpperBound |定义冷凝器的用户上限。 请参阅上述使用情况冷凝器。 |Integer |2 或更高（0 禁用冷凝器） |
| 说明 |生成描述。 |String |任何文本，最多 512 个字符。 |
| EnableModelingInsights |支持计算建议模型上的度量值。 |布尔 |True/False |
| UseFeaturesInModel |指示是否可以使用特征来增强建议模型。 |布尔 |True/False |
| ModelingFeatureList |要在建议版本中使用的特征名称逗号分隔列表，用于增强建议。 |String |功能名称，最多 512 个字符。 |
| AllowColdItemPlacement |指示建议是否也应该通过特征相似度推送冷项。 |布尔 |True/False |
| EnableFeatureCorrelation |指示特征是否可用于推论。 |布尔 |True/False |
| ReasoningFeatureList |用于推论句子（例如建议说明）的特征名称逗号分隔列表。 |String |功能名称，最多 512 个字符。 |
| EnableU2I |支持个性化建议，又称为 U2I（用户对项目的建议）。 |布尔 |True/False（默认值） |

##### <a name="1114-fbt-build-parameters"></a>11.1.4. FBT 版本参数
下表介绍了建议生成的生成参数。

| 键 | 说明 | 类型 | 有效值（默认） |
|:--- |:--- |:--- |:--- |
| FbtSupportThreshold |模型的保守程度。 建模时要考虑项目的共同出现次数。 |Integer |3-50 (6) |
| FbtMaxItemSetSize |频繁集中的项目数边界。 |Integer |2-3 (2) |
| FbtMinimalScore |频繁集应该具有的最低分数，以包含在返回的结果中。 越高越好。 |Double |0 及以上 (0) |
| FbtSimilarityFunction |定义版本使用的相似度函数。 Lift 有利于机缘巧合、Co-occurrence 有助于可预测性，而 Jaccard 可在两者间获取一个很好的平衡。 |String |cooccurrence, lift, jaccard (lift) |

### <a name="112-trigger-a-recommendation-build"></a>11.2. 触发建议生成
  默认情况下，此 API 将触发建议模型生成。 若要触发排名生成（为了使功能得分），应使用带有生成类型参数的生成 API 变体。

| HTTP 方法 | URI |
|:--- |:--- |
| POST |`<rootURI>/BuildModel?modelId=%27<modelId>%27&userDescription=%27<description>%27&apiVersion=%271.0%27`<br><br>示例：<br>`<rootURI>/BuildModel?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&userDescription=%27First%20build%27&apiVersion=%271.0%27` |
| 标头 |`"Content-Type", "text/xml"`（如果发送请求正文） |

| 参数名称 | 有效值 |
|:--- |:--- |
| modelId |模型的唯一标识符 |
| userDescription |目录的文本标识符。 请注意，如果使用空格，必须使用 %20 对其进行编码。 请参看上述示例。<br>最大长度：50 |
| apiVersion |1.0 |
|  | |
| 请求正文 |如果留空，则通过默认生成参数执行生成。<br><br>如果要设置生成参数，请将参数作为 XML 发送至正文，如以下示例所示。 （有关参数介绍，请参阅“生成参数”部分。）`<NumberOfModelIterations>40</NumberOfModelIterations><NumberOfModelDimensions>20</NumberOfModelDimensions><MinItemAppearance>5</MinItemAppearance><MinUserAppearance>5</MinUserAppearance><EnableModelingInsights>true</EnableModelingInsights><UseFeaturesInModel>false</UseFeaturesInModel><ModelingFeatureList>feature_name_1,feature_name_2,...</ModelingFeatureList><AllowColdItemPlacement>false</AllowColdItemPlacement><EnableFeatureCorrelation>false</EnableFeatureCorrelation><ReasoningFeatureList>feature_name_a,feature_name_b,...</ReasoningFeatureList></BuildParametersList>` |

**响应**：

HTTP 状态代码：200

这是异步 API。 作为回复，你将获取生成 ID。 若要了解生成结束时间，应调用“Get Builds Status of a Model”API，并在响应中找到此生成 ID。 请注意，生成可花费几分钟到几小时，具体取决于数据大小。

在生成结束前无法使用建议。

有效生成状态：

* 创建 - 生成请求已创建。
* 已排队 - 生成请求已发送并已排队。
* 正在生成 - 生成正在进行中。
* 成功 - 生成成功结束。
* 错误 - 生成因失败而结束。
* 已取消 - 生成已取消。
* 正在取消 - 取消生成的请求已发送。

请注意，生成 ID 可在以下路径找到：`Feed\entry\content\properties\Id`

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
      <subtitle type="text">Build a Model with RequestBody</subtitle>
      <id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'</id>
      <rights type="text" />
      <updated>2014-10-05T08:56:34Z</updated>
      <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'" />
      <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">1000272</d:Id>
        <d:UserName m:type="Edm.String"></d:UserName>
        <d:ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:ModelId>
        <d:ModelName m:type="Edm.String">docTest</d:ModelName>
        <d:Type m:type="Edm.String">Recommendation</d:Type>
        <d:CreationTime m:type="Edm.String">2014-10-05T08:56:31.893</d:CreationTime>
        <d:Progress_BuildId m:type="Edm.String">1000272</d:Progress_BuildId>
        <d:Progress_ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:Progress_ModelId>
        <d:Progress_UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:Progress_UserName>
        <d:Progress_IsExecutionStarted m:type="Edm.String">false</d:Progress_IsExecutionStarted>
        <d:Progress_IsExecutionEnded m:type="Edm.String">false</d:Progress_IsExecutionEnded>
        <d:Progress_Percent m:type="Edm.String">0</d:Progress_Percent>
        <d:Progress_StartTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_StartTime>
        <d:Progress_EndTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_EndTime>
        <d:Progress_UpdateDateUTC m:type="Edm.String"></d:Progress_UpdateDateUTC>
        <d:Status m:type="Edm.String">Queued</d:Status>
        <d:Key1 m:type="Edm.String">UseFeaturesInModel</d:Key1>
        <d:Value1 m:type="Edm.String">False</d:Value1>
      </m:properties>
    </content>
      </entry>
    </feed>

### <a name="113-trigger-build-recommendation-rank-or-fbt"></a>11.3. 触发生成（建议、排名或 FBT）
| HTTP 方法 | URI |
|:--- |:--- |
| POST |`<rootURI>/BuildModel?modelId=%27<modelId>%27&userDescription=%27<description>%27&buildType=%27<buildType>%27&apiVersion=%271.0%27`<br><br>示例：<br>`<rootURI>/BuildModel?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&userDescription=%27First%20build%27&buildType=%27Ranking%27&apiVersion=%271.0%27` |
| 标头 |`"Content-Type", "text/xml"`（如果发送请求正文） |

| 参数名称 | 有效值 |
|:--- |:--- |
| modelId |模型的唯一标识符 |
| userDescription |目录的文本标识符。 请注意，如果使用空格，必须使用 %20 对其进行编码。 请参看上述示例。<br>最大长度：50 |
| buildType |调用的生成类型： <br/> -“建议”用于建议生成 <br> -“排名”用于排名生成 <br/> -“Fbt”用于 FBT 生成 |
| apiVersion |1.0 |
|  | |
| 请求正文 |如果留空，则通过默认生成参数执行生成。<br><br>如果要设置生成参数，请将它们作为 XML 发送到正文，如以下示例所示。 （有关参数完整列表的介绍，请参阅“生成参数”部分。）`<BuildParametersList><NumberOfModelIterations>40</NumberOfModelIterations><NumberOfModelDimensions>20</NumberOfModelDimensions><MinItemAppearance>5</MinItemAppearance><MinUserAppearance>5</MinUserAppearance></BuildParametersList>` |

**响应**：

HTTP 状态代码：200

这是异步 API。 作为回复，你将获取生成 ID。 若要了解生成结束时间，应调用“Get Builds Status of a Model”API，并在响应中找到此生成 ID。 请注意，生成可花费几分钟到几小时，具体取决于数据大小。

在生成结束前无法使用建议。

有效生成状态：

* 创建 - 模型已创建。
* 已排队 - 模型生成已触发并已排队。
* 正在生成 - 模型正在生成。
* 成功 - 生成成功结束。
* 错误 - 生成因失败而结束。
* 已取消 - 生成已取消。
* 正在取消 - 生成正在取消。

请注意，生成 ID 可在以下路径找到：`Feed\entry\content\properties\Id`

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
      <subtitle type="text">Build a Model with RequestBody</subtitle>
      <id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'</id>
      <rights type="text" />
      <updated>2014-10-05T08:56:34Z</updated>
      <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'" />
      <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">1000272</d:Id>
        <d:UserName m:type="Edm.String"></d:UserName>
        <d:ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:ModelId>
        <d:ModelName m:type="Edm.String">docTest</d:ModelName>
        <d:Type m:type="Edm.String">Recommendation</d:Type>
        <d:CreationTime m:type="Edm.String">2014-10-05T08:56:31.893</d:CreationTime>
        <d:Progress_BuildId m:type="Edm.String">1000272</d:Progress_BuildId>
        <d:Progress_ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:Progress_ModelId>
        <d:Progress_UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:Progress_UserName>
        <d:Progress_IsExecutionStarted m:type="Edm.String">false</d:Progress_IsExecutionStarted>
        <d:Progress_IsExecutionEnded m:type="Edm.String">false</d:Progress_IsExecutionEnded>
        <d:Progress_Percent m:type="Edm.String">0</d:Progress_Percent>
        <d:Progress_StartTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_StartTime>
        <d:Progress_EndTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_EndTime>
        <d:Progress_UpdateDateUTC m:type="Edm.String"></d:Progress_UpdateDateUTC>
        <d:Status m:type="Edm.String">Queued</d:Status>
        <d:Key1 m:type="Edm.String">UseFeaturesInModel</d:Key1>
        <d:Value1 m:type="Edm.String">False</d:Value1>
      </m:properties>
    </content>
      </entry>
    </feed>




### <a name="114-get-builds-status-of-a-model"></a>11.4. 获取模型的生成状态
检索指定模型的生成及其状态。

| HTTP 方法 | URI |
|:--- |:--- |
| GET |`<rootURI>/GetModelBuildsStatus?modelId=%27<modelId>%27&onlyLastBuild=<bool>&apiVersion=%271.0%27`<br><br>示例：<br>`<rootURI>/GetModelBuildsStatus?modelId=%279559872f-7a53-4076-a3c7-19d9385c1265%27&onlyLastBuild=true&apiVersion=%271.0%27` |

| 参数名称 | 有效值 |
|:--- |:--- |
| modelId |模型的唯一标识符 |
| onlyLastBuild |指示是返回模型的所有生成历史记录还是仅返回最近生成的状态 |
| apiVersion |1.0 |

**响应**：

HTTP 状态代码：200

响应包含每个生成的一个条目。 每个条目均具有以下数据：

* `feed/entry/content/properties/UserName` - 用户名称。
* `feed/entry/content/properties/ModelName` - 模型名称。
* `feed/entry/content/properties/ModelId` - 模型唯一标识符。
* `feed/entry/content/properties/IsDeployed` - 生成是否已部署（也称为 活动生成）。
* `feed/entry/content/properties/BuildId` - 生成唯一标识符。
* `feed/entry/content/properties/BuildType` - 生成类型。
* `feed/entry/content/properties/Status` - 生成状态。 可以是以下状态之一：错误、正在生成、已排队、正在取消、已取消、成功。
* `feed/entry/content/properties/StatusMessage` - 详细的状态消息（仅适用于特定状态）。
* `feed/entry/content/properties/Progress` - 生成进度 (%)。
* `feed/entry/content/properties/StartTime` - 生成开始时间。
* `feed/entry/content/properties/EndTime` - 生成结束时间。
* `feed/entry/content/properties/ExecutionTime` - 生成持续时间。
* `feed/entry/content/properties/ProgressStep` - 有关正在进行的生成当前阶段的详细信息。

有效生成状态：

* 已创建 - 生成请求条目已创建。
* 已排队 - 生成请求已触发并已排队。
* 正在生成 - 生成正在处理中。
* 成功 - 生成成功结束。
* 错误 - 生成因失败而结束。
* 已取消 - 生成已取消。
* 正在取消 - 生成正在取消。

生成类型的有效值：

* 排名 - 排名生成。
* 建议 - 建议生成。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get builds status of a model</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'</id>
        <rights type="text" />
        <updated>2014-11-05T17:51:10Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'" />
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
            <title type="text">GetBuildsStatusEntity</title>
            <updated>2014-11-05T17:51:10Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:UserName m:type="Edm.String">b-434e-b2c9-84935664ff20@dm.com</d:UserName>
                    <d:ModelName m:type="Edm.String">ModelName</d:ModelName>
                    <d:ModelId m:type="Edm.String">1d20c34f-dca1-4eac-8e5d-f299e4e4ad66</d:ModelId>
                    <d:IsDeployed m:type="Edm.String">true</d:IsDeployed>
                    <d:BuildId m:type="Edm.String">1000272</d:BuildId>
                    <d:BuildType m:type="Edm.String">Recommendation</d:BuildType>
                    <d:Status m:type="Edm.String">Success</d:Status>
                    <d:StatusMessage m:type="Edm.String"></d:StatusMessage>
                    <d:Progress m:type="Edm.String">0</d:Progress>
                    <d:StartTime m:type="Edm.String">2014-11-02T13:43:51</d:StartTime>
                    <d:EndTime m:type="Edm.String">2014-11-02T13:45:10</d:EndTime>
                    <d:ExecutionTime m:type="Edm.String">00:01:19</d:ExecutionTime>
                    <d:IsExecutionStarted m:type="Edm.String">false</d:IsExecutionStarted>
                    <d:ProgressStep m:type="Edm.String"></d:ProgressStep>
                </m:properties>
            </content>
        </entry>
    </feed>


### <a name="115-get-builds-status"></a>11.5. 获取生成状态
检索用户所有模型的生成状态。

| HTTP 方法 | URI |
|:--- |:--- |
| GET |`<rootURI>/GetUserBuildsStatus?onlyLastBuilds=<bool>&apiVersion=%271.0%27`<br><br>示例：<br>`<rootURI>/GetUserBuildsStatus?onlyLastBuilds=true&apiVersion=%271.0%27` |

| 参数名称 | 有效值 |
|:--- |:--- |
| onlyLastBuild |指示是返回模型的所有生成历史记录还是仅返回最近生成的状态。 |
| apiVersion |1.0 |

**响应**：

HTTP 状态代码：200

响应包含每个生成的一个条目。 每个条目均具有以下数据：

* `feed/entry/content/properties/UserName` - 用户名称。
* `feed/entry/content/properties/ModelName` - 模型名称。
* `feed/entry/content/properties/ModelId` - 模型唯一标识符。
* `feed/entry/content/properties/IsDeployed` - 生成是否已部署。
* `feed/entry/content/properties/BuildId` - 生成唯一标识符。
* `feed/entry/content/properties/BuildType` - 生成类型。
* `feed/entry/content/properties/Status` - 生成状态。 可以是以下状态之一：错误、正在生成、已排队、已取消、正在取消、成功。
* `feed/entry/content/properties/StatusMessage` - 详细的状态消息（仅适用于特定状态）。
* `feed/entry/content/properties/Progress` - 生成进度 (%)。
* `feed/entry/content/properties/StartTime` - 生成开始时间。
* `feed/entry/content/properties/EndTime` - 生成结束时间。
* `feed/entry/content/properties/ExecutionTime` - 生成持续时间。
* `feed/entry/content/properties/ProgressStep` - 有关正在进行的生成当前阶段的详细信息。

有效生成状态：

* 已创建 - 生成请求条目已创建。
* 已排队 - 生成请求已触发并已排队。
* 正在生成 - 生成正在处理中。
* 成功 - 生成成功结束。
* 错误 - 生成因失败而结束。
* 已取消 - 生成已取消。
* 正在取消 - 生成正在取消。

生成类型的有效值：

* 排名 - 排名生成。
* 建议 - 建议生成。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get builds status of a user</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'</id>
        <rights type="text" />
        <updated>2014-11-05T18:41:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'" />
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
            <title type="text">GetBuildsStatusEntity</title>
            <updated>2014-11-05T18:41:21Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:UserName m:type="Edm.String">b-434e-b2c9-84935664ff20@dm.com</d:UserName>
                    <d:ModelName m:type="Edm.String">ModelName</d:ModelName>
                    <d:ModelId m:type="Edm.String">1d20c34f-dca1-4eac-8e5d-f299e4e4ad66</d:ModelId>
                    <d:IsDeployed m:type="Edm.String">true</d:IsDeployed>
                    <d:BuildId m:type="Edm.String">1000272</d:BuildId>
                    <d:BuildType m:type="Edm.String">Recommendation</d:BuildType>
                    <d:Status m:type="Edm.String">Success</d:Status>
                    <d:StatusMessage m:type="Edm.String"></d:StatusMessage>
                    <d:Progress m:type="Edm.String">0</d:Progress>
                    <d:StartTime m:type="Edm.String">2014-11-02T13:43:51</d:StartTime>
                    <d:EndTime m:type="Edm.String">2014-11-02T13:45:10</d:EndTime>
                    <d:ExecutionTime m:type="Edm.String">00:01:19</d:ExecutionTime>
                    <d:IsExecutionStarted m:type="Edm.String">false</d:IsExecutionStarted>
                    <d:ProgressStep m:type="Edm.String"></d:ProgressStep>
                </m:properties>
            </content>
        </entry>
    </feed>


### <a name="116-delete-build"></a>11.6. 删除生成
删除生成。

注意： <br>无法删除活动生成。 在删除前，模型应更新为其他活动生成。<br>正在进行的生成无法删除。 应调用 <strong>Cancel Build</strong> 先取消生成。

| HTTP 方法 | URI |
|:--- |:--- |
| 删除 |`<rootURI>/DeleteBuild?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>示例：<br>`<rootURI>/DeleteBuild?buildId=%271500068%27&apiVersion=%271.0%27` |

| 参数名称 | 有效值 |
|:--- |:--- |
| buildId |生成的唯一标识符。 |
| apiVersion |1.0 |

**响应：**

HTTP 状态代码：200

### <a name="117-cancel-build"></a>11.7. 取消生成
取消处于正在生成状态的生成。

| HTTP 方法 | URI |
|:--- |:--- |
| PUT |`<rootURI>/CancelBuild?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>示例：<br>`<rootURI>/CancelBuild?buildId=%271500076%27&apiVersion=%271.0%27` |

| 参数名称 | 有效值 |
|:--- |:--- |
| buildId |生成的唯一标识符。 |
| apiVersion |1.0 |

**响应：**

HTTP 状态代码：200

### <a name="118-get-build-parameters"></a>11.8. 获取生成参数
检索生成参数。

| HTTP 方法 | URI |
|:--- |:--- |
| GET |`<rootURI>/GetBuildParameters?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>示例：<br>`<rootURI>/GetBuildParameters?buildId=%271000653%27&apiVersion=%271.0%27` |

| 参数名称 | 有效值 |
|:--- |:--- |
| buildId |生成的唯一标识符。 |
| apiVersion |1.0 |

**响应：**

HTTP 状态代码：200

此 API 返回密钥/值元素集合。 每个元素代表参数及其值：

* `feed/entry/content/properties/Key` - 生成参数名称。
* `feed/entry/content/properties/Value` - 生成参数值。

下表介绍了每个密钥表示的值。

| 键 | 说明 | 类型 | 有效值 |
|:--- |:--- |:--- |:--- |
| NumberOfModelIterations |总体计算时间和模型精确度反映模型执行迭代的次数。 数字越高，得到的精确度就越高，但需要较长的计算时间。 |Integer |10-50 |
| NumberOfModelDimensions |维度的数目与“特征”的数目相关，模型尝试查找数据中的数目。 增加维度的数目将允许结果进一步微调成较小的群集。 但是，太多维度将使模型无法查找项目之间的相关性。 |Integer |10-40 |
| ItemCutOffLowerBound |定义冷凝器的项目下限。 请参阅上述使用情况冷凝器。 |Integer |2 或更高（0 禁用冷凝器） |
| ItemCutOffUpperBound |定义冷凝器的项目上限。 请参阅上述使用情况冷凝器。 |Integer |2 或更高（0 禁用冷凝器） |
| UserCutOffLowerBound |定义冷凝器的用户下限。 请参阅上述使用情况冷凝器。 |Integer |2 或更高（0 禁用冷凝器） |
| UserCutOffUpperBound |定义冷凝器的用户上限。 请参阅上述使用情况冷凝器。 |Integer |2 或更高（0 禁用冷凝器） |
| 说明 |生成描述。 |String |任何文本，最多 512 个字符。 |
| EnableModelingInsights |支持计算建议模型上的度量值。 |布尔 |True/False |
| UseFeaturesInModel |指示是否可以使用特征来增强建议模型。 |布尔 |True/False |
| ModelingFeatureList |要在建议版本中使用的特征名称逗号分隔列表，用于增强建议。 |String |功能名称，最多 512 个字符。 |
| AllowColdItemPlacement |指示建议是否也应该通过特征相似度推送冷项。 |布尔 |True/False |
| EnableFeatureCorrelation |指示特征是否可用于推论。 |布尔 |True/False |
| ReasoningFeatureList |用于推论句子（例如建议说明）的特征名称逗号分隔列表。 |String |功能名称，最多 512 个字符。 |

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get build parameters</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'</id>
        <rights type="text" />
        <updated>2015-01-08T13:50:57Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'" />
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">UseFeaturesInModel</d:Key>
                    <d:Value m:type="Edm.String">False</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">AllowColdItemPlacement</d:Key>
                    <d:Value m:type="Edm.String">False</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">EnableFeatureCorrelation</d:Key>
                    <d:Value m:type="Edm.String">False</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">EnableModelingInsights</d:Key>
                    <d:Value m:type="Edm.String">False</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">NumberOfModelIterations</d:Key>
                    <d:Value m:type="Edm.String">10</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
            <titletype="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">NumberOfModelDimensions</d:Key>
                    <d:Value m:type="Edm.String">10</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <linkrel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">ItemCutOffLowerBound</d:Key>
                    <d:Value m:type="Edm.String">2</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">ItemCutOffUpperBound</d:Key>
                    <d:Value m:type="Edm.String">2147483647</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">UserCutOffLowerBound</d:Key>
                    <d:Value m:type="Edm.String">2</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">UserCutOffUpperBound</d:Key>
                    <d:Value m:type="Edm.String">2147483647</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">ModelingFeatureList</d:Key>
                    <d:Value m:type="Edm.String"/>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">ReasoningFeatureList</d:Key>
                    <d:Value m:type="Edm.String"/>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">Description</d:Key>
                    <d:Value m:type="Edm.String">rankBuild</d:Value>
                </m:properties>
            </content>
        </entry>
    </feed>

## <a name="12-recommendation"></a>12.建议
### <a name="121-get-item-recommendations-for-active-build"></a>12.1. 获取项目建议（对于活动生成）
根据种子（输入）项目列表，获取“建议”或“Fbt”类型的活动生成建议。

| HTTP 方法 | URI |
|:--- |:--- |
| GET |`<rootURI>/ItemRecommend?modelId=%27<modelId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>示例：<br>`<rootURI>/ItemRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27` |

| 参数名称 | 有效值 |
|:--- |:--- |
| modelId |模型的唯一标识符 |
| itemIds |要建议的项目的逗号分隔列表。 <br>如果活动生成属于 FBT 类型，则仅可发送一个项目。 <br>最大长度：1024 |
| numberOfResults |所需结果数 <br> 最大值：150 |
| includeMetatadata |供将来使用，始终为 false |
| apiVersion |1.0 |

**响应：**

HTTP 状态代码：200

响应包括每个建议项的一个条目。 每个条目均具有以下数据：

* `Feed\entry\content\properties\Id` - 建议项 ID。
* `Feed\entry\content\properties\Name` - 项目名称。
* `Feed\entry\content\properties\Rating` - 建议分级；数字越大，说明信心越足。
* `Feed\entry\content\properties\Reasoning` - 建议推理（例如建议说明）。

以下响应示例包括 10 个建议项。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
      <subtitle type="text">Get Recommendation</subtitle>
      <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'</id>
      <rights type="text" />
      <updated>2014-10-05T12:28:48Z</updated>
      <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'" />
      <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">159</d:Id>
        <d:Name m:type="Edm.String">159</d:Name>
        <d:Rating m:type="Edm.Double">0.543343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '159'</d:Reasoning>
      </m:properties>
    </content>
      </entry>
      <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">52</d:Id>
        <d:Name m:type="Edm.String">52</d:Name>
        <d:Rating m:type="Edm.Double">0.539588900748721</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '52'</d:Reasoning>
      </m:properties>
    </content>
      </entry>
      <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">35</d:Id>
        <d:Name m:type="Edm.String">35</d:Name>
        <d:Rating m:type="Edm.Double">0.53842946443853</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '35'</d:Reasoning>
      </m:properties>
    </content>
      </entry>
      <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">124</d:Id>
        <d:Name m:type="Edm.String">124</d:Name>
        <d:Rating m:type="Edm.Double">0.536712832792886</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '124'</d:Reasoning>
      </m:properties>
    </content>
      </entry>
      <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">120</d:Id>
        <d:Name m:type="Edm.String">120</d:Name>
        <d:Rating m:type="Edm.Double">0.533673023762878</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '120'</d:Reasoning>
      </m:properties>
    </content>
      </entry>
      <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">96</d:Id>
        <d:Name m:type="Edm.String">96</d:Name>
        <d:Rating m:type="Edm.Double">0.532544826370521</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '96'</d:Reasoning>
      </m:properties>
    </content>
      </entry>
      <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">69</d:Id>
        <d:Name m:type="Edm.String">69</d:Name>
        <d:Rating m:type="Edm.Double">0.531678607847759</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '69'</d:Reasoning>
      </m:properties>
    </content>
      </entry>
      <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">172</d:Id>
        <d:Name m:type="Edm.String">172</d:Name>
        <d:Rating m:type="Edm.Double">0.530957821375951</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '172'</d:Reasoning>
      </m:properties>
    </content>
      </entry>
      <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">155</d:Id>
        <d:Name m:type="Edm.String">155</d:Name>
        <d:Rating m:type="Edm.Double">0.529093541481333</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '155'</d:Reasoning>
      </m:properties>
    </content>
      </entry>
      <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">32</d:Id>
        <d:Name m:type="Edm.String">32</d:Name>
        <d:Rating m:type="Edm.Double">0.528917978168322</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '32'</d:Reasoning>
      </m:properties>
    </content>
      </entry>
    </feed>

### <a name="122-get-item-recommendations-of-a-specific-build"></a>12.2. 获取（特定生成的）项目建议
获取“建议”或“Fbt”类型的特定生成的建议。

| HTTP 方法 | URI |
|:--- |:--- |
| GET |`<rootURI>/ItemRecommend?modelId=%27<modelId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>示例：<br>`<rootURI>/ItemRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&buildId=1234&apiVersion=%271.0%27` |

| 参数名称 | 有效值 |
|:--- |:--- |
| modelId |模型的唯一标识符 |
| itemIds |要建议的项目的逗号分隔列表。 <br>如果活动生成属于 FBT 类型，则仅可发送一个项目。 <br>最大长度：1024 |
| numberOfResults |所需结果数 <br> 最大值：150 |
| includeMetatadata |供将来使用，始终为 false |
| buildId |用于此建议请求的生成 ID |
| apiVersion |1.0 |

**响应：**

HTTP 状态代码：200

响应包括每个建议项的一个条目。 每个条目均具有以下数据：

* `Feed\entry\content\properties\Id` - 建议项 ID。
* `Feed\entry\content\properties\Name` - 项目名称。
* `Feed\entry\content\properties\Rating` - 建议分级；数字越大，说明信心越足。
* `Feed\entry\content\properties\Reasoning` - 建议推理（例如建议说明）。

请参阅 12.1 中的响应示例

### <a name="123-get-fbt-recommendations-for-active-build"></a>12.3. 获取 FBT 建议（对于活动生成）
根据种子（输入）项目，获取“Fbt”类型活动生成的建议。

| HTTP 方法 | URI |
|:--- |:--- |
| GET |`<rootURI>/ItemFbtRecommend?modelId=%27<modelId>%27&itemId=%27<itemId>%27&numberOfResults=<int>&minimalScore=<double>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>示例：<br>`<rootURI>/ItemFbtRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemId=%271003%27&numberOfResults=10&minimalScore=<double>&includeMetadata=false&apiVersion=%271.0%27` |

| 参数名称 | 有效值 |
|:--- |:--- |
| modelId |模型的唯一标识符 |
| itemId |要建议的项目。 <br>最大长度：1024 |
| numberOfResults |所需结果数 <br>最大值：150 |
| minimalScore |频繁集应该具有的最低分数，以包含在返回的结果中。 |
| includeMetatadata |供将来使用，始终为 false |
| apiVersion |1.0 |

**响应：**

HTTP 状态代码：200

响应包括每个建议项目集的一个条目（项目集通常与种子/输入项一起购买）。 每个条目均具有以下数据：

* `Feed\entry\content\properties\Id1` - 建议项 ID。
* `Feed\entry\content\properties\Name1` - 项目名称。
* `Feed\entry\content\properties\Id2` - 第 2 个建议项 ID（可选）。
* `Feed\entry\content\properties\Name2` - 第 2 个项的名称（可选）。
* `Feed\entry\content\properties\Rating` - 建议分级；数字越大，说明信心越足。
* `Feed\entry\content\properties\Reasoning` - 建议推理（例如建议说明）。

以下响应示例包括 3 个建议项集。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
      <subtitle type="text">Get Recommendation</subtitle>
      <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemId='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'</id>
      <rights type="text" />
      <updated>2014-10-05T12:28:48Z</updated>
      <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'" />
      <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetFbtRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id1 m:type="Edm.String">159</d:Id1>
        <d:Name1 m:type="Edm.String">159</d:Name1>
        <d:Id2 m:type="Edm.String"></d:Id2>
        <d:Name2 m:type="Edm.String"></d:Name2>
        <d:Rating m:type="Edm.Double">0.543343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who bought '1003' also bought '159'</d:Reasoning>
      </m:properties>
    </content>
      </entry>
      <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetFbtRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id1 m:type="Edm.String">52</d:Id1>
        <d:Name1 m:type="Edm.String">52</d:Name1>
        <d:Id2 m:type="Edm.String"></d:Id2>
        <d:Name2 m:type="Edm.String"></d:Name2>
        <d:Rating m:type="Edm.Double">0.533343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who bought '1003' also bought '52'</d:Reasoning>
      </m:properties>
    </content>
      </entry>
      <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetFbtRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id1 m:type="Edm.String">35</d:Id1>
        <d:Name1 m:type="Edm.String">35</d:Name1>
        <d:Id2 m:type="Edm.String">102</d:Id2>
        <d:Name2 m:type="Edm.String">102</d:Name2>
        <d:Rating m:type="Edm.Double">0.523343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who bought '1003' also bought '35' and '102'</d:Reasoning>
      </m:properties>
    </content>
      </entry>
    </feed>

### <a name="124-get-fbt-recommendations-of-a-specific-build"></a>12.4. 获取（特定生成的）FBT 建议
获取“Fbt”类型特定生成的建议。

| HTTP 方法 | URI |
|:--- |:--- |
| GET |`<rootURI>/ItemFbtRecommend?modelId=%27<modelId>%27&itemId=%27<itemId>%27&numberOfResults=<int>&minimalScore=<double>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>示例：<br>`<rootURI>/ItemFbtRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemId=%271003%27&numberOfResults=10&minimalScore=0.1&includeMetadata=false&buildId=1234&apiVersion=%271.0%27` |

| 参数名称 | 有效值 |
|:--- |:--- |
| modelId |模型的唯一标识符 |
| itemId |要建议的项目。 <br>最大长度：1024 |
| numberOfResults |所需结果数 <br>最大值：150 |
| minimalScore |频繁集应该具有的最低分数，以包含在返回的结果中。 |
| includeMetatadata |供将来使用，始终为 false |
| buildId |用于此建议请求的生成 ID |
| apiVersion |1.0 |

**响应：**

HTTP 状态代码：200

响应包括每个建议项目集的一个条目（项目集通常与种子/输入项一起购买）。 每个条目均具有以下数据：

* `Feed\entry\content\properties\Id1` - 建议项 ID。
* `Feed\entry\content\properties\Name1` - 项目名称。
* `Feed\entry\content\properties\Id2` - 第 2 个建议项 ID（可选）。
* `Feed\entry\content\properties\Name2` - 第 2 个项的名称（可选）。
* `Feed\entry\content\properties\Rating` - 建议分级；数字越大，说明信心越足。
* `Feed\entry\content\properties\Reasoning` - 建议推理（例如建议说明）。

请参阅 12.3 中的响应示例

### <a name="125-get-user-recommendations-for-active-build"></a>12.5. 获取用户建议（对于活动生成）
获取“建议”类型标记为活动生成的生成建议。

API 将根据用户的使用情况历史记录返回预测项的列表。

说明： 

1. FBT 生成没有用户建议。
2. 如果活动生成是 FBT，此方法将返回错误。

| HTTP 方法 | URI |
|:--- |:--- |
| GET |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>示例：<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27` |

| 参数名称 | 有效值 |
|:--- |:--- |
| modelId |模型的唯一标识符 |
| userId |用户的唯一标识符。 |
| numberOfResults |所需结果数 |
| includeMetatadata |供将来使用，始终为 false |
| apiVersion |1.0 |

**响应：**

HTTP 状态代码：200

响应包括每个建议项的一个条目。 每个条目均具有以下数据：

* `Feed\entry\content\properties\Id` - 建议项 ID。
* `Feed\entry\content\properties\Name` - 项目名称。
* `Feed\entry\content\properties\Rating` - 建议分级；数字越大，说明信心越足。
* `Feed\entry\content\properties\Reasoning` - 建议推理（例如建议说明）。

请参阅 12.1 中的响应示例

### <a name="126-get-user-recommendations-with-item-list-for-active-build"></a>12.6. 获取带有项目列表的用户建议（对于活动生成）
获取“建议”类型标记为带有其他项目列表的活动生成的用户建议。

API 将根据用户的使用情况历史记录和其他提供项目返回预测项的列表。

说明： 

1. FBT 生成没有用户建议。
2. 如果活动生成是 FBT，此方法将返回错误。

| HTTP 方法 | URI |
|:--- |:--- |
| GET |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>&itemsIds=%27<itemsIds>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>示例：<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&itemsIds=%271003%2C1000%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27` |

| 参数名称 | 有效值 |
|:--- |:--- |
| modelId |模型的唯一标识符 |
| userId |用户的唯一标识符。 |
| itemsIds |要建议的项目的逗号分隔列表。 最大长度：1024 |
| numberOfResults |所需结果数 |
| includeMetatadata |供将来使用，始终为 false |
| apiVersion |1.0 |

**响应：**

HTTP 状态代码：200

响应包括每个建议项的一个条目。 每个条目均具有以下数据：

* `Feed\entry\content\properties\Id` - 建议项 ID。
* `Feed\entry\content\properties\Name` - 项目名称。
* `Feed\entry\content\properties\Rating` - 建议分级；数字越大，说明信心越足。
* `Feed\entry\content\properties\Reasoning` - 建议推理（例如建议说明）。

请参阅 12.1 中的响应示例

### <a name="127-get-user-recommendations-of-a-specific-build"></a>12.7. 获取（特定生成的）用户建议
获取“建议”类型的特定生成的用户建议。

API 将根据用户的使用情况历史记录返回预测项的列表（用于特定生成）。

注意：FBT 生成没有用户建议。

| HTTP 方法 | URI |
|:--- |:--- |
| GET |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>%27&numberOfResults=<int>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>示例：<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&numberOfResults=10&includeMetadata=false&buildId=50012&apiVersion=%271.0%27` |

| 参数名称 | 有效值 |
|:--- |:--- |
| modelId |模型的唯一标识符 |
| userId |用户的唯一标识符。 |
| numberOfResults |所需结果数 |
| includeMetatadata |供将来使用，始终为 false |
| buildId |用于此建议请求的生成 ID |
| apiVersion |1.0 |

**响应：**

HTTP 状态代码：200

响应包括每个建议项的一个条目。 每个条目均具有以下数据：

* `Feed\entry\content\properties\Id` - 建议项 ID。
* `Feed\entry\content\properties\Name` - 项目名称。
* `Feed\entry\content\properties\Rating` - 建议分级；数字越大，说明信心越足。
* `Feed\entry\content\properties\Reasoning` - 建议推理（例如建议说明）。

请参阅 12.1 中的响应示例

### <a name="128-get-user-recommendations-with-item-list-of-a-specific-build"></a>12.8. 获取带有项目列表的用户建议（特定生成的）
获取“建议”类型的特定生成的用户建议和其他项目的列表。

API 将根据用户的使用情况历史记录和其他项目列表返回预测项的列表。

注意：FBT 生成没有用户建议。

| HTTP 方法 | URI |
|:--- |:--- |
| GET |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>%27&itemsIds=%27<itemsIds>%27&numberOfResults=<int>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>示例：<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&itemsIds=%271003%27&numberOfResults=10&includeMetadata=false&buildId=50012&apiVersion=%271.0%27` |

| 参数名称 | 有效值 |
|:--- |:--- |
| modelId |模型的唯一标识符 |
| userId |用户的唯一标识符。 |
| itemIds |要建议的项目的逗号分隔列表。 最大长度：1024 |
| numberOfResults |所需结果数 |
| includeMetatadata |供将来使用，始终为 false |
| buildId |用于此建议请求的生成 ID |
| apiVersion |1.0 |

**响应：**

HTTP 状态代码：200

响应包括每个建议项的一个条目。 每个条目均具有以下数据：

* `Feed\entry\content\properties\Id` - 建议项 ID。
* `Feed\entry\content\properties\Name` - 项目名称。
* `Feed\entry\content\properties\Rating` - 建议分级；数字越大，说明信心越足。
* `Feed\entry\content\properties\Reasoning` - 建议推理（例如建议说明）。

请参阅 12.1 中的响应示例

## <a name="13-user-usage-history"></a>13.用户使用情况历史记录
生成建议模型后，系统将允许检索用于该生成的用户历史记录（与特定用户关联的项目）。
此 API 允许检索用户历史记录。

注意：用户历史记录当前仅用于建议生成。

### <a name="131-retrieve-user-history"></a>13.1 检索用户历史记录
检索在活动生成或给定用户 ID 的指定生成中使用的项目列表。

| HTTP 方法 | URI |
|:--- |:--- |
| GET |获取活动生成的用户历史记录。<br/>`<rootURI>/GetUserHistory?modelId=%27<model_id>%27&userId=%27<userId>%27&apiVersion=%271.0%27`<br/><br/>获取指定生成 `<rootURI>/GetUserHistory?modelId=%27<model_id>%27&userId=%27<userId>%27&buildId=<int>&apiVersion=%271.0%27` 的用户历史记录<br/><br/>示例：`<rootURI>/GetUserHistory?modelId=%2727967136e8-f868-4258-9331-10d567f87fae%27&&userId=%27u_1013%27&apiVersion=%271.0%277` |

| 参数名称 | 有效值 |
|:--- |:--- |
| modelId |模型的唯一标识符。 |
| userId |用户的唯一标识符。 |
| buildId |可选参数，支持指示从中提取用户历史记录的生成 |
| apiVersion |1.0 |

**响应：**

HTTP 状态代码：200

响应包括每个建议项的一个条目。 每个条目均具有以下数据：

* `Feed\entry\content\properties\Id` - 建议项 ID。
* `Feed\entry\content\properties\Name` - 项目名称。
* `Feed\entry\content\properties\Rating` - 不适用。
* `Feed\entry\content\properties\Reasoning` - 不适用。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Get User History</subtitle>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2015-05-26T15:32:47Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'" />
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
        <title type="text">CatalogItemsThatContainATokenEntity</title>
        <updated>2015-05-26T15:32:47Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Id m:type="Edm.String">2406E770-769C-4189-89DE-1C9283F93A96</d:Id>
                <d:Name m:type="Edm.String">Clara Callan</d:Name>
                <d:Rating m:type="Edm.Double">0</d:Rating>
                <d:Reasoning m:type="Edm.String"/>
                <d:Metadata m:type="Edm.String"/>
                <d:FormattedRating m:type="Edm.Double" m:null="true"/>
            </m:properties>
        </content>
    </entry>
</feed>

## <a name="14-notifications"></a>14.通知
当系统中持续发生错误时，Azure 机器学习建议将创建通知。 有 3 种通知：

1. 生成失败 - 每个生成失败都会触发此通知。
2. 数据获取处理失败 - 当在处理每个模型的使用情况事件时，5 分钟内发生了超过 100 个的错误，将触发此通知。
3. 建议使用失败 - 在处理每个模型的建议请求时，5 分钟内发生了超过 100 个的错误，将触发此通知。

### <a name="141-get-notifications"></a>14.1. 获取通知
为所有模型或单个模型检索所有通知。

| HTTP 方法 | URI |
|:--- |:--- |
| GET |`<rootURI>/GetNotifications?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>为所有模型获取所有通知：<br>`<rootURI>/GetNotifications?apiVersion=%271.0%27`<br><br>为特定模型获取通知的示例：<br>`<rootURI>/GetNotifications?modelId=%27967136e8-f868-4258-9331-10d567f87fae%27&apiVersion=%271.0%277` |

| 参数名称 | 有效值 |
|:--- |:--- |
| modelId |可选参数。 如果省略，将获取所有模型的所有通知。 <br>有效值：模型的唯一标识符。 |
| apiVersion |1.0 |
|  | |
| 请求正文 |无 |

**响应：**

HTTP 状态代码：200

OData XML

    The response includes one entry per notification. Each entry has the following data:
        * feed\entry\content\properties\UserName - Internal user name identification.
        * feed\entry\content\properties\ModelId - Model ID.
        * feed\entry\content\properties\Message - Notification message.
        * feed\entry\content\properties\DateCreated - Date that this notification was created in UTC format.
        * feed\entry\content\properties\NotificationType - Notification types. Values are BuildFailure, RecommendationFailure, and DataAquisitionFailure.

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get a list of Notifications for a user</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'</id>
        <rights type="text" />
        <updated>2014-11-04T13:24:23Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'" />
        <entry>
                <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
            <title type="text">GetAListOfNotificationsForAUserEntity</title>
            <updated>2014-11-04T13:24:23Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:UserName m:type="Edm.String">515506bc-3693-4dce-a5e2-81cb3e8efb56@dm.com</d:UserName>
                    <d:ModelId m:type="Edm.String">967136e8-f868-4258-9331-10d567f87fae</d:ModelId>
                    <d:Message m:type="Edm.String">Build failed for user</d:Message>
                    <d:DateCreated m:type="Edm.String">2014-11-04T13:23:14.383</d:DateCreated>
                    <d:NotificationType m:type="Edm.String">BuildFailure</d:NotificationType>
                </m:properties>
            </content>
        </entry>
    </feed>

### <a name="142-delete-model-notifications"></a>14.2. 删除模型通知
删除模型的所有已读通知。

| HTTP 方法 | URI |
|:--- |:--- |
| 删除 |`<rootURI>/DeleteModelNotifications?modelId=%<model_id>%27&apiVersion=%271.0%27`<br><br>示例：<br>`<rootURI>/DeleteModelNotifications?modelId=%27967136e8-f868-4258-9331-10d567f87fae%27&apiVersion=%271.0%27` |

| 参数名称 | 有效值 |
|:--- |:--- |
| modelId |模型的唯一标识符 |
| apiVersion |1.0 |
|  | |
| 请求正文 |无 |

**响应**：

HTTP 状态代码：200

### <a name="143-delete-user-notifications"></a>14.3. 删除用户通知
删除所有模型的所有通知。

| HTTP 方法 | URI |
|:--- |:--- |
| 删除 |`<rootURI>/DeleteUserNotifications?apiVersion=%271.0%27` |

| 参数名称 | 有效值 |
|:--- |:--- |
| apiVersion |1.0 |
|  | |
| 请求正文 |无 |

**响应**：

HTTP 状态代码：200

## <a name="15-legal"></a>15.合法
本文档按原样提供。 本文档表达的信息和观点，包括 URL 和其他 Internet Web 站点参考，若有更改，恕不通知。<br><br>
在此介绍的一些示例仅为解释提供，并且纯属虚构。 没有计划产生实际关联或联系，也不应妄加推断。<br><br>
本文档不向你提供对任何 Microsoft 产品的任何知识产权的任何法律权利。 可复制本文档，将其用于内部、参考用途。<br><br>
© 2015 Microsoft。 保留所有权利。




<!--HONumber=Nov16_HO3-->


