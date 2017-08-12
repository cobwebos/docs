---
title: "Azure Site Recovery 中两个 Azure 区域之间的网络映射 | Microsoft Docs"
description: "Azure Site Recovery 可以协调虚拟机和物理服务器的复制、故障转移与恢复。 了解有关故障转移到 Azure 或辅助数据中心的信息。"
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/11/2017
ms.author: pratshar
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 34f5caa13aa94dc982ed497d7c8ddeceed61a6d8
ms.contentlocale: zh-cn
ms.lasthandoff: 06/15/2017

---
# <a name="network-mapping-between-two-azure-regions"></a>两个 Azure 区域之间的网络映射


本文介绍如何将两个 Azure 区域的 Azure 虚拟网络相互映射。 网络映射可确保在目标 Azure 区域中创建复制的虚拟机时，同样在映射到源虚拟机虚拟网络的虚拟网络上创建该虚拟机。  

## <a name="prerequisites"></a>先决条件
映射网络之前，请确保已在源 Azure 区域和目标 Azure 区域创建 [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)。

## <a name="map-networks"></a>映射网络

若要将一个 Azure 区域中的 Azure 虚拟网络映射到另一个区域中的另一个虚拟网络，请转到“Site Recovery 基础结构”->“网络映射(针对 Azure 虚拟机)”，然后创建网络映射。

![网络映射](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)


在下面的示例中，我的虚拟机在东亚区域运行，要复制到东南亚。

选择源网络和目标网络，然后单击“确定”创建从东亚到东南亚的网络映射。

![网络映射](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)


执行与上面相同的操作，创建从东南亚到东亚的网络映射。  
![网络映射](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="mapping-network-when-enabling-replication"></a>启用复制时映射网络

如果在第一次将虚拟机从一个 Azure 区域复制到另一个 Azure 区域时未创建网络映射，可以在相同的过程中选择目标网络。 Site Recovery 会基于此选择创建从源区域到目标区域以及从目标区域到源区域的网络映射。   

![网络映射](./media/site-recovery-network-mapping-azure-to-azure/network-mapping4.png)

默认情况下，Site Recovery 会在目标区域中创建与源网络相同的网络：在源网络的名称后面添加“-asr”后缀。 你可以单击“自定义”，选择已创建的网络。

![网络映射](./media/site-recovery-network-mapping-azure-to-azure/network-mapping5.png)


如果已创建网络映射，则启用复制时，无法更改目标虚拟网络。 若要更改它，需修改现有的网络映射。  

![网络映射](./media/site-recovery-network-mapping-azure-to-azure/network-mapping6.png)

![网络映射](./media/site-recovery-network-mapping-azure-to-azure/modify-network-mapping.png)

> [!IMPORTANT]
> 如果修改从区域 1 到区域 2 的网络映射，请确保同样修改从区域 2 到区域 1 的网络映射。
>
>


## <a name="subnet-selection"></a>子网选择
根据源虚拟机子网的名称选择目标虚拟机的子网。 如果目标网络中有与源虚拟机子网同名的子网，则为目标虚拟机选择该子网。 如果目标网络中没有同名的子网，则按字母顺序选择第一个子网作为目标子网。 可以转到虚拟机的“计算和网络”设置来修改此子网。

![修改子网](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="ip-address"></a>IP 地址

按如下方式为目标虚拟机的各个网络接口选择 IP 地址：

### <a name="dhcp"></a>DHCP
如果源虚拟机的网络接口使用 DHCP，则目标虚拟机的网络接口也设置为 DHCP。

### <a name="static-ip"></a>静态 IP
如果源虚拟机的网络接口使用静态 IP，则目标虚拟机的网络接口也设置为使用静态 IP。 按如下方式选择静态 IP：

#### <a name="same-address-space"></a>地址空间相同

如果源子网和目标子网具有相同的地址空间，则将目标 IP 设置为与源虚拟机网络接口的 IP 相同。 如果相同的 IP 不可用，则将其他可用的 IP 设置为目标 IP。

#### <a name="different-address-space"></a>地址空间不同

如果源子网和目标子网具有不同的地址空间，则将目标 IP 设置为目标子网中任何可用的 IP。

可以转到虚拟机的“计算和网络”设置来修改各网络接口上的目标 IP。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure VM 复制网络指南](site-recovery-azure-to-azure-networking-guidance.md)。

