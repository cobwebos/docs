---
title: 使用 Azure 门户创建分区的 Windows VM | Microsoft Docs
description: 使用 Azure 门户在可用性区域中创建 Windows VM
services: virtual-machines-windows
documentationcenter: virtual-machines
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 3d3561cf1ad760930821fabeef9839c25d55f2a9
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
ms.locfileid: "30321982"
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-the-azure-portal"></a>使用 Azure 门户在可用性区域中创建 Windows 虚拟机

本文逐步说明如何使用 Azure 门户在 Azure 可用性区域中创建虚拟机。 [可用性区域](../../availability-zones/az-overview.md)是 Azure 区域中物理上独立的区域。 使用可用性区域可以在整个数据中心发生故障或服务中断（这种情况很少见）时保护应用和数据。

若要使用可用性区域，请在[受支持的 Azure 区域](../../availability-zones/az-overview.md#regions-that-support-availability-zones)中创建虚拟机。

## <a name="log-in-to-azure"></a>登录 Azure 

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="create-virtual-machine"></a>创建虚拟机

1. 在 Azure 门户的左上角单击“创建资源”。

2. 选择“计算”，然后选择“Windows Server 2016 Datacenter”。 

3. 输入虚拟机信息。 在此处输入的用户名和密码用于登录到虚拟机。 密码必须至少 12 个字符长，且符合[定义的复杂性要求](faq.md#what-are-the-password-requirements-when-creating-a-vm)。 选择一个位置，例如支持可用性区域的美国东部 2。 完成后，单击“确定”。

    ![在门户边栏选项卡中输入 VM 的基本信息](./media/create-portal-availability-zone/create-windows-vm-portal-basic-blade.png)

4. 为 VM 选择大小。 选择一个建议的大小，或根据功能进行筛选。 确认该大小在要使用的区域中可用。

    ![选择 VM 大小](./media/create-portal-availability-zone/create-windows-vm-portal-sizes.png)  

5. 在“设置” > “高可用性”下，从“可用性区域”下拉列表中选择一个带编号的区域，保留其余默认值，并单击“确定”。

    ![选择可用性区域](./media/create-portal-availability-zone/create-windows-vm-portal-availability-zone.png)

6. 在摘要页上，单击“创建”以启动虚拟机部署。

7. VM 将固定到 Azure 门户仪表板。 完成部署后，会自动打开 VM 摘要。

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>为托管磁盘和 IP 地址确认区域

当 VM 部署在可用性区域中时，将在同一可用性区域中创建 VM 的托管磁盘。 默认情况下，还会在该区域中创建一个公用 IP 地址。

可以在门户中确认这些资源的区域设置。  

1. 单击“资源组”，然后单击 VM 的资源组的名称，例如 *myResourceGroup*。

2. 单击磁盘资源的名称。 “概述”页包含有关资源的位置和可用性区域的详细信息。

    ![托管磁盘的可用性区域](./media/create-portal-availability-zone/create-windows-vm-portal-disk.png)

3. 单击公用 IP 地址资源的名称。 “概述”页包含有关资源的位置和可用性区域的详细信息。

    ![IP 地址的可用性区域](./media/create-portal-availability-zone/create-windows-vm-portal-ip.png)



## <a name="next-steps"></a>后续步骤

在本文中，你已学习了如何在可用性区域中创建 VM。 详细了解 Azure VM 的[区域和可用性](regions-and-availability.md)。
