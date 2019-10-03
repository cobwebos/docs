---
title: 在 Windows 虚拟机上部署 Windows 7 虚拟机-Azure
description: 如何在 Windows 虚拟机上配置和部署 Windows 7 虚拟机。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: helohr
ms.openlocfilehash: e7f565a995e4c2a5338f08437b0dd336846ba154
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679914"
---
# <a name="deploy-a-windows-7-virtual-machine-on-windows-virtual-desktop"></a>在 Windows 虚拟机上部署 Windows 7 虚拟机

在 Windows 虚拟机上部署 Windows 7 虚拟机（VM）的过程与运行更高版本 Windows 的 Vm 略有不同。 本指南将介绍如何部署 Windows 7。

## <a name="prerequisites"></a>先决条件

在开始之前，请按照[使用 PowerShell 创建主机池](create-host-pools-powershell.md)中的说明创建主机池。 之后，请按照在[Azure Marketplace 中创建主机池](create-host-pools-azure-marketplace.md#optional-assign-additional-users-to-the-desktop-application-group)中的说明将一个或多个用户分配到桌面应用程序组。

## <a name="configure-a-windows-7-virtual-machine"></a>配置 Windows 7 虚拟机

完成先决条件后，即可在 Windows 虚拟桌面上配置 Windows 7 VM 以进行部署。

若要在 Windows 虚拟桌面上设置 Windows 7 VM：

1. 登录到 Azure 门户，并搜索 Windows 7 企业版映像或上传自己的自定义 Windows 7 企业版（x64）映像。  
2. 将一个或多个虚拟机部署为 Windows 7 Enterprise 作为其主机操作系统。 请确保虚拟机允许远程桌面协议（RDP）（TCP/3389 端口）。
3. 使用 RDP 连接到 Windows 7 企业主机，并使用配置部署时定义的凭据进行身份验证。 
4. 将使用 RDP 连接到主机时使用的帐户添加到 "远程桌面用户" 组。 否则，可能无法在将 VM 加入到 Active Directory 域后连接到该 VM。
5. 请在 VM 上中转到 Windows 更新。
6. 安装 "重要" 类别中的所有 Windows 更新。
7. 在可选类别中安装所有 Windows 更新（语言包除外）。 这将安装完成这些说明所需的远程桌面协议8.0 更新（[KB2592687](https://www.microsoft.com/download/details.aspx?id=35393)）。
8. 打开本地组策略编辑器，导航到 "**计算机配置**"  > **管理模板**@no__t 3 个**Windows 组件** > **远程桌面服务** > **远程桌面会话主机** > **远程会话环境**。
9. 启用远程桌面协议8.0 策略。
10. 通过运行以下命令重新启动虚拟机：
    
     ```cmd
     shutdown /r /t 0
     ```
    
## <a name="next-steps"></a>后续步骤

你的 Windows 7 VM 现在已准备好在 Windows 虚拟桌面上进行部署。 按照[为 Windows 虚拟桌面代理安装准备虚拟机](create-host-pools-powershell.md#prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations)中的说明完成部署。

有关 windows 虚拟桌面上 Windows 7 的已知问题和疑难解答说明的列表，请参阅故障排除一文，了解 windows[虚拟机中的 windows 7 虚拟机疑难解答](troubleshoot-windows-7-vm.md)。

