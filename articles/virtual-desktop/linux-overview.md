---
title: Windows 虚拟桌面 Linux 支持-Azure
description: Windows 虚拟桌面的简要概述 Linux 支持。
author: Heidilohr
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: bd3cc6c5220e2e84cbbd30b29b8034f53c813f1e
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2020
ms.locfileid: "88008689"
---
# <a name="linux-support"></a>Linux 支持

你可以使用 Linux 瘦客户端合作伙伴提供的以下受支持的客户端访问 Windows 虚拟桌面资源。 我们正在与许多合作伙伴合作，在基于 Linux 的操作系统和设备上启用受支持的 Windows 虚拟桌面客户端。 如果你想要在此处未列出的 Linux 平台上使用 Windows 虚拟桌面支持，请在我们的[UserVoice 页面](https://remotedesktop.uservoice.com/forums/923035-remote-desktop-support-on-linux)上告知我们。

## <a name="connect-with-your-linux-device"></a>连接到 Linux 设备

以下合作伙伴已批准适用于 Linux 设备的 Windows 虚拟桌面客户端。

|合作伙伴|合作伙伴文档|合作伙伴支持|
|:------|:--------------------|:--------------|
|![IGEL 徽标](./media/partners/igel.png)|[IGEL 客户端文档](https://www.igel.com/igel-solution-family/windows-virtual-desktop/)|[IGEL 支持](https://www.igel.com/support/)|

## <a name="what-is-the-linux-sdk"></a>什么是 Linux SDK？

Linux 瘦客户端合作伙伴可以使用 Windows 虚拟桌面 Linux SDK Api 来检索资源源、连接到桌面或远程应用程序会话，并使用我们的第一方客户端支持的多个重定向。 SDK 与大多数基于 Ubuntu 18.04 或更高版本的操作系统兼容。

### <a name="feature-support"></a>功能支持

SDK 支持多个到桌面和远程应用程序会话的连接。 支持以下重定向：

| 重定向       | 支持 |
| :---------------- | :-------: |
| Keyboard          | &#10004;  |
| 鼠标             | &#10004;  |
| 音频传入          | &#10004;  |
| 音频输出         | &#10004;  |
| 剪贴板 (文本)   | &#10004;  |
| 剪贴板 (图像)  | &#10004;  |
| 剪贴板 (文件)   | &#10004;  |
| 智能卡         | &#10004;  |
| 驱动器/文件夹      | &#10004;  |

SDK 还支持多个监视器显示配置，前提是你为会话选择的监视器是连续的。

我们将更新此文档，因为我们添加了对新功能和重定向的支持。 若要建议新功能和其他改进，请访问我们的[UserVoice 页](https://go.microsoft.com/fwlink/?linkid=2116523)。

## <a name="next-steps"></a>后续步骤

请查看以下客户端的文档：

- [Windows 桌面客户端](connect-windows-7-10.md)
- [Web 客户端](connect-web.md)
- [Android 客户端](connect-android.md)
- [macOS 客户端](connect-macos.md)
- [iOS 客户端](connect-ios.md)
