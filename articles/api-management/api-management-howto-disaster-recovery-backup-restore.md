---
title: 使用 Azure API 管理中的备份和还原实现灾难恢复 | Microsoft 文档
description: 了解如何在 Azure API 管理中使用备份和还原执行灾难恢复。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: apimpm
ms.openlocfilehash: 4135bd66e839037d7db694cb3c6df8f3905222e6
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39283082"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>如何使用 Azure API 管理中的服务备份和还原实现灾难恢复

通过 Azure API 管理选择发布和管理 API，即可充分利用了许多容错和基础结构功能，否则必须设计、实现和管理这些功能。 Azure 平台通过花费少量成本消除大量潜在故障。

若要从影响托管着 API 管理服务的区域的可用性问题中恢复，应随时准备好在不同的区域中重建服务。 根据可用性目标和恢复时间目标，可能要在一个或多个区域中保留备份服务，并尝试使其配置和内容与活动服务保持同步。 服务“备份和还原”功能为实现灾难恢复策略提供了必要的构建基块。

本指南介绍了如何对 Azure 资源管理器请求进行身份验证以及如何备份和还原 API 管理服务实例。

> [!NOTE]
> 为灾难恢复备份和还原 API 管理服务实例的过程还可用于为暂存之类的方案复制 API 管理服务实例。
>
> 每个备份都会在 30 天后过期。 如果在 30 天有效期到期后尝试还原备份，还原会失败并显示 `Cannot restore: backup expired` 消息。
>
>

## <a name="authenticating-azure-resource-manager-requests"></a>对 Azure 资源管理器请求进行身份验证

> [!IMPORTANT]
> 用于还原和备份的 REST API 使用 Azure 资源管理器，并且具有与用于管理 API 管理实体的 REST API 不同的身份验证机制。 本部分中的步骤介绍如何对 Azure 资源管理器请求进行身份验证。 有关详细信息，请参阅[对 Azure 资源管理器请求进行身份验证](http://msdn.microsoft.com/library/azure/dn790557.aspx)。
>
>

使用 Azure 资源管理器对资源所进行的所有任务都必须使用以下步骤通过 Azure Active Directory 进行身份验证：

* 向 Azure Active Directory 租户添加应用程序。
* 为添加的应用程序设置权限。
* 获取用于对 Azure 资源管理器的请求进行身份验证的令牌。

### <a name="create-an-azure-active-directory-application"></a>创建 Azure Active Directory 应用程序

1. 登录到 [Azure 门户](https://portal.azure.com)。 
2. 使用包含 API 管理服务实例的订阅导航到 Azure Active Directory 中的“应用注册”选项卡（Azure Active Directory > 管理/应用注册）。

    > [!NOTE]
    > 如果 Azure Active Directory 默认目录对帐户不可见，请联系 Azure 订阅的管理员以向帐户授予所需权限。
3. 单击“新建应用程序注册”。

    此时将在右侧显示“创建”窗口。 可以在其中输入 AAD 应用相关信息。
4. 输入应用程序的名称。
5. 对于应用程序类型，选择“本机”。
6. 输入占位符 URL，如为“重定向 URI”输入 `http://resources`，因为它是必填字段，但以后不使用该值。 单击此复选框保存应用程序。
7. 单击“创建”。

### <a name="add-an-application"></a>添加应用程序

1. 创建应用程序后，单击“设置”。
2. 单击“所需权限”。
3. 单击“+添加”。
4. 按“选择 API”。
5. 选择“Windows Azure 服务管理 API”。
6. 按“选择”。 

    ![添加权限](./media/api-management-howto-disaster-recovery-backup-restore/add-app.png)

7. 单击新添加的应用程序旁边的“委派的权限”，选中“访问 Azure 服务管理(预览版)”复选框。
8. 按“选择”。
9. 单击“授予权限”。

### <a name="configuring-your-app"></a>配置应用

在调用生成备份并还原它的 API 之前，必须获取令牌。 以下示例使用 [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet 包来检索令牌。

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace GetTokenResourceManagerRequests
{
    class Program
    {
        static void Main(string[] args)
        {
            var authenticationContext = new AuthenticationContext("https://login.microsoftonline.com/{tenant id}");
            var result = authenticationContext.AcquireTokenAsync("https://management.azure.com/", "{application id}", new Uri("{redirect uri}"), new PlatformParameters(PromptBehavior.Auto)).Result;

            if (result == null) {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }

            Console.WriteLine(result.AccessToken);

            Console.ReadLine();
        }
    }
}
```

根据以下说明替换 `{tentand id}`、`{application id}` 和 `{redirect uri}`。

1. 将 `{tenant id}` 替换为你创建的 Azure Active Directory 应用程序的租户 ID。 可通过单击“应用注册” -> “终结点”访问此 ID。

    ![终结点][api-management-endpoint]
2. 将 `{application id}` 替换为通过导航到“设置”页面获得的值。
3. 将 `{redirect uri}` 替换为 Azure Active Directory 应用程序“重定向 URI”选项卡上的值。

    指定这些值后，代码示例应返回类似于以下示例的令牌：

    ![令牌][api-management-arm-token]

    > [!NOTE]
    > 该令牌可能在一段时间后过期。 再次执行示例代码以生成新令牌。

## <a name="calling-the-backup-and-restore-operations"></a>调用备份和还原操作

REST API 为 [Api 管理服务 - 备份](https://docs.microsoft.com/rest/api/apimanagement/apimanagementservice/backup)和 [Api 管理服务 - 还原](https://docs.microsoft.com/rest/api/apimanagement/apimanagementservice/restore)。

在调用以下部分中所述的“备份和还原”操作之前，为 REST 调用设置授权请求标头。

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="step1"></a>备份 API 管理服务
若要备份 API 管理服务问题，请发送以下 HTTP 请求：

```
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

其中：

* `subscriptionId` - 包含尝试备份的 API 管理服务的订阅的 ID
* `resourceGroupName` - Azure API 管理服务的资源组名称
* `serviceName` - 正在创建其备份的 API 管理服务的名称，在创建时指定
* `api-version` - 替换为 `2018-06-01-preview`

在请求正文中，指定目标 Azure 存储帐户名称、访问密钥、blob 容器名称和备份名称：


```json
{
  "storageAccount": "{storage account name for the backup}",
  "accessKey": "{access key for the account}",
  "containerName": "{backup container name}",
  "backupName": "{backup blob name}"
}
```

将 `Content-Type` 请求标头的值设置为 `application/json`。

备份是长时间运行的操作，可能需要数分钟才能完成。  如果请求已成功并且已启动备份过程，将收到带有 `Location` 标头的 `202 Accepted` 响应状态代码。  向 `Location` 标头中的 URL 发出“GET”请求以查明操作状态。 当备份正在进行时，将继续收到“202 已接受”状态代码。 响应代码 `200 OK` 指示备份操作成功完成。

发出备份请求时请注意以下限制。

* 请求正文中指定的**容器****必须存在**。
* 当备份正在进行时，**不应尝试任何服务管理操作**，如 SKU 升级或降级、域名更改等。
* 从创建时开始，**备份还原仅保证 30 天**。
* 用于创建分析报表的**用法数据****不包括**在备份中。 使用 [Azure API 管理 REST API][Azure API Management REST API] 定期检索分析报表以保证安全。
* 执行服务备份的频率将影响恢复点目标。 为最大程度地降低影响，建议实现定期备份，并且在对 API 管理服务进行重大更改后执行按需备份。
* 备份操作正在进行时对服务配置（例如 API、策略、开发人员门户外观）所做的**更改****可能不包含在备份中，因此将丢失**。

### <a name="step2"></a>还原 API 管理服务
若要从之前创建的备份还原 API 管理服务，请发出以下 HTTP 请求：

```
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

其中：

* `subscriptionId` - 包含正在将备份还原到的 API 管理服务的订阅 ID
* `resourceGroupName` - 采用“Api-Default-{service-region}”形式的字符串，其中 `service-region` 标识托管正在将备份还原到的 API 管理服务的 Azure 区域，例如 `North-Central-US`
* `serviceName` - 正在还原到的 API 管理服务的名称，在创建时指定
* `api-version` - 替换为 `2018-06-01-preview`

在请求正文中，指定备份文件位置，即 Azure 存储帐户名称、访问密钥、blob 容器名称和备份名称：

```json
{
  "storageAccount": "{storage account name for the backup}",
  "accessKey": "{access key for the account}",
  "containerName": "{backup container name}",
  "backupName": "{backup blob name}"
}
```

将 `Content-Type` 请求标头的值设置为 `application/json`。

还原是长时间运行的操作，可能需要长达 30 分钟或更长时间才能完成。 如果请求已成功并且已启动还原过程，将收到带有 `Location` 标头的 `202 Accepted` 响应状态代码。 向 `Location` 标头中的 URL 发出“GET”请求以查明操作状态。 当还原正在进行时，将继续收到“202 已接受”状态代码。 响应代码 `200 OK` 指示还原操作成功完成。

> [!IMPORTANT]
> 要还原到的服务的 **SKU** 必须与正在还原的已备份服务的 SKU **匹配**。
>
> 还原操作正在进行时对服务配置（例如 API、策略、开发人员门户外观）所做的**更改****可能会被覆盖**。

> [!NOTE]
> 也可分别使用 Powershell *Backup-AzureRmApiManagement* 和 *Restore-AzureRmApiManagement* 命令执行备份和还原操作。

## <a name="next-steps"></a>后续步骤
查看以下 Microsoft 博客了解备份/还原过程的两种不同的演练。

* [复制 Azure API 管理帐户](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
* [Azure API 管理：备份和还原配置](http://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
  * Stuart 详述的方法与官方指南不匹配，但非常有趣。

[Backup an API Management service]: #step1
[Restore an API Management service]: #step2


[Azure API Management REST API]: http://msdn.microsoft.com/library/azure/dn781421.aspx

[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png

[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
