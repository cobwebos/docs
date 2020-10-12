---
author: baanders
description: 用于验证 Azure 数字孪生安装程序中的角色分配的包含文件
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: fdb3bd034d93e623037be9fa0721a805924af5c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88864772"
---
检查是否已成功设置角色分配的一种方法是在 [Azure 门户](https://portal.azure.com)中查看 Azure 数字孪生实例的角色分配。 在 Azure 门户中中转到 Azure 数字孪生实例 (可以在 [Azure 数字孪生实例](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) 的页面上查找，也可以在门户搜索栏中搜索其名称) 。

然后，在 "访问控制" 下查看其所有已分配的角色 * (IAM) > 角色分配*。 用户应在列表中显示 *Azure 数字孪生所有者 (预览) *的角色。 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="在 Azure 门户中查看 Azure 数字孪生实例的角色分配":::