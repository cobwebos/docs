---
author: baanders
description: Azure 数字孪生安装程序中的包含角色要求的文件
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 2f10a6915a3edf673316ae9151b6052442678ef9
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87832310"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites-permission-requirements"></a>先决条件：权限要求

若要完成本文中的所有步骤，需要在 Azure 订阅中将其作为所有者进行分类。 

您可以通过在 Cloud Shell 中运行以下命令来检查您的权限级别：

```azurecli-interactive
az role assignment list --assignee <your-Azure-email>
```

> [!NOTE]
> 如果此命令返回一个错误，指出 CLI**在 graph 数据库中找不到用户或服务主体**：
>
> 在本文的其余部分，请使用用户的*对象 ID* ，而不是电子邮件。 对于个人 Microsoft 帐户 (的用户，可能会发生这种情况， [msa) ](https://account.microsoft.com/account)。 
>
> 使用[Azure Active Directory 用户](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)的 "Azure 门户" 页选择用户帐户并打开其详细信息。 复制用户的*ObjectID*：
>
> :::image type="content" source="../articles/digital-twins/media/includes/user-id.png" alt-text="Azure 门户突出显示 "对象 ID" 字段中的 GUID 的用户页面视图" lightbox="../articles/digital-twins/media/includes/user-id.png":::
>
> 然后，使用用户的*对象 ID*和电子邮件重复 "角色分配列表" 命令。

运行角色分配列表命令后，如果你是所有者，则 `roleDefinitionName` 输出中的值为*owner*：

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/cloud-shell/owner-role.png" alt-text="显示 az role 赋值 list 命令输出的 Cloud Shell 窗口":::

如果发现值为 "*参与者*" 或 "*所有者*" 以外的其他内容，则可以通过以下方式之一继续：
* 请与订阅所有者联系，并请求所有者自行完成本文中所述的步骤
* 联系你的订阅所有者或订阅上具有用户访问管理员角色的某人，并请求他们将你提升为订阅的所有者，这样你就有权继续操作。 这是否合适取决于你的组织和其中的角色。