---
title: 在 VMware VM 灾难恢复到 Azure 期间使用 Azure Site Recovery ，对故障回复到本地进行故障排除 | Microsoft Docs
description: 本文介绍了在 VMware VM 灾难恢复到 Azure 期间使用 Azure Site Recovery 排查故障回复和重新保护问题的方法。
author: vDonGlover
manager: JarrettRenshaw
ms.service: site-recovery
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: v-doglov
ms.openlocfilehash: c598c5e238458c010500579c5371622b85e71de0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60565185"
---
# <a name="troubleshoot-vcenter-discovery-failures"></a>排查 vCenter 发现故障

本文可帮助你排查问题由于 VMware vCenter 发现失败而导致的。

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>MaxSnapShots 属性中的非数字值

9\.20 之前版本中，在 vCenter 断开的连接检索属性的非数字值时`snapshot.maxSnapShots`VM 上的属性。

错误 ID 95126 由标识此问题。

    ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
    System.FormatException: Input string was not in a correct format.
       at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
       at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
       at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
    
若要解决问题，请执行以下操作：

- 确定 VM 并将值设置为数字值 （在 vCenter 中的编辑 VM 设置）。

或

- 将配置服务器升级到 9.20 或更高版本。

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>VCenter 的连接的代理配置问题

vCenter 发现遵循通过系统用户配置的系统默认代理设置。 DRA 服务遵循用户在使用统一安装程序安装程序或 OVA 模板的配置服务器的安装过程中提供的代理设置。 

一般情况下，使用代理进行通信向公共网络;例如，与 Azure 通信。 如果配置了代理并且 vCenter 在本地环境，它不能与 DRA 进行通信。

遇到此问题时，可能发生以下情况：

- VCenter 服务器\<vCenter > 由于出现错误不可访问：远程服务器返回错误：不可用 (503) 服务器
- VCenter 服务器\<vCenter > 由于出现错误不可访问：远程服务器返回错误：无法连接到远程服务器。
- 无法连接到 vCenter/ESXi 服务器。

若要解决问题，请执行以下操作：

下载 [PsExec 工具](https://aka.ms/PsExec)。 

使用 PsExec 工具访问系统用户上下文来确定是否为配置了代理地址。 然后可以使用以下过程在绕过列表到添加 vCenter。

发现代理配置：

1. 在系统用户上下文中使用的 PsExec 工具打开 IE。
    
    psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"

2. 修改在 Internet Explorer 中绕过 vCenter IP 地址的代理设置。
3. 重新启动 tmanssvc 服务。

DRA 代理配置：

1. 打开命令提示符并打开 Microsoft Azure Site Recovery 提供程序文件夹。
 
    **cd C:\Program Files\Microsoft Azure Site Recovery 提供程序**

3. 从命令提示符处，运行以下命令。
   
   **DRCONFIGURATOR。EXE / 配置 /AddBypassUrls [IP 地址/FQDN 的 vCenter 服务器添加 vCenter 时提供]**

4. 重新启动的 DRA 提供程序服务。

## <a name="next-steps"></a>后续步骤

[管理配置服务器以进行 VMware VM 灾难恢复](https://docs.microsoft.com/azure/site-recovery/vmware-azure-manage-configuration-server#refresh-configuration-server) 
