---
title: 了解 Azure Service Fabric 群集版本 | Microsoft Docs
description: 支持的 Azure Service Fabric 群集版本
services: service-fabric
documentationcenter: .net
author: twhitney
manager: jpconnock
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/24/2018
ms.author: TylerMSFT
ms.openlocfilehash: 1041f37486c556ae29eed47728336a1ccb518e78
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2018
ms.locfileid: "48040353"
---
# <a name="supported-service-fabric-versions"></a>支持的 Service Fabric 版本

确保群集始终运行支持的 Service Fabric 版本。 宣布发行新版 Service Fabric 标志着自该日期起至少 60 天以后结束对旧版本的支持。 新版本[在 Service Fabric 团队博客](https://blogs.msdn.microsoft.com/azureservicefabric/)上公布。

请参阅以下文档，详细了解如何才能让群集始终运行支持的 Service Fabric 版本。

- [在 Azure 群集上升级 Service Fabric 版本](service-fabric-cluster-upgrade.md)
- [在单独的 Windows Server 群集上升级 Service Fabric 版本](service-fabric-cluster-upgrade-windows-server.md)

下面是支持的 Service Fabric 版本的列表以及支持结束日期。

| **群集中的 Service Fabric 运行时** | **可以直接从群集版本升级** |兼容的 SDK/NuGet 包版本 | **支持结束日期** |
| --- | --- |--- | --- |
| 5.3.121 之前的所有群集版本 | 5.1.158* |低于或等于版本 2.3 |2017 年 1 月 20 日 |
| 5.3.* | 5.1.158.* |低于或等于版本 2.3 |2017 年 2 月 24 日 |
| 5.4.* | 5.1.158.* |低于或等于版本 2.4 |2017 年 5 月 10 日       |
| 5.5.* | 5.4.164.* |低于或等于版本 2.5 |2017 年 8 月 10 日    |
| 5.6.* | 5.4.164.* |低于或等于版本 2.6 |2017 年 10 月 13 日   |
| 5.7.* | 5.4.164.* |低于或等于版本 2.7 |2017 年 12 月 15 日  |
| 6.0.* | 5.6.205.* |版本 2.8 或更低版本 |2018 年 3 月 30 日     |
| 6.1.* | 5.7.221.* |版本 3.0 或更低版本 |2018 年 7 月 15 日      |
| 6.2.* | 6.0.232.* |版本 3.1 或更低版本 |2018 年 10 月 26 日 |
| 6.3.* | 6.1.480.* |版本 3.2 或更低版本 |最新版本，因此尚无结束日期 |