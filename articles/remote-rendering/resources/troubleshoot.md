---
title: 疑难解答
description: Azure 远程呈现的疑难解答信息
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: c1b807c6e4fa269ac2ab8d7eacd3ca1d4f81a1ca
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792609"
---
# <a name="troubleshoot"></a>疑难解答

此页列出了干扰 Azure 远程呈现的常见问题，以及解决这些问题的方法。

## <a name="client-cant-connect-to-server"></a>客户端无法连接到服务器

请确保防火墙（设备上、路由器内部等）不会阻止以下端口：

* **50051 （TCP）** -初始连接需要此项（HTTP 握手）
* **8266 （TCP + UDP）** -数据传输必需的
* **5000 （tcp）**、 **5433 （tcp）**、 **8443 （tcp）** - [ArrInspector](tools/arr-inspector.md)是必需的

## <a name="error-disconnected-videoformatnotavailable"></a>"Disconnected： VideoFormatNotAvailable" 错误

检查 GPU 是否支持硬件视频解码。 请参阅[开发 PC](../overview/system-requirements.md#development-pc)。

如果使用的是具有两个 Gpu 的便携式计算机，则默认情况下运行的 GPU 可能不提供硬件视频解码功能。 如果是这样，请尝试强制应用使用其他 GPU。 通常可以在 GPU 驱动程序设置中进行此设置。

## <a name="h265-codec-not-available"></a>H265 编解码器不可用

服务器拒绝连接时出现的原因有两个，即 "**未提供编解码器**错误" 错误。

**未安装 H265 编解码器：**

首先，请确保安装**HEVC 视频扩展**，如系统要求的[软件](../overview/system-requirements.md#software)部分所述。

如果仍然遇到问题，请确保图形卡支持 H265，并且安装了最新的图形驱动程序。 有关供应商特定信息，请参阅系统要求的[开发 PC](../overview/system-requirements.md#development-pc)部分。

**已安装编解码器，但无法使用：**

此问题的原因是 Dll 上的安全设置不正确。 当尝试观看编码为 H265 的视频时，此问题不是清单。 重新安装编解码器并不能解决此问题。 请改为执行以下步骤：

1. **使用管理员权限打开 PowerShell**并运行

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    该命令应输出编`InstallLocation`解码器的，如下所示：
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. 在 Windows 资源管理器中打开该文件夹
1. 应有一个**x86**和一个**x64**子文件夹。 右键单击其中一个文件夹，然后选择 "**属性**"
    1. 选择 "**安全**" 选项卡，然后单击 "**高级**设置" 按钮
    1. 单击**所有者**的**更改**
    1. 在文本字段中键入**管理员**
    1. 单击 "**检查名称**"，然后单击 **"确定"**
1. 对其他文件夹重复以上步骤
1. 同时，在两个文件夹中的每个 DLL 文件上重复上述步骤。 应有四个 Dll。

若要验证设置是否正确，请对四个 Dll 中的每一个执行此操作：

1. 选择**属性 > 安全性 > 编辑**
1. 浏览所有**组/用户**的列表，并确保每个组/用户具有**读取 & 执行**权限集（**允许**列中的复选标记必须勾选）

## <a name="low-video-quality"></a>低视频质量

视频质量可能会受到网络质量或缺少 H265 视频编解码器的威胁。

* 请参阅[标识网络问题](#unstable-holograms)的步骤。
* 请参阅安装最新图形驱动程序的[系统要求](../overview/system-requirements.md#development-pc)。

## <a name="video-recorded-with-mrc-does-not-reflect-the-quality-of-the-live-experience"></a>记录了 MRC 的视频不反映实时体验的质量

可以通过[混合现实捕获（MRC）](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers)在 Hololens 上记录视频。 不过，由于以下两个原因，生成的视频的质量更糟：
* 视频的帧速率上限为 30 Hz，而不是 60 Hz。
* 视频图像不会经历[后期 reprojection](../overview/features/late-stage-reprojection.md)处理步骤，因此视频看起来是 choppier。

这两者都是录制技术的固有局限性。

## <a name="black-screen-after-successful-model-loading"></a>成功加载模型后的黑屏

如果已连接到呈现运行时并成功加载了模型，但随后仅看到黑屏，则这可能会有几个不同的原因。

在进行更深入的分析之前，我们建议先测试以下内容：

* 是否安装了 H265 编解码器？ 尽管应该回退到 H264 编解码器，但我们发现此回退无法正常工作的情况。 请参阅安装最新图形驱动程序的[系统要求](../overview/system-requirements.md#development-pc)。
* 使用 Unity 项目时，关闭 Unity，删除项目目录中的临时*库*和*obj*文件夹，然后重新加载/生成项目。 在某些情况下，缓存的数据导致该示例不能正常运行，因此没有明显的原因。

如果这两个步骤都没有帮助，则需要确定客户端是否收到视频帧。 这可以通过编程方式进行查询，如[服务器端性能查询](../overview/features/performance-queries.md)一章中所述。 `FrameStatistics struct`具有一个成员，该成员指示已收到的视频帧的数量。 如果此数字大于0，并且随着时间的推移而增加，则客户端将从服务器接收实际的视频帧。 因此，它必须是客户端的问题。

### <a name="common-client-side-issues"></a>常见的客户端问题

**模型超出了所选 VM 的限制，尤其是最大多边形数量：**

请参阅特定[VM 大小限制](../reference/limits.md#overall-number-of-polygons)。

**模型不在视图的 "截锥" 中：**

在许多情况下，模型会正确显示，但位于相机的 "截锥" 的外部。 常见的原因是该模型已使用远距离中心轴进行了导出，因此它被照相机的最远的剪辑平面剪切。 它有助于以编程方式查询模型的边界框，并将 Unity 显示为线条框，或将其值输出到调试日志。

此外，转换过程还会生成一个[输出 json 文件](../how-tos/conversion/get-information.md)以及转换后的模型。 若要调试模型定位问题，有必要查看`boundingBox` [outputStatistics 部分](../how-tos/conversion/get-information.md#the-outputstatistics-section)中的条目：

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

边界框在三维空间中描述`min`为`max`和位置，以米为单位。 因此，1000.0 的坐标意味着离原点为 1 kilometer。

此边界框可能出现两个导致不可见几何的问题：
* **此框可以远离中心**，因此，由于远平面剪辑，将完全剪裁对象。 这`boundingBox`种情况下的值将如下所示`min = [-2000, -5,-5], max = [-1990, 5,5]`：，在 x 轴上使用大偏移量作为示例。 若要解决此类问题，请在`recenterToOrigin` [模型转换配置](../how-tos/conversion/configure-model-conversion.md)中启用选项。
* **此框可以居中，但数量级太大**。 这意味着，尽管相机在模型中心开始，但其几何在所有方向上都是剪裁的。 在`boundingBox`这种情况下，典型值如下所`min = [-1000,-1000,-1000], max = [1000,1000,1000]`示：。 此类问题的原因通常是单元比例不匹配。 若要进行补偿，请[在转换过程中指定缩放值](../how-tos/conversion/configure-model-conversion.md#geometry-parameters)或使用正确的单位标记源模型。 在运行时加载模型时，还可以将缩放应用于根节点。

**Unity 呈现管道不包括呈现挂钩：**

Azure 远程呈现挂钩到 Unity 呈现管道，以通过视频进行帧合成，并执行 reprojection。 若要验证这些挂钩是否存在，请打开菜单*窗口 > 分析 > 帧调试器*。 启用它并确保管道`HolographicRemotingCallbackPass`中存在两个条目：

![Unity 框架调试器](./media/troubleshoot-unity-pipeline.png)

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>使用远程呈现 API 的 Unity 代码不会编译

### <a name="use-debug-when-compiling-for-unity-editor"></a>在编译 Unity 编辑器时使用 Debug

切换要**调试**的 Unity 解决方案的*生成类型*。 在 Unity 编辑器中测试 ARR 时，定义`UNITY_EDITOR`仅在 "调试" 生成中可用。 请注意，这与用于[部署的应用程序](../quickstarts/deploy-to-hololens.md)的生成类型无关，你应首选 "发布" 版本。

### <a name="compile-failures-when-compiling-unity-samples-for-hololens-2"></a>编译 HoloLens 2 的 Unity 示例时编译失败

尝试编译 HoloLens 2 的 Unity 示例（快速入门、ShowCaseApp、...）时出现虚假故障。 Visual Studio 投诉原因不能复制某些文件，而不能复制这些文件。 如果遇到此问题：
* 请从项目中删除所有临时 Unity 文件，然后重试。
* 请确保这些项目位于磁盘上具有合理短路径的目录，因为复制步骤有时会遇到长文件名问题。
* 如果这没有帮助，则可能是 MS 感知干扰复制步骤。 若要设置异常，请从命令行运行此注册表命令（需要管理员权限）：
    ```cmd
    reg.exe ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Advanced Threat Protection" /v groupIds /t REG_SZ /d "Unity”
    ```
    
## <a name="unstable-holograms"></a>不稳定全息影像

如果呈现的对象看上去与头运动一起移动，则可能会遇到*后期阶段 Reprojection* （LSR）的问题。 有关如何解决此类情况的指导，请参阅有关 Reprojection 的最[晚阶段](../overview/features/late-stage-reprojection.md)的部分。

不稳定的全息影像（wobbling、弯曲、jittering 或跳转影像）的另一个原因可能是网络连接不好，尤其是网络带宽不足或延迟过高。 [性能统计值是性能统计](../overview/features/performance-queries.md)值`ARRServiceStats.VideoFramesReused`的良好指标。 重复使用的帧表示需要在客户端上重复使用旧视频帧的情况，因为没有可用的新视频帧–例如，由于数据包丢失或网络延迟变体的原因。 如果`ARRServiceStats.VideoFramesReused`频繁大于零，则表示存在网络问题。

另一个要查看的值`ARRServiceStats.LatencyPoseToReceiveAvg`是。 它应持续低于100毫秒。 如果你看到较高的值，则表示你连接到的数据中心太远。

有关可能的缓解措施的列表，请参阅[网络连接指南](../reference/network-requirements.md#guidelines-for-network-connectivity)。

## <a name="next-steps"></a>后续步骤

* [系统要求](../overview/system-requirements.md)
* [网络要求](../reference/network-requirements.md)
