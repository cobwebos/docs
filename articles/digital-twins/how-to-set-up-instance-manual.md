---
title: 设置实例和身份验证（手动）
titleSuffix: Azure Digital Twins
description: 请参阅如何设置 Azure 数字孪生服务的实例，包括正确的身份验证。 手动版本。
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 8d0e965360caab704bcf6c8f7d29e7bba421207e
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2020
ms.locfileid: "87125881"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-manual"></a>设置 Azure 数字孪生实例和身份验证（手动）

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

本文介绍**设置新的 Azure 数字孪生实例**的步骤，包括创建实例和设置身份验证。 完成本文后，你将拥有一个可开始对进行编程的 Azure 数字孪生实例。

本文的此版本逐个手动完成这些步骤。 若要通过使用部署脚本示例的自动安装运行，请参阅本文的脚本编写版本：操作[*方法：设置实例和身份验证（脚本化）*](how-to-set-up-instance-scripted.md)。

[!INCLUDE [digital-twins-setup-starter.md](../../includes/digital-twins-setup-starter.md)]

## <a name="set-up-cloud-shell-session"></a>设置 Cloud Shell 会话
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-the-azure-digital-twins-instance"></a>创建 Azure 数字孪生实例

在本部分中，将使用 Cloud Shell 命令**创建 Azure 数字孪生的新实例**。 需要提供：
* 要在其中部署该资源的资源组。 如果尚未记住现有的资源组，可以使用以下命令创建一个资源组：
    ```azurecli
    az group create --location <region> --name <name-for-your-resource-group>
    ```
* 部署的区域。 若要查看哪些区域支持 Azure 数字孪生，请访问[*按区域提供的 azure 产品*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)。
* 实例的名称。 新实例的名称在该区域中必须是唯一的（也就是说，如果该区域中的另一个 Azure 数字孪生实例已在使用你选择的名称，则系统将要求你选择其他名称）。

在以下命令中使用这些值来创建实例：

```azurecli
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

### <a name="verify-success"></a>验证是否成功

如果已成功创建实例，Cloud Shell 中的结果如下所示，输出有关已创建资源的信息：

:::image type="content" source="media/how-to-set-up-instance/create-instance.png" alt-text="成功创建资源组和 Azure 数字孪生实例命令窗口":::

请注意输出中的 Azure 数字孪生实例的*主机名*、*名称*和*资源*组。 这些是你在继续使用 Azure 数字孪生实例时可能需要的所有重要值，用于设置身份验证和相关的 Azure 资源。

> [!TIP]
> 你可以随时通过运行来查看这些属性和实例的所有属性 `az dt show --dt-name <your-Azure-Digital-Twins-instance>` 。

现已准备好使用 Azure 数字孪生实例。 接下来，你将为适当的 Azure 用户授予管理权限。

## <a name="set-up-your-users-access-permissions"></a>设置用户的访问权限

Azure 数字孪生使用[Azure Active Directory （Azure AD）](../active-directory/fundamentals/active-directory-whatis.md)来实现基于角色的访问控制（RBAC）。 这意味着，在用户可以对 Azure 数字孪生实例进行数据平面调用之前，必须先为该用户分配具有此权限的角色。

对于 Azure 数字孪生，此角色是_**Azure 数字孪生所有者（预览版）**_。 有关角色和安全性的详细信息，请参阅[*概念： Azure 数字孪生解决方案的安全性*](concepts-security.md)。

本部分将介绍如何通过与 Azure 订阅上的 Azure AD 租户关联的电子邮件，为 Azure 数字孪生实例中的用户创建角色分配。 根据你的角色和你对 Azure 订阅的权限，你可以自行设置此设置，也可以代表将管理 Azure 数字孪生实例的其他人对此进行设置。

### <a name="assign-the-role"></a>分配角色

若要授予用户管理 Azure 数字孪生实例的权限，必须在实例中为他们分配_**Azure 数字孪生所有者（预览版）**_ 角色。 

请注意，此角色不同于 .。。
* 整个 Azure 订阅的*所有者*角色。 *Azure 数字孪生所有者（预览版）* 是 Azure 数字孪生中的一个角色，其作用域为此单个 Azure 数字孪生实例。
* Azure 数字孪生中的*所有者*角色。 它们是两个不同的 Azure 数字孪生管理角色， *Azure 数字孪生所有者（预览版）* 是在预览期间用于管理的角色。

使用以下命令分配角色（必须由 Azure 订阅的所有者运行）：

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-email-of-user-to-assign>" --role "Azure Digital Twins Owner (Preview)"
```

此命令的结果将输出有关已创建的角色分配的信息。

> [!TIP]
> 如果改为收到*400： BadRequest*错误，请运行以下命令获取用户的*ObjectID* ：
> ```azurecli
> az ad user show --id <Azure-AD-email-of-user-to-assign> --query objectId
> ```
> 然后，使用用户的*对象 ID*替代其电子邮件，重复执行角色分配命令。

### <a name="verify-success"></a>验证是否成功

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

现在，你已准备好使用 Azure 数字孪生实例，并已分配了管理它的权限。 接下来，你将设置对客户端应用程序的访问权限。

## <a name="set-up-access-permissions-for-client-applications"></a>设置客户端应用程序的访问权限

设置 Azure 数字孪生实例后，通常可以通过创建的客户端应用程序与该实例进行交互。 要执行此操作，需要确保客户端应用能够对 Azure 数字孪生进行身份验证。 为此，可设置用于客户端应用程序的[Azure Active Directory （Azure AD）](../active-directory/fundamentals/active-directory-whatis.md) **应用注册**。

通过此应用注册，你可以配置对[Azure 数字孪生 api](how-to-use-apis-sdks.md)的访问权限。 以后，客户端应用程序将对应用程序注册进行身份验证，因此，会将配置的访问权限授予 Api。

>[!TIP]
> 作为订阅所有者，你可能更倾向于为每个新的 Azure 数字孪生实例设置新的应用注册，*或*只执行此操作一次，并建立单个应用注册，将在订阅中的所有 Azure 数字孪生实例之间共享。 这就是在 Microsoft 自己的租户中完成此操作的方式。

### <a name="create-the-registration"></a>创建注册

若要创建应用注册，需要提供 Azure 数字孪生 Api 的资源 Id，以及 API 的基准权限。 在工作目录中，打开一个新文件，然后输入以下 JSON 代码片段来配置这些详细信息： 

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

将此文件保存为*manifest.js*。

> [!NOTE] 
> 在某些位置，"友好" 的可读字符串 `https://digitaltwins.azure.net` 可用于 Azure 数字孪生资源应用 ID 而不是 GUID `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` 。 例如，在此文档集中，许多示例将使用 MSAL 库进行身份验证，并且可以使用友好字符串。 但是，在创建应用注册的这一步中，需要 ID 的 GUID 格式，如上面所示。 

在 Cloud Shell 窗口中，单击 "上传/下载文件" 图标，然后选择 "上传"。

:::image type="content" source="media/how-to-set-up-instance/cloud-shell-upload.png" alt-text="显示上传选项选择的 Cloud Shell 窗口":::
导航到刚刚创建的*manifest.js* ，并单击 "打开"。

接下来，运行以下命令以创建应用注册（根据需要替换占位符）：

```azurecli
az ad app create --display-name <name-for-your-app> --native-app --required-resource-accesses manifest.json --reply-url http://localhost
```

下面是此命令的输出摘录，显示已创建的注册的相关信息：

:::image type="content" source="media/how-to-set-up-instance/new-app-registration.png" alt-text="新 Azure AD 应用注册 Cloud Shell 输出":::

### <a name="verify-success"></a>验证是否成功

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

首先，请验证注册上是否已正确设置上传的*manifest.js*的设置。 为此，请从菜单栏中选择 "*清单*"，以查看应用注册的清单代码。 滚动到代码窗口的底部，然后在下面的*manifest.js*中查找字段 `requiredResourceAccess` ：

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

### <a name="collect-important-values"></a>收集重要值

接下来，从菜单栏中选择 "*概述*" 以查看应用注册的详细信息：

:::image type="content" source="media/how-to-set-up-instance/app-important-values.png" alt-text="应用注册重要值的门户视图":::

记下显示**在页面上**的*应用程序（客户端） id*和*目录（租户） id* 。 稍后将需要这些值对[Azure 数字孪生 api 的客户端应用进行身份验证](how-to-authenticate-client.md)。 如果您不是将为此类应用程序编写代码的人，则需要与将要进行共享的人员共享这些值。

### <a name="other-possible-steps-for-your-organization"></a>组织的其他可能步骤

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>后续步骤

请参阅如何通过编写客户端应用的身份验证代码将客户端应用程序连接到实例：
* [*操作说明：编写应用身份验证代码*](how-to-authenticate-client.md)
