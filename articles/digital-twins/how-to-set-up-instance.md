---
title: 创建 Azure 数字孪生实例
titleSuffix: Azure Digital Twins
description: 请参阅如何设置 Azure 数字孪生服务的实例。
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: fecacbd2c7c6549a1321367157bb179321779ca9
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86206518"
---
# <a name="set-up-an-azure-digital-twins-instance"></a>设置 Azure 数字孪生实例

本文将指导你完成设置新的 Azure 数字孪生实例的基本步骤。 这包括创建实例，并为自己将[Azure Active Directory (AAD) ](../active-directory/fundamentals/active-directory-whatis.md)权限分配给实例。

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="set-up-an-azure-digital-twins-instance"></a>设置 Azure 数字孪生实例

接下来，你将创建新的 Azure 资源组，以便在本操作方法中使用。 然后，可以在该资源组中**创建 Azure 数字孪生的新实例**。 

你还需要为你的实例提供一个名称，并为部署选择一个区域。 若要查看哪些区域支持 Azure 数字孪生，请访问[按区域提供的 azure 产品](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)。

>[!NOTE]
> 新实例的名称在该区域中必须是唯一的 (这意味着，如果该区域中的另一个 Azure 数字孪生实例已在使用所选的名称，则必须选择不同的名称) 。

用以下命令创建资源组和实例：

```azurecli
az group create --location <region> --name <name-for-your-resource-group>
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

这些命令的结果如下所示，输出有关已创建的资源的信息：

:::image type="content" source="media/how-to-set-up-instance/create-instance.png" alt-text="成功创建资源组和 Azure 数字孪生实例命令窗口":::

请注意输出中的 Azure 数字孪生实例的*主机名*、*名称*和*资源*组。 这些是你在继续使用 Azure 数字孪生实例时可能需要的所有关键值，用于设置身份验证和相关的 Azure 资源。

> [!TIP]
> 你可以随时通过运行来查看这些属性和实例的所有属性 `az dt show --dt-name <your-Azure-Digital-Twins-instance>` 。

### <a name="assign-azure-active-directory-permissions"></a>分配 Azure Active Directory 权限

Azure 数字孪生使用[Azure Active Directory (AAD) ](../active-directory/fundamentals/active-directory-whatis.md)用于基于角色的访问控制 (RBAC) 。 这意味着，在对 Azure 数字孪生实例进行数据平面调用之前，必须先为自己分配具有这些权限的角色。

若要将 Azure 数字孪生用于客户端应用程序，你还需要确保客户端应用可以针对 Azure 数字孪生进行身份验证。 这是通过设置 Azure Active Directory (AAD) 应用注册来完成的，你可以在[如何：验证客户端应用程序](how-to-authenticate-client.md)中了解相关信息。

#### <a name="assign-yourself-a-role"></a>为自己分配角色

使用与 Azure 订阅上的 AAD 租户关联的电子邮件，在 Azure 数字孪生实例中为自己创建角色分配。 

若要实现此目的，需要在 Azure 订阅中将其作为所有者进行分类。 可以通过运行命令来检查此项 `az role assignment list --assignee <your-Azure-email>` ，并在输出中验证*roleDefinitionName*值是否为 "*所有者*"。 如果发现值为 "*参与者*" 或 "*所有者*" 以外的其他值，请与订阅管理员联系，以在订阅中授予权限。 它们可以提升你对整个订阅的角色，以便可以运行以下命令，或者所有者可以代表你运行以下命令，为你设置 Azure 数字孪生权限。

若要在 Azure 数字孪生实例中分配用户 "所有者" 权限，请使用以下命令 (必须由 Azure 订阅) 的所有者运行：

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-AAD-email>" --role "Azure Digital Twins Owner (Preview)"
```

此命令的结果将输出有关已创建的角色分配的信息。

> [!TIP]
> 如果改为收到*400： BadRequest*错误，请运行以下命令获取用户的*ObjectID* ：
> ```azurecli
> az ad user show --id <your-AAD-email> --query objectId
> ```
> 然后，使用用户的*对象 ID*和电子邮件重复角色分配命令。

现在，你已准备好可供使用的 Azure 数字孪生实例，以及管理该实例的权限。

## <a name="next-steps"></a>后续步骤

请参阅如何设置客户端应用并对其进行身份验证，以便使用实例：
* [如何：对客户端应用程序进行身份验证](how-to-authenticate-client.md)
