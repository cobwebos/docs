---
title: 设置源环境以便使用 Azure Site Recovery 从 VMware 复制到 Azure | Microsoft Docs
description: 本文介绍如何设置本地环境，以便使用 Azure Site Recovery 将 VMware VM 复制到 Azure。
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 536b7ed21e7dd54fcbea97951330b08925961713
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57884959"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>设置源环境以便从 VMware 复制到 Azure

本文介绍如何设置源本地环境，以便将 VMware VM 复制到 Azure。 它包括以下相关步骤：选择复制方案、将本地计算机设置为 Site Recovery 配置服务器和自动发现本地 VM。 

## <a name="prerequisites"></a>必备组件

本文假设已完成以下操作：

- 借助 [Azure Site Recovery 部署规划器](site-recovery-deployment-planner.md)规划了你的部署。 这可以帮助你根据每日数据更改率分配足够的带宽以满足你需要的恢复点目标 (RPO)。
- 在 [Azure 门户](https://portal.azure.com)中[设置资源](tutorial-prepare-azure.md)。
- [设置本地 VMware](vmware-azure-tutorial-prepare-on-premises.md)，包括用于自动发现的专用帐户。

## <a name="choose-your-protection-goals"></a>选择保护目标

1. 在“恢复服务保管库”中，选择保管库名称。 我们在此方案中使用 **ContosoVMVault**。
2. 在“入门”中，选择“Site Recovery”， 然后选择“准备基础结构”。
3. 在“保护目标” > “计算机所在位置”中，选择“本地”。
4. 在“要将计算机复制到何处?”中，选择“复制到 Azure”。
5. 在“你的计算机是否已虚拟化”中，选择“是，带有 VMware vSphere 虚拟机监控程序”。 然后选择“确定”。

## <a name="set-up-the-configuration-server"></a>设置配置服务器

可以通过开放虚拟化应用程序 (OVA) 模板将配置服务器设置为本地 VMware VM。 [详细了解](concepts-vmware-to-azure-architecture.md)将在 VMware VM 上安装的组件。

1. 了解配置服务器部署的[先决条件](vmware-azure-deploy-configuration-server.md#prerequisites)。
2. [检查容量数字](vmware-azure-deploy-configuration-server.md#capacity-planning)以便进行部署。
3. [下载](vmware-azure-deploy-configuration-server.md#download-the-template)并[导入](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) OVA 模板，设置运行配置服务器的本地 VMware VM。 模板一起提供的许可证是评估许可证，有效期为 180 天。 发布此期间，客户需要激活 windows 的已采购的许可证。
4. 打开 VMware VM，并将其[注册](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services)到恢复服务保管库中。

## <a name="azure-site-recovery-folder-exclusions-from-antivirus-program"></a>从防病毒程序中排除 Azure Site Recovery 文件夹

### <a name="if-antivirus-software-is-active-on-source-machine"></a>如果防病毒软件在源计算机上处于活动状态

如果源计算机具有处于活动状态的防病毒软件，则应排除安装文件夹。 因此，为了平稳进行复制，请排除文件夹 *C:\ProgramData\ASR\agent*。

### <a name="if-antivirus-software-is-active-on-configuration-server"></a>如果防病毒软件在配置服务器上处于活动状态

为了平稳进行复制并避免出现连接问题，请从防病毒软件中排除以下文件夹

- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\Program Files\Microsoft Azure Site Recovery Provider
- C:\Program Files\Microsoft Azure Site Recovery Configuration Manager 
- C:\Program Files\Microsoft Azure Site Recovery Error Collection Tool 
  - C:\thirdparty
  - C:\Temp
  - C:\strawberry
  - C:\ProgramData\MySQL
  - C:\Program Files (x86)\MySQL
  - C:\ProgramData\ASR
  - C:\ProgramData\Microsoft Azure Site Recovery
  - C:\ProgramData\ASRLogs
  - C:\ProgramData\ASRSetupLogs
  - C:\ProgramData\LogUploadServiceLogs
  - C:\inetpub
  - ASR 服务器安装目录。 例如：E:\Program Files (x86)\Microsoft Azure Site Recovery

### <a name="if-antivirus-software-is-active-on-scale-out-process-servermaster-target"></a>如果防病毒软件在横向扩展流程服务器/主目标上处于活动状态

请从防病毒软件中排除以下文件夹

1. C:\Program Files\Microsoft Azure Recovery Services Agent
2. C:\ProgramData\ASR
3. C:\ProgramData\ASRLogs
4. C:\ProgramData\ASRSetupLogs
5. C:\ProgramData\LogUploadServiceLogs
6. C:\ProgramData\Microsoft Azure Site Recovery
7. ASR 负载均衡进程服务器安装目录，示例：C:\Program Files (x86)\Microsoft Azure Site Recovery


## <a name="next-steps"></a>后续步骤
[设置目标环境](./vmware-azure-set-up-target.md) 
