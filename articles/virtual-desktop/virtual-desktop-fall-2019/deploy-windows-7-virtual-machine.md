---
title: 部署 Windows 7 虚拟机 Windows 虚拟桌面-Azure
description: 如何在 Windows 虚拟机上配置和部署 Windows 7 虚拟机。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0cb5b2ee8b8391dc4fcb78cc1d3bd212c44f1803
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82615404"
---
# <a name="deploy-a-windows-7-virtual-machine-on-windows-virtual-desktop"></a>在 Windows 虚拟桌面上部署 Windows 7 虚拟机

>[!IMPORTANT]
>此内容适用于不支持 Azure 资源管理器 Windows 虚拟桌面对象的秋季2019版本。

在 Windows 虚拟机上部署 Windows 7 虚拟机（VM）的过程与运行更高版本 Windows 的 Vm 略有不同。 本指南将介绍如何部署 Windows 7。

## <a name="prerequisites"></a>先决条件

在开始之前，请按照[使用 PowerShell 创建主机池](create-host-pools-powershell-2019.md)中的说明创建主机池。 之后，请按照在[Azure Marketplace 中创建主机池](create-host-pools-azure-marketplace-2019.md#optional-assign-additional-users-to-the-desktop-application-group)中的说明将一个或多个用户分配到桌面应用程序组。

## <a name="configure-a-windows-7-virtual-machine"></a>配置 Windows 7 虚拟机

完成先决条件后，即可在 Windows 虚拟桌面上配置 Windows 7 VM 以进行部署。

若要在 Windows 虚拟桌面上设置 Windows 7 VM：

1. 登录到 Azure 门户，并搜索 Windows 7 企业版映像或上传自己的自定义 Windows 7 企业版（x64）映像。  
2. 将一个或多个虚拟机部署为 Windows 7 Enterprise 作为其主机操作系统。 请确保虚拟机允许远程桌面协议（RDP）（TCP/3389 端口）。
3. 使用 RDP 连接到 Windows 7 企业主机，并使用配置部署时定义的凭据进行身份验证。 
4. 将使用 RDP 连接到主机时使用的帐户添加到 "远程桌面用户" 组。 否则，可能无法在将 VM 加入到 Active Directory 域后连接到该 VM。
5. 请在 VM 上中转到 Windows 更新。
6. 安装 "重要" 类别中的所有 Windows 更新。
7. 在可选类别中安装所有 Windows 更新（语言包除外）。 这将安装完成这些说明所需的远程桌面协议8.0 更新（[KB2592687](https://www.microsoft.com/download/details.aspx?id=35387)）。
8. 打开本地组策略编辑器，导航到 "**计算机配置** > **管理模板** > **Windows 组件** > **远程桌面服务** > **远程桌面会话主机** > **远程会话环境**"。
9. 启用远程桌面协议8.0 策略。
10. 将此 VM 加入到 Active Directory 域。
11. 通过运行以下命令重新启动虚拟机：
    
     ```cmd
     shutdown /r /t 0
     ```
    
12. 按照[此处](/powershell/module/windowsvirtualdesktop/export-rdsregistrationinfo/)的说明获取注册令牌。
13. [下载适用于 windows 7 的 Windows 虚拟桌面代理](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm)。
14. [下载适用于 windows 7 的 Windows 虚拟桌面代理程序管理器](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3)。
15. 打开 Windows 虚拟桌面代理安装程序，然后按照说明进行操作。 出现提示时，请提供在步骤12中创建的注册密钥。
16. 打开 Windows 虚拟桌面安装程序，然后按照说明进行操作。
17. 或者，阻止 TCP/3389 端口删除对 VM 的直接远程桌面协议访问权限。

## <a name="next-steps"></a>后续步骤

你的 Windows 虚拟桌面部署现已准备就绪，可供使用。 [下载最新版本的 Windows 虚拟桌面客户端](https://aka.ms/wvd/clients/windows)以开始工作。

有关 windows 虚拟桌面上 Windows 7 的已知问题和疑难解答说明的列表，请参阅故障排除一文，了解 windows[虚拟机中的 windows 7 虚拟机疑难解答](troubleshoot-windows-7-vm.md)。
