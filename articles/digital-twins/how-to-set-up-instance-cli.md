---
title: 设置实例和身份验证 (CLI)
titleSuffix: Azure Digital Twins
description: 请参阅如何使用 CLI 设置 Azure 数字孪生服务的实例
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: bb5ff0bf7347b87fdc3a103a03f9ff58279a367d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320757"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-cli"></a>设置 Azure 数字孪生实例和 (CLI 的身份验证) 

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

本文介绍 **设置新的 Azure 数字孪生实例**的步骤，包括创建实例和设置身份验证。 完成本文后，你将拥有一个可开始对进行编程的 Azure 数字孪生实例。

本文的此版本通过使用 CLI 逐个手动完成这些步骤。
* 若要使用 Azure 门户手动完成这些步骤，请参阅本文的门户版本： [*如何：设置实例和身份验证 (门户) *](how-to-set-up-instance-portal.md)。
* 若要通过使用部署脚本示例的自动安装运行，请参阅本文的脚本编写版本： how [*to： Set a instance and authentication (script) *](how-to-set-up-instance-scripted.md)。

[!INCLUDE [digital-twins-setup-steps-prereq.md](../../includes/digital-twins-setup-steps-prereq.md)]
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-cloud-shell-session"></a>设置 Cloud Shell 会话
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-the-azure-digital-twins-instance"></a>创建 Azure 数字孪生实例

在本部分中，将使用 Cloud Shell 命令 **创建 Azure 数字孪生的新实例** 。 需要提供：
* 要在其中部署该资源的资源组。 如果尚未记住现有的资源组，可以使用以下命令创建一个资源组：
    ```azurecli
    az group create --location <region> --name <name-for-your-resource-group>
    ```
* 部署的区域。 若要查看哪些区域支持 Azure 数字孪生，请访问 [*按区域提供的 azure 产品*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)。
* 实例的名称。 新实例的名称在你的订阅的区域内必须是唯一的 (这意味着，如果你的订阅在已使用所选名称的区域中有另一个 Azure 数字孪生实例，则系统将要求你选择不同的名称) 。

在以下命令中使用这些值来创建实例：

```azurecli
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

### <a name="verify-success-and-collect-important-values"></a>验证成功并收集重要值

如果已成功创建实例，Cloud Shell 中的结果如下所示，输出有关已创建资源的信息：

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/create-instance.png" alt-text="成功创建资源组和 Azure 数字孪生实例命令窗口":::

请注意输出中的 Azure 数字孪生实例的 *主机名*、 *名称*和 *资源* 组。 这些是你在继续使用 Azure 数字孪生实例时可能需要的所有重要值，用于设置身份验证和相关的 Azure 资源。 如果其他用户将对该实例进行编程，则应该与它们共享这些值。

> [!TIP]
> 你可以随时通过运行来查看这些属性和实例的所有属性 `az dt show --dt-name <your-Azure-Digital-Twins-instance>` 。

现已准备好使用 Azure 数字孪生实例。 接下来，你将为适当的 Azure 用户授予管理权限。

## <a name="set-up-user-access-permissions"></a>设置用户访问权限

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

使用以下命令分配角色 (必须由在 Azure 订阅) 中具有 [足够权限](#prerequisites-permission-requirements) 的用户运行。 命令要求用户在应分配角色的用户的 Azure AD 帐户上传递 *用户主体名称* 。 在大多数情况下，这将与 Azure AD 帐户上的用户电子邮件匹配。

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-user-principal-name-of-user-to-assign>" --role "Azure Digital Twins Owner (Preview)"
```

此命令的结果将输出有关已创建的角色分配的信息。

> [!NOTE]
> 如果此命令返回一个错误，指出 CLI **在 graph 数据库中找不到用户或服务主体**：
>
> 改为使用用户的 *对象 ID* 分配角色。 对于个人 Microsoft 帐户 (的用户，可能会发生这种情况， [msa) ](https://account.microsoft.com/account)。 
>
> 使用 [Azure Active Directory 用户](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) 的 "Azure 门户" 页选择用户帐户并打开其详细信息。 复制用户的 *ObjectID*：
>
> :::image type="content" source="media/includes/user-id.png" alt-text="成功创建资源组和 Azure 数字孪生实例命令窗口" lightbox="media/includes/user-id.png":::
>
> 然后，使用上面参数的用户 *对象 ID* 重复角色分配列表命令 `assignee` 。

### <a name="verify-success"></a>验证是否成功

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

现在，你已准备好使用 Azure 数字孪生实例，并已分配了管理它的权限。 接下来，你将设置对客户端应用程序的访问权限。

## <a name="set-up-access-permissions-for-client-applications"></a>设置客户端应用程序的访问权限

[!INCLUDE [digital-twins-setup-app-registration.md](../../includes/digital-twins-setup-app-registration.md)]

若要创建应用注册，需要提供 Azure 数字孪生 Api 的资源 Id，以及 API 的基准权限。

在工作目录中，创建一个新文件，并输入以下 JSON 代码片段来配置这些详细信息： 

```json
[{
    "resourceAppId": "0b07f429-9f4b-4714-9392-cc5e8e80c8b0",
    "resourceAccess": [
     {
       "id": "4589bd03-58cb-4e6c-b17f-b580e39652f8",
       "type": "Scope"
     }
    ]
}]
``` 

将此文件保存为 _**manifest.js**_。

> [!NOTE] 
> 在某些位置，"友好" 的可读字符串 `https://digitaltwins.azure.net` 可用于 Azure 数字孪生资源应用 ID 而不是 GUID `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` 。 例如，在此文档集中，许多示例将使用 MSAL 库进行身份验证，并且可以使用友好字符串。 但是，在创建应用注册的这一步中，需要 ID 的 GUID 格式，如上面所示。 

接下来，将此文件上传到 Cloud Shell。 在 Cloud Shell 窗口中，单击 "上传/下载文件" 图标，然后选择 "上传"。

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="成功创建资源组和 Azure 数字孪生实例命令窗口":::
导航到刚刚创建的 *manifest.js* ，并单击 "打开"。

接下来，运行以下命令以创建应用注册，并使用 *公用客户端/本机 (移动 & 桌面) * 回复 URL `http://localhost` 。 根据需要替换占位符：

```azurecli
az ad app create --display-name <name-for-your-app-registration> --native-app --required-resource-accesses manifest.json --reply-url http://localhost
```

下面是此命令的输出摘录，显示已创建的注册的相关信息：

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/new-app-registration.png" alt-text="成功创建资源组和 Azure 数字孪生实例命令窗口":::

### <a name="verify-success"></a>验证是否成功

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

接下来，请验证注册上已正确设置上传的 *manifest.js* 的设置。 为此，请从菜单栏中选择 " *清单* "，以查看应用注册的清单代码。 滚动到代码窗口的底部，然后在下面的 *manifest.js* 中查找字段 `requiredResourceAccess` ：

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

### <a name="collect-important-values"></a>收集重要值

接下来，从菜单栏中选择 " *概述* " 以查看应用注册的详细信息：

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="成功创建资源组和 Azure 数字孪生实例命令窗口":::

记下**在页面上显示的***应用程序 (客户端) id*和*目录 (租户) id* 。 稍后将需要这些值对 [Azure 数字孪生 api 的客户端应用进行身份验证](how-to-authenticate-client.md)。 如果您不是将为此类应用程序编写代码的人，则需要与将要进行共享的人员共享这些值。

### <a name="other-possible-steps-for-your-organization"></a>组织的其他可能步骤

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>后续步骤

使用 Azure 数字孪生 CLI 命令测试实例上的单个 REST API 调用： 
* [az dt reference](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest&preserve-view=true)
* [*操作说明：使用 Azure 数字孪生 CLI*](how-to-use-cli.md)

或者，请参阅如何通过编写客户端应用的身份验证代码将客户端应用程序连接到实例：
* [*操作说明：编写应用身份验证代码*](how-to-authenticate-client.md)