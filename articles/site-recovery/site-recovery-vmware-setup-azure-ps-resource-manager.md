---
title: " 管理在 Azure 中运行的进程服务器 (Resource Manager) | Microsoft Docs"
description: "本文介绍如何在 Azure 中设置故障回复进程服务器 (Resource Manager)。"
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/19/2017
ms.author: anoopkv
ms.openlocfilehash: 1b75acb13ac4c8990f99f7454a6de5483f6ca2f1
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="manage-a-process-server-running-in-azure-resource-manager"></a>管理在 Azure (Resource Manager) 中运行的进程服务器
> [!div class="op_single_selector"]
> * [资源管理器](./site-recovery-vmware-setup-azure-ps-resource-manager.md)
> * [经典](./site-recovery-vmware-setup-azure-ps-classic.md)

故障回复期间，如果 Azure 虚拟网络与本地网络之间存在高延迟，建议将进程服务器部署在 Azure 中。 本指南介绍如何设置、配置和管理在 Azure 中运行的进程服务器。

> [!NOTE]
> 如果在故障转移期间使用了 **Resource Manager** 部署模型作为虚拟机的部署模型，则要使用本文。 如果使用**经典**部署模型作为部署模型，请按照[如何设置和配置故障回复进程服务器（经典）](./site-recovery-vmware-setup-azure-ps-classic.md)中的步骤进行操作

## <a name="prerequisites"></a>先决条件

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]

## <a name="deploy-a-process-server-on-azure"></a>在 Azure 上部署进程服务器
1. 在“保管库”>“Site Recovery 基础结构”（在“管理”标题下）>“配置服务器”（在“针对 VMware 和物理计算机”标题下）中，选择配置服务器。
2. 在打开的“配置服务器详细信息”页中，单击“+ 进程服务器”

  ![添加进程服务器库](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps.png)

3.  在“添加进程服务器”页上，选择以下值

  ![添加进程服务器库项](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps-page-1.png)
|**字段名称**|**值**|
|-|-|
|选择要部署进程服务器的位置|选择值“在 Azure 中部署故障回复进程服务器” |
|订阅|选择已将虚拟机故障转移到的 Azure 订阅|
|资源组|可以创建资源组以部署此进程服务器，或者选择在现有资源组中部署进程服务器|
|Location|选择虚拟机已故障转移到的 Azure 数据中心|
|Azure 网络|选择虚拟机已故障转移到的 Azure 虚拟网络(VNet)。 如果已将虚拟机故障转移到多个 Azure VNet，则需要为每个 VNet 部署一个进程服务器|

4. 填写进程服务器的其余属性

  ![添加进程服务器摘要](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps-page-2.png)
|**字段名称**|**值**|
|-|-|
|服务器名称|进程服务器虚拟机的显示名称和主机名|
| 用户名|成为该虚拟机上的管理员的用户名|
|存储帐户|放置虚拟机的虚拟磁盘的存储帐户名称|
|子网|虚拟机将连接到的 Azure VNet 的子网|
| IP 地址|想要进程服务器在启动后采用的 IP 地址|
5. 单击“确定”按钮开始部署进程服务器虚拟机。

> [!NOTE]
> 要能够使用此进程服务器进行故障回复，需要将其注册到本地配置服务器。

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>将进程服务器（在 Azure 中运行）注册到配置服务器（在本地运行）

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

## <a name="upgrading-the-process-server-to-latest-version"></a>将进程服务器升级到最新版本。

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-upgrade-process-server.md)]

## <a name="unregistering-the-process-server-running-in-azure-from-a-configuration-server-running-on-premises"></a>将进程服务器（在 Azure 中运行）从配置服务器（在本地运行）中注销

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]
