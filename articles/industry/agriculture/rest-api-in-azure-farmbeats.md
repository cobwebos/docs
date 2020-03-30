---
title: 参考
description: ''
author: sunasing
ms.topic: article
ms.date: 11/04/2019
ms.author: sunasing
ms.openlocfilehash: 318b3e3f774a4381434fd56154f4c0d95c28c7a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479509"
---
# <a name="references"></a>参考

本文介绍 Azure 服务器场节拍 API。

## <a name="rest-api"></a>REST API

Azure FarmBeats API 为农业企业提供具有基于 JSON 的响应的标准化 RESTful 界面，以帮助您利用 Azure FarmBeats 功能，例如：

- API 获取传感器、摄像机、无人机、天气、卫星和精心策划的地面数据。
- 跨通用数据提供程序的数据规范化和上下文化。
- 对所有引入的数据进行架构化访问和查询功能。
- 基于农艺功能自动生成可查询的元数据。
- 自动生成快速模型构建的时间序列聚合。
- 集成 Azure 数据工厂引擎，轻松构建自定义数据处理管道。

## <a name="application-development"></a>应用程序开发

FarmBeats API 包含斯瓦格技术文档。 有关所有 API 及其相应请求或响应的信息，请参阅[Swagger](https://aka.ms/FarmBeatsDatahubSwagger)。

下表汇总了 FarmBeats Datahub 中的所有对象和资源：

| 对象和资源 | 描述
--- | ---|
场 | 服务器场对应于 FarmBeats 系统中感兴趣的物理位置。 每个服务器场都有一个服务器场名称和一个唯一的服务器场 ID。 |
设备  | 设备对应于服务器场上存在的物理设备。 每个设备都有一个唯一的设备 ID。 设备通常预配到具有服务器场 ID 的服务器场。
DeviceModel  | DeviceModel 对应于设备的元数据，例如制造商和设备类型（即网关或节点）。
传感器  | 传感器对应于记录值的物理传感器。 传感器通常连接到具有设备 ID 的设备。
传感器模型  | SensorModel 对应于传感器的元数据（如制造商、模拟或数字传感器类型）和传感器测量（如环境温度和压力）。
遥测  | 遥测提供了读取特定传感器和时间范围的遥测消息的能力。
作业  | 作业对应于在 FarmBeats 系统中执行的任何活动工作流，以获得所需的输出。 每个作业都与作业 ID 和作业类型相关联。
JobType  | 作业类型对应于系统支持的不同作业类型。 包括系统定义和用户定义的作业类型。
ExtendedType  | 扩展类型对应于系统中系统和用户定义的类型列表。 扩展类型有助于在 FarmBeats 系统中设置新的传感器、场景或场景文件类型。
Partner  | 合作伙伴对应于 FarmBeats 的传感器和影像集成合作伙伴。
场景  | 场景对应于服务器场上下文中生成的任何输出。 每个场景都有一个与其关联的场景 ID、场景源、场景类型和服务器场 ID。 每个场景 ID 可以有多个与其关联的场景文件。
场景文件 |场景文件对应于为单个场景生成的所有文件。 单个场景 ID 可以有多个与其关联的场景文件 ID。
规则  |规则对应于与服务器场相关的数据以触发警报的条件。 每个规则都位于服务器场数据的上下文中。
警报  | 警报对应于通知，该通知在满足规则条件时生成。 每个警报都位于规则的上下文中。
RoleDefinition  | 角色定义定义角色的允许和不允许的操作。
RoleAssignment  |角色分配对应于角色分配给用户或服务主体。

### <a name="data-format"></a>数据格式

JSON 是一种与语言无关的通用数据格式，它提供了任意数据结构的简单文本表示形式。 有关详细信息，请参阅[JSON 网站](https://www.json.org/)。

## <a name="authentication-and-authorization"></a>身份验证和授权

对 REST API 的 HTTP 请求受 Azure 活动目录 （Azure AD） 的保护。
要向 REST API 发出经过身份验证的请求，客户端代码需要使用有效凭据进行身份验证，然后才能调用 API。 Azure AD 协调各种参与者之间的身份验证。 它为客户端提供访问令牌作为身份验证的证明。 然后，在 REST API 请求的 HTTP 授权标头中发送令牌。 要了解有关 Azure AD 身份验证的更多内容，请参阅开发人员的[Azure 活动目录](https://portal.azure.com)。

访问令牌必须在后续 API 请求中（标头部分）中发送，如：

```
headers = {"Authorization": "Bearer " + **access_token**}
```

### <a name="http-request-headers"></a>HTTP 请求标头

以下是对 Azure FarmBeats Datahub 进行 API 调用时必须指定的最常见请求标头。


**头** | **说明和示例**
--- | ---
Content-Type  | 请求格式（内容类型：应用程序/<format>）。 对于 Azure 服务器场数据库 API，格式为 JSON。 Content-Type: application/json
授权  | 指定进行 API 调用所需的访问令牌。 授权：承载<访问令牌>
Accept | 响应格式。 对于 Azure 服务器场数据库 API，格式为 JSON。 接受：应用程序/json

### <a name="api-requests"></a>API 请求

要发出 REST API 请求，请组合 HTTP（GET、POST、PUT 或 DELETE）方法、API 服务的 URL、要查询、将数据提交到、更新或删除的资源的 URI，然后添加一个或多个 HTTP 请求标头。

API 服务的 URL 是 Datahub URL，例如，https://\<您的数据中心网站名称>.azure 网站.net。

或者，您可以在 GET 调用上包括查询参数，以筛选 、限制 的大小和在响应中对数据进行排序。

以下示例请求用于获取设备列表：

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

大多数 GET、POST 和 PUT 呼叫都需要 JSON 请求正文。

以下示例请求创建设备。 此请求具有与请求正文一起输入 JSON。

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

### <a name="query-parameters"></a>查询参数

对于 REST GET 调用，您可以通过在请求 URI 上包含一个或多个查询参数来筛选 、限制 大小和在 API 响应中排序数据。 有关查询参数，请参阅 API 文档和各个 GET 调用。
例如，当您查询设备列表（GET 调用 /Device）时，可以指定以下查询参数：

![设备列表](./media/references-for-azure-farmbeats/query-parameters-device-1.png)

### <a name="error-handling"></a>错误处理

Azure FarmBeats Datahub API 返回标准 HTTP 错误。 最常见的错误代码如下所示：

 |错误代码             | 描述 |
 |---                    | --- |
 |200                    | Success |
 |201                    | 创造（后）成功 |
 |400                    | 错误的请求。 请求中出错。 |
 |401                    | 未授权。 API 的调用方无权访问资源。 |
 |404                    | 未找到资源 |
 |5XX                    | 内部服务器错误。 以 5XX 开头的错误代码表示服务器上存在一些错误。 有关详细信息，请参阅服务器日志和以下部分。 |


除了标准 HTTP 错误之外，Azure FarmBeats Datahub API 还以以下格式返回内部错误：

```json
    {
      "message": "<More information on the error>",
      "status": "<error code>”,
      "code": "<InternalErrorCode>",
      "moreInfo": "<Details of the error>"
    }
```

在此示例中，创建服务器场时，在输入负载中未指定强制字段"Name"。 生成的错误消息是：

 ```json    
    {
      "message": "Model validation failed",
      "status": 400,
      "code": "ModelValidationFailed",
      "moreInfo": "[\"The Name field is required.\"]"
    }
  ```

## <a name="add-users-or-app-registrations-to-azure-active-directory"></a>将用户或应用注册添加到 Azure 活动目录

Azure FarmBeats API 可以由用户或 Azure 活动目录中的应用注册访问。 要在 Azure 活动目录中创建应用注册，请按照以下步骤操作：

1. 转到[Azure 门户](https://portal.azure.com)，然后选择**Azure 活动目录** > **应用注册** > **"新注册**"。 或者，您可以使用现有帐户。
2. 对于新帐户，执行以下操作：

    - 输入名称。
    - **仅选择此组织目录中的帐户（单个租户）。**
    - 使用字段的其余部分中的默认值。
    - 选择“注册”****。

3. 在新和现有应用注册**概述**窗格中，执行以下操作：

    - 捕获**客户端 ID**和**租户 ID**。
    - 转到**证书和机密**以生成新的客户端密钥并捕获**客户端-机密**。
    - 返回**概述**，然后选择**本地目录中管理应用程序**旁边的链接。
    - 转到**属性**以捕获对象**ID**。

4. 转到[Datahub Swagger，](https://<yourdatahub>.azurewebsites.net/swagger/index.html)执行以下操作：
    - 转到**角色分配 API**。
    - 执行 POST 为刚刚创建**的对象 ID**创建**角色分配**对象。

  > [!NOTE]
  > 有关如何添加用户和活动目录注册的详细信息，请参阅[Azure 活动目录](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)。

完成上述步骤后，应用注册（客户端）可以使用通过承载身份验证的访问令牌调用 Azure FarmBeats API。

使用访问令牌在标头部分中的后续 API 请求中将其发送，如：

```
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```
