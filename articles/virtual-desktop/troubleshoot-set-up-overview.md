---
title: Windows 虚拟桌面进行故障排除概述、 反馈和支持-Azure
description: 概述用于设置的 Windows 虚拟桌面租户环境时解决问题。
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: b349d7f384c801e95d745f7a18535f9f5d13cf86
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605211"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>故障排除概述、反馈和支持

本文概述了设置的 Windows 虚拟桌面租户环境时可能会遇到并提供方法来解决问题的问题。

## <a name="provide-feedback"></a>提供反馈

目前我们不会受理 Windows 虚拟桌面预览版的支持案例。 请访问 [Windows 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，与产品团队和活跃的社区成员共同探讨 Windows 虚拟桌面服务。

## <a name="escalation-tracks"></a>升级跟踪

使用下表来确定和解决设置使用远程桌面客户端的租户环境时可能遇到的问题。 你的租户的设置后，你可以使用我们的新[诊断服务](https://docs.microsoft.com/azure/virtual-desktop/diagnostics-role-service)来识别常见方案的问题。

>[!NOTE]
>目前我们不会受理 Windows 虚拟桌面预览版的支持案例。 只要我们引用 Windows 虚拟桌面支持，请转到我们的技术社区论坛现在。 请访问[Windows 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)讨论与产品团队和活跃的社区成员的问题。 如果你需要解决支持问题，包括活动 ID 和近似时间范围时，出现了问题。

| **问题**                                                            | **建议的解决方案**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| 创建租户                                                    | 如果 Azure 服务中断，请联系[Azure 支持](https://azure.microsoft.com/support/options/); 否则为联系人**远程桌面服务/Windows 虚拟桌面支持**。|
| 访问 Azure 门户中的 Marketplace 模板       | 如果 Azure 服务中断，请联系[Azure 支持](https://azure.microsoft.com/support/options/)。 <br> <br> Azure Marketplace Windows 虚拟桌面模板是免费提供。|
| 从 GitHub 中访问 Azure 资源管理器模板                                  | 请参阅的"创建 Windows 虚拟桌面会话主机的虚拟机"部分[租户以及主机池创建](troubleshoot-set-up-issues.md)。 如果仍未解决此问题，请联系[GitHub 支持团队](https://github.com/contact)。 <br> <br> 如果访问 GitHub 中的模板后，出现错误，请联系[Azure 支持](https://azure.microsoft.com/support/options/)。|
| 会话主机池 Azure 虚拟网络 (VNET) 和 Express 路由设置               | 请联系**Azure 支持 （网络）** 。 |
| 会话主机池时不会使用与 Windows 虚拟桌面提供的 Azure 资源管理器模板创建虚拟机 (VM) | 请联系**Azure 支持 （计算）** 。 <br> <br> 有关使用 Windows 虚拟桌面提供的 Azure 资源管理器模板的问题，请参阅创建 Windows 虚拟桌面租户部分[租户以及主机池创建](troubleshoot-set-up-issues.md)。 |
| 从 Azure 门户中管理 Windows 虚拟桌面会话主机环境    | 请联系**Azure 支持**。 <br> <br> 有关使用远程桌面服务/Windows 虚拟桌面 PowerShell 时的管理问题，请参阅[Windows 虚拟桌面 PowerShell](troubleshoot-powershell.md)或联系**远程桌面服务/Windows 虚拟桌面支持团队**. |
| 管理 Windows 虚拟桌面配置绑定到主机池和应用程序组 （应用组）      | 请参阅[Windows 虚拟桌面 PowerShell](troubleshoot-powershell.md)，或联系**远程桌面服务/Windows 虚拟桌面支持团队**。 <br> <br> 如果问题都将绑定到示例图形用户界面 (GUI)，通过 Yammer 社区。|
| 开始上的远程桌面客户端工作不正常                                                 | 请参阅[远程桌面客户端连接](troubleshoot-client-connection.md)，如果仍未解决此问题，请联系**远程桌面服务/Windows 虚拟桌面支持团队**。  <br> <br> 如果是网络问题，你的用户将需要与网络管理员联系。 |
| 已连接但没有源                                                                 | 使用进行故障排除"用户连接，但不是显示任何内容 （任何源）"部分[远程桌面客户端连接](troubleshoot-client-connection.md)。 <br> <br> 如果在将用户分配到应用程序组，升级到**远程桌面服务/Windows 虚拟桌面支持团队**。 |
| 由于网络源的发现问题                                            | 你的用户需要与网络管理员联系。 |
| 将客户端连接                                                                    | 请参阅[远程桌面客户端连接](troubleshoot-client-connection.md)如果这样做无法解决你遇到的问题，请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration.md)。 |
| 远程应用程序或桌面的响应能力                                      | 如果问题绑定到特定的应用程序或产品，请与负责该产品的团队。 |
| 授权消息或错误                                                          | 如果问题绑定到特定的应用程序或产品，请与负责该产品的团队。 |

## <a name="next-steps"></a>后续步骤

- 若要在 Windows 虚拟桌面环境中创建的租户和主机池时排查问题，请参阅[租户以及主机池创建](troubleshoot-set-up-issues.md)。
- 若要在 Windows 虚拟机中配置虚拟机 (VM) 时解决问题，请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration.md)。
- 若要对 Windows 虚拟桌面客户端连接的问题进行故障排除，请参阅[远程桌面客户端连接](troubleshoot-client-connection.md)。
- 若要排查问题时使用 PowerShell 管理 Windows 虚拟桌面，请参阅[Windows 虚拟桌面 PowerShell](troubleshoot-powershell.md)。
- 若要了解有关预览服务的详细信息，请参阅[Windows Desktop 预览环境](https://docs.microsoft.com/azure/virtual-desktop/environment-setup)。
- 若要完成故障排除教程，请参阅[教程：对资源管理器模板部署进行故障排除](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)。
- 若要了解审核操作，请参阅[使用 Resource Manager 执行审核操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)。
- 若要了解部署期间为确定错误需要执行哪些操作，请参阅[查看部署操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations)。