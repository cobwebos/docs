---
author: baanders
description: 用于验证 Azure 数字孪生安装程序中的角色分配的包含文件
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: e5e4bced12ffe9dc674b25a9a6513218cf8989e4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87095963"
---
检查是否已成功设置角色分配的一种方法是在 Azure 门户中查看 Azure 数字孪生实例的角色分配。 在[Azure 数字孪生实例](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances)的门户页中，选择要检查的实例的名称。 然后，在 "*访问控制（IAM）*" 下查看其分配的所有角色 > 角色分配。 用户应在列表中显示一个角色为 " *Azure 数字孪生所有者（预览版）*"。 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/verify-role-assignment.png" alt-text="在 Azure 门户中查看 Azure 数字孪生实例的角色分配":::