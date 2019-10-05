---
title: 在 VMware VM 灾难恢复到 Azure 期间使用 Azure Site Recovery ，对故障回复到本地进行故障排除 | Microsoft Docs
description: 本文介绍了在 VMware VM 灾难恢复到 Azure 期间使用 Azure Site Recovery 排查故障回复和重新保护问题的方法。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: raynew
ms.openlocfilehash: c27e72333618f73b67eec9b5c0c3a70239a1c0b3
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970855"
---
# <a name="troubleshoot-vcenter-discovery-failures"></a>排查 vCenter 发现故障

本文将帮助你排查由于 VMware vCenter 发现失败而发生的问题。

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>MaxSnapShots 属性中的非数字值

在9.20 之前的版本中，vCenter 在检索虚拟机上的属性 `snapshot.maxSnapShots` 属性时断开连接。

此问题由错误 ID 95126 标识。

    ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
    System.FormatException: Input string was not in a correct format.
       at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
       at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
       at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
    
若要解决问题，请执行以下操作：

- 标识 VM，并将值设置为数值（vCenter 中的 VM 编辑设置）。

或

- 将配置服务器升级到版本9.20 或更高版本。

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>VCenter 连接的代理配置问题

vCenter 发现采用系统用户配置的系统默认代理设置。 DRA 服务在使用统一安装程序安装程序或 .OVA 模板安装配置服务器的过程中遵守用户提供的代理设置。 

通常，代理用于与公用网络通信;例如，与 Azure 通信。 如果已配置代理并且 vCenter 在本地环境中，则它无法与 DRA 通信。

遇到此问题时，会发生以下情况：

- 由于以下错误，无法访问 vCenter server \<vCenter >：远程服务器返回错误：（503）服务器不可用
- 由于以下错误，无法访问 vCenter server \<vCenter >：远程服务器返回错误：无法连接到远程服务器。
- 无法连接到 vCenter/ESXi 服务器。

若要解决问题，请执行以下操作：

下载 [PsExec 工具](https://aka.ms/PsExec)。 

使用 PsExec 工具来访问系统用户上下文并确定是否配置了代理地址。 然后，可以使用以下过程将 vCenter 添加到跳过列表。

对于发现代理配置：

1. 使用 PsExec 工具在系统用户上下文中打开 IE。
    
    psexec-s-i "%Programfiles%\internet explorer\ Explorer\iexplore.exe"

2. 修改 Internet Explorer 中的代理设置，以绕过 vCenter IP 地址。
3. 重新启动 tmanssvc 服务。

对于 DRA 代理配置：

1. 打开命令提示符并打开 Microsoft Azure Site Recovery 提供程序 "文件夹。
 
    **cd C:\Program Files\Microsoft Azure Site Recovery 提供程序**

3. 在命令提示符下，运行以下命令。
   
   **DRCONFIGURATOR.EXE.EXE/configure/AddBypassUrls [在添加 vCenter 时提供的 vCenter Server 的 IP 地址/FQDN]**

4. 重新启动 DRA 提供程序服务。

## <a name="next-steps"></a>后续步骤

[为 VMware VM 灾难恢复管理配置服务器](https://docs.microsoft.com/azure/site-recovery/vmware-azure-manage-configuration-server#refresh-configuration-server) 
