---
title: Windows 虚拟桌面 Linux 支持 - Azure
description: 概述 Linux 对 Windows 虚拟桌面的支持。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8c5de43ed29856451ad67e02a426b07cc34a0d54
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422415"
---
# <a name="linux-support"></a>Linux 支持

合作伙伴可以使用 Windows 虚拟桌面的 Linux SDK 构建独立的 Windows 虚拟桌面客户端。 您还可以使用它在客户端应用程序上启用 Windows 虚拟桌面支持。 本快速指南将解释什么是 Linux SDK 以及如何开始使用它。

## <a name="connect-with-your-linux-device"></a>与 Linux 设备连接

以下合作伙伴已批准适用于 Linux 设备的 Windows 虚拟桌面客户端。

|Partner|合作伙伴文档|合作伙伴支持|
|:------|:--------------------|:--------------|
|![IGEL 徽标](./media/partners/igel.png)|[IGEL 客户端文档](https://www.igel.com/igel-solution-family/windows-virtual-desktop/)|[IGEL 支持](https://www.igel.com/support/)|

## <a name="what-is-the-linux-sdk"></a>什么是 Linux SDK？

您可以使用 SDK API 检索资源馈送、连接到桌面或远程应用程序会话，并使用我们的第一方客户端支持的许多重定向。

> [!NOTE]
> SDK 目前正在开发中。 我们将更新本文档，并提供访问 SDK 的说明，当 SDK 通常可用时。

### <a name="supported-linux-distributions"></a>支持的 Linux 发行版

SDK 与基于 Ubuntu 18.04 或更高版本的大多数操作系统兼容。 如果您有不同的 Linux 发行版，我们可以与您合作，了解如何最好地满足您的需求。

### <a name="feature-support"></a>功能支持

SDK 支持与桌面和远程应用程序会话的多个连接。 支持以下重定向：

| 重定向       | 支持 |
| :---------------- | :-------: |
| 键盘          | &#10004;  |
| 鼠标             | &#10004;  |
| 音频          | &#10004;  |
| 音频出         | &#10004;  |
| 剪贴板（文本）  | &#10004;  |
| 剪贴板（图像） | &#10004;  |
| 剪贴板（文件）  | &#10004;  |
| 智能卡         | &#10004;  |
| 驱动器/文件夹      | &#10004;  |

SDK 还支持多个监视器显示配置，只要为会话选择的监视器是连续的。

我们将更新本文档，因为我们添加对新功能和重定向的支持。 如果您想建议新功能和其他改进，请访问我们的[用户语音页面](https://go.microsoft.com/fwlink/?linkid=2116523)。

## <a name="get-started-with-the-linux-sdk"></a>开始使用 Linux SDK

在为 Windows 虚拟桌面开发 Linux 客户端之前，您需要执行以下操作：

1. 生成和部署用于测试或生产使用的 Windows 虚拟桌面环境。
2. 测试可用的第一方客户端，以熟悉 Windows 虚拟桌面用户体验。

## <a name="next-steps"></a>后续步骤

查看以下客户的文档：

- [Windows 桌面客户端](connect-windows-7-and-10.md)
- [Web 客户端](connect-web.md)
- [Android 客户端](connect-android.md)
- [macOS 客户端](connect-macos.md)
- [iOS 客户端](connect-ios.md)
