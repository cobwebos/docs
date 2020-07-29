---
author: baanders
description: Azure 数字孪生安装程序中的包含 starter 信息的文件
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: dcc616dd8d30bfadb751df9ba24d71d72675428a
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87371442"
---
>[!NOTE]
>这些操作旨在由在 Azure 订阅上具有*所有者*角色的用户完成。 尽管有些部分可以在没有此提升权限的情况下完成，但需要所有者的合作来完全设置一个可用的实例。 有关详细信息，请查看下面的[*先决条件： Required 权限*](#prerequisites-permission-requirements)部分。

新的 Azure 数字孪生实例的完全安装由三个部分组成：
1. **创建实例**
2. **设置用户的访问权限**： azure 用户需要在实例上具有*Azure 数字孪生所有者（预览版）* 角色才能执行管理活动。 在此步骤中，你将为自己分配此角色（如果你是 Azure 订阅中的所有者），或获取订阅的所有者，以将其分配给你。
3. **设置客户端应用程序的访问权限**：通常会编写一个客户端应用程序，用于与实例进行交互。 为了使该客户端应用能够访问 Azure 数字孪生，需要在客户端应用程序将用于向实例进行身份验证的[Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md)中设置*应用注册*。

若要继续，你将需要一个 Azure 订阅。 你可以在[此处](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)免费设置一个。

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites-permission-requirements"></a>先决条件：权限要求

若要完成本文中的所有步骤，需要在 Azure 订阅中将其作为所有者进行分类。 

您可以通过在 Cloud Shell 中运行以下命令来检查您的权限级别：

```azurecli-interactive
az role assignment list --assignee <your-Azure-email>
```

如果你是所有者，则 `roleDefinitionName` 输出中的值为*owner*：

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/owner-role.png" alt-text="显示 az role 赋值 list 命令输出的 Cloud Shell 窗口":::

如果发现值为 "*参与者*" 或 "*所有者*" 以外的其他内容，则可以通过以下方式之一继续：
* 请与订阅所有者联系，并请求所有者自行完成本文中所述的步骤
* 联系你的订阅所有者或订阅上具有用户访问管理员角色的某人，并请求他们将你提升为订阅的所有者，这样你就有权继续操作。 这是否合适取决于你的组织和其中的角色。
