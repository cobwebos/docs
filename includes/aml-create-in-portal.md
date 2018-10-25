---
title: include 文件
description: include 文件
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 09/24/2018
ms.openlocfilehash: 0b95441fd2805308c601509f1afc477f72bde321
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2018
ms.locfileid: "49476356"
---
使用将所使用的 Azure 订阅的凭据登录到 [Azure 门户](https://portal.azure.com/)。 如果还没有 Azure 订阅，请现在就创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

门户的工作区仪表板仅在 Edge、Chrome 和 Firefox 浏览器上受支持。

   ![Azure 门户](./media/aml-create-in-portal/portal-dashboard.png)

在门户左上角选择“创建资源”。

   ![在 Azure 门户中创建资源](./media/aml-create-in-portal/portal-create-a-resource.png)

在搜索栏中输入“机器学习”。 选择搜索结果“机器学习服务工作区”。

   ![搜索工作区](./media/aml-create-in-portal/allservices-search.PNG)

在“机器学习服务工作区”窗格中，首先滚动到底部并选择“创建”。

   ![创建](./media/aml-create-in-portal/portal-create-button.png)

在“机器学习服务工作区”窗格中，配置工作区。

   字段|Description
   ---|---
   工作区名称 |输入用于标识工作区的唯一名称。 在这里，我们使用 docs-ws。 名称在整个资源组中必须唯一。 使用易于记忆且区别于其他人所创建工作区的名称。  
   订阅 |选择要使用的 Azure 订阅。
   资源组 | 使用订阅中的现有资源组，或者输入一个名称，创建新的资源组。 资源组是用于保存 Azure 解决方案相关资源的容器。 在这里，我们使用 docs-aml。 
   位置 | 选择最靠近用户和数据资源的位置。 这是用于创建工作区的位置。

   ![创建工作区](./media/aml-create-in-portal/workspace-create.png)

选择“创建”以开始进行创建。 创建工作区可能需要一些时间。

若要检查部署状态，请选择工具栏上的“通知”图标（铃铛）。

   ![工作区创建状态](./media/aml-create-in-portal/notifications.png)

完成创建后，会显示部署成功消息。 通知部分也会出现该消息。 若要查看新工作区，请选择“转到资源”。
