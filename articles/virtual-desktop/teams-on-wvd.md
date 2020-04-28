---
title: Windows 虚拟桌面上的 Microsoft 团队-Azure
description: 如何在 Windows 虚拟桌面上使用 Microsoft 团队。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a860b005457c6e02187423a3ffbbc63fe7c758b1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187522"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>在 Windows 虚拟桌面上使用 Microsoft 团队

> 适用于： Windows 10 和 Windows 10 IoT 企业版

虚拟化环境为协作应用（如 Microsoft 团队）提供了一系列独特的挑战，包括更高的延迟、高主机 CPU 使用率，以及总体音频和视频性能不佳。 若要详细了解如何在 VDI 环境中使用 Microsoft 团队，请查看[团队了解虚拟化桌面基础结构](https://docs.microsoft.com/microsoftteams/teams-for-vdi)。

## <a name="prerequisites"></a>先决条件

在 Windows 虚拟桌面上使用 Microsoft 团队之前，需要执行以下操作：

- 在满足 Microsoft 团队[硬件要求](https://docs.microsoft.com/microsoftteams/hardware-requirements-for-the-teams-app)的 windows 10 设备上安装[windows 桌面客户端](connect-windows-7-and-10.md)。
- 连接到 Windows 10 多会话或 Windows 10 企业版虚拟机（VM）。
- 为 Microsoft 团队[准备你的网络](https://docs.microsoft.com/microsoftteams/prepare-network)。

## <a name="use-unoptimized-microsoft-teams"></a>使用未优化的 Microsoft 团队

你可以使用 Windows 虚拟桌面环境中的 Microsoft 团队来利用 Microsoft 团队的聊天和协作功能。 Windows 虚拟桌面不支持针对 VDI 音频/视频（AV）优化的团队。 不支持调用和会议。 如果你的组织的策略允许，你仍可通过音频呼叫和加入会议，但调用中的未优化音频质量将因主机配置而异，并且可能不可靠。 若要了解详细信息，请查看[有关 VDI 性能的团队](https://docs.microsoft.com/microsoftteams/teams-for-vdi#teams-on-vdi-performance-considerations)。

### <a name="prepare-your-image-for-teams"></a>为团队准备映像

若要启用团队每计算机安装，请在主机上设置以下注册表项：

```shell
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Teams\IsWVDEnvironment]
  Type: REG_DWORD
  Value: 1
```

### <a name="install-microsoft-teams"></a>安装 Microsoft 团队

你可以使用每个计算机的安装来部署团队桌面应用程序。 若要在 Windows 虚拟桌面环境中安装 Microsoft 团队：

1. 下载与你的环境匹配的[团队 MSI 包](https://docs.microsoft.com/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm)。 建议在64位操作系统上使用64位安装程序。
2. 运行此命令以将 MSI 安装到主机 VM。

      ```shell
      msiexec /i <msi_name> /l*v < install_logfile_name> ALLUSER=1
      ```

      这会将团队安装到程序文件或程序文件（x86）。 下次登录并启动团队时，应用程序将要求提供凭据。

      > [!NOTE]
      > 此时，用户和管理员无法在登录时对团队禁用自动启动。

      若要从主机 VM 中卸载 MSI，请运行以下命令：

      ```shell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      > [!NOTE]
      > 如果你安装的团队的 MSI 设置 ALLUSER = 1，则将禁用自动更新。 建议确保至少每月更新一次团队。 若要了解有关部署团队桌面应用的详细信息，请参阅将[团队桌面应用部署到 VM](https://docs.microsoft.com/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm)。

## <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>自定义主机池远程桌面协议属性
自定义主机池的远程桌面协议（RDP）属性（例如多监视器体验、启用麦克风和音频重定向），可以根据用户的需要为用户提供最佳体验。 你可以使用**RdsHostPool** cmdlet 中的 **-CustomRdpProperty**参数自定义 Windows 虚拟桌面中的 RDP 属性。
有关支持的属性及其默认值的完整列表，请参阅[支持的 RDP 文件设置](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context)。
