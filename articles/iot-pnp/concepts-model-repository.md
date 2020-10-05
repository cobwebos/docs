---
title: 了解 Azure IoT 模型存储库的概念 |Microsoft Docs
description: 作为解决方案开发人员或 IT 专业人员，请了解 Azure IoT 模型存储库的基本概念。
author: prashmo
ms.author: prashmo
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5d07257d1e23ee792aa996e31a2c28c17bc23d34
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91715064"
---
# <a name="azure-iot-model-repository"></a>Azure IoT 模型存储库

借助 Azure IoT 模型存储库，设备构建者可以管理和共享 IoT 即插即用设备模型。 设备模型是使用 [数字孪生建模语言 (DTDL) ](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)定义的 JSON LD 文档。 存储在模型存储库服务中的模型可通过访问控制或公开方式与解决方案开发人员共享，无需任何身份验证即可集成和开发 IoT 即插即用云解决方案。

> [!NOTE]
> 设备构建者可以选择直接在设备上实现 IoT 即插即用设备型号，使用模块，或在 IoT Edge 模块中实现。

您可以使用访问模型存储库：

- [Azure IoT 模型存储库](https://aka.ms/iotmodelrepo) 门户
- [Azure IoT 模型存储库 REST API](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync)
- [Azure CLI IoT 模型存储库命令](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp?view=azure-cli-latest&preserve-view=true)

## <a name="public-models"></a>公共模型

存储在模型存储库中的公共数字克隆模型可供所有人使用，无需任何身份验证即可在其应用程序中集成。 此外，公共模型使得设备制造商和解决方案开发人员开放的 eco 系统可以共享和重复使用其 IoT 即插即用设备型号。

有关如何在模型存储库中发布模型以使其成为公共模型的说明，请参阅**公司模型**下的[发布模型](#publish-a-model)部分。

使用模型存储库门户查看公共模型：

1. 请参阅 [Azure IoT 模型存储库门户](https://aka.ms/iotmodelrepo)。

1. 选择 " **查看公共模型**"。

    ![查看公共模型](./media/concepts-model-repository/public-models.png)

若要使用 REST API 以编程方式查看公共模型，请参阅 [获取模型](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync) REST API 文档。

```csharp
var httpClient = new HttpClient();
httpClient.BaseAddress = new Uri("https://repo.azureiotrepository.com");

var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.GetAsync($"/models/{modelId}?api-version=2020-05-01-preview").ConfigureAwait(false);
```

若要使用 CLI 查看公共模型，请参阅 Azure CLI [获取模型](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-show&preserve-view=true) "命令。

## <a name="company-models"></a>公司型号

公司模型存储库是适用于你的组织的 Azure IoT 模型存储库中的一个租户，用于创建和管理你的公司或组织内的用户创作的数字克隆模型。 公司型号仅适用于公司或组织的经过身份验证的用户。 模型存储库租户管理员可以向公司或组织中的其他用户分配权限和控制对公司模型存储库中的模型的访问权限。

### <a name="set-up-your-company-model-repository"></a>设置公司模型存储库

使用你的 *工作或学校 Azure Active Directory (Azure AD) 帐户* 访问模型存储库。 如果你的组织已有 Azure AD 租户，则可以使用此 Azure AD 租户中的用户帐户 () 和服务主体。

若要了解如何设置 Azure AD 租户以及如何在 Azure AD 租户中创建用户或服务主体，请参阅 [其他信息](#additional-information) 部分。

- 如果你是组织中的第一个用户来访问模型存储库或登录到门户，则会向你授予 **租户管理员** 角色。 此角色允许将角色分配给组织的存储库租户中的其他用户。

- 可以通过 **租户管理员** （如 **读取模型** 或 **创建模型**）分配其他角色。

### <a name="understand-access-management"></a>了解访问管理

下表总结了公司模型存储库中支持的功能及其相关权限：

| 功能  | 权限| 说明|
|-------------|-----------|------------|
|读取模型|读取模型|默认情况下，公司租户中的所有用户都可以查看其公司型号。 此外，用户还可以查看其他公司共享的专用模型)  (。|
|管理访问权限|管理访问权限|管理 (为组织中的其他用户添加或删除) 的用户角色分配。|
|创建模型|创建模型|在公司模型存储库中创建模型。|
|发布模型|发布模型|发布模型，使任何人都可以查看模型。|

下表总结了可用于访问管理的模型存储库中支持的角色及其功能。

|角色|功能|
|----|----------|
|TenantAdministrator|管理访问权限，读取模型|
|创建者|创建模型，读取模型|
|Publisher|发布模型，读取模型|

#### <a name="passing-a-security-token-when-accessing-company-models-with-a-rest-api"></a>使用 REST API 访问公司模型时传递安全令牌

当你调用 REST Api 来管理专用或共享的公司模型时，必须为 JWT 格式的用户或服务主体提供授权令牌。 请参阅 " [其他信息](#additional-information) " 部分，了解如何获取用户或服务主体的 JWT 令牌。

当目标为公司模型或共享模型时，必须在 API 的授权 HTTP 标头中传递 JWT 标记。 针对公共模型，不需要 JWT 标记。

```csharp
// sample token
var authorizationToken = "eyJhbGciOiJIUzI1NiIsInR5cCTI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c";
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authorizationToken);
```

### <a name="view-company-or-shared-models"></a>查看公司或共享模型

您必须是存储库租户的 " *读者* " 角色的成员，或者必须与您共享该模型才能读取模型。 你可以查看已与你共享的未发布模型的列表，以及与你共享的已发布模型的列表。 默认情况下，用户可以读取其公司的模型、其他公司与他们共享的模型以及所有公共模型。

使用门户查看公司或共享模型：

1. 登录到 [Azure IoT 模型存储库门户](https://aka.ms/iotmodelrepo)。

1. 展开左侧窗格上的 " **公司模型** - **未发布** "

    ![查看公司模型](./media/concepts-model-repository/view-company-models.png)

1. 展开 " **共享模型"-** 在左窗格中未发布

    ![查看共享模型](./media/concepts-model-repository/view-shared-models.png)

若要使用 REST API 查看公司或共享模型，请参阅 [获取模型](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync) REST API 文档。 有关如何传入 HTTP 请求中的 JWT 授权标头的信息，请参阅 [使用 REST API 访问公司模型时传递安全令牌](#passing-a-security-token-when-accessing-company-models-with-a-rest-api) 。

```csharp
var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.GetAsync($"/models/{modelId}?api-version=2020-05-01-preview").ConfigureAwait(false);
```

若要使用 CLI 查看公司模型或共享模型，请参阅 Azure CLI [获取模型](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-show&preserve-view=true) "命令。

### <a name="manage-roles"></a>管理角色

租户管理员可以向存储库租户中的用户分配角色，以便他们能够创建专用于公司或组织的模型，发布模型，或管理其他用户的角色。

使用门户将用户添加到模型存储库租户角色：

1. 登录到 [Azure IoT 模型存储库门户](https://aka.ms/iotmodelrepo)。

1. 选择左窗格中的 " **访问管理** "，然后选择 " **+ 添加**"。 在 " **添加权限** " 窗格上，键入要添加到角色的用户的工作地址：

    ![添加工作地址](./media/concepts-model-repository/add-user.png)

1. 从 " **角色** " 下拉列表中选择要将用户添加到的角色。 然后选择 " **保存**"：

    ![选择角色](./media/concepts-model-repository/choose-role.png)

### <a name="upload-a-model"></a>上传模型

您必须是存储库租户的 " **创建者** " 角色的成员，才能将模型上载到公司模型存储库中。

默认情况下，这些模型不会发布，并且只能由组织中的用户访问。 您还可以将一个或多个未发布的模型与外部用户共享。

上传的模型是不可变的。

对于所有已上传的模型，这些模型的模型 Id 在所有存储库租户中必须是全局唯一的。

使用门户上载模型：

1. 登录到 [Azure IoT 模型存储库门户](https://aka.ms/iotmodelrepo)。

1. 展开左侧窗格中的 " **公司模型** "，然后选择 " **创建模型**"。 然后选择 " **导入 Json**"。

    ![创建模型](./media/concepts-model-repository/create-model.png)

1. 选择要上传的文件。 如果门户成功验证了模型，请选择 " **保存**"。

若要使用 REST API 上传模型，请参阅 [创建模型](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/createorupdateasync/createorupdateasync) API。 有关如何传入 HTTP 请求中的 JWT 授权标头的信息，请参阅 [使用 REST API 访问公司模型时传递安全令牌](#passing-a-security-token-when-accessing-company-models-with-a-rest-api) 。

```csharp
var httpContent = new StringContent(jsonLdModel, Encoding.UTF8, "application/json");
var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.PutAsync($"/models/{modelId}?api-version=2020-05-01-preview", httpContent).ConfigureAwait(false);
```

若要使用 CLI 上传模型，请参阅 Azure CLI [创建模型](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-create&preserve-view=true) "命令。

### <a name="publish-a-model"></a>发布模型

要发布模型，必须满足以下要求：

1. 你的组织需要是 [Microsoft 合作伙伴网络](https://docs.microsoft.com/partner-center/)的成员才能发布模型。 要创建合作伙伴中心帐户，请参阅[创建合作伙伴中心帐户](https://docs.microsoft.com/partner-center/mpn-create-a-partner-center-account)。 在你的帐户获得批准后，即可发布模型。 有关详细信息，请参阅[合作伙伴中心常见问题解答](https://support.microsoft.com/help/4340639/partner-center-account-faqs)。

2. 用户必须是存储库租户的 " *发布者* " 角色的成员。

你的组织中的用户创建和发布的模型将显示为 *已发布的模型*。 这些模型是公共的，可由 **公共模型**下的任何人找到。

使用门户发布模型：

1. 登录到 [Azure IoT 模型存储库门户](https://aka.ms/iotmodelrepo)。

2. 展开左侧窗格中的 " **公司模型** "，然后选择要发布的模型。 然后选择“发布”。

    ![发布模型](./media/concepts-model-repository/publish-model.png)

> [!NOTE]
> 如果收到通知，指出你没有 Microsoft 合作伙伴 (MPN) ID，请按照通知中的注册步骤操作。 有关详细信息，请参阅本部分开头的要求。

若要使用 REST API 发布模型，请参阅 [发布模型](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/createorupdateasync/createorupdateasync) REST API 文档。 提供查询字符串参数 `update-metadata=true` ，以便使用 REST API 发布模型。 有关如何传入 HTTP 请求中的 JWT 授权标头的信息，请参阅 [使用 REST API 访问公司模型时传递安全令牌](#passing-a-security-token-when-accessing-company-models-with-a-rest-api) 。

若要使用 CLI 发布模型，请参阅 Azure CLI [发布模型](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-publish&preserve-view=true) "命令。

> [!NOTE]
> 在运行认证测试之前，必须将模型发布到模型存储库中。 若要了解详细信息，请参阅 [如何认证 IoT 即插即用设备](howto-certify-device.md)。

### <a name="share-a-model"></a>共享模型

你可以共享你为外部组织的用户创建的公司模型。 通过这种方式，你可以允许协作者查看和开发专用公司模型的解决方案。

例如，设备制造商可能需要维护专用于公司或组织的模型。 他们可能会要求其设备功能保持机密。

跨公司或组织共享模型允许安全访问非公共模型。

使用门户共享公司模型：

- 如果你是模型的创建者，则在 "**公司模型**" 部分中查看该模型时，"**共享**" 和 "**共享**" 按钮处于活动状态。

    ![共享模型](./media/concepts-model-repository/share-model.png)

- 若要与外部用户共享模型，请选择 " **共享**"。 在 " **共享模型** " 窗格中，输入外部用户的电子邮件地址，然后选择 " **保存**"。

- 若要查看已与共享模型的用户，请选择 " **与共享**"。

- 若要停止与特定用户共享模型，请在 " **共享方式** " 窗格中从用户列表中选择用户。 然后选择 " **删除** "，并在出现提示时确认你的选择。

    ![停止共享](./media/concepts-model-repository/stop-sharing.png)

## <a name="additional-information"></a>其他信息

使用 Azure AD 时，可能会发现以下主题很有用：

- 若要创建新的 Azure AD 租户，请参阅 [在 Azure AD 中创建新租户](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)。 大多数组织都有 Azure AD 租户。

- 若要将用户或来宾用户添加到 Azure AD 租户，请参阅 [使用 Azure AD 添加或删除用户](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory)。

- 若要将服务主体添加到 Azure AD 租户，请参阅 [如何使用门户创建可访问资源的 Azure AD 应用程序和服务主体](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)。

- 若要了解如何从 Azure AD 获取用于调用 REST Api 的 JWT 令牌，请参阅从 [Azure AD 获取用于从客户端应用程序授权请求的令牌](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app)。

## <a name="next-steps"></a>后续步骤

建议的下一步是查看 [IoT 即插即用的体系结构](concepts-architecture.md)。
