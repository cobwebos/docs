---
title: 疑难解答
description: Azure 远程呈现的故障排除信息
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: b86af2ff8fad3793fc47cec9399fd499c1cabba7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681860"
---
# <a name="troubleshoot"></a>疑难解答

此页列出了干扰 Azure 远程呈现的常见问题，以及解决这些问题的方法。

## <a name="client-cant-connect-to-server"></a>客户端无法连接到服务器

确保您的防火墙（在设备上、路由器内部等）不会阻止以下端口：

* **50051 （TCP）** - 初始连接（HTTP 握手）需要
* **8266 （TCP+UDP）** - 数据传输需要
* **5000 （TCP）**， **5433 （TCP）**， **8443 （TCP）** - [ArrInspector](tools/arr-inspector.md)需要

## <a name="error-disconnected-videoformatnotavailable"></a>错误"已断开连接：视频格式不可用"

检查您的 GPU 是否支持硬件视频解码。 请参阅[开发 PC](../overview/system-requirements.md#development-pc)。

如果您在具有两个 GPU 的便携式计算机上工作，则默认情况下正在运行的 GPU 可能无法提供硬件视频解码功能。 如果是这样，请尝试强制你的应用使用其他 GPU。 这在 GPU 驱动程序设置中通常是可能的。

## <a name="h265-codec-not-available"></a>H265 编解码器不可用

服务器可能拒绝连接**编解码器不可用**错误的原因有两个。

**未安装 H265 编解码器：**

首先，请确保安装系统要求[的软件](../overview/system-requirements.md#software)部分中提到的**HEVC 视频扩展**。

如果仍然遇到问题，请确保您的显卡支持 H265 并安装了最新的图形驱动程序。 有关特定于供应商的信息，请参阅系统要求的["开发 PC"](../overview/system-requirements.md#development-pc)部分。

**已安装编解码器，但无法使用：**

此问题的原因是 DLL 上的安全设置不正确。 在尝试观看使用 H265 编码的视频时，此问题不会显现出来。 重新安装编解码器也不能解决问题。 而是执行以下步骤：

1. 打开**具有管理权限的 PowerShell**并运行

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    该命令应输出编`InstallLocation`解码器，如下所示：
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. 在 Windows 资源管理器中打开该文件夹
1. 应该有一个**x86**和一个**x64**子文件夹。 右键单击其中一个文件夹并选择 **"属性"**
    1. 选择 **"安全**"选项卡，然后单击 **"高级**设置"按钮
    1. 单击 **"** 所有者更改 **"**
    1. 在文本字段中键入**管理员**
    1. 单击 **"检查名称**"和 **"确定"**
1. 对其他文件夹重复上述步骤
1. 同时，对两个文件夹中的每个 DLL 文件重复上述步骤。 应该总共有四个 DLL。

要验证设置现在是否正确，请对四个 DLL 中的每一个执行以下操作：

1. 选择 **">安全>编辑的属性**
1. 浏览所有**组/用户**的列表，并确保每个组都具有 **"读取&执行**"右侧集（必须勾选**允许**列中的复选标记）

## <a name="low-video-quality"></a>视频质量低

网络质量或缺少的 H265 视频编解码器可能会损害视频质量。

* 请参阅[识别网络问题](#unstable-holograms)的步骤。
* 请参阅安装最新图形驱动程序[的系统要求](../overview/system-requirements.md#development-pc)。

## <a name="video-recorded-with-mrc-does-not-reflect-the-quality-of-the-live-experience"></a>使用 MRC 录制的视频不反映实时体验的质量

视频可以通过[混合现实捕获 （MRC）](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers)在霍洛伦斯上录制。 但是，生成的视频的质量比现场体验差，原因有二：
* 视频帧速率上限为 30 Hz，而不是 60 Hz。
* 视频图像不会经过[后期重新投影](../overview/features/late-stage-reprojection.md)处理步骤，因此视频看起来是斩波的。

两者都是录音技术的内在局限性。

## <a name="black-screen-after-successful-model-loading"></a>成功加载模型后黑屏

如果连接到渲染运行时并成功加载了模型，但之后只能看到黑屏，则这可能有几个不同的原因。

我们建议在进行更深入的分析之前测试以下操作：

* 是否安装了 H265 编解码器？ 尽管应该对 H264 编解码器进行回退，但我们已经看到此回退无法正常工作的情况。 请参阅安装最新图形驱动程序[的系统要求](../overview/system-requirements.md#development-pc)。
* 使用 Unity 项目时，关闭 Unity，删除项目目录中的临时*库*和*obj*文件夹，然后再次加载/生成项目。 在某些情况下，缓存的数据会导致示例无法正常工作，原因显而易见。

如果这两个步骤没有帮助，则需要了解客户端是否接收视频帧。 这可以通过服务器[端性能查询](../overview/features/performance-queries.md)章节中解释的方式以编程方式查询。 具有`FrameStatistics struct`一个成员，指示已收到的视频帧数。 如果此数字大于 0 并且随时间而增加，则客户端将从服务器接收实际视频帧。 因此，它必须是客户端上的问题。

### <a name="common-client-side-issues"></a>常见的客户端问题

**模型不在视图角度内：**

在许多情况下，模型显示正确，但位于摄像机外部。 一个常见原因是模型已导出到离中心远的枢轴，因此它被摄像机的远剪平面剪切。 它有助于以编程方式查询模型的边界框，并将 Unity 作为行框可视化框，或将其值打印到调试日志。

此外，转换过程将生成与转换模型一起生成的[输出 json 文件](../how-tos/conversion/get-information.md)。 要调试模型定位问题，值得查看[输出统计部分](../how-tos/conversion/get-information.md#the-outputstatistics-section)中的`boundingBox`条目：

```JSON
{
    ...
    "outputStatistics": {
        ...
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

边界框被描述为 3D 空间`min`中的`max`和 位置，以米为单位。 因此，坐标 1000.0 表示它距离原点 1 公里。

此边界框可能有两个问题，导致不可见的几何体：
* **该框可以远离中心**，因此由于远平面剪切，对象被完全剪切。 在这种情况下`boundingBox`，值如下所示： `min = [-2000, -5,-5], max = [-1990, 5,5]`，使用 x 轴上的大偏移作为示例。 要解决此问题，在[模型转换配置](../how-tos/conversion/configure-model-conversion.md)中启用`recenterToOrigin`选项。
* **盒子可以居中，但数量过大**。 这意味着，尽管摄像机从模型的中心开始，但其几何形状被剪切到所有方向。 在这种情况下`boundingBox`，典型值如下所示： `min = [-1000,-1000,-1000], max = [1000,1000,1000]`。 此类问题的原因通常是单位比例不匹配。 要进行补偿，请在[转换期间指定缩放值](../how-tos/conversion/configure-model-conversion.md#geometry-parameters)，或使用正确的单位标记源模型。 在运行时加载模型时，也可以将缩放应用于根节点。

**Unity 渲染管道不包括渲染挂钩：**

Azure 远程渲染将挂到 Unity 渲染管道中，以便使用视频执行帧合成，并执行重新投影。 要验证这些挂钩是否存在，请打开菜单*窗口>分析>帧调试器*。 启用它并确保管道`HolographicRemotingCallbackPass`中有两个条目：

![统一帧调试器](./media/troubleshoot-unity-pipeline.png)

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>使用远程呈现 API 的统一代码不编译

将 Unity 解决方案的*生成类型*切换到**调试**。 在 Unity 编辑器中测试 ARR`UNITY_EDITOR`时，该定义仅在"调试"生成中可用。 请注意，这与用于[部署应用程序的](../quickstarts/deploy-to-hololens.md)生成类型无关，在其中您应该更喜欢"发布"生成。

## <a name="unstable-holograms"></a>不稳定的全息图

如果渲染的对象似乎随着头部移动而移动，则可能会遇到*后期投影*（LSR） 的问题。 请参阅["后期重新投影](../overview/features/late-stage-reprojection.md)"一节，了解如何处理此类情况。

全息图不稳定的另一个原因（摆动、扭曲、抖动或跳跃全息图）可能是网络连接不良，特别是网络带宽不足或延迟过高。 [性能统计](../overview/features/performance-queries.md)值`ARRServiceStats.VideoFramesReused`是网络连接质量的一个很好的指标。 重复使用的帧指示旧视频帧需要在客户端重复使用的情况，因为没有新的视频帧可用 - 例如，由于数据包丢失或网络延迟的变化。 如果`ARRServiceStats.VideoFramesReused`通常大于零，则表示存在网络问题。

另一个要查看的值`ARRServiceStats.LatencyPoseToReceiveAvg`是 。 它应该始终低于 100 毫秒。 如果看到较高的值，则表示您已连接到太远的数据中心。

有关潜在缓解措施的列表，请参阅[网络连接指南](../reference/network-requirements.md#guidelines-for-network-connectivity)。

## <a name="next-steps"></a>后续步骤

* [系统要求](../overview/system-requirements.md)
* [网络要求](../reference/network-requirements.md)
