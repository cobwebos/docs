---
title: "开始使用 Azure CLI 在经典部署模型中创建面向 Internet 的负载均衡器 | Microsoft Docs"
description: "了解如何使用 Azure CLI 在经典部署模型中创建面向 Internet 的负载平衡器"
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: e433a824-4a8a-44d2-8765-a74f52d4e584
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e8a346c1b2d430eceb4aa1b8bc94fbbe89394556


---
# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-the-azure-cli"></a>开始在 Azure CLI 中创建面向 Internet 的负载平衡器（经典）
[!INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

本文介绍经典部署模型。 还可以[了解如何使用 Azure Resource Manager 创建面向 Internet 的负载均衡器](load-balancer-get-started-internet-arm-ps.md)。

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="step-by-step-creating-an-internet-facing-load-balancer-using-cli"></a>使用 CLI 逐步创建面向 Internet 的负载平衡器
本指南演示如何基于上述方案创建 Internet 负载平衡器。

1. 如果你从未使用过 Azure CLI，请参阅 [安装和配置 Azure CLI](../xplat-cli-install.md) ，并按照说明进行操作，直到选择 Azure 帐户和订阅。
2. 运行 **azure config mode** 命令以切换到经典模式，如下所示。
   
        azure config mode asm
   
    预期输出：
   
        info:    New mode is asm

## <a name="create-endpoint-and-load-balancer-set"></a>创建终结点和负载平衡器集
此方案假定已创建虚拟机“web1”和“web2”。
本指南将使用端口 80 作为公用端口和本地端口创建负载平衡器集。 还将在端口 80 上配置探测端口，并将负载平衡器集命名为“lbset”。

### <a name="step-1"></a>步骤 1
使用 `azure network vm endpoint create` 为虚拟机“web1”创建第一个终结点和负载平衡器集。

    azure vm endpoint create web1 80 -k 80 -o tcp -t 80 -b lbset

使用的参数：

**-k** -本地虚拟机端口<br>
**-o** - 协议<BR>
**-t** - 探测端口<BR>
**-b** - 负载均衡器名称<BR>

## <a name="step-2"></a>步骤 2
将第二个虚拟机“web2”添加到负载平衡器集。

    azure vm endpoint create web2 80 -k 80 -o tcp -t 80 -b lbset

## <a name="step-3"></a>步骤 3
使用 `azure vm show` 验证负载平衡器配置。

    azure vm show web1

输出将为：

    data:    DNSName "contoso.cloudapp.net"
    data:    Location "East US"
    data:    VMName "web1"
    data:    IPAddress "10.0.0.5"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-2015
    6-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "joaoma-1-web1-0-201509251804250879"
    data:    OSDisk mediaLink "https://XXXXXXXXXXXXXXX.blob.core.windows.
    /vhds/joaomatest-web1-2015-09-25.vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Se
    r-2012-R2-20150916-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "XXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 name "XXXXXXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    Network Endpoints 0 loadBalancedEndpointSetName "lbset"
    data:    Network Endpoints 0 localPort 80
    data:    Network Endpoints 0 name "tcp-80-80"
    data:    Network Endpoints 0 port 80
    data:    Network Endpoints 0 loadBalancerProbe port 80
    data:    Network Endpoints 0 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 0 loadBalancerProbe intervalInSeconds 15
    data:    Network Endpoints 0 loadBalancerProbe timeoutInSeconds 31
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 5986
    data:    Network Endpoints 1 name "PowerShell"
    data:    Network Endpoints 1 port 5986
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 3389
    data:    Network Endpoints 2 name "Remote Desktop"
    data:    Network Endpoints 2 port 58081
    info:    vm show command OK

## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>为虚拟机创建远程桌面终结点
你可以使用 `azure vm endpoint create` 创建远程桌面终结点，将网络流量从公共端口转发到特定虚拟机的本地端口。

    azure vm endpoint create web1 54580 -k 3389


## <a name="remove-virtual-machine-from-load-balancer"></a>从负载平衡器中删除虚拟机
你必须从虚拟机中删除关联到负载平衡器集的终结点。 删除终结点后，虚拟机将不再属于该负载平衡器集。

 使用上面的示例，你可以使用命令 `azure vm endpoint delete` 从负载平衡器“lbset”中删除为虚拟机“web1”创建的终结点。

    azure vm endpoint delete web1 tcp-80-80


> [!NOTE]
> 你可以使用命令 `azure vm endpoint --help` 浏览管理终结点的更多选项
> 
> 

## <a name="next-steps"></a>后续步骤
[开始配置内部负载均衡器](load-balancer-get-started-ilb-arm-ps.md)

[配置负载均衡器分发模式](load-balancer-distribution-mode.md)

[配置负载均衡器的空闲 TCP 超时设置](load-balancer-tcp-idle-timeout.md)




<!--HONumber=Nov16_HO2-->


