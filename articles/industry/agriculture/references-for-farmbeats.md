---
title: Azure FarmBeats 的参考
description: ''
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 8efc98ad2785a9052244556bddc60a5ba34bd3d8
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74900393"
---
# <a name="references"></a>参考

本文介绍 Azure FarmBeats Api。

## <a name="rest-api"></a>REST API

Azure FarmBeats Api 为农业企业提供了标准化的 RESTful 接口，并提供基于 JSON 的响应，帮助你充分利用 Azure FarmBeats 功能，例如：

- 用于获取传感器、照相机、无人机、天气、卫星和特选地面数据的 Api。
- 跨通用数据提供程序的数据的规范化和 contextualization。
- 架构化对所有引入数据进行访问和查询。
- 自动生成可根据 agronomic 功能查询的元数据。 
- 自动生成的用于快速构建模型的时间序列聚合。
- 集成的 Azure 数据工厂引擎，可轻松构建自定义数据处理管道。

## <a name="application-development"></a>应用程序开发

FarmBeats Api 包含 Swagger 技术文档。 有关所有 Api 及其相应的请求或响应的信息，请参阅[Swagger](https://aka.ms/FarmBeatsDatahubSwagger)。

下表汇总了 FarmBeats Datahub 中的所有对象和资源。

| 对象和资源 | 描述
--- | ---|
Farm（场） | 场对应于 FarmBeats 系统中感兴趣的物理位置。 每个场都有场名称和唯一场 ID。 |
设备  | 设备对应于在场上存在的物理设备。 每台设备都具有唯一的设备 ID。 通常会将设备设置为具有场 ID 的场。
DeviceModel  | DeviceModel 对应于设备的元数据，如制造商和设备类型（网关或节点）。
传感器  | 传感器对应于记录值的物理传感器。 传感器通常连接到设备 ID 为的设备。
SensorModel  | SensorModel 对应于传感器的元数据，如制造商、传感器类型（模拟或数字）以及传感器测量，如环境温度和压力。
遥测  | 遥测提供读取特定传感器和时间范围的遥测消息的功能。
作业  | 作业对应于在 FarmBeats 系统中执行的任何活动工作流以获取所需的输出。 每个作业都与作业 ID 和作业类型相关联。
JobType  | JobType 对应于系统支持的不同作业类型。 包含系统定义和用户定义的作业类型。
ExtendedType  | ExtendedType 对应于系统中的系统定义类型和用户定义类型的列表。 ExtendedType 可帮助在 FarmBeats 系统中设置新的传感器、场景或场景文件类型。
Partner  | 合作伙伴对应于 FarmBeats 的传感器和图像集成合作伙伴。
场景  | 场景对应于在场的上下文中生成的任何输出。 每个场景都具有与之关联的场景 ID、场景源、场景类型和场 ID。 每个场景 ID 都可以有多个与之关联的场景文件。
SceneFile |SceneFile 对应于针对单个场景生成的所有文件。 一个场景 ID 可以有多个与之关联的 SceneFile Id。
规则  |规则对应于与场相关的用于触发警报的数据的条件。 每个规则都在场的数据的上下文中。
警报  | 警报对应于在满足规则条件时生成的通知。 每个警报都在规则的上下文中。
RoleDefinition  | RoleDefinition 为角色定义允许和禁止的操作。
RoleAssignment  |RoleAssignment 对应于将角色分配给用户或服务主体。

### <a name="data-format"></a>数据格式

JSON 是一种与语言无关的公共数据格式，提供任意数据结构的简单文本表示形式。 有关详细信息，请参阅[JSON 网站](https://www.json.org/)。

## <a name="authentication-and-authorization"></a>身份验证和授权

对 REST API 的 HTTP 请求受到 Azure Active Directory （Azure AD）的保护。
若要向 REST Api 发出经过身份验证的请求，客户端代码需要使用有效凭据进行身份验证，然后才能调用 API。 身份验证是通过 Azure AD 在不同的参与者之间进行协调。 它向客户端提供一个访问令牌作为身份验证的证明。 然后在 REST API 请求的 HTTP 授权标头中发送令牌。 若要详细了解 Azure AD 身份验证，请参阅开发人员[Azure Active Directory](https://portal.azure.com) 。

必须在后续 API 请求的标头部分中发送访问令牌，如下所示：

```
headers = {"Authorization": "Bearer " + **access_token**}
```

### <a name="http-request-headers"></a>HTTP 请求标头

下面是在对 Azure FarmBeats Datahub 进行 API 调用时必须指定的最常见请求标头。


**标头** | **说明和示例**
--- | ---
Content-Type  | 请求格式（Content-type： application/<format>）。 对于 Azure FarmBeats Datahub Api，格式为 JSON。 Content-Type: application/json
授权  | 指定进行 API 调用所需的访问令牌。 授权：持有者 < 访问令牌 >
接受 | 响应格式。 对于 Azure FarmBeats Datahub Api，格式为 JSON。 接受： application/json

### <a name="api-requests"></a>API 请求

若要发出 REST API 请求，请将 HTTP （GET、POST、PUT 或 DELETE）方法、API 服务的 URL、要查询的资源的 URI、提交数据到、更新或删除，然后添加一个或多个 HTTP 请求标头。

API 服务的 URL 是你的 Datahub URL，例如 https://\<yourdatahub-name >. appname>.azurewebsites.net。
或者，您可以在 GET 调用中包含查询参数以筛选、限制的大小，并对响应中的数据进行排序。

以下示例请求用于获取设备列表：

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

大多数 GET、POST 和 PUT 调用都需要 JSON 请求正文。

以下示例请求创建设备。 此请求具有带有请求正文的输入 JSON。

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

### <a name="query-parameters"></a>查询参数

对于 REST GET 调用，你可以通过在请求 URI 上包含一个或多个查询参数来筛选、限制的大小，并对数据进行排序。 有关查询参数的详细说明，请参阅 API 文档和单个 GET 调用。
例如，在查询设备列表时（/Device 上的 GET 调用），可以指定以下查询参数： 

![设备列表](./media/for-references/query-parameters-device-1.png)

### <a name="error-handling"></a>错误处理。

Azure FarmBeats Datahub Api 返回标准 HTTP 错误。 最常见的错误代码如下所示：

 |错误代码             | 描述 |
 |---                    | --- |
 |200                    | Success |
 |201                    | 创建（Post）成功 |
 |400                    | 错误的请求。 请求中存在错误。 |
 |401                    | 未授权。 API 的调用方无权访问该资源。 |
 |404                    | 找不到资源 |
 |5XX                    | 内部服务器错误。 从5XX 开始的错误代码意味着服务器上出现一些错误。 有关更多详细信息，请参阅服务器日志和以下部分。 |


除了标准 HTTP 错误外，Azure FarmBeats Datahub Api 还会返回以下格式的内部错误：

```json
    {
      "message": "<More information on the error>",
      "status": "<error code>”,
      "code": "<InternalErrorCode>",
      "moreInfo": "<Details of the error>"
    }
```

在此示例中，在创建场时，输入负载中未指定必填字段 "Name"。 生成的错误消息为：

 ```json    
    {
      "message": "Model validation failed",
      "status": 400,
      "code": "ModelValidationFailed",
      "moreInfo": "[\"The Name field is required.\"]"
    }
  ```

## <a name="add-users-or-app-registrations-to-azure-active-directory"></a>将用户或应用注册添加到 Azure Active Directory

可以通过 Azure Active Directory 中的用户或应用注册来访问 Azure FarmBeats Api。 若要在 Azure Active Directory 中创建应用注册，请按照下列步骤操作。

1. 中转到[Azure 门户](https://portal.azure.com)，并选择 " **Azure Active Directory** > **应用注册**" > **新注册**"。 或者，您可以使用现有帐户。
2. 对于新帐户，请执行以下操作：

    - 输入名称。
    - **仅选择此组织目录中的帐户（单租户）** 。
    - 使用其余字段中的默认值。
    - 选择“注册”。

3. 在 "新建和现有应用注册**概述**" 窗格中，执行以下操作：

    - 捕获**客户端 id**和**租户 id**。
    - 请参阅**证书和机密**以生成新的客户端密钥并捕获**客户端密钥**。
    - 返回到 "**概述**"，并选择 "**管理本地目录中的应用程序**" 旁边的链接。
    - 请参阅 "**属性**" 以捕获**对象 ID**。

4. 中转到[Datahub Swagger](https://<yourdatahub>.azurewebsites.net/swagger/index.html) ，并执行以下操作：
    - 请参阅**ROLEASSIGNMENT API**。
    - 执行 POST，为刚创建的**对象 ID**创建**RoleAssignment**对象。 

  > [!NOTE]
  > 有关如何添加用户和 Active Directory 注册的详细信息，请参阅[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)。

完成前面的步骤后，应用注册（客户端）可以通过使用者身份验证通过使用访问令牌来调用 Azure FarmBeats Api。 

在标头部分中，使用访问令牌将其发送到后续 API 请求，如下所示：

```
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```
