---
title: include 文件
description: include 文件
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 06/20/2019
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: 72a69359d412a7560472fbb73ec525ab5d4a4fce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "67325787"
---
1. 登录到 Azure[门户]。

2. 单击 **"创建资源**"。

3. 在搜索框中，键入“Web 应用”****。
    
4. 在结果列表中，从“市场”中选择“Web 应用”****。

5. 选择**订阅****和资源组**（选择现有资源组_或_创建新资源组（使用与应用相同的名称）。

6. 选择 Web 应用的唯一**名称**。

7. 选择默认**的"发布"** 选项作为**代码**。

8. 在**运行时堆栈**中，您需要在**ASP.NET**或**节点**下选择一个版本。 如果要构建 .NET 后端，请在"ASP.NET"下选择一个版本。 否则，如果您的目标是基于节点的应用程序，请从 Node 中选择一个版本。

9. 选择正确的**操作系统**，Linux 或 Windows。 

10. 选择要部署此应用**的区域**。 

11. 选择适当的**应用服务计划**并点击 **"审阅"并创建**。 

12. 在 **"资源组**"下，选择现有资源组_或_创建新资源组（使用与应用相同的名称）。

13. 单击 **“创建”**。 稍等几分钟，等服务部署成功后再继续。 查看门户标题中的通知（铃铛）图标以获取状态更新。

14. 部署完成后，单击 **"部署详细信息**"部分，然后单击 Microsoft 类型**的资源.Web/网站**。 它将导航到刚刚创建的应用服务 Web 应用。 

15. 单击“设置”**** 下的“配置”**** 边栏选项卡，并在“应用程序设置”**** 中，单击“新建应用程序设置”**** 按钮。

16. 在“添加/编辑应用程序设置”**** 页中，将**名称**输入为 **MobileAppsManagement_EXTENSION_VERSION**，将“值”输入为 **latest** 并点击“确定”。

你已准备好将此新创建的应用服务 Web 应用用作移动应用。

<!-- URLs. -->
[Azure 门户]: https://portal.azure.com/