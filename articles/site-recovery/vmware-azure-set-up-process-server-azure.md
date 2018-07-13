---
title: 在 Azure 中设置进程服务器，以便使用 Azure Site Recovery 进行 VMware VM 和物理服务器故障回复 | Microsoft Docs
description: 本文介绍如何在 Azure 中设置进程服务器，以便将 Azure VM 故障回复到 VMware。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: 20c3642bb9f9ad5089c37bb6da8562e570896cb4
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38308474"
---
# <a name="set-up-a-process-server-in-azure-for-failback"></a>在 Azure 中设置进程服务器以便进行故障回复

使用 [Site Recovery](site-recovery-overview.md) 将 VMware VM 或物理服务器故障转移到 Azure 后，可以在其重新启动并运行时将其故障回复到本地站点。 若要进行故障回复，需要在 Azure 中设置临时进程服务器，以便处理从 Azure 到本地的复制。 故障回复完成后，可以删除此 VM。

## <a name="before-you-start"></a>开始之前

详细了解[重新保护](vmware-azure-reprotect.md)和[故障回复](vmware-azure-failback.md)过程。

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]

## <a name="deploy-a-process-server-in-azure"></a>在 Azure 中部署进程服务器

1. 在保管库 >“Site Recovery 基础结构”> “管理” > “配置服务器”中，选择配置服务器。
2. 在服务器页中，单击“+ 进程服务器”
3. 在“添加进程服务器”页中，选择在 Azure 中部署进程服务器。
4. 指定 Azure 设置，包括用于故障转移的订阅、资源组、用于故障转移的 Azure 区域以及 Azure VM 所位于的虚拟网络。 如果使用多个 Azure 网络，则需要在每个 Azure 网络中部署进程服务器。

  ![添加进程服务器库项](./media/vmware-azure-set-up-process-server-azure/add-ps-page-1.png)

4. 在**服务器名称**、**用户名**和**密码**中指定进程服务器的名称以及将为其分配服务器上的管理员权限的凭据。
5. 指定要用于服务器 VM 磁盘的存储帐户、进程服务器 VM 将位于的子网以及 VM 启动时将分配的服务器 IP 地址。
6. 单击“确定”按钮开始部署进程服务器 VM。

>

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>将进程服务器（在 Azure 中运行）注册到配置服务器（在本地运行）

在进程服务器 VM 已启动并运行后，需要将其注册到本地配置服务器，如下所示：

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]


