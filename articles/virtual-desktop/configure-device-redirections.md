---
title: 配置设备重定向-Azure
description: 如何配置 Windows 虚拟桌面的设备重定向。
author: Heidilohr
ms.topic: how-to
ms.date: 09/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f3441d7c7f42c58928bb97c945e7b1e7673f7afa
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876929"
---
# <a name="configure-device-redirections"></a>配置设备重定向

为 Windows 虚拟桌面环境配置设备重定向可让你在远程会话中使用打印机、USB 设备、麦克风和其他外围设备。 某些设备重定向要求对远程桌面协议 (RDP) 属性和组策略设置进行更改。

## <a name="supported-device-redirections"></a>支持的设备重定向

每个客户端支持不同的设备重定向。 对于每个客户端，请查看 [比较客户端](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-app-compare) 以了解支持的设备重定向的完整列表。

## <a name="customizing-rdp-properties-for-a-host-pool"></a>为主机池自定义 RDP 属性

若要详细了解如何使用 PowerShell 或 Azure 门户为主机池自定义 RDP 属性，请参阅 [RDP 属性](customize-rdp-properties.md)。 有关受支持的 RDP 属性的完整列表，请参阅 [支持的 rdp 文件设置](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context)。

## <a name="setup-device-redirections"></a>设置设备重定向

你可以使用以下 RDP 属性和组策略设置来配置设备重定向。

### <a name="audio-input-microphone-redirection"></a>音频输入 (麦克风) 重定向

设置以下 RDP 属性以配置音频输入重定向：

- `audiocapturemode:i:1` 启用音频输入重定向。
- `audiocapturemode:i:0` 禁用音频输入重定向。

### <a name="audio-output-speaker-redirection"></a>音频输出 (扬声器) 重定向

设置以下 RDP 属性以配置音频输出重定向：

- `audiomode:i:0` 启用音频输出重定向。
- `audiomode:i:1` 或 `audiomode:i:2` 禁用音频输出重定向。

### <a name="camera-redirection"></a>照相机重定向

设置以下 RDP 属性以配置照相机重定向：

- `camerastoredirect:s:*` 重定向所有相机。
- `camerastoredirect:s:` 禁用相机重定向。

你还可以使用以分号分隔的 KSCATEGORY_VIDEO_CAMERA 接口列表（例如）来重定向特定相机 `camerastoredirect:s:\?\usb#vid_0bda&pid_58b0&mi` 。

### <a name="clipboard-redirection"></a>剪贴板重定向

设置以下 RDP 属性以配置剪贴板重定向：

- `redirectclipboard:i:1` 启用剪贴板重定向。
- `redirectclipboard:i:0` 禁用剪贴板重定向。

### <a name="com-port-redirections"></a>COM 端口重定向

设置以下 RDP 属性以配置 COM 端口重定向：

- `redirectcomports:i:1` 启用 COM 端口重定向。
- `redirectcomports:i:0` 禁用 COM 端口重定向。

### <a name="usb-redirection"></a>USB 重定向

首先，设置以下 RDP 属性以启用 USB 设备重定向：

- `usbdevicestoredirect:s:*` 启用 USB 设备重定向。
- `usbdevicestoredirect:s:` 禁用 USB 设备重定向。

其次，在用户的本地设备上设置以下组策略：

- 导航到 "**计算机配置**  >  **策略**" >  **管理模板**  >  **Windows 组件**  >  **远程桌面服务**  >  **远程桌面连接客户端**  >  **RemoteFX USB 设备重定向**"。
- 选择 " **允许来自此计算机的其他支持的 REMOTEFX USB 设备的 RDP 重定向**"。
- 选择 " **已启用** " 选项，然后选择 " **RemoteFX USB 重定向访问权限" 框中的管理员和用户** 。
- 选择“确定”  。

### <a name="plug-and-play-device-redirection"></a>即插即用设备重定向

设置以下 RDP 属性以配置即插即用设备重定向：

- `devicestoredirect:s:*` 启用所有即插即用设备的重定向。
- `devicestoredirect:s:` 禁用即插即用设备的重定向。

你还可以使用以分号分隔的列表（例如）来选择特定的即插即用设备 `devicestoredirect:s:root\*PNP0F08` 。

### <a name="local-drive-redirection"></a>本地驱动器重定向

设置以下 RDP 属性以配置本地驱动器重定向：

- `drivestoredirect:s:*` 启用所有磁盘驱动器的重定向。
- `drivestoredirect:s:` 禁用本地驱动器重定向。

你还可以使用以分号分隔的列表（例如）来选择特定驱动器 `drivestoredirect:s:C:;E:;` 。

### <a name="printer-redirection"></a>打印机重定向

设置以下 RDP 属性以配置打印机重定向：

- `redirectprinters:i:1` 启用打印机重定向。
- `redirectprinters:i:0` 禁用打印机重定向。

### <a name="smart-card-redirection"></a>智能卡重定向

设置以下 RDP 属性以配置智能卡重定向：

- `redirectsmartcards:i:1` 启用智能卡重定向。
- `redirectsmartcards:i:0` 禁用智能卡重定向。
