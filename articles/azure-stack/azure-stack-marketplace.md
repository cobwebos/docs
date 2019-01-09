---
title: 在 Azure Stack 中发布自定义的市场项（面向云操作员）| Microsoft Docs
description: 向 Azure Stack 操作员介绍了解如何在 Azure Stack 中发布自定义的市场项。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 60871cbb-eed2-433c-a76d-d605c7aec06c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 6c8a2cd746f1090e802f90a49f9a1f7469e98f5f
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2019
ms.locfileid: "54121509"
---
# <a name="azure-stack-marketplace-overview"></a>Azure Stack Marketplace 概述

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

Azure Stack Marketplace 是一系列服务、 应用程序和针对 Azure Stack 自定义的资源。 资源包括网络、 虚拟机、 存储和的详细信息。 使用应用商店创建新资源和部署新的应用程序;或者浏览并选择你想要使用的项。 若要使用某个市场项，用户必须订阅一个可让他们访问该项的产品/服务。

Azure Stack 操作员可以确定要添加（发布）到市场的项。 您可以发布项，例如数据库、 应用服务，等等。 发布项可让所有用户看到它们。 可以发布自定义项的创建，也可以发布项从一个不断增长[的 Azure Marketplace 项列表](azure-stack-marketplace-azure-items.md)。 将某个项发布到市场后，用户在五分钟内就能看到它。

> [!CAUTION]  
> 所有库项项目，包括图像和 JSON 文件都进行访问而无需身份验证后使其在 Azure Stack Marketplace 中可用。 有关发布自定义市场项时的其他注意事项，请参阅[创建和发布市场项](azure-stack-create-and-publish-marketplace-item.md)。

若要打开 Marketplace，在管理员门户中选择 **+ 创建资源**。

![市场](media/azure-stack-publish-custom-marketplace-item/image1.png)

## <a name="marketplace-items"></a>市场项

Azure Stack 市场项是用户可下载并使用的服务、应用程序或资源。 所有 Azure Stack Marketplace 项都会对所有用户，包括计划和产品等管理项可见。 这些项不需要对视图中，但用户无法正常工作的订阅。

每个市场项具有：

* 资源预配 Azure 资源管理器模板。
* 元数据，例如字符串、 图标和其他市场营销宣传材料。
* 若要在门户中显示项的格式设置信息。

发布到市场的每个项使用 Azure 库包 (.azpkg) 格式。 添加部署或运行时资源 （代码、 包含软件或虚拟机映像的 zip 文件） 到 Azure Stack 分别不作为 Marketplace 项的一部分。

在 1803 和更高版本中，从 Azure 下载映像或上传自定义映像时，Azure Stack 会将映像转换为稀疏文件。 此过程会在添加映像时延长时间，但可节省空间并加速这些映像的部署。 转换仅适用于新映像。 现有映像不会更改。

## <a name="next-steps"></a>后续步骤

* [下载市场项](azure-stack-download-azure-marketplace-item.md)  
* [创建和发布市场项](azure-stack-create-and-publish-marketplace-item.md)
