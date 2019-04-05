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
ms.date: 04/03/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: f76dd423cb3f7fbae6cc88d064e49dc2d56f1a1c
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2019
ms.locfileid: "59045281"
---
# <a name="app-service-environment-management-addresses"></a>应用服务环境管理地址

应用服务环境 (ASE) 是在 Azure 虚拟网络 (VNet) 中运行的 Azure 应用服务的单个租户部署。  ASE 会在 VNet 中运行，而它必须仍可访问多个 Azure 应用服务用于管理服务的专用 IP 地址。  在 ASE 中，管理流量遍历用户控制的网络。 如果此流量被阻塞或被错误路由，则 ASE 将会挂起。 有关 ASE 网络依赖项的详细信息，请阅读[网络注意事项和应用服务环境][networking]。 有关 ASE 的一般信息，请先阅读[应用服务环境简介][intro]。

所有 ASE 都有一个公用 VIP，管理流量将从其中进入。 来自这些地址的传入管理流量将传入到 ASE 的公共 VIP 上的端口 454 和 455。 本文档列出了发往 ASE 的管理流量的应用服务源地址。 这些地址也是名为 AppServiceManagement IP 服务标记中。

在路由表，以避免管理流量的非对称路由问题，可以配置如下所示的地址。 路由操作在 IP 层的流量，但没有意识的流量方向或 TCP 答复消息中的流量。 如果 TCP 请求的答复地址不同于已发送到的地址，您具有非对称路由问题。 若要避免出现与你的 ASE 管理流量的非对称路由问题，需要确保返回的相同的地址发送到发送回复。 若要详细了解如何配置 ASE 以在从本地发送出站流量的环境中运行，请阅读[为 ASE 配置强制隧道][forcedtunnel]

## <a name="list-of-management-addresses"></a>管理地址的列表 ##

| 区域 | 地址 |
|--------|-----------|
| 所有公共区域 | 13.64.115.203, 13.75.127.117, 13.94.141.115, 13.94.143.126, 13.94.149.179, 23.102.135.246, 23.102.188.65, 40.83.120.64, 40.83.121.56, 40.83.125.161, 40.124.47.188, 52.151.25.45, 52.165.152.214, 52.165.153.122, 52.165.154.193, 52.165.158.140, 52.174.22.21, 52.178.177.147, 52.178.184.149, 52.178.190.65, 52.178.195.197, 52.187.56.50, 52.187.59.251, 52.187.63.19, 52.187.63.37, 52.224.105.172, 52.225.177.153, 65.52.14.230, 65.52.172.237, 65.52.193.203, 70.37.57.58, 70.37.89.222, 104.44.129.141, 104.44.129.243, 104.44.129.255, 104.44.134.255, 104.208.54.11, 157.55.176.93, 157.55.208.185, 191.236.154.88 |
| Microsoft Azure 政府 | 23.97.29.209、13.72.53.37、13.72.180.105、23.97.0.17、23.97.16.184 |

## <a name="configuring-a-network-security-group"></a>配置网络安全组

使用网络安全组，不需要担心的单个地址或维护你自己的配置。 没有名为 AppServiceManagement 进行更新的所有地址的 IP 服务标记。 若要在 NSG 中使用此 IP 服务标记，请转到门户，打开网络安全组 UI，并选择入站的安全规则。 如果你有一个预先存在规则的入站的管理流量，对其进行编辑。 如果未对 ASE 创建此 NSG，或者此 NSG 是全新的，请选择“添加”。 在“源”下拉列表中，选择“服务标记”。  在源服务标记中，选择**AppServiceManagement**。 将源端口范围设置为 \*，将“目标”设置为“任何”，将目标端口范围设置为 **454-455**，将“协议”设置为“TCP”，将“操作”设置为“允许”。 如果要建立的规则，然后您需要设置的优先级。 

![使用服务标记创建 NSG][1]

## <a name="configuring-a-route-table"></a>配置路由表

可将管理地址放在包含 Internet 下一跃点的路由表中，以确保所有入站管理流量能够通过同一路径返回。 配置强制隧道时需要这些路由。 若要创建路由表，可以使用门户、PowerShell 或 Azure CLI。  下面是在 PowerShell 提示符下使用 Azure CLI 创建路由表的命令。 

    $rg = "resource group name"
    $rt = "route table name"
    $location = "azure location"
    az network route-table create --name $rt --resource-group $rg --location $location
    az network route-table route create -g $rg --route-table-name $rt -n 13.64.115.203 --next-hop-type Internet --address-prefix 13.64.115.203/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.75.127.117 --next-hop-type Internet --address-prefix 13.75.127.117/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.141.115 --next-hop-type Internet --address-prefix 13.94.141.115/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.143.126 --next-hop-type Internet --address-prefix 13.94.143.126/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.149.179 --next-hop-type Internet --address-prefix 13.94.149.179/32
    az network route-table route create -g $rg --route-table-name $rt -n 23.102.135.246 --next-hop-type Internet --address-prefix 23.102.135.246/32
    az network route-table route create -g $rg --route-table-name $rt -n 23.102.188.65 --next-hop-type Internet --address-prefix 23.102.188.65/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.120.64 --next-hop-type Internet --address-prefix 40.83.120.64/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.121.56 --next-hop-type Internet --address-prefix 40.83.121.56/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.125.161 --next-hop-type Internet --address-prefix 40.83.125.161/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.124.47.188 --next-hop-type Internet --address-prefix 40.124.47.188/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.151.25.45 --next-hop-type Internet --address-prefix 52.151.25.45/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.152.214 --next-hop-type Internet --address-prefix 52.165.152.214/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.153.122 --next-hop-type Internet --address-prefix 52.165.153.122/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.154.193 --next-hop-type Internet --address-prefix 52.165.154.193/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.158.140 --next-hop-type Internet --address-prefix 52.165.158.140/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.174.22.21 --next-hop-type Internet --address-prefix 52.174.22.21/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.177.147 --next-hop-type Internet --address-prefix 52.178.177.147/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.184.149 --next-hop-type Internet --address-prefix 52.178.184.149/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.190.65 --next-hop-type Internet --address-prefix 52.178.190.65/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.195.197 --next-hop-type Internet --address-prefix 52.178.195.197/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.56.50 --next-hop-type Internet --address-prefix 52.187.56.50/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.59.251 --next-hop-type Internet --address-prefix 52.187.59.251/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.63.19 --next-hop-type Internet --address-prefix 52.187.63.19/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.63.37 --next-hop-type Internet --address-prefix 52.187.63.37/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.224.105.172 --next-hop-type Internet --address-prefix 52.224.105.172/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.225.177.153 --next-hop-type Internet --address-prefix 52.225.177.153/32
    az network route-table route create -g $rg --route-table-name $rt -n 65.52.14.230 --next-hop-type Internet --address-prefix 65.52.14.230/32
    az network route-table route create -g $rg --route-table-name $rt -n 65.52.172.237 --next-hop-type Internet --address-prefix 65.52.172.237/32
    az network route-table route create -g $rg --route-table-name $rt -n 65.52.193.203 --next-hop-type Internet --address-prefix 65.52.193.203/32
    az network route-table route create -g $rg --route-table-name $rt -n 70.37.57.58 --next-hop-type Internet --address-prefix 70.37.57.58/32
    az network route-table route create -g $rg --route-table-name $rt -n 70.37.89.222 --next-hop-type Internet --address-prefix 70.37.89.222/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.141 --next-hop-type Internet --address-prefix 104.44.129.141/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.243 --next-hop-type Internet --address-prefix 104.44.129.243/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.255 --next-hop-type Internet --address-prefix 104.44.129.255/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.134.255 --next-hop-type Internet --address-prefix 104.44.134.255/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.208.54.11 --next-hop-type Internet --address-prefix 104.208.54.11/32
    az network route-table route create -g $rg --route-table-name $rt -n 157.55.176.93 --next-hop-type Internet --address-prefix 157.55.176.93/32
    az network route-table route create -g $rg --route-table-name $rt -n 157.55.208.185 --next-hop-type Internet --address-prefix 157.55.208.185/32
    az network route-table route create -g $rg --route-table-name $rt -n 191.236.154.88 --next-hop-type Internet --address-prefix 191.236.154.88/32

创建路由表后，需在 ASE 子网中设置该路由表。  

## <a name="get-your-management-addresses-from-api"></a>通过 API 获取管理地址 ##

可以使用以下 API 调用列出与 ASE 匹配的管理地址。

    get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01

API 返回一个 JSON 文档，其中包含 ASE 的所有入站地址。 地址列表包括管理地址、ASE 使用的 VIP 和 ASE 子网地址范围本身。  

若要使用 [armclient](https://github.com/projectkudu/ARMClient) 调用此 API，请使用以下命令，但请替换为你的订阅 ID、资源组和 ASE 名称。  

    armclient login
    armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01


<!--IMAGES-->
[1]: ./media/management-addresses/managementaddr-nsg.png

<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md
