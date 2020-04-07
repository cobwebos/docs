---
title: 系统要求
description: 列出 Azure 远程呈现的系统要求
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: article
ms.openlocfilehash: c239f7062dc39492a0cf63ac3aadbaf94acbf032
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680435"
---
# <a name="system-requirements"></a>系统要求

> [!IMPORTANT]
> **Azure 远程呈现**当前处于公共预览版中。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本章列出了使用*Azure 远程呈现*（ARR） 的最低系统要求。

## <a name="development-pc"></a>开发 PC

* Windows 10 版本 1903 或更高版本。
* 最新的图形驱动程序。
* 可选：如果要使用远程渲染内容的本地预览（例如 Unity），则 H265 硬件视频解码器。

> [!IMPORTANT]
> Windows 更新并不总是提供最新的 GPU 驱动程序，请查看 GPU 制造商的网站以获取最新的驱动程序：
>
> * [AMD 驱动程序](https://www.amd.com/en/support)
> * [英特尔驱动程序](https://www.intel.com/content/www/us/en/support/detect.html)
> * [NVIDIA 驱动程序](https://www.nvidia.com/Download/index.aspx)

下表列出了哪些 GPU 支持 H265 硬件视频解码。

| GPU 制造商 | 支持的模型 |
|-----------|:-----------|
| NVIDIA | 查看[本页底部的](https://developer.nvidia.com/video-encode-decode-gpu-support-matrix) **NVDEC 支持矩阵**。 您的 GPU 需要在**H.265 4：2：0 8 位**列中使用 YES。 |
| AMD | GPU与至少版本6的AMD[的统一视频解码器](https://en.wikipedia.org/wiki/Unified_Video_Decoder#UVD_6)。 |
| Intel | 天湖和较新的CPU |

即使可能安装了正确的 H265 编解码器，编解码器 DLL 上的安全属性也可能导致编解码器初始化失败。 [故障排除指南](../resources/troubleshoot.md#h265-codec-not-available)介绍了如何解决此问题的步骤。 DLL 问题只能在桌面应用程序中使用服务时发生，例如在 Unity 中。

## <a name="devices"></a>设备

Azure 远程呈现目前仅支持**HoloLens 2**和 Windows 桌面 UWP 作为目标设备。

使用最新的 HEVC 编解码器非常重要，因为较新版本的延迟有显著改善。 要检查设备上安装了哪个版本：

1. 启动**微软商店**。
1. 单击右上角的 **"..."** 按钮。
1. 选择**下载和更新**。
1. 从设备制造商搜索**HEVC 视频扩展的列表**。
1. 确保列出的编解码器至少具有版本**1.0.21821.0**。
1. 单击"**获取更新**"按钮并等待安装。

## <a name="network"></a>网络

稳定、低延迟的网络连接对于良好的用户体验至关重要。

有关[网络要求](../reference/network-requirements.md)，请参阅专用章节。

有关解决网络问题的疑难解答，请参阅[故障排除指南](../resources/troubleshoot.md#unstable-holograms)。

## <a name="software"></a>软件

必须安装以下软件：

* 最新版本的**可视化工作室 2019** [（下载）](https://visualstudio.microsoft.com/vs/older-downloads/)
* **Windows SDK 10.0.18362.0（**[下载）](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* **GIT（**[下载）](https://git-scm.com/downloads)
* 可选：要从桌面 PC 上的服务器查看视频流，您需要**HEVC 视频扩展（Microsoft** [应用商店链接）。](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7)

## <a name="unity"></a>Unity

对于使用 Unity 进行开发，请安装

* Unity 2019.3.1 [（下载）](https://unity3d.com/get-unity/download)
* 在 Unity 中安装这些模块：
  * **UWP** - 通用 Windows 平台构建支持
  * **IL2CPP** - Windows 构建支持 （IL2CPP）

## <a name="next-steps"></a>后续步骤

* [快速入门：使用 Unity 渲染模型](../quickstarts/render-model.md)
