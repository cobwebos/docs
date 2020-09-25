---
title: 系统要求
description: 列出 Azure 远程呈现的系统要求
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: article
ms.openlocfilehash: 31fde0c7af652bc50eb5f06743c5dd5807a1762e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91323719"
---
# <a name="system-requirements"></a>系统要求

> [!IMPORTANT]
> Azure 远程渲染目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本章列出了使用 *Azure 远程渲染* (ARR) 的最低系统要求。

## <a name="development-pc"></a>开发 PC

* Windows 10 版本1903或更高版本。
* 最新图形驱动程序。
* 可选：如果你想要使用远程呈现内容的本地预览 (例如，在 Unity) 中，则可以选择 " [H265](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7)"。

> [!IMPORTANT]
> Windows 更新不会始终提供最新的 GPU 驱动程序，请查看你的 GPU 制造商的网站以了解最新的驱动程序：
>
> * [AMD 驱动程序](https://www.amd.com/en/support)
> * [Intel 驱动程序](https://www.intel.com/content/www/us/en/support/detect.html)
> * [NVIDIA 驱动程序](https://www.nvidia.com/Download/index.aspx)

下表列出了哪些 Gpu 支持 H265 硬件视频解码。

| GPU 制造商 | 支持的模型 |
|-----------|:-----------|
| NVIDIA | 请查看本页[底部](https://developer.nvidia.com/video-encode-decode-gpu-support-matrix)的**NVDEC 支持矩阵**。 你的 GPU 在 **4:2:0 8 位** 的列中需要 YES。 |
| AMD | 具有最低版本6的 AMD [统一视频解码器](https://en.wikipedia.org/wiki/Unified_Video_Decoder#UVD_6)的 gpu。 |
| Intel | Skylake 和更高版本的 Cpu |

尽管可能安装了正确的 H265 编解码器，但编解码器 Dll 上的安全属性可能会导致编解码器初始化失败。 [故障排除指南](../resources/troubleshoot.md#h265-codec-not-available)介绍了如何解决此问题的步骤。 仅当在桌面应用程序中使用该服务时，才会出现 DLL 问题，例如，在 Unity 中使用。

## <a name="devices"></a>设备

Azure 远程呈现目前仅支持将 **HoloLens 2** 和 Windows 桌面作为目标设备。 请参阅 " [平台限制](../reference/limits.md#platform-limitations) " 部分。

使用最新的 HEVC 编解码器是非常重要的，因为较新版本的延迟显著改进。 检查设备上安装的版本：

1. 启动 **Microsoft Store**。
1. 单击右上方的 **"..."** 按钮。
1. 选择 " **下载和更新**"。
1. 在列表中搜索 **设备制造商提供的 HEVC 视频扩展**。 如果未在 "更新" 下列出此项，则已安装最新版本。
1. 请确保列出的编解码器至少具有版本 **1.0.21821.0**。
1. 单击 " **获取更新** " 按钮，并等待安装完成。

## <a name="network"></a>网络

稳定的低延迟网络连接对于良好的用户体验至关重要。

有关 [网络要求](../reference/network-requirements.md)，请参阅专用章节。

有关网络问题的疑难解答，请参阅 [故障排除指南](../resources/troubleshoot.md#unstable-holograms)。

### <a name="network-ports"></a>网络端口

确保防火墙（在设备上、路由器内部等）未阻止以下端口：

| 端口              | 协议 | 允许    | 说明 |
|-------------------|----------|----------|-------------|
| 50051             | TCP      | 传出 | 初始连接 (HTTP 握手)  |
| 8266              | UDP      | 传出 | 数据传输 |
| 5000、5433、8443  | TCP      | 传出 | [ArrInspector 工具](../resources/tools/arr-inspector.md)必需的|


## <a name="software"></a>软件

必须安装以下软件：

* 最新版本的 **Visual Studio 2019** [ (下载) ](https://visualstudio.microsoft.com/vs/older-downloads/)
* [适用于混合现实的 Visual Studio tools](https://docs.microsoft.com/windows/mixed-reality/install-the-tools)。 具体来说，必须安装以下工作负载：
  * **使用 C++ 的桌面开发**
  * **通用 Windows 平台 (UWP) 开发**
* **Windows SDK 10.0.18362.0** [ (下载) ](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* **GIT** [ (下载) ](https://git-scm.com/downloads)
* 可选：若要在台式计算机上查看服务器中的视频流，需要 **HEVC 视频扩展** [ (Microsoft Store 链接) ](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7)。 确保通过检查存储中的更新来安装最新版本。

## <a name="unity"></a>Unity

若要通过 Unity 进行开发，请安装

* Unity 2019.3.1[（下载）](https://unity3d.com/get-unity/download)
* 在 Unity 中安装以下模块：
  * **UWP** - 通用 Windows 平台生成支持
  * **IL2CPP** - Windows 生成支持 (IL2CPP)

## <a name="next-steps"></a>后续步骤

* [快速入门：使用 Unity 渲染模型](../quickstarts/render-model.md)
