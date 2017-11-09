---
title: "Azure 堆栈 （云操作员） 中发布的自定义应用商店项 |Microsoft 文档"
description: "作为 Azure 堆栈操作员，了解如何在 Azure 堆栈中发布的自定义应用商店项。"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 60871cbb-eed2-433c-a76d-d605c7aec06c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: erikje
ms.openlocfilehash: 7b5f976eb2d51eb86761a2bd0be6adb45ca87681
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="the-azure-stack-marketplace-overview"></a>Azure 堆栈应用商店概述

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

应用商店是服务、 应用程序和自定义 Azure 堆栈，如网络、 虚拟机、 存储和等等的资源的集合。 用户到这里来创建新资源和部署新的应用程序。 可以将它视为购物目录用户可以浏览并选择他们想要使用的项。 若要使用的应用商店项，用户必须订阅向其授予了对项的访问的产品。

作为 Azure 堆栈操作员，你决定要添加的项 （发布） 到应用商店。 你可以发布如数据库、 应用程序服务和等等。 这使它们对所有用户可见。 你可以发布创建的自定义项。 你也可以发布项从一个不断增长[的 Azure 应用商店项目列表](azure-stack-marketplace-azure-items.md)。 在将项发布到应用商店，用户可以看到它在五分钟内。

若要打开应用商店，请单击**新建**。

![](media/azure-stack-publish-custom-marketplace-item/image1.png)

## <a name="marketplace-items"></a>应用商店项
Azure 堆栈应用商店项是服务、 应用程序或你的用户可以下载和使用的资源。 Azure 堆栈应用商店的所有项都都对所有用户可见。

每个应用商店项具有：

* 资源设置的 Azure 资源管理器模板
* 元数据，如字符串、 图标和其他市场宣传资料
* 若要显示在门户中的项的格式设置信息

发布到应用商店的每一项使用的格式称为 Azure 库包 (azpkg)。 部署或运行时将资源添加 （与代码一样，使用软件或虚拟机映像的 zip 文件） 到 Azure 堆栈单独，不是应用商店项目的一部分。 

## <a name="next-steps"></a>后续步骤
[创建和发布的应用商店项](azure-stack-create-and-publish-marketplace-item.md)

