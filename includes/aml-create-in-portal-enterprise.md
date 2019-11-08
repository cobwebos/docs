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
ms.date: 10/04/2019
ms.openlocfilehash: c1f4b3d60c0b8af96d851ce54c2aac57c76335a7
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73633618"
---
1. 使用 Azure 订阅的凭据登录到 [Azure 门户](https://portal.azure.com/)。 

1. 在 Azure 门户的左上角，选择“+ 创建资源”  。

      ![创建新资源](media/aml-create-in-portal/create-workspace.gif)

1. 使用搜索栏查找“机器学习”  。

1. 选择“机器学习”** **。

1. 在“机器学习”窗格中，选择“创建”以开始   。

1. 提供以下信息来配置新工作区：

   字段|说明 
   ---|---
   工作区名称 |输入用于标识工作区的唯一名称。 本示例使用 docs-ws  。 名称在整个资源组中必须唯一。 使用易于记忆且区别于其他人所创建工作区的名称。  
   Subscription |选择要使用的 Azure 订阅。
   Resource group | 使用订阅中的现有资源组，或者输入一个名称以创建新的资源组。 资源组保存 Azure 解决方案的相关资源。 本示例使用 docs-aml  。 
   位置 | 选择离你的用户和数据资源最近的位置来创建工作区。
   工作区版本 | 选择“Enterprise”  。  本教程需要使用 Enterprise 版本。  Enterprise 版本处于预览阶段，目前不会增加任何额外成本。  

1. 完成工作区配置后，选择“创建”  。 

   > [!Warning] 
   > 在云中创建工作区可能需要几分钟时间。

   完成创建后，会显示部署成功消息。 
 
 1. 若要查看新工作区，请选择“转到资源”  。

