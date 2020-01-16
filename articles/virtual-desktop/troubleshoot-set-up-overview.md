---
title: Windows 虚拟桌面故障排除概述-Azure
description: 有关在设置 Windows 虚拟桌面租户环境时解决问题的概述。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/06/2019
ms.author: helohr
ms.openlocfilehash: ce1af278d67f96873ed611db8dc05938205638dc
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980243"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>故障排除概述、反馈和支持

本文概述了在设置 Windows 虚拟桌面租户环境时可能会遇到的问题，并提供解决这些问题的方法。

## <a name="provide-feedback"></a>提供反馈

请访问 [Windows 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，与产品团队和活跃的社区成员共同探讨 Windows 虚拟桌面服务。

## <a name="escalation-tracks"></a>升级跟踪

使用下表来确定和解决使用远程桌面客户端设置租户环境时可能遇到的问题。 设置租户后，可以使用新的[诊断服务](https://docs.microsoft.com/azure/virtual-desktop/diagnostics-role-service)来识别常见方案的问题。

>[!NOTE]
> 我们有一个技术社区论坛，你可以访问它来讨论产品团队和活动社区成员的问题。 请访问[Windows 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，

| **问题**                                                            | **建议的解决方案**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| 创建租户                                                    | 如果出现 Azure 中断，请联系[Azure 支持](https://azure.microsoft.com/support/options/)部门;否则 **，请为 Windows 虚拟桌面（计算）打开支持请求**。|
| 在 Azure 门户中访问 Marketplace 模板       | 如果出现 Azure 中断，请联系[Azure 支持](https://azure.microsoft.com/support/options/)部门。 <br> <br> Azure Marketplace Windows 虚拟桌面模板可免费使用。|
| 从 GitHub 访问 Azure 资源管理器模板                                  | 请参阅[租户和主机池创建](troubleshoot-set-up-issues.md)的 "创建 Windows 虚拟桌面会话主机 vm" 部分。 如果仍未解决问题，请联系[GitHub 支持团队](https://github.com/contact)。 <br> <br> 如果在访问 GitHub 中的模板之后出现错误，请联系[Azure 支持](https://azure.microsoft.com/support/options/)。|
| 会话主机池 Azure 虚拟网络（VNET）和快速路由设置               | 联系**Azure 支持（网络）** 。 |
| 当未使用随 Windows 虚拟桌面提供的 Azure 资源管理器模板时，会话主机池虚拟机（VM）创建 | 联系**Azure 支持部门（计算）** 。 <br> <br> 有关随 Windows 虚拟桌面一起提供的 Azure 资源管理器模板的问题，请参阅创建[租户和主机池创建](troubleshoot-set-up-issues.md)的 Windows 虚拟桌面租户部分。 |
| 从 Azure 门户管理 Windows 虚拟桌面会话主机环境    | 联系**Azure 支持部门**。 <br> <br> 有关使用远程桌面服务/Windows 虚拟桌面 PowerShell 时的管理问题，请参阅[Windows 虚拟桌面 powershell](troubleshoot-powershell.md)或**为 windows 虚拟桌面（计算）打开支持请求**。 |
| 管理绑定到主机池和应用程序组的 Windows 虚拟桌面配置（应用组）      | 请参阅[Windows 虚拟桌面 PowerShell](troubleshoot-powershell.md)，或**提出对 windows 虚拟桌面（计算）的支持请求**。 <br> <br> 如果问题与示例图形用户界面（GUI）相关联，请联系 Yammer 社区。|
| 远程桌面客户端在启动时无法正常工作                                                 | 请参阅对[远程桌面客户端进行故障排除](troubleshoot-client.md)，如果这不能解决问题，请**为 Windows 虚拟桌面（计算）提供支持请求**。  <br> <br> 如果这是网络问题，用户需要联系其网络管理员。 |
| 已连接但无源                                                                 | 排查[远程桌面客户端故障](troubleshoot-client.md)时使用 "用户连接但不显示任何数据源（无源）" 部分。 <br> <br> 如果已将用户分配到应用组，请**打开对 Windows 虚拟机（计算）的支持请求**。 |
| 由于网络导致的源发现问题                                            | 用户需要联系其网络管理员。 |
| 连接客户端                                                                    | 请参阅[Windows 虚拟桌面服务连接](troubleshoot-service-connection.md)，如果这不能解决你的问题，请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration.md)。 |
| 远程应用程序或桌面的响应能力                                      | 如果问题与特定应用程序或产品相关，请联系负责该产品的团队。 |
| 授权消息或错误                                                          | 如果问题与特定应用程序或产品相关，请联系负责该产品的团队。 |
| 使用 GitHub 上的 Windows 虚拟桌面工具时的问题（Azure 资源管理器模板、诊断工具、管理工具） | 若要报告问题，请参阅[Azure 资源管理器模板远程桌面服务](https://github.com/Azure/RDS-Templates/blob/master/README.md)。 |

## <a name="next-steps"></a>后续步骤

- 若要在 Windows 虚拟桌面环境中创建租户和主机池时排查问题，请参阅[租户和主机池创建](troubleshoot-set-up-issues.md)。
- 若要解决在 Windows 虚拟桌面中配置虚拟机（VM）时遇到的问题，请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration.md)。
- 若要解决 Windows 虚拟桌面客户端连接问题，请参阅[Windows 虚拟桌面服务连接](troubleshoot-service-connection.md)。
- 若要解决远程桌面客户端的问题，请参阅[排查远程桌面客户端](troubleshoot-client.md)问题
- 若要解决将 PowerShell 与 Windows 虚拟桌面结合使用时遇到的问题，请参阅[Windows 虚拟桌面 PowerShell](troubleshoot-powershell.md)。
- 若要了解有关该服务的详细信息，请参阅[Windows 虚拟桌面环境](environment-setup.md)。
- 若要浏览疑难解答教程，请参阅[教程：排查资源管理器模板部署问题](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
- 若要了解审核操作，请参阅[使用 Resource Manager 执行审核操作](../azure-resource-manager/management/view-activity-logs.md)。
- 若要了解有关在部署期间确定错误的操作，请参阅[查看部署操作](../azure-resource-manager/templates/deployment-history.md)。
