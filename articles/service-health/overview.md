---
title: 什么是 Azure 服务运行状况？
description: 有关当前和将来的 Azure 服务问题及维护对 Azure 应用造成的影响的个性化信息。
author: stephbaron
ms.author: stbaron
services: service-health
ms.service: service-health
ms.topic: article
ms.date: 05/10/2019
ms.openlocfilehash: 058a171766680f09eaf4de14ddd25235020b1ba4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079569"
---
# <a name="what-is-azure-service-health"></a>什么是 Azure 服务运行状况？

Azure 提供了一系列的体验，以向您通知有关你的云资源的运行状况。 此信息包括当前和将来的问题，例如服务影响事件、 计划内的维护和可能影响你的可用性的其他更改。

Azure 服务运行状况是三个单独的较小服务的组合。

[Azure 状态](azure-status-overview.md)上告知你在 Azure 中的服务中断 **[Azure 状态页](https://status.azure.com)** 。 页是在所有 Azure 区域的所有 Azure 服务运行状况的全局视图。 状态页，使用广泛的影响，这些事件很好的参考，但我们强烈建议当前的 Azure 用户利用 Azure 服务运行状况，以便随时了解 Azure 事件和维护。

[Azure 服务运行状况](service-health-overview.md)提供正在使用的区域和 Azure 服务的运行状况的个性化的视图。 这是查找有关停机、计划内维护活动以及其他运行状况建议等影响服务的信息的最佳地方，因为已经过身份验证的 Azure 服务运行状况体验知道你当前使用哪些服务和资源。 使用服务运行状况的最佳方法是，设置服务运行状况警报，以便在服务问题、计划内维护或其他更改可能会影响你使用的 Azure 服务和区域时通过你首选的通信通道通知你。

[Azure 资源运行状况](resource-health-overview.md)提供单独的云资源等的特定虚拟机实例的运行状况的信息。 使用 Azure Monitor，还可以配置警报以通知你有关云资源可用性更改的信息。 Azure 资源运行状况将随 Azure Monitor 通知一起，帮助你更好地随时了解资源可用性并快速评估问题是由于你方导致还是与 Azure 平台事件相关。

同时，这些体验在与你最相关的粒度上提供 Azure 运行状况的综合视图。

**观看 Azure 状态页、 Azure 服务运行状况和 Azure 资源运行状况的概述**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OgX6]
