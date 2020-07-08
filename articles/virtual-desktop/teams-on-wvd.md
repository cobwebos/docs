---
title: Windows 虚拟桌面上的 Microsoft 团队-Azure
description: 如何在 Windows 虚拟桌面上使用 Microsoft 团队。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 05/29/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3fc44ca18f237fecd1c694e96f9ebc2d5b541757
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564464"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>在 Windows 虚拟桌面上使用 Microsoft 团队

>[!IMPORTANT]
>Microsoft 团队的媒体优化目前为公共预览版。 建议在为生产工作负荷部署团队之前评估优化的团队用户体验。 某些功能可能不受支持或者受限。

>[!NOTE]
>Microsoft 团队的媒体优化仅适用于 Windows 10 计算机上的 Windows 桌面客户端。 媒体优化需要 Windows 桌面客户端版本1.2.1026.0 或更高版本。

Windows 虚拟桌面上的 Microsoft 团队支持聊天和协作。 借助媒体优化，它还支持调用和会议功能。 若要了解有关如何在虚拟桌面基础结构（VDI）环境中使用 Microsoft 团队的详细信息，请参阅[面向虚拟桌面基础结构的团队](/microsoftteams/teams-for-vdi/)。

借助 Microsoft 团队的媒体优化，Windows 桌面客户端会在本地处理音频和视频，以供团队调用和会议。 你仍可将 Windows 虚拟桌面上的 Microsoft 团队与其他客户端配合使用，而无需进行优化的呼叫和会议。 所有平台都支持团队聊天和协作功能。 若要在远程会话中重定向本地设备，请查看[自定义主机池的远程桌面协议属性](#customize-remote-desktop-protocol-properties-for-a-host-pool)。

## <a name="prerequisites"></a>先决条件

在 Windows 虚拟桌面上使用 Microsoft 团队之前，需要执行以下操作：

- 为 Microsoft 团队[准备你的网络](/microsoftteams/prepare-network/)。
- 在 Windows 10 或 Windows 10 IoT 企业设备上安装[Windows 桌面客户端](connect-windows-7-and-10.md)，该设备满足[Windows 电脑上团队](/microsoftteams/hardware-requirements-for-the-teams-app#hardware-requirements-for-teams-on-a-windows-pc/)的 Microsoft 团队硬件要求。
- 连接到 Windows 10 多会话或 Windows 10 企业版虚拟机（VM）。
- 使用每计算机安装在主机上安装 "团队" 桌面应用程序。 Microsoft 团队的媒体优化要求团队桌面应用版本1.3.00.4461 或更高版本。

## <a name="install-the-teams-desktop-app"></a>安装团队桌面应用

本部分将介绍如何在 Windows 10 多会话或 Windows 10 企业 VM 映像上安装团队桌面应用程序。 若要了解详细信息，请参阅[在 VDI 上安装或更新团队桌面应用](/microsoftteams/teams-for-vdi#install-or-update-the-teams-desktop-app-on-vdi/)。

### <a name="prepare-your-image-for-teams"></a>为团队准备映像

若要为团队启用媒体优化，请在主机上设置以下注册表项：

1. 从 "开始" 菜单中，以管理员身份运行**RegEdit** 。 导航到**HKEY_LOCAL_MACHINE \software\microsoft\teams**"。
2. 为团队项创建以下值：

| 名称             | 类型   | 数据/值  |
|------------------|--------|-------------|
| IsWVDEnvironment | DWORD  | 1           |

### <a name="install-the-teams-websocket-service"></a>安装团队 WebSocket 服务

在 VM 映像上安装[WebSocket 服务](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4yj0i)。 如果遇到安装错误，请安装[最新的 Microsoft Visual C++ 可再发行组件](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)，然后重试。

### <a name="install-microsoft-teams"></a>安装 Microsoft 团队

你可以使用每个计算机或每个用户的安装来部署团队桌面应用程序。 若要在 Windows 虚拟桌面环境中安装 Microsoft 团队：

1. 下载与你的环境匹配的[团队 MSI 包](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm/)。 建议在64位操作系统上使用64位安装程序。

      > [!NOTE]
      > Microsoft 团队的媒体优化要求团队桌面应用版本1.3.00.4461 或更高版本。

2. 运行以下命令之一，将 MSI 安装到主机 VM：

    - 按用户安装

        ```powershell
        msiexec /i <path_to_msi> /l*v <install_logfile_name> ALLUSERS=1
        ```

        此过程是默认安装，它将团队安装到 **% AppData%** 用户文件夹中。 在非持久设置上，团队将无法正常使用基于用户的安装。

    - 每计算机安装

        ```powershell
        msiexec /i <path_to_msi> /l*v <install_logfile_name> ALLUSER=1 ALLUSERS=1
        ```

        这会将团队安装到64位操作系统上的 Program Files （x86）文件夹和32位操作系统上的 Program Files 文件夹。 此时，黄金映像设置已完成。 对于非持久设置，需要为每台计算机安装团队。

        下次在会话中打开团队时，系统将要求你提供凭据。

        > [!NOTE]
        > 此时，用户和管理员无法在登录时对团队禁用自动启动。

3. 若要从主机 VM 中卸载 MSI，请运行以下命令：

      ```powershell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      这会根据操作系统环境从 Program Files （x86）文件夹或 Program Files 文件夹中卸载团队。

      > [!NOTE]
      > 当你安装具有 MSI 设置 ALLUSER = 1 的团队时，将禁用自动更新。 建议确保至少每月更新一次团队。 若要了解有关部署团队桌面应用的详细信息，请参阅将[团队桌面应用部署到 VM](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm/)。

### <a name="verify-media-optimizations-loaded"></a>验证加载的媒体优化

安装 WebSocket 服务和团队桌面应用后，请按照以下步骤验证是否加载了团队媒体优化：

1. 选择用户配置文件映像，然后选择 "**关于**"。
2. 选择 "**版本**"。

      如果已加载媒体优化，则横幅会显示**优化的 WVD 媒体**。 如果横幅显示 WVD "**未连接媒体**"，请退出团队应用，然后重试。

3. 选择用户配置文件映像，然后选择 "**设置**"。

      如果已加载媒体优化，则将在 "设备" 菜单中枚举本地可用的音频设备和相机。 如果菜单显示**远程音频**，请退出团队应用，然后重试。 如果菜单中未显示设备，请返回以[安装 Microsoft 团队](#install-microsoft-teams)，并确保已完成安装过程。

## <a name="known-issues-and-limitations"></a>已知问题和限制

在虚拟化环境中使用团队不同于在非虚拟化环境中使用团队。 有关虚拟化环境中团队限制的详细信息，请查看[团队了解虚拟化桌面基础结构](/microsoftteams/teams-for-vdi#known-issues-and-limitations/)。

### <a name="client-deployment-installation-and-setup"></a>客户端部署、安装和设置

- 对于每计算机安装，VDI 上的团队不会自动更新为非 VDI 团队客户端。 若要更新客户端，你将需要通过安装新的 MSI 来更新 VM 映像。
- 团队当前只显示聊天、频道和日历中的 UTC 时区。
- 只有运行 Windows 10 的计算机上的 Windows 桌面客户端支持团队的媒体优化。
- 不支持使用在端点上定义的显式 HTTP 代理。

### <a name="calls-and-meetings"></a>呼叫和会议

- Windows 虚拟桌面环境中的团队桌面客户端不支持实时事件。 目前，我们建议你改为在远程会话中加入[团队 web 客户端](https://teams.microsoft.com)的实时事件。
- 当你展开应用程序时，最大程度地降低团队应用程序的运行可能导致传入视频源消失。
- 呼叫或会议当前不支持应用程序共享。 桌面会话支持桌面共享。
- 在多监视器安装程序中进行桌面共享时，将共享所有监视器。
- 当前不支持对控件进行控制和控制。
- Windows 虚拟桌面上的团队每次仅支持一个传入视频输入。 这意味着每当有人尝试共享其屏幕时，都会显示其屏幕，而不是会议主持人的屏幕。
- 由于 WebRTC 限制，传入和传出视频流解析仅限于720p。
- 团队应用不支持对其他设备进行 HID 按钮或 LED 控件。

对于与虚拟化环境无关的已知问题，请参阅[组织中的支持团队](/microsoftteams/known-issues/)

## <a name="uservoice-site"></a>UserVoice 网站

在团队[UserVoice 网站](https://microsoftteams.uservoice.com/)上的 Windows 虚拟桌面上为 Microsoft 团队提供反馈。

## <a name="collect-teams-logs"></a>收集团队日志

如果你在 Windows 虚拟桌面环境中遇到团队桌面应用问题，请在主机 VM 上的 **% appdata% \Microsoft\Teams\logs.txt**下收集客户端日志。

如果在调用和会议时遇到问题，请通过键组合**Ctrl**  +  **Alt**  +  **Shift**  +  **1**收集团队 Web 客户端日志。 日志将写入主机 VM 上的 **%Userprofile%\Downloads\MSTeams Diagnostics 日志 DATE_TIME.txt** 。

## <a name="contact-microsoft-teams-support"></a>联系 Microsoft 团队支持部门

若要联系 Microsoft 团队支持部门，请访问[Microsoft 365 管理中心](/microsoft-365/admin/contact-support-for-business-products)。

## <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>自定义主机池远程桌面协议属性

通过自定义主机池的远程桌面协议（RDP）属性（例如多监视器体验或启用麦克风和音频重定向），可以根据用户的需要为用户提供最佳体验。

使用具有媒体优化的团队时，无需启用设备重定向。 如果使用的是没有媒体优化的团队，请设置以下 RDP 属性以启用麦克风和照相机重定向：

- `audiocapturemode:i:1`从本地设备启用音频捕获，并在远程会话中重定向音频应用程序。
- `audiomode:i:0`在本地计算机上播放音频。
- `camerastoredirect:s:*`重定向所有相机。

若要了解详细信息，请参阅[自定义主机池的远程桌面协议属性](customize-rdp-properties.md)。
