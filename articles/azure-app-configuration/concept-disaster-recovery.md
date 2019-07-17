---
title: 使用 Azure 应用程序配置实现复原和灾难恢复 | Microsoft Docs
description: 概述如何使用 Azure 应用程序配置实现复原和灾难恢复。
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 05/29/2019
ms.author: yegu
ms.openlocfilehash: c05957cda16c96b841433483a90429aab2b4d22d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706506"
---
# <a name="resiliency-and-disaster-recovery"></a>复原能力和灾难恢复

目前，Azure 应用配置是一个区域性的服务。 每个配置存储在特定的 Azure 区域中创建。 区域范围的服务中断会影响该区域中的所有存储。 应用配置不提供自动故障转移到另一个区域的机制。 本文提供有关如何使用跨 Azure 区域的多个配置存储来提高应用程序异地复原能力的一般性指导。

## <a name="high-availability-architecture"></a>高可用性体系结构

若要实现跨区域冗余，需要在不同的区域中创建多个应用程序配置存储。 完成此设置后，应用程序至少会获得一个附加的配置存储，当主要存储不可访问时，应用程序可以回退到该附加存储。 下图演示了应用程序与其主要配置存储和辅助配置存储之间的拓扑：

![异地冗余的存储](./media/geo-redundant-app-configuration-stores.png)

应用程序会以并行方式从主要存储和辅助存储加载其配置。 这样做可以提高成功获取配置数据的可能性。 使两个存储中的数据保持同步由你负责。以下部分介绍如何将异地复原能力内置到应用程序中。

## <a name="failover-between-configuration-stores"></a>配置存储之间的故障转移

从技术上讲，应用程序不会执行故障转移。 它将会尝试同时从两个应用配置存储检索相同的配置数据集。 按以下方式安排代码：让代码先从辅助存储加载数据，再从主要存储加载数据。 此方法可确保只要主要存储中的配置数据可用就会优先进行加载。 以下代码片段演示如何在 .NET Core CLI 中实现这种安排：

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(settings["ConnectionString_SecondaryStore"], optional: true)
                  .AddAzureAppConfiguration(settings["ConnectionString_PrimaryStore"], optional: true);
        })
        .UseStartup<Startup>();
    }
```

请注意传入 `AddAzureAppConfiguration` 函数中的 `optional` 参数。 将此参数设置为 `true` 时，如果该函数无法加载配置数据，则此参数可防止应用程序无法继续。

## <a name="synchronization-between-configuration-stores"></a>配置存储之间的同步

所有异地冗余的配置存储必须包含相同的数据集，这一点非常重要。 可以按需使用应用配置中的“导出”功能将数据从主要存储复制到辅助存储  。 可通过 Azure 门户和 CLI 使用此功能。

在 Azure 门户中，可以按照以下步骤将更改推送到另一个配置存储。

1. 转到“导入/导出”选项卡  ，然后依次选择“导出”   > “应用配置”   > “目标”   > “选择资源”  。

2. 在打开的新边栏选项卡中，指定辅助存储的订阅、资源组和资源名称，然后选择“应用”  。

3. UI 将会更新，使你可以选择要导出到辅助存储的配置数据。 可将默认时间值保持原样，并将“原始标签”  和“添加标签”  设置为相同的值。 选择“应用”。 

4. 针对所有配置更改重复执行上述步骤。

若要自动执行此导出过程，请使用 Azure CLI。 以下命令演示如何将单个配置更改从主要存储导出到辅助存储：

    az appconfig kv export --destination appconfig --name {PrimaryStore} --label {Label} --dest-name {SecondaryStore} --dest-label {Label}

## <a name="next-steps"></a>后续步骤

本文已介绍如何增强应用程序，以便在应用程序配置运行时实现异地复原。 也可以在生成或部署时嵌入来自应用配置的配置数据。 有关详细信息，请参阅[与 CI/CD 管道集成](./integrate-ci-cd-pipeline.md)。

