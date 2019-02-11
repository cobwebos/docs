---
title: 渲染器管理器支持 - Azure Batch
description: 通过 Azure Batch 渲染器管理器集成将 Azure 用于渲染
services: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 4eeece4946b4f957d9f864da7c46d77d119863b5
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2018
ms.locfileid: "53539916"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>将 Azure Batch 与渲染器场管理器配合使用

如果使用的是现有的本地渲染器场，则很有可能是由渲染器管理器控制渲染器场容量和渲染器作业。

Azure 为常用的渲染器管理器提供内置支持或加载项。 然后，你就可以添加和删除 Azure VM，包括带付费使用应用程序许可的 VM，以及低优先级 VM。

支持以下渲染器管理器：

* [PipelineFX Qube!](https://www.pipelinefx.com/)
* [Royal Render](http://www.royalrender.de/)
* [Thinkbox Deadline](https://deadline.thinkboxsoftware.com/)

## <a name="using-azure-with-pipelinefx-qube"></a>将 Azure 与 PipelineFX Qube 配合使用

若要将 Azure Batch 池 VM 用作 Qube 辅助角色，请使用 [GitHub 存储库](https://github.com/Azure/azure-qube)中的脚本和说明。

## <a name="using-azure-with-royal-render"></a>将 Azure 与 Royal Render 配合使用

Royal Render 内置了 Azure 和 Azure Batch 集成，可以通过基于 Azure 的 VM 来扩展渲染器场。 有关摘要，请查看[帮助文件](http://www.royalrender.de/help8/index.html?Cloudrendering.html)。

有关 Royal Render 客户使用 Azure 集成的示例，请参阅 [Jellyfish Pictures 客户案例](https://customers.microsoft.com/story/jellyfishpictures)。

## <a name="using-azure-with-thinkbox-deadline"></a>将 Azure 与 Thinkbox Deadline 配合使用

若要将 Azure Batch 池 VM 用作 Deadline 从属角色，请使用 [GitHub 存储库](https://github.com/Azure/azure-deadline)中的脚本和说明。

## <a name="next-steps"></a>后续步骤

尝试适用于渲染器管理器的 Azure Batch 集成，根据需要使用 GitHub 上的适当插件和说明。