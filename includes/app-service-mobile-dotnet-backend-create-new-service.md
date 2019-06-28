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
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2019
ms.locfileid: "67325787"
---
1. 登录到 [Azure 门户]。

2. 单击“创建资源”  。

3. 在搜索框中，键入**Web 应用**。
    
4. 在结果列表中，选择**Web 应用**从 Marketplace。

5. 选择你**订阅**并**资源组**(选择现有资源组_或_创建新组 （作为您的应用程序使用相同的名称）)。

6. 选择一个唯一**名称**的 web 应用。

7. 选择默认**发布**选项作为**代码**。

8. 在中**运行时堆栈**，你需要选择下的一个版本**ASP.NET**或**节点**。 如果要构建一个.NET 后端，选择在 ASP.NET 下的一个版本。 否则如果你面向的基于节点应用程序，从选择一个版本的节点。

9. 选择正确**操作系统**，Linux 或 Windows。 

10. 选择**区域**你想要部署此应用。 

11. 选择适当**应用服务计划**并点击**查看和创建**。 

12. 在“资源组”下  ，选择现有资源组，_或_创建新组（使用与应用相同的名称）。

13. 单击**创建**。 稍等几分钟，等服务部署成功后再继续。 查看门户标题中的通知（铃铛）图标以获取状态更新。

14. 完成部署后，单击**部署详细信息**部分，然后单击资源的类型上**microsoft.web/sites**。 它会将您导航到刚刚创建应用服务 Web 应用。 

15. 单击**配置**边栏选项卡下的**设置**并在**应用程序设置**，单击**新建应用程序设置**按钮。

16. 在中**添加/编辑应用程序设置**页上，输入**名称**作为**MobileAppsManagement_EXTENSION_VERSION**和值作为**最新**和点击确定。

您将使用这个新创建的应用服务 Web 应用为移动应用。

<!-- URLs. -->
[Azure 门户]: https://portal.azure.com/