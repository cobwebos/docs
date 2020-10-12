---
title: 渲染器管理器支持
description: 使用 Azure Batch 渲染器管理器集成。 了解针对常用的渲染器管理器的内置支持或加载项。
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: c44cbf86d8bf2fe83a6dc91dee1c4f58eec156c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "83726445"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>将 Azure Batch 与渲染器场管理器配合使用

如果使用的是现有的本地渲染器场，则很有可能是由渲染器管理器控制渲染器场容量和渲染器作业。

Azure 为常用的渲染器管理器提供内置支持或加载项。 然后，你就可以添加和删除 Azure VM，包括带付费使用应用程序许可的 VM，以及低优先级 VM。

支持以下渲染器管理器：

* [PipelineFX Qube!](https://www.pipelinefx.com/)
* [Royal Render](https://www.royalrender.de/)
* [Thinkbox Deadline](https://deadline.thinkboxsoftware.com/)

## <a name="azure-render-hub"></a>Azure 渲染器中心

Azure 渲染器中心简化了 Azure 渲染器场的创建和管理。  渲染器中心提供对 PipelineFx Qube 和 Deadline 10 的原生支持。  有关详细信息和说明，请参阅 [GitHub 存储库](https://github.com/Azure/azure-render-hub)。

## <a name="using-azure-with-pipelinefx-qube"></a>将 Azure 与 PipelineFX Qube 配合使用

Azure 渲染器中心支持常用的渲染器管理器，包括 Deadline。  有关部署和使用渲染器中心的说明，请参阅 [GitHub 存储库](https://github.com/Azure/azure-render-hub)。

若要将 Azure Batch 池 VM 用作 Qube 辅助角色，[GitHub 存储库](https://github.com/Azure/azure-qube)中也提供了相关的脚本和说明。

## <a name="using-azure-with-royal-render"></a>将 Azure 与 Royal Render 配合使用

Royal Render 内置了 Azure 和 Azure Batch 集成，可以通过基于 Azure 的 VM 来扩展渲染器场。 有关摘要，请查看[帮助文件](https://www.royalrender.de/help8/index.html?Cloudrendering.html)。

有关 Royal Render 客户使用 Azure 集成的示例，请参阅 [Jellyfish Pictures 客户案例](https://customers.microsoft.com/story/jellyfishpictures)。

## <a name="using-azure-with-thinkbox-deadline"></a>将 Azure 与 Thinkbox Deadline 配合使用

Azure 渲染器中心支持常用的渲染器管理器，包括 Deadline。  有关部署和使用渲染器中心的说明，请参阅 [GitHub 存储库](https://github.com/Azure/azure-render-hub)。

## <a name="next-steps"></a>后续步骤

尝试适用于渲染器管理器的 Azure Batch 集成，根据需要使用 GitHub 上的适当插件和说明。
