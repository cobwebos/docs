---
title: Azure 网络观察程序中的网络配置诊断简介 |Microsoft Docs
description: 本页提供网络观察程序-网络配置诊断的概述
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2020
ms.author: damendo
ms.openlocfilehash: 4d308b8a1a589308e2481c8bb9e0dc9ec64be25b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90994251"
---
# <a name="introduction-to-network-configuration-diagnostics-in-azure-network-watcher"></a>Azure 网络观察程序中的网络配置诊断简介

网络配置诊断工具可帮助客户了解 Azure 虚拟网络中将允许或拒绝的流量，以及用于调试的详细信息。 它可以帮助你了解你的 NSG 规则是否已正确配置。 

## <a name="pre-requisites"></a>先决条件
要使用网络配置诊断，必须在订阅中启用网络观察程序。 若要启用，请参阅 [创建 Azure 网络观察程序实例](https://docs.microsoft.com/azure/network-watcher/network-watcher-create) 。

## <a name="background"></a>背景

- Azure 中的资源通过 (Vnet) 和子网的虚拟网络进行连接。 可以使用 (NSG) 的网络安全组管理这些 Vnet 和子网的安全性。
- NSG 包含一系列安全规则，这些规则允许或拒绝到连接到的资源的网络流量。 Nsg 可以与子网、单个 Vm 或) 附加到 Vm (Nic 的单个网络接口相关联。 
- 网络中的所有流量流都是使用适用 NSG 中的规则评估的。
- 规则根据优先级编号从低到高进行评估 

## <a name="how-does-network-configuration-diagnostic-work"></a>网络配置诊断是如何工作的？ 

对于给定的流，NCD 工具会运行流模拟，并返回是否允许 (或拒绝流) 以及有关允许/拒绝流的规则的详细信息。  客户必须提供有关流的详细信息，例如源、目标、协议等。此工具将返回是否允许或拒绝流量、为指定流计算的 NSG 规则和每个规则的评估结果。

## <a name="next-steps"></a>后续步骤

使用网络配置诊断通过其他接口
 - [REST API](https://docs.microsoft.com/rest/api/network-watcher/networkwatchers/getnetworkconfigurationdiagnostic)
 - [PowerShell](https://docs.microsoft.com/powershell/module/az.network/invoke-aznetworkwatchernetworkconfigurationdiagnostic?view=azps-4.6.1)
 - [Azure CLI](https://docs.microsoft.com/cli/azure/network/watcher?view=azure-cli-latest#az_network_watcher_run_configuration_diagnostic)

