---
title: 应用服务环境管理地址 - Azure
description: 列出用于监管应用服务环境的管理地址
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 7fb39886b19a2229188821eb39d4fb8a5928bb43
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2018
ms.locfileid: "53276672"
---
# <a name="app-service-environment-management-addresses"></a>应用服务环境管理地址

应用服务环境 (ASE) 是指 Azure 应用服务部署到 Azure 虚拟网络 (VNet) 的子网中。  必须可以从 Azure 应用服务使用的管理面板中访问 ASE。  此 ASE 管理流量遍历用户控制的网络。 如果此流量被阻塞或被错误路由，则 ASE 将会挂起。 有关 ASE 网络依赖项的详细信息，请阅读[网络注意事项和应用服务环境][networking]。 有关 ASE 的一般信息，请先阅读[应用服务环境简介][intro]。

本文档列出了发往 ASE 的管理流量的应用服务源地址并指明了两个重要用途。  

1. 可以使用这些地址创建网络安全组来锁定传入流量。  
2. 可以使用这些地址创建路由来支持强制隧道部署。 若要详细了解如何配置 ASE 以在从本地发送出站流量的环境中运行，请阅读[为 ASE 配置强制隧道][forcedtunnel]

所有 ASE 都有一个公用 VIP，管理流量将从其中进入。 来自这些地址的传入管理流量将传入到 ASE 的公共 VIP 上的端口 454 和 455。  

## <a name="list-of-management-addresses"></a>管理地址的列表 ##

| 区域 | 地址 |
|--------|-----------|
| 所有公共区域 | 70.37.57.58、157.55.208.185、52.174.22.21、13.94.149.179、13.94.143.126、13.94.141.115、52.178.195.197、52.178.190.65、52.178.184.149、52.178.177.147、13.75.127.117、40.83.125.161、40.83.121.56、40.83.120.64、52.187.56.50、52.187.63.37、52.187.59.251、52.187.63.19、52.165.158.140、52.165.152.214、52.165.154.193、52.165.153.122、104.44.129.255、104.44.134.255、104.44.129.243、104.44.129.141、23.102.188.65、191.236.154.88、13.64.115.203、65.52.193.203、70.37.89.222、52.224.105.172、23.102.135.246 |
| Microsoft Azure 政府版（Fairfax 或 MAG） | 23.97.29.209、13.72.53.37、13.72.180.105、23.97.0.17、23.97.16.184 |

## <a name="get-your-management-addresses-from-api"></a>通过 API 获取管理地址 ##

可以使用以下 API 调用列出与 ASE 匹配的管理地址。

    get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01

API 返回一个 JSON 文档，其中包含 ASE 的所有入站地址。 地址列表包括管理地址、ASE 使用的 VIP 和 ASE 子网地址范围本身。  

若要使用 [armclient](https://github.com/projectkudu/ARMClient) 调用此 API，请使用以下命令，但请替换为你的订阅 ID、资源组和 ASE 名称。  

    armclient login
    armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01


<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md
