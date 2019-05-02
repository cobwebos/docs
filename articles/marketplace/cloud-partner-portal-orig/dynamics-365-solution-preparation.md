---
title: Dynamics 365 解决方案准备
description: 用于打包、安装和卸载组件的框架
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricarod.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 919feb39c9cd84f8da0ef89827ad3e83e0eb9bc5
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935256"
---
# <a name="dynamics-365-solution-preparation"></a>Dynamics 365 解决方案准备

Dynamics 365 解决方案系统是用于打包、安装和卸载提供特定业务功能的组件的框架。 ISV 和其他 Microsoft Dynamics 365 合作伙伴使用解决方案来分发他们创建的扩展。

如果你现在是 Dynamics 365 (xRM) ISV，则很可能已经创建了托管解决方案并拥有 solution.zip 文件。 在你的解决方案中，请确保“显示名称”和“说明”字段反映你希望客户看到的内容。 这些内容会显示在 CRM 在线管理中心中。

![CRMScreenShot1](media/CRMScreenShot1.png)

_**注意：** 在下面的包示例中，我们假设解决方案名称为“SampleSolution.zip”_

如果你是新手 ISV，可以在此处获得有关创建解决方案的更多详细信息：[https://msdn.microsoft.com/library/gg334530.aspx](https://msdn.microsoft.com/library/gg334530.aspx)

如果你的解决方案需要支持数据：

* 在测试环境中创建示例数据
* 使用配置迁移工具创建包含数据比较规则的架构。
* 使用项目文件保存配置架构。 如果更新配置数据，稍后将需要此功能。
* 导出配置数据。 请确保为导出文件指定一个对导出有意义的名称。
