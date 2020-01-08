---
title: 渲染器管理器支持 - Azure Batch
description: 使用 Azure Batch 呈现管理器集成。 了解适用于常用呈现管理器的内置支持或加载项。
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 246907b16534d1a91833cab633a1973c97429f47
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75449684"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>将 Azure Batch 与渲染器场管理器配合使用

如果使用的是现有的本地渲染器场，则很有可能是由渲染器管理器控制渲染器场容量和渲染器作业。

Azure 为常用的渲染器管理器提供内置支持或加载项。 然后，你就可以添加和删除 Azure VM，包括带付费使用应用程序许可的 VM，以及低优先级 VM。

支持以下渲染器管理器：

* [PipelineFX Qube!](https://www.pipelinefx.com/)
* [Royal Render](https://www.royalrender.de/)
* [Thinkbox Deadline](https://deadline.thinkboxsoftware.com/)

## <a name="azure-render-hub"></a>Azure Render Hub

Azure 渲染中心简化了 Azure 渲染场的创建和管理。  呈现中心提供对 PipelineFx Qube 和截止时间10的本机支持。  有关详细信息和详细说明，请参阅[GitHub 存储库](https://github.com/Azure/azure-render-hub)。

## <a name="using-azure-with-pipelinefx-qube"></a>将 Azure 与 PipelineFX Qube 配合使用

Azure 呈现中心支持常用的呈现管理器，包括截止时间。  有关部署和使用呈现中心的说明，请参阅[GitHub 存储库](https://github.com/Azure/azure-render-hub)。

[GitHub 存储库](https://github.com/Azure/azure-qube)中也提供了用于启用要用作 Qube 工作线程的 Azure Batch 池 vm 的脚本和说明。

## <a name="using-azure-with-royal-render"></a>将 Azure 与 Royal Render 配合使用

Royal Render 内置了 Azure 和 Azure Batch 集成，可以通过基于 Azure 的 VM 来扩展渲染器场。 有关摘要，请查看[帮助文件](https://www.royalrender.de/help8/index.html?Cloudrendering.html)。

有关 Royal Render 客户使用 Azure 集成的示例，请参阅 [Jellyfish Pictures 客户案例](https://customers.microsoft.com/story/jellyfishpictures)。

## <a name="using-azure-with-thinkbox-deadline"></a>将 Azure 与 Thinkbox Deadline 配合使用

Azure 呈现中心支持常用的呈现管理器，包括截止时间。  有关部署和使用呈现中心的说明，请参阅[GitHub 存储库](https://github.com/Azure/azure-render-hub)。

## <a name="next-steps"></a>后续步骤

尝试适用于渲染器管理器的 Azure Batch 集成，根据需要使用 GitHub 上的适当插件和说明。
