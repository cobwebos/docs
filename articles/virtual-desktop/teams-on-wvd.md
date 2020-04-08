---
title: 微软团队在 Windows 虚拟桌面 - Azure
description: 如何在 Windows 虚拟桌面上使用 Microsoft 团队。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 702df1bfaf0701a40e514f8a47151f96917adf38
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811597"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>在 Windows 虚拟桌面上使用 Microsoft 团队

> 适用于：Windows 10 和 Windows 10 IoT 企业

虚拟化环境为 Microsoft Teams 等协作应用带来了一系列独特的挑战，包括延迟增加、主机 CPU 使用率高以及整体音频和视频性能不佳。 要了解有关在 VDI 环境中使用 Microsoft 团队的详细信息，请查看[虚拟化桌面基础结构团队](https://docs.microsoft.com/microsoftteams/teams-for-vdi)。

## <a name="prerequisites"></a>先决条件

在 Windows 虚拟桌面上使用 Microsoft 团队之前，您需要执行以下操作：

- 在满足微软团队[硬件要求的](https://docs.microsoft.com/microsoftteams/hardware-requirements-for-the-teams-app)Windows 10 设备上安装[Windows 桌面客户端](connect-windows-7-and-10.md)。
- 连接到 Windows 10 多会话或 Windows 10 企业虚拟机 （VM）。
- 为 Microsoft 团队[准备网络](https://docs.microsoft.com/microsoftteams/prepare-network)。

## <a name="use-unoptimized-microsoft-teams"></a>使用未优化的微软团队

您可以在 Windows 虚拟桌面环境中使用未优化的 Microsoft 团队来利用 Microsoft 团队的完整聊天和协作功能以及音频呼叫。 呼叫中的音频质量将因主机配置而异，因为未优化的呼叫会使用更多主机 CPU。

### <a name="prepare-your-image-for-teams"></a>为团队准备您的形象

要启用按计算机安装的 Teams，在主机上设置以下注册表项：

```shell
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Teams\IsWVDEnvironment]
  Type: REG_DWORD
  Value: 0x1
```

### <a name="install-microsoft-teams"></a>安装微软团队

您可以使用每台计算机安装部署 Teams 桌面应用。 要在 Windows 虚拟桌面环境中安装 Microsoft 团队，请执行：

1. 下载与您的环境相匹配[的团队 MSI 包](https://docs.microsoft.com/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm)。 我们建议在 64 位操作系统上使用 64 位安装程序。
2. 运行此命令以将 MSI 安装到主机 VM。

      ```shell
      msiexec /i <msi_name> /l*v < install_logfile_name> ALLUSER=1
      ```

      这将安装团队到程序文件或程序文件 （x86）。 下次登录并启动 Teams 时，应用将要求您提供凭据。

      > [!NOTE]
      > 此时，用户和管理员无法禁用 Teams 的自动启动。

      要从主机 VM 卸载 MSI，请运行以下命令：

      ```shell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      > [!NOTE]
      > 如果安装具有 MSI 设置"ALLUSERS_1"的团队，将自动更新将被禁用。 我们建议您确保每月至少更新一次 Teams。
