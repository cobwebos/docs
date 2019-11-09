---
title: FarmBeats 的引用
description: ''
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 057037807a75e50eb2305bfab19d1fcff7fe77ce
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889595"
---
# <a name="references"></a>参考

下面是概述 Azure FarmBeats Api 的说明和说明的集合。

## <a name="rest-api"></a>REST API

Azure FarmBeats Api 提供具有基于 JSON 的响应的标准化 RESTful 接口的农业业务，这将帮助你充分利用 Azure FarmBeats 功能：

- 用于获取传感器、照相机、无人机、天气、卫星和特选地面数据的 API。
- 跨通用数据提供程序的数据的规范化/contextualization。
- 架构化对所有引入数据进行访问和查询。
- 自动生成可根据 agronomic 功能查询的元数据。  
- 自动生成的用于快速构建模型的时间序列聚合。
- 集成的 Azure 数据工厂（ADF）引擎，可轻松构建自定义数据处理管道。

## <a name="application-development"></a>应用程序开发

Api 包含 Swagger 技术文档。 有关所有 Api 及其相应的请求/响应的信息，请参阅[Swagger](https://aka.ms/FarmBeatsDatahubSwagger) 。

这是 FarmBeats 数据中心内所有对象/资源的摘要：

场所 | 场对应于 FarmBeats 系统中感兴趣的物理位置。 每个场都有场名称和唯一场 ID。
--- | ---|
设备  | 设备对应于在场中存在的物理设备。 每台设备都具有唯一的设备 ID。 通常会将设备设置为具有场 ID 的场。
DeviceModel  | DeviceModel 对应于设备的元数据，如制造商、设备的类型或 "节点"。
传感器  | 传感器对应于记录值的物理传感器。 传感器通常连接到设备 ID 为的设备。
SensorModel  | SensorModel 对应于传感器的元数据，如制造商、传感器类型、模拟或数字、传感器度量值（如环境温度、压力等）。
遥测  | 遥测提供读取特定传感器和时间范围的遥测消息的功能。
作业  | 作业对应于在 FarmBeats 系统中执行的任何活动工作流，以获取所需的输出。 每个作业都与作业 ID 和作业类型相关联。
JobType  | JobType 对应于系统支持的不同作业类型。 这包括系统定义的 & 用户定义的作业类型。
ExtendedType  | ExtendedType 对应于系统中系统 & 用户定义类型的列表。 这有助于在 FarmBeats 系统中设置新的传感器或场景或 Scenefile 类型。
合作伙伴  | 合作伙伴对应于 FarmBeats 的传感器/图像集成合作伙伴
场景  | 场景对应于在场的上下文中生成的任何输出。 每个场景都具有与之关联的场景 ID、场景源、场景类型和场 ID。 每个场景 ID 都可以有多个与之关联的场景文件。
SceneFile |SceneFile 对应于为单一场景生成的所有文件。 一个场景 ID 可以有多个与之关联的 SceneFile Id。
规则  |规则对应于与场相关的用于触发警报的数据的条件。 每个规则都将位于场的数据的上下文中。
警报  | 警报对应于在满足规则条件时生成的通知。 每个警报都将位于规则的上下文中。
RoleDefinition  | RoleDefinition 为角色定义允许和禁止的操作。
RoleAssignment  |RoleAssignment 对应于将角色分配给用户或服务主体。

**数据格式**

JSON （JavaScript 对象表示法）是一种与语言无关的公共数据格式，提供任意数据结构的简单文本表示形式。 有关详细信息，请参阅 json.org。

## <a name="authentication-and-authorization"></a>身份验证和授权

对 REST API 的 HTTP 请求受到 Azure Active Directory （Azure AD）的保护。
若要向 REST Api 发出经过身份验证的请求，客户端代码需要使用有效凭据进行身份验证，然后才能调用 API。 身份验证通过 Azure AD 在各种执行组件之间进行协调，并为客户端提供访问令牌作为身份验证的证明。 然后在 REST API 请求的 HTTP 授权标头中发送令牌。 若要详细了解 Azure AD 身份验证，请参阅开发人员[Azure Active Directory](https://portal.azure.com) 。

需要在标头部分中的后续 API 请求中发送访问令牌，如下所示：

```
headers = {"Authorization": "Bearer " + **access_token**}
```

**HTTP 请求标头**

以下是在对 Azure FarmBeats 数据中心进行 API 调用时需要指定的最常见请求标头：


**标头** | **说明和示例**
--- | ---
Content-Type  | Azure FarmBeats 数据中心 Api 格式的请求格式（Content-type： application/<format>）为 json。 Content-type： application/json
授权  | 指定进行 API 调用所需的访问令牌。 **授权：持有者 < 访问令牌 >**
接受 | 响应格式。 对于 Azure FarmBeats 数据中心 Api，格式为 json **Accept： application/json**

**API 请求**

若要发出 REST API 请求，请将 HTTP （GET、POST、PUT 或 DELETE）方法、API 服务的 URL、要查询的资源的 URI、将数据提交到、更新或删除以及一个或多个 HTTP 请求标头组合在一起。

API 服务的 URL 是你的数据中心 URL https://\<yourdatahub >. appname>.azurewebsites.net。例如，你可以根据需要在 GET 调用上包含查询参数，并限制的大小，并对响应中的数据进行排序。

下面的示例请求是获取设备的列表：

```azurepowershell-interactive
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

大多数 GET、POST 和 PUT 调用都需要 JSON 请求正文。

下面的示例请求是创建一个设备（它具有带有请求正文的输入 json）。

```json
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

**Query parameters**

对于 REST **GET**调用，你可以通过在请求 URI 上包含一个或多个查询参数来筛选、限制的大小，并对数据进行排序。 有关查询参数，请参阅 API 文档并查看各个 GET 调用。
例如，在查询设备列表时（对/Device 的 GET 调用），可以指定以下查询参数：  

![项目场节拍](./media/for-references/query-parameters-device-1.png)

**错误处理**

Azure FarmBeats 数据中心 API 返回标准 HTTP 错误。 最常见的错误代码如下所示：

 |错误代码             | 说明 |
 |---                    | --- |
 |200                    | 成功 |
 |201                    | 创建（Post）成功 |
 |400                    | 错误的请求。 请求中存在错误 |
 |401                    | 未授权。 API 的调用方无权访问资源 |
 |404                    | 找不到资源 |
 |5XX                    | 内部服务器错误。 从5XX 开始的错误代码意味着服务器上出现一些错误。 有关更多详细信息，请参阅服务器日志和以下部分。 |


除了标准 HTTP 错误外，Azure FarmBeats 数据中心 Api 还会返回以下格式的内部错误：

    ```
    {
      "message": "<More information on the error>",
      "status": "<error code>”,
      "code": "<InternalErrorCode>",
      "moreInfo": "<Details of the error>"
    }
    ```

示例：创建场时，输入负载中未指定必填字段 "Name"。 生成的错误消息为：

    ```json
    {
      "message": "Model validation failed",
      "status": 400,
      "code": "ModelValidationFailed",
      "moreInfo": "[\"The Name field is required.\"]"
    }
    ```

## <a name="adding-users-or-app-registrations-to-azure-active-directory"></a>将用户或应用注册添加到 Azure Active Directory

可以通过 Azure Active Directory 中的用户或应用注册来访问 Azure FarmBeats Api。 若要在 Azure Active Directory 上创建应用注册，请执行以下步骤：  

1. 请参阅[Azure 门户](https://portal.azure.com) **Azure Active Directory，应用注册**> "**新注册**"。 或者，您可以使用现有帐户。
2. 对于新帐户，请确保完成以下操作：

    - 输入名称
    - **仅选择此组织目录中的帐户（单租户）**
    - 其余字段中的默认值
    - 选择**注册**

3. 从新的/现有应用注册，**概述**，完成以下

    - 捕获**客户端 id**和**租户 id**。
    - 请参阅**证书和机密**以生成新的客户端密钥并捕获**客户端密钥**。
    - 返回到 "概述"，然后单击 "**管理本地目录中的应用程序**" 旁边的链接
    - 中转到 "**属性**" 以捕获**对象 ID**

4. 请中转到[数据中心 Swagger](https://<yourdatahub>.azurewebsites.net/swagger/index.html)并执行以下步骤：
    - 导航到**ROLEASSIGNMENT API**
    - 执行**POST** ，为刚创建的**对象 ID**创建 RoleAssignment。 –输入 json 为：

  > [!NOTE]
  > 有关添加用户和 AD 注册的详细信息，请参阅[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) 。

完成上述步骤后，应用注册（客户端）可以使用访问令牌通过持有者身份验证调用 Azure FarmBeats Api。  

在标头部分中，使用访问令牌将其发送到后续 API 请求，如下所示：

```
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```
