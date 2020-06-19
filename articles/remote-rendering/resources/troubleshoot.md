---
title: 疑难解答
description: Azure 远程渲染的故障排除信息
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: b6cb9c70de27e40c62d6a7adeece5cb39554c090
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2020
ms.locfileid: "83844552"
---
# <a name="troubleshoot"></a>疑难解答

此页列出了干扰 Azure 远程渲染的常见问题，以及解决这些问题的方式。

## <a name="cant-link-storage-account-to-arr-account"></a>无法将存储帐户链接到 ARR 帐户

有时在[存储帐户链接](../how-tos/create-an-account.md#link-storage-accounts)期间，未列出远程渲染帐户。 若要解决此问题，请转到 Azure 门户中找的 ARR 帐户，在左侧“设置”组下选择“标识”。 确保“状态”设置为“开启”。
![Unity 帧调试器](./media/troubleshoot-portal-identity.png)

## <a name="client-cant-connect-to-server"></a>客户端无法连接到服务器

确保防火墙（在设备上、路由器内部等）未阻止以下端口：

* 50051 (TCP) - 初始连接（HTTP 握手）需要
* 8266 (TCP+UDP) - 数据传输需要
* 5000 (TCP)、5433 (TCP)、8443 (TCP) - [ArrInspector](tools/arr-inspector.md) 需要

## <a name="error-disconnected-videoformatnotavailable"></a>发生错误“已断开连接:VideoFormatNotAvailable”

检查 GPU 是否支持硬件视频解码。 请参阅[开发电脑](../overview/system-requirements.md#development-pc)。

如果在具有两个 GPU 的笔记本电脑上工作，则默认情况下运行的 GPU 可能不提供硬件视频解码功能。 如果是这样，请尝试强制应用使用另一个 GPU。 通常可以在 GPU 驱动程序设置中执行此操作。

## <a name="h265-codec-not-available"></a>H265 编解码器不可用

有两个原因会导致服务器可能拒绝连接，发生“编解码器不可用”错误。

H265 编解码器未安装：

首先确保安装 HEVC 视频扩展，如系统要求的[软件](../overview/system-requirements.md#software)部分所述。

如果仍然遇到问题，请确保图形卡支持 H265，并且安装了最新图形驱动程序。 有关特定于供应商的信息，请参阅系统要求的[开发电脑](../overview/system-requirements.md#development-pc)部分。

编解码器已安装，但无法使用：

此问题的原因是 DLL 上的安全设置不正确。 尝试观看使用 H265 编码的视频时，此问题不会出现。 重新安装编解码器也无法解决问题。 而是执行以下步骤：

1. 以管理员权限打开 PowerShell 并运行

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    该命令应输出编解码器的 `InstallLocation`，如下所示：
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. 在 Windows 资源管理器中打开该文件夹
1. 应存在 x86 和 x64 子文件夹。 右键单击其中一个文件夹，然后选择“属性”
    1. 选择“安全性”选项卡，然后单击“高级”设置按钮
    1. 对“所有者”单击“更改” 
    1. 在文本字段中键入 Administrators
    1. 依次单击“检查名称”和“确定”
1. 针对其他文件夹重复上述步骤
1. 此外，对两个文件夹中的每个 DLL 文件重复上述步骤。 总共应有四个 DLL。

若要验证设置现在正确，请对所有四个 DLL 执行此操作：

1. 选择“属性”>“安全性”>“编辑”
1. 浏览所有“组/用户”的列表，确保各自设置了“读取和执行”权限（必须勾选“允许”列中的复选标记）

## <a name="low-video-quality"></a>视频质量较低

视频质量可能会受到网络质量或缺少 H265 视频编解码器的影响。

* 请参阅[确定网络问题](#unstable-holograms)的步骤。
* 请参阅有关安装最新图形驱动程序的[系统要求](../overview/system-requirements.md#development-pc)。

## <a name="video-recorded-with-mrc-does-not-reflect-the-quality-of-the-live-experience"></a>使用 MRC 录制的视频未反映实时体验的质量

可以通过[混合现实捕捉 (MRC)](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers) 在 Hololens 上录制视频。 但是，由于以下两个原因，所生成视频的质量不如实时体验：
* 视频帧速率上限为 30 Hz，而不是 60 Hz。
* 视频图像未经历[后期阶段重新投影](../overview/features/late-stage-reprojection.md)处理步骤，因此视频显得断断续续。

这两者都是录制技术的固有限制。

## <a name="black-screen-after-successful-model-loading"></a>成功加载模型之后黑屏

如果已连接到渲染运行时并成功加载了模型，但随后只看到黑屏，则这可能有几个不同的原因。

在进行更深入的分析之前，建议测试以下内容：

* 是否安装了 H265 编解码器？ 尽管应回退到 H264 编解码器，但我们发现了此回退无法正确发挥作用的情况。 请参阅有关安装最新图形驱动程序的[系统要求](../overview/system-requirements.md#development-pc)。
* 使用 Unity 项目时，关闭 Unity，删除项目目录中的临时 library和 obj 文件夹，并再次加载/生成项目。 在某些情况下，缓存的数据导致示例不能正常运行，没有明显的原因。

如果这两个步骤都没有帮助，则需要查明客户端是否收到视频帧。 这可以通过编程方式进行查询，如[服务器端性能查询](../overview/features/performance-queries.md)一章中所述。 `FrameStatistics struct` 具有一个成员，指示已收到的视频帧数。 如果此数字大于 0，并且随时间推移而增加，则客户端从服务器收到了实际视频帧。 因此，肯定是客户端的问题。

### <a name="common-client-side-issues"></a>常见客户端问题

模型超过所选 VM 的限制，尤其是最大多边形数：

请参阅特定 [VM 大小限制](../reference/limits.md#overall-number-of-polygons)。

模型不在视锥内：

在许多情况下，模型会正确显示，但位于相机视锥外部。 一个常见原因是模型使用远离中心的透视导出，因此它被相机的远点剪切平面所剪切。 以编程方式查询模型的边界框，并使用 Unity 将边框显示为线框，或将其值输出到调试日志，这会十分有帮助。

此外，转换过程会随转换后的模型一起生成[输出 json 文件](../how-tos/conversion/get-information.md)。 若要调试模型位置问题，有必要查看 [outputStatistics 部分](../how-tos/conversion/get-information.md#the-outputstatistics-section)中的 `boundingBox` 条目：

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

边界框在三维空间中描述为 `min` 和 `max` 位置（以米为单位）。 因此，1000.0 的坐标表示距离原点 1 千米。

此边界框可能出现两个导致不可见几何的问题：
* 边界框可能远离中心，因此会由于远点平面剪切而完全剪切对象。 本例中的 `boundingBox` 值将如下所示：`min = [-2000, -5,-5], max = [-1990, 5,5]`，此处在 x 轴上使用大偏移作为示例。 若要解决此类问题，请在[模型转换配置](../how-tos/conversion/configure-model-conversion.md)中启用 `recenterToOrigin` 选项。
* 边界框可能会居中，但数量级太大。 这意味着，尽管相机从模型中心开始，但其几何在所有方向上进行剪切。 在这种情况下，典型 `boundingBox` 值如下所示：`min = [-1000,-1000,-1000], max = [1000,1000,1000]`。 此类问题的原因通常是单位比例不匹配。 若要进行补偿，请[在转换期间指定缩放比例值](../how-tos/conversion/configure-model-conversion.md#geometry-parameters)或使用正确的单位标记源模型。 在运行时加载模型时，也可以将缩放比例应用于根节点。

Unity 渲染管道不包含渲染挂钩：

Azure 远程渲染挂钩到 Unity 渲染管道中，以通过视频进行帧合成，并执行重新投影。 若要验证这些挂钩是否存在，请打开菜单“窗口”>“分析”>“帧调试器”。 启用它并确保管道中的 `HolographicRemotingCallbackPass` 有两个条目：

![Unity 帧调试器](./media/troubleshoot-unity-pipeline.png)

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>使用远程渲染 API 的 Unity 代码未编译

### <a name="use-debug-when-compiling-for-unity-editor"></a>在为 Unity 编辑器进行编译时使用调试

将 Unity 解决方案的“生成类型”切换为“调试”。 在 Unity 编辑器中测试 ARR 时，定义 `UNITY_EDITOR` 仅在“调试”生成中可用。 请注意，这与用于[部署的应用程序](../quickstarts/deploy-to-hololens.md)的生成类型无关，在这种情况下应首选“发布”生成。

### <a name="compile-failures-when-compiling-unity-samples-for-hololens-2"></a>为 HoloLens 2 编译 Unity 示例时编译失败

尝试为 HoloLens 2 编译 Unity 示例（快速入门、ShowCaseApp、...）时出现虚假失败。 Visual Studio 提示无法复制某些文件，尽管这些文件存在。 如果遇到此问题：
* 从项目中删除所有临时 Unity 文件，然后重试。 即，关闭 Unity，删除项目目录中的临时 library和 obj 文件夹，并再次加载/生成项目。
* 确保项目位于磁盘上具有合理短路径的目录中，因为复制步骤有时会遇到长文件名问题。
* 如果这没有帮助，则可能是 MS 感知干扰了复制步骤。 若要设置例外，请从命令行运行此注册表命令（需要管理员权限）：
    ```cmd
    reg.exe ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Advanced Threat Protection" /v groupIds /t REG_SZ /d "Unity”
    ```
    
## <a name="unstable-holograms"></a>不稳定全息影像

如果渲染的对象看起来像是随着头部移动而移动，则可能会遇到后期阶段重新投影 (LSR) 问题。 有关如何解决此类情况的指南，请参阅有关[后期阶段重新投影](../overview/features/late-stage-reprojection.md)的部分。

不稳定全息影像（晃动、弯曲、抖动或跳转全息影像）的另一个原因可能是网络连接不佳，尤其是网络带宽不足或延迟过高。 网络连接质量的一个良好指示器是[性能统计信息](../overview/features/performance-queries.md)值 `ARRServiceStats.VideoFramesReused`。 重复使用的帧表示因为没有新视频帧可用，而需要在客户端上重复使用旧视频帧的情况（例如，由于数据包丢失或网络延迟变化）。 如果 `ARRServiceStats.VideoFramesReused` 经常大于零，则表示存在网络问题。

另一个要查看的值是 `ARRServiceStats.LatencyPoseToReceiveAvg`。 它应始终低于 100 毫秒。 如果出现较高值，则表示连接到的数据中心太远。

有关可能的缓解措施的列表，请参阅[网络连接指导原则](../reference/network-requirements.md#guidelines-for-network-connectivity)。

## <a name="next-steps"></a>后续步骤

* [系统要求](../overview/system-requirements.md)
* [网络要求](../reference/network-requirements.md)
