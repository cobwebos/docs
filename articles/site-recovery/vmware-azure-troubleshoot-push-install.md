---
title: 从 VMware 到 Azure 的 Azure Site Recovery 故障排除 | Microsoft 文档
description: 解决复制 Azure 虚拟机时出现的错误。
services: site-recovery
author: anoopkv
manager: gauravd
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: bdd5d87670ddb26d2c4474a319fe19bef1db62f5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
ms.locfileid: "29811414"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>解决移动服务推送安装问题

本文介绍如何排查尝试在源服务器上安装 Azure Site Recovery 移动服务以启用保护时可能遇到的常见错误。

## <a name="error-78007---the-requested-operation-could-not-be-completed"></a>Error 78007 - 无法完成请求的操作
服务引发此错误的原因可能有多种。 请选择相应的提供程序错误，以进一步进行排除故障。

* [错误 95103](#error-95103---protection-could-not-be-enabled-ep0854) 
* [错误 95105](#error-95105---protection-could-not-be-enabled-ep0856) 
* [错误 95107](#error-95107---protection-could-not-be-enabled-ep0858) 
* [错误 95108](#error-95108---protection-could-not-be-enabled-ep0859) 
* [错误 95117](#error-95117---protection-could-not-be-enabled-ep0865) 
* [错误 95213](#error-95213---protection-could-not-be-enabled-ep0874) 
* [错误 95224](#error-95224---protection-could-not-be-enabled-ep0883) 
* [错误 95265](#error-95265---protection-could-not-be-enabled-ep0902) 


## <a name="error-95105---protection-could-not-be-enabled-ep0856"></a>错误 95105 - 无法启用保护 (EP0856)

**错误代码** | **可能的原因** | **错误特定建议**
--- | --- | ---
95105 </br>**消息：** 移动服务到源计算机的推送安装失败，错误代码为 **EP0856**。 <br> 源计算机上不允许启用“文件和打印机共享”，或进程服务器和源计算机之间的网络连接出现问题。| 未启用“文件和打印机共享”。 | 在源计算机上的 Windows 防火墙中允许“文件和打印机共享”。 在源计算机上，在“Windows 防火墙” > “允许应用或功能通过防火墙”下，为所有配置文件选择“文件和打印机共享”。 </br> 此外，请检查以下先决条件以成功完成推送安装。<br> 详细了解如何[排查 WMI 问题](#troubleshoot-wmi-issues)。


## <a name="error-95107---protection-could-not-be-enabled-ep0858"></a>错误 95107 - 无法启用保护 (EP0858)

**错误代码** | **可能的原因** | **错误特定建议**
--- | --- | ---
95107 </br>**消息：** 移动服务到源计算机的推送安装失败，错误代码为 **EP0858**。 <br> 提供的用于安装移动服务的凭据不正确或用户帐户没有足够权限。 | 为在源计算机上安装移动服务而提供的用户凭据不正确。 | 请确保为配置服务器上的源计算机提供的用户凭据正确。 <br> 若要添加或编辑用户凭据，请转到配置服务器并选择“Cspsconfigtool” > “管理帐户”。 </br> 此外，请检查以下[先决条件](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery)以成功完成推送安装。


## <a name="error-95117---protection-could-not-be-enabled-ep0865"></a>错误 95117 - 无法启用保护 (EP0865)

**错误代码** | **可能的原因** | **错误特定建议**
--- | --- | ---
95117 </br>**消息：** 移动服务到源计算机的推送安装失败，错误代码为 **EP0865**。 <br> 源计算机未运行，或进程服务器和源计算机之间的网络连接出现问题。 | 进程服务器和源服务器之间的网络连接性问题。 | 检查进程服务器和源服务器之间的连接性。 </br> 此外，请检查以下[先决条件](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery)以成功完成推送安装。|

## <a name="error-95103---protection-could-not-be-enabled-ep0854"></a>错误 95103 - 无法启用保护 (EP0854)

**错误代码** | **可能的原因** | **错误特定建议**
--- | --- | ---
95103 </br>**消息：** 移动服务到源计算机的推送安装失败，错误代码为 **EP0854**。 <br> 源计算机上不允许启用 Windows Management Instrumentation (WMI)，或进程服务器和源计算机之间的网络连接出现问题。| Windows 防火墙中阻止了 WMI。 | 在 Windows 防火墙中允许 WMI。 在“Windows 防火墙” > “允许应用或功能通过防火墙”下，选择“为所有配置文件选择 WMI”。 </br> 此外，请检查以下[先决条件](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery)以成功完成推送安装。|

## <a name="error-95213---protection-could-not-be-enabled-ep0874"></a>错误 95213 - 无法启用保护 (EP0874)

**错误代码** | **可能的原因** | **错误特定建议**
--- | --- | ---
95213 </br>**消息：** 向源计算机 %SourceIP 安装移动服务失败，错误代码为 **EP0874**。 <br> | 源计算机上的操作系统版本不受支持。 <br>| 请确保源计算机 OS 版本受支持。 阅读[支持矩阵](https://aka.ms/asr-os-support)。 </br> 此外，请检查以下[先决条件](https://aka.ms/pushinstallerror)以成功完成推送安装。| 


## <a name="error-95108---protection-could-not-be-enabled-ep0859"></a>错误 95108 - 无法启用保护 (EP0859)

**错误代码** | **可能的原因** | **错误特定建议**
--- | --- | ---
95108 </br>**消息：** 移动服务到源计算机的推送安装失败，错误代码为 **EP0859**。 <br>| 提供的用于安装移动服务的凭据不正确或用户帐户没有足够权限。 <br>| 请确保提供的凭据是**根**帐户的凭据。 若要添加或编辑用户凭据，请转到配置服务器并选择桌面上的 **Cspsconfigtool** 快捷方式图标。 选择“管理帐户”以添加或编辑凭据。|

## <a name="error-95265---protection-could-not-be-enabled-ep0902"></a>错误 95265 - 无法启用保护 (EP0902)

**错误代码** | **可能的原因** | **错误特定建议**
--- | --- | ---
95265 </br>**消息：** 向源计算机推送移动服务安装成功，但需要重启源计算机才能使某些系统更改生效。 <br>| 服务器上已安装了移动服务的旧版本。| 虚拟机复制能够顺畅继续执行。<br> 在下一个维护时段请重新启动服务器，以利用移动服务中的新增强功能。|


## <a name="error-95224---protection-could-not-be-enabled-ep0883"></a>错误 95224 - 无法启用保护 (EP0883)

**错误代码** | **可能的原因** | **错误特定建议**
--- | --- | ---
95224 </br>**消息：** 向源计算机 %SourceIP; 推送移动服务安装失败，错误代码为 **EP0883**。 在上次安装或更新后进行的系统重新启动挂起。| 卸载较旧的或不兼容版本的移动服务时，系统未重新启动。| 请确保服务器上不存在任何版本的移动服务。 <br> 重新启动服务器，然后重新运行“启用保护”作业。|

## <a name="resource-to-troubleshoot-push-installation-problems"></a>用于排查推送安装问题的资源

#### <a name="troubleshoot-file-and-print-sharing-issues"></a>排查文件和打印共享问题
* [使用组策略启用或禁用文件共享](https://technet.microsoft.com/library/cc754359(v=ws.10).aspx)
* [通过 Windows 防火墙启用文件和打印共享](https://technet.microsoft.com/library/ff633412(v=ws.10).aspx)

#### <a name="troubleshoot-wmi-issues"></a>排查 WMI 问题
* [基本 WMI 测试](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [WMI 故障排除](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [使用 WMI 脚本和 WMI 服务排查问题](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="next-steps"></a>后续步骤

[了解如何](vmware-azure-tutorial.md)为 VMware VM 设置灾难恢复。