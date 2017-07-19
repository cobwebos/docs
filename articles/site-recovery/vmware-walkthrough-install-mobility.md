---
title: "安装 Mobility Service 以便将 VMware 复制到 Azure | Microsoft Docs"
description: "本文介绍了如何安装 Mobility Service 代理，以便使用 Azure Site Recovery 服务将 VMware 复制到 Azure。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 3189fbcd-6b5b-4ffb-b5a9-e2080c37f9d9
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: bc520bd2ea54208889861a7a3b275e3008a05d53
ms.contentlocale: zh-cn
ms.lasthandoff: 06/29/2017

---

# <a name="step-10-install-the-mobility-service"></a>步骤 10：安装 Mobility Service


本文介绍了在 Azure 门户中使用 [Azure Site Recovery](site-recovery-overview.md) 服务将本地 VMware 虚拟机复制到 Azure 时，如何配置源和目标设置。

Mobility Service 可以捕获计算机上的数据写入，并将其转发给进程服务器。 应在要复制到 Azure 的每台计算机上安装 Mobility Service。

可以手动安装 Mobility Service，也可以在启用复制后，从 Site Recovery 进程服务器推送安装，亦可以使用 System Center Configuration Manager 工具进行安装。 若要推送安装，请在启用复制后在 VM上安装 Mobility Service。

请将评论和问题发布到这篇文章的底部，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。

## <a name="install-manually"></a>手动安装

1. 查看手动安装的[先决条件](site-recovery-vmware-to-azure-install-mob-svc.md#prerequisites)。
2. 按照[这些说明](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui)操作，使用门户进行手动安装。
3. 如果想要从命令行进行安装，请按照[这些说明](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-at-a-command-prompt)进行操作。

## <a name="install-from-the-process-server"></a>从进程服务器安装

若要在为 VM 启用复制后从进程服务器推送 Mobility Service 安装，需要有一个可供进程服务器访问 VM 的帐户。 该帐户仅用于推送安装。

1. 应[创建了一个帐户](vmware-walkthrough-prepare-vmware.md)用于推送安装。 然后指定在 Site Recovery 部署过程中配置源设置时要使用的帐户。
2. 若要在运行 Windows 或 Linux 的 VM 上推送 Mobility Service，请按照[这些说明](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery)进行操作。

## <a name="other-methods"></a>其他方法

详细了解如何[使用 Configuration Manager 安装 Mobility Service](site-recovery-install-mobility-service-using-sccm.md) 或使用 [Azure Automation DSC](site-recovery-automate-mobility-service-install.md) 安装 Mobility Service。

## <a name="next-steps"></a>后续步骤

转到[步骤 11：启用复制](vmware-walkthrough-enable-replication.md)

