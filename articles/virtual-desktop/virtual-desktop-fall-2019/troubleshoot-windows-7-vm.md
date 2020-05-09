---
title: Windows 7 虚拟机 Windows 虚拟桌面-Azure
description: 如何解决 Windows 虚拟桌面环境中 Windows 7 虚拟机（Vm）的问题。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 74f2e22bcc9d75070e4f7af304f92d9c5640ca7a
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82615274"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-windows-virtual-desktop"></a>排查 Windows 虚拟桌面中的 Windows 7 虚拟机问题

>[!IMPORTANT]
>此内容适用于不支持 Azure 资源管理器 Windows 虚拟桌面对象的秋季2019版本。

本文介绍配置 Windows 虚拟桌面会话主机虚拟机（Vm）时遇到的问题。

## <a name="known-issues"></a>已知问题

Windows 虚拟桌面上的 windows 7 不支持以下功能：

- 虚拟化应用程序（RemoteApps）
- 时区重定向
- 自动 DPI 缩放

Windows 虚拟桌面只能虚拟化 Windows 7 的整个桌面。

虽然不支持自动 DPI 缩放，但你可以通过右键单击远程桌面客户端中的图标，然后选择 "**解决**方法" 来手动更改虚拟机上的分辨率。

## <a name="error-cant-access-the-remote-desktop-user-group"></a>错误：无法访问远程桌面用户组

如果 Windows 虚拟桌面在远程桌面用户组中找不到你或用户的凭据，你可能会看到以下错误消息之一：

- "此用户不是远程桌面用户组的成员"
- "必须被授予通过远程桌面服务登录的权限"

若要修复此错误，请将用户添加到远程桌面用户组：

1. 打开 Azure 门户。
2. 选择出现错误消息的虚拟机。
3. 选择 "**运行命令**"。
4. 运行以下命令，并`<username>`将其替换为要添加的用户的名称：
   
   ```cmd
   net localgroup "Remote Desktop Users" <username> /add
   ```