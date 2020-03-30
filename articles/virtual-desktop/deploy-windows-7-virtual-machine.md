---
title: 部署 Windows 7 虚拟机 Windows 虚拟桌面 - Azure
description: 如何在 Windows 虚拟桌面上配置和部署 Windows 7 虚拟机。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 158dd9d6a38cc1aaf7667c0b16518e23b3f7cecf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366677"
---
# <a name="deploy-a-windows-7-virtual-machine-on-windows-virtual-desktop"></a>在 Windows 虚拟桌面上部署 Windows 7 虚拟机

在 Windows 虚拟桌面上部署 Windows 7 虚拟机 （VM） 的过程与运行更高版本的 Windows 的 VM 略有不同。 本指南将告诉您如何部署 Windows 7。

## <a name="prerequisites"></a>先决条件

开始之前，请按照使用[PowerShell 创建主机池](create-host-pools-powershell.md)的说明创建主机池以创建主机池。 之后，按照[Azure 应用商店中创建主机池中的](create-host-pools-azure-marketplace.md#optional-assign-additional-users-to-the-desktop-application-group)说明将一个或多个用户分配给桌面应用程序组。

## <a name="configure-a-windows-7-virtual-machine"></a>配置 Windows 7 虚拟机

完成先决条件后，即可配置 Windows 7 VM 以在 Windows 虚拟桌面上部署。

要在 Windows 虚拟桌面上设置 Windows 7 VM，可以：

1. 登录到 Azure 门户并搜索 Windows 7 企业版映像或上传自己的自定义 Windows 7 企业版 （x64） 映像。  
2. 部署一个或多个以 Windows 7 企业版为主机操作系统的虚拟机。 确保虚拟机允许远程桌面协议 （RDP）（TCP/3389 端口）。
3. 使用 RDP 连接到 Windows 7 企业版主机，并使用配置部署时定义的凭据进行身份验证。 
4. 将使用 RDP 连接到主机时使用的帐户添加到"远程桌面用户"组。 如果不执行此操作，则在将其加入到 Active Directory 域后可能无法连接到 VM。
5. 转到 VM 上的 Windows 更新。
6. 在"重要"类别中安装所有 Windows 更新。
7. 在"可选"类别中安装所有 Windows 更新（不包括语言包）。 这将安装需要完成这些说明的远程桌面协议 8.0 更新 （[KB2592687](https://www.microsoft.com/download/details.aspx?id=35387)）。
8. 打开本地组策略编辑器并导航到**计算机配置** > **管理模板** > **Windows 组件** > **远程桌面服务** > **远程桌面会话主机** > **远程会话环境**。
9. 启用远程桌面协议 8.0 策略。
10. 将此 VM 加入您的活动目录域。
11. 通过运行以下命令重新启动虚拟机：
    
     ```cmd
     shutdown /r /t 0
     ```
    
12. 按照[此处](/powershell/module/windowsvirtualdesktop/export-rdsregistrationinfo/)的说明获取注册令牌。
13. [下载适用于 Windows 7 的 Windows 虚拟桌面代理](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm)。
14. [下载 Windows 7 的 Windows 虚拟桌面代理管理器](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3)。
15. 打开 Windows 虚拟桌面代理安装程序并按照说明操作。 出现提示后，请提供您在步骤 12 中创建的注册密钥。
16. 打开 Windows 虚拟桌面安装程序并按照说明操作。
17. 或者，阻止 TCP/3389 端口以删除对 VM 的直接远程桌面协议访问。

## <a name="next-steps"></a>后续步骤

Windows 虚拟桌面部署现已准备就绪，可供使用。 [下载最新版本的 Windows 虚拟桌面客户端](https://aka.ms/wvd/clients/windows)以开始。

有关 Windows 虚拟桌面上的 Windows 7 的已知问题和故障排除说明的列表，请参阅我们在[Windows 虚拟桌面中的 Windows 7 虚拟机疑难解答](troubleshoot-windows-7-vm.md)一文。
