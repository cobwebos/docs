---
author: baanders
description: 用于验证 Azure 数字孪生安装程序中的角色分配的包含文件
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: e651b02bf72ced58b6cba637a68ace3258514176
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87405550"
---
检查是否已成功设置角色分配的一种方法是在[Azure 门户](https://portal.azure.com)中查看 Azure 数字孪生实例的角色分配。 请在[Azure 数字孪生实例](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances)的门户页面上 (你可以使用此链接，或在门户搜索栏) 中搜索该链接，然后选择你想要检查的实例的名称。 

然后，在 "访问控制" 下查看其所有已分配的角色* (IAM) > 角色分配*。 用户应在列表中显示*Azure 数字孪生所有者 (预览) *的角色。 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="在 Azure 门户中查看 Azure 数字孪生实例的角色分配":::