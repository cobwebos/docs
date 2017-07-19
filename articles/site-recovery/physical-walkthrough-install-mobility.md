---
title: "安装 Mobility Service 以便将物理服务器复制到 Azure | Microsoft Docs"
description: "本文介绍了如何在物理服务器上安装 Mobility Service 代理，以便使用 Azure Site Recovery 服务将物理服务器复制到 Azure。"
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
ms.openlocfilehash: d73267d7a64221a3138af19e9a2d5dd15809b927
ms.contentlocale: zh-cn
ms.lasthandoff: 06/29/2017

---

# <a name="step-9-install-the-mobility-service"></a>步骤 9：安装移动服务


本文介绍了如何安装 Mobility Service 组件，以便在 Azure 门户中使用 [Azure Site Recovery](site-recovery-overview.md) 服务将本地 Windows/Linux 物理服务器复制到 Azure。

Mobility Service 可以捕获计算机上的数据写入，并将其转发给进程服务器。 应在要复制到 Azure 的每个服务器上安装 Mobility Service。

可以手动安装 Mobility Service，也可以在启用复制后，从 Site Recovery 进程服务器推送安装，亦可以使用 System Center Configuration Manager 等工具。 若要推送安装，请在启用复制后在服务器上安装 Mobility Service。

请将评论和问题发布到这篇文章的底部，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。

## <a name="install-manually"></a>手动安装

1. 查看手动安装的[先决条件](site-recovery-vmware-to-azure-install-mob-svc.md#prerequisites)。
2. 按照[这些说明](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui)操作，使用门户进行手动安装。
3. 若要通过命令行进行安装，请按照[这些说明](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-at-a-command-prompt)操作。

## <a name="install-from-the-process-server"></a>从进程服务器安装

若要在为计算机启用复制后从进程服务器推送 Mobility Service 安装，需要有一个可供进程服务器访问计算机的帐户。 该帐户仅用于推送安装。

1. 如果尚未创建帐户，请遵循以下指南创建一个：

    - 可以使用域帐户，也可以使用本地帐户
    - 对于 Windows，如果你使用的不是域帐户，则需在本地计算机上禁用远程用户访问控制。 为此，请在注册表中的 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** 下添加值为 1 的 DWORD 项 **LocalAccountTokenFilterPolicy**。
    - 若要通过 CLI 添加 Windows 注册表项，请键入：

        ```
        REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.
        ```

    - 对于 Linux，该帐户应是源 Linux 服务器上的根。

2. 若要在运行 Windows 或 Linux 的 VM 上推送 Mobility Service ，请按照[这些说明](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery)操作。

## <a name="other-installation-methods"></a>其他安装方法

- [了解](site-recovery-install-mobility-service-using-sccm.md)如何使用 Configuration Manager 安装 Mobility Service
- [了解](site-recovery-automate-mobility-service-install.md)如何使用 Azure Automation DSC 安装 Mobility Service。


## <a name="next-steps"></a>后续步骤

转到[第 10 步：启用复制](physical-walkthrough-enable-replication.md)

