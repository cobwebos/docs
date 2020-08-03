---
author: baanders
description: Azure 数字孪生安装程序中的包含角色要求的文件
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 1714fac465531a2d3a5829e7080132510ccd1ec4
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407410"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites-permission-requirements"></a>先决条件：权限要求

若要完成本文中的所有步骤，需要在 Azure 订阅中将其作为所有者进行分类。 

您可以通过在 Cloud Shell 中运行以下命令来检查您的权限级别：

```azurecli-interactive
az role assignment list --assignee <your-Azure-email>
```

如果你是所有者，则 `roleDefinitionName` 输出中的值为*owner*：

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/cloud-shell/owner-role.png" alt-text="显示 az role 赋值 list 命令输出的 Cloud Shell 窗口":::

如果发现值为 "*参与者*" 或 "*所有者*" 以外的其他内容，则可以通过以下方式之一继续：
* 请与订阅所有者联系，并请求所有者自行完成本文中所述的步骤
* 联系你的订阅所有者或订阅上具有用户访问管理员角色的某人，并请求他们将你提升为订阅的所有者，这样你就有权继续操作。 这是否合适取决于你的组织和其中的角色。