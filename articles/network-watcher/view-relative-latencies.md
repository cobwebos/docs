---
title: "查看从特定位置到 Azure 区域的相对延迟 | Microsoft Docs"
description: "了解如何查看从 Internet 提供商的特定位置到 Azure 区域的相对延迟。"
services: network-watcher
documentationcenter: 
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: a6c2ffa619eeff8b455df8a8b2157525af12c640
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2018
---
# <a name="view-relative-latency-to-azure-regions-from-specific-locations"></a>查看从特定位置到 Azure 区域的相对延迟

本教程介绍如何使用 Azure [网络观察程序](network-watcher-monitoring-overview.md)服务，根据用户的人口统计，帮助确定要将应用程序或服务部署到哪个 Azure 区域。 此外，还可以使用此服务来帮助评估服务提供商与 Azure 之间的连接。  
        
## <a name="create-a-network-watcher"></a>创建网络观察程序

如果已至少在一个 Azure [区域](https://azure.microsoft.com/regions)中部署了网络观察程序，则可以跳过本部分所述的任务。 创建网络观察程序的资源组。 在此示例中，已在美国东部区域创建了资源组，但你可以在任何 Azure 区域创建资源组。

```powershell
New-AzureRmResourceGroup -Name NetworkWatcherRG -Location eastus
```

创建网络观察程序。 必须至少在一个 Azure 区域创建网络观察程序。 在此示例中，网络观察程序是在美国东部 Azure 区域中创建的。

```powershell
New-AzureRmNetworkWatcher -Name NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG -Location eastus
```

## <a name="compare-relative-network-latencies-to-a-single-azure-region-from-a-specific-location"></a>将特定位置到单个 Azure 区域的相对网络延迟进行比较

评估服务提供商，或者排查用户在从特定位置连接到部署服务的 Azure 区域时报告的问题（例如，“该站点运行速度缓慢”）。 例如，以下命令返回了 2017 年 12 月 13 到 15 日，美国华盛顿州与 Azure 美国西部 2 区域之间的平均相对 Internet 服务提供商延迟：

```powershell
Get-AzureRmNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Location "West US 2" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

> [!NOTE]
> 在上述命令中指定的区域，不需要与检索网络观察程序时指定的区域相同。 上述命令只要求指定现有的网络观察程序。 网络观察程序可以位于任何区域。 如果为 `-Country` 和 `-State` 指定了值，这些值必须有效。 这些值区分大小写。 数据适用于有限数量的国家/地区、州/省和城市。 运行[查看可用的国家/地区、州/省、城市和提供商](#view-available)中所述的命令，以查看要配合上述命令使用的可用国家/地区、城市和州/省。 

> [!WARNING]
> 对于 `-StartTime` 和 `-EndTime`，必须指定 2017 年 11 月 14 日之后的日期。 指定 2017 年 11 月 14 日之前的日期不会返回任何数据。 

上述命令的输出如下所示：

```powershell
AggregationLevel   : State
ProviderLocation   : {
                       "Country": "United States",
                       "State": "washington"
                     }
ReachabilityReport : [
                       {
                         "Provider": "Qwest Communications Company, LLC - ASN 209",
                         "AzureLocation": "West US 2",
                         "Latencies": [
                           {
                             "TimeStamp": "2017-12-14T00:00:00Z",
                             "Score": 92
                           },
                           {
                             "TimeStamp": "2017-12-13T00:00:00Z",
                             "Score": 92
                           }
                         ]
                       },
                       {
                         "Provider": "Comcast Cable Communications, LLC - ASN 7922",
                         "AzureLocation": "West US 2",
                         "Latencies": [
                           {
                             "TimeStamp": "2017-12-14T00:00:00Z",
                             "Score": 96
                           },
                           {
                             "TimeStamp": "2017-12-13T00:00:00Z",
                             "Score": 96
                           }
                         ]
                       }
                     ]
```

在返回的输出中，**Score** 的值是区域和提供商之间的相对延迟。 分数为 1 表示延迟最差（最高），100 表示延迟最低。 相对延迟采用当日的平均值。 在前面的示例中，很明显可以看到，这两天的延迟相同，并且两家提供商的延迟差别很小，同时，在 1-100 的梯度上，两家提供商的延迟都比较低。 尽管这符合预期，但由于美国华盛顿州的实际位置靠近 Azure 美国西部 2 区域，有时结果将不符合预期。 指定的日期范围越大，在一段时间后，平均延迟会越高。

## <a name="compare-relative-network-latencies-across-azure-regions-from-a-specific-location"></a>将特定位置到不同 Azure 区域的相对网络延迟进行比较

如果未使用 `-Location` 指定特定位置与特定 Azure 区域之间的相对延迟，也同样可以确定从特定实际位置到所有 Azure 区域的相对延迟。 例如，如果主要用户是位于华盛顿州的 Comcast 用户，以下命令可以帮助评估要将服务部署在哪个 Azure 区域：

```powershell
Get-AzureRmNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Provider "Comcast Cable Communications, LLC - ASN 7922" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

>[!NOTE]
与指定单个位置时不同，如果未指定位置，或指定了多个位置（例如“美国西部 2”、“美国西部”），则运行该命令时，必须指定 Internet 服务提供商。 

## <a name="view-available"></a>查看可用的国家/地区、州/省、城市和提供商

数据适用于特定的 Internet 服务提供商、国家/地区、州/省和城市。 若要查看所有可查看其数据的可用 Internet 服务提供商、国家/地区、州/省和城市列表，请输入以下命令：

```powershell
Get-AzureRmNetworkWatcherReachabilityProvidersList -NetworkWatcherName NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG
```

数据仅适用于上述命令返回的国家/地区、州/省和城市。 上述命令要求指定现有的网络观察程序。 该示例指定了名为 *NetworkWatcherRG* 的资源组中的 *NetworkWatcher_eastus* 网络观察程序，但你可以指定任何现有的网络观察程序。 如果没有现有的网络观察程序，可以通过完成[创建网络观察程序](#create-a-network-watcher)中的任务来创建一个。 

运行上述命令后，可以根据需要，通过指定 **Country**、**State** 和 **City** 的有效值，来筛选返回的输出。  例如，若要查看美国华盛顿州西雅图市的 Internet 服务提供商列表，请输入以下命令：

```powershell
Get-AzureRmNetworkWatcherReachabilityProvidersList `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -City seattle `
  -Country "United States" `
  -State washington
```

> [!WARNING]
> 为 **Country** 指定的值必须采用大写和小写。 为 **State** 和 **City** 指定的值必须采用小写。 这些值必须已列在运行上述命令（未在该命令中指定 **Country**、**State** 和 **City** 的值）后返回的输出中。 如果指定错误的大小写，或者为 **Country**、**State** 或 **City** 指定的值不在运行上述命令（未在该命令中指定这些属性的值）后返回的输出中，则返回的输出为空。
