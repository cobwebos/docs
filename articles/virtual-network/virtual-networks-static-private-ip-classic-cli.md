---
title: "为 VM（经典）配置专用 IP 地址 - Azure CLI 1.0 | Microsoft 文档"
description: "了解如何使用 Azure 命令行接口 (CLI) 1.0 为虚拟机（经典）配置专用 IP 地址。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 17386acf-c708-4103-9b22-ff9bf04b778d
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: 0d0b896e64dfba0081e2ec23f2181e07226faebe
ms.lasthandoff: 02/28/2017


---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-cli-10"></a>使用 Azure CLI 1.0 为虚拟机（经典）配置专用 IP 地址

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

本文介绍经典部署模型。 还可以[在 Resource Manager 部署模型中管理静态专用 IP 地址](virtual-networks-static-private-ip-arm-cli.md)。

下面的示例 Azure CLI 命令需要已创建简单的环境。 若要运行本文档中所显示的命令，请首先构建[创建 VNet](virtual-networks-create-vnet-classic-cli.md) 中所述的测试环境。

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>如何在创建 VM 时指定静态专用 IP 地址
若要根据上述方案在名为 *TestService* 的新云服务中创建名为 *DNS01* 的新 VM，请按照以下步骤进行操作：

1. 如果你从未使用过 Azure CLI，请参阅 [安装和配置 Azure CLI](../xplat-cli-install.md) ，并按照说明进行操作，直到选择 Azure 帐户和订阅。
2. 运行 **azure service create** 命令以创建云服务。
   
        azure service create TestService --location uscentral
   
    预期输出：
   
        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name TestService
        info:    service create command OK
3. 运行 **azure create vm** 命令以创建 VM。 请注意静态专用 IP 地址的值。 在输出后显示的列表说明了所用的参数。
   
        azure vm create -l centralus -n DNS01 -w TestVNet -S "192.168.1.101" TestService bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 adminuser AdminP@ssw0rd
   
    预期输出：
   
        info:    Executing command vm create
        warn:    --vm-size has not been specified. Defaulting to "Small".
        info:    Looking up image bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
        info:    Looking up virtual network
        info:    Looking up cloud service
        warn:    --location option will be ignored
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Retrieving storage accounts
        info:    Creating VM
        info:    OK
        info:    vm create command OK
   
   * **-l（或 --location）**。 将在其中创建 VM 的 Azure 区域。 对于我们的方案，为 *centralus*。
   * **-n（或 --vm-name）** 要创建的 VM 的名称。
   * **-w（或 --virtual-network-name）**。 将在其中创建 VM 的 VNet 的名称。 
   * **-S（或 --static-ip）**。 VM 的静态专用 IP 地址。
   * **TestService**。 将在其中创建 VM 的云服务的名称。
   * **bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2**。 用于创建 VM 的映像。
   * **adminuser**。 Windows VM 的本地管理员。
   * **AdminP@ssw0rd**。 Windows VM 的本地管理员密码。

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>如何检索 VM 的静态专用 IP 地址信息
若要查看使用上述脚本创建的 VM 的静态专用 IP 地址信息，请运行以下 Azure CLI 命令并观察 *Network StaticIP* 的值：

    azure vm static-ip show DNS01

预期输出：

    info:    Executing command vm static-ip show
    info:    Getting virtual machines
    data:    Network StaticIP "192.168.1.101"
    info:    vm static-ip show command OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>如何从 VM 中删除静态专用 IP 地址
若要删除使用上述脚本添加到 VM 的静态专用 IP 地址，请运行以下 Azure CLI 命令：

    azure vm static-ip remove DNS01

预期输出：

    info:    Executing command vm static-ip remove
    info:    Getting virtual machines
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip remove command OK

## <a name="how-to-add-a-static-private-ip-to-an-existing-vm"></a>如何将静态专用 IP 添加到现有 VM
若要向使用上述脚本创建的 VM 添加静态专用 IP 地址，请运行以下命令：

    azure vm static-ip set DNS01 192.168.1.101

预期输出：

    info:    Executing command vm static-ip set
    info:    Getting virtual machines
    info:    Looking up virtual network
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip set command OK

## <a name="next-steps"></a>后续步骤
* 了解[保留公共 IP](virtual-networks-reserved-public-ip.md) 地址。
* 了解[实例层级公共 IP (ILPIP) 地址](virtual-networks-instance-level-public-ip.md)。
* 查阅[保留 IP REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx)。


