---
title: "替换 Azure 堆栈中的物理磁盘 |Microsoft 文档"
description: "概述了如何替换 Azure 堆栈中的物理磁盘的过程。"
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: 26e5e9f8882cc764922a2cbf0f39e7a3d1b6995b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="replace-a-physical-disk-in-azure-stack"></a>替换 Azure 堆栈中的物理磁盘

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

本文介绍替换 Azure 堆栈中的物理磁盘的一般过程。 如果发生物理磁盘故障，你应尽可能快地替换它。

对于集成系统，而且对于开发工具包部署具有热插拔的磁盘，你可以使用此过程。

步骤将会有所不同的实际磁盘替换基于原始设备制造商 (OEM) 硬件供应商联系。 请参阅特定于你的系统的详细步骤的供应商的字段可更换部件 (FRU) 文档。 

## <a name="review-disk-alert-information"></a>查看磁盘警报信息
当发生磁盘故障时，你会收到警报，告知您连接已丢失的物理磁盘。 

 ![警报显示连接已断开至物理磁盘](media/azure-stack-replace-disk/DiskAlert.png)

如果打开的警报，警报描述包含缩放单元节点和磁盘，必须对其进行替换的确切物理槽位置。 进一步 azure 堆栈可帮助你通过使用 LED 指示符功能确定故障的磁盘。

 ## <a name="replace-the-disk"></a>替换磁盘

请按照 OEM 硬件供应商的实际磁盘替换 FRU 说明。

若要防止在集成的系统的不受支持的磁盘使用，则系统会阻止不受你的供应商的磁盘。 如果你尝试使用不受支持的磁盘，则会出现新的警报，告知你磁盘已被隔离由于的不受支持的模型或固件。

更换磁盘后，Azure 堆栈将自动发现新磁盘，并启动虚拟磁盘修复过程。  
 
 ## <a name="check-the-status-of-virtual-disk-repair"></a>请检查虚拟磁盘修复的状态
 
 更换磁盘后，你可以监视虚拟磁盘运行状况状态，并通过使用特权终结点修复作业进度。 从具有特权的终结点的网络连接的任何计算机，请按照下列步骤。

1. 打开 Windows PowerShell 会话并连接到特权终结点。
    ````PowerShell
        $cred = Get-Credential
        Enter-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
    ```` 
  
2. 运行以下命令以查看虚拟磁盘运行状况：
    ````PowerShell
        Get-VirtualDisk -CimSession s-cluster
    ````
   ![Powershell Get-virtualdisk 命令输出](media/azure-stack-replace-disk/GetVirtualDiskOutput.png)

3. 运行以下命令以查看当前的存储作业状态：
    ```PowerShell
        Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
    ````
      ![Get-storagejob 命令 Powershell 输出](media/azure-stack-replace-disk/GetStorageJobOutput.png)

## <a name="troubleshoot-virtual-disk-repair"></a>对虚拟磁盘修复进行故障排除

如果虚拟磁盘修复作业会出现停滞，运行以下命令以重新启动作业：
  ````PowerShell
        Get-VirtualDisk -CimSession s-cluster | Repair-VirtualDisk
  ```` 
