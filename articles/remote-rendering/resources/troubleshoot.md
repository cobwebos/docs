---
title: 疑难解答
description: Azure 远程渲染的故障排除信息
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: a5b625ea2b5b76d0938ac62be2202127ff0af66e
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90982963"
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

## <a name="error-disconnected-videoformatnotavailable"></a>错误 " `Disconnected: VideoFormatNotAvailable` "

检查 GPU 是否支持硬件视频解码。 请参阅[开发电脑](../overview/system-requirements.md#development-pc)。

如果在具有两个 GPU 的笔记本电脑上工作，则默认情况下运行的 GPU 可能不提供硬件视频解码功能。 如果是这样，请尝试强制应用使用另一个 GPU。 通常可以在 GPU 驱动程序设置中执行此操作。

## <a name="retrieve-sessionconversion-status-fails"></a>检索会话/转换状态失败

发送 REST API 命令过于频繁会导致服务器中止并最终返回故障。 限制情况下的 http 状态代码为 429 ( "请求太多" ) 。 根据经验法则，后续调用之间应有 5-10 秒的延迟。

请注意，此限制不仅影响直接调用时的 REST API 调用，还会影响其 c #/C + + 对应项，例如 `Session.GetPropertiesAsync` 、 `Session.RenewAsync` 或 `Frontend.GetAssetConversionStatusAsync` 。

如果你遇到服务器端限制，请更改代码以降低调用次数。 服务器每分钟会重置限制状态，因此在一分钟后重新运行代码是安全的。

## <a name="h265-codec-not-available"></a>H265 编解码器不可用

服务器可能因两个原因而拒绝连接 `codec not available` 。

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

请参阅特定 [服务器大小限制](../reference/limits.md#overall-number-of-polygons)。

**模型不在相机截锥内：**

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

Azure 远程渲染挂钩到 Unity 渲染管道中，以通过视频进行帧合成，并执行重新投影。 若要验证这些挂钩是否存在，请打开菜单 *:::no-loc text="Window > Analysis > Frame debugger":::* 。 启用它并确保管道中的 `HolographicRemotingCallbackPass` 有两个条目：

![Unity 帧调试器](./media/troubleshoot-unity-pipeline.png)

## <a name="checkerboard-pattern-is-rendered-after-model-loading"></a>在模型加载后呈现棋盘模式

如果呈现的图像如下所示： ![ 屏幕截图显示带有 "工具" 菜单的黑色和白色方块网格。](../reference/media/checkerboard.png)
然后，呈现器将达到 [标准配置大小的多边形限制](../reference/vm-sizes.md)。 若要缓解这种情况，请切换到 **高级** 配置大小或减少可见多边形的数目。

## <a name="the-rendered-image-in-unity-is-upside-down"></a>Unity 中呈现的图像颠倒

请确保遵循 [Unity 教程：完全查看远程模型](../tutorials/unity/view-remote-models/view-remote-models.md) 。 倒置图像表示创建离屏呈现目标需要 Unity。 当前不支持此行为，并在 HoloLens 2 上产生了巨大的性能影响。

此问题的原因可能是 MSAA、HDR 或启用后处理。 请确保选择低质量配置文件并将其设置为 Unity 中的默认配置文件。 为此，请参阅 *编辑 > 项目设置 ... > 质量*。

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
    
### <a name="arm64-builds-for-unity-projects-fail-because-audiopluginmshrtfdll-is-missing"></a>由于缺少 AudioPluginMsHRTF.dll，Unity 项目的 Arm64 生成失败

`AudioPluginMsHRTF.dll`已将 Arm64 的添加到*Windows Mixed Reality* (包 *) *版本3.0.1 中的 windowsmr。 确保已通过 Unity 包管理器安装了版本3.0.1 或更高版本。 从 Unity 菜单栏中，导航到 " *窗口 >" 包管理器* "，并查找" *Windows Mixed Reality* "包。

## <a name="unstable-holograms"></a>不稳定全息影像

如果渲染的对象看起来像是随着头部移动而移动，则可能会遇到后期阶段重新投影 (LSR) 问题。 有关如何解决此类情况的指南，请参阅有关[后期阶段重新投影](../overview/features/late-stage-reprojection.md)的部分。

不稳定全息影像（晃动、弯曲、抖动或跳转全息影像）的另一个原因可能是网络连接不佳，尤其是网络带宽不足或延迟过高。 网络连接质量的一个良好指示器是[性能统计信息](../overview/features/performance-queries.md)值 `ARRServiceStats.VideoFramesReused`。 重复使用的帧表示因为没有新视频帧可用，而需要在客户端上重复使用旧视频帧的情况（例如，由于数据包丢失或网络延迟变化）。 如果 `ARRServiceStats.VideoFramesReused` 经常大于零，则表示存在网络问题。

另一个要查看的值是 `ARRServiceStats.LatencyPoseToReceiveAvg`。 它应始终低于 100 毫秒。 如果出现较高值，则表示连接到的数据中心太远。

有关可能的缓解措施的列表，请参阅[网络连接指导原则](../reference/network-requirements.md#guidelines-for-network-connectivity)。

## <a name="z-fighting"></a>Z 冲突

虽然 ARR 提供了 [z 反击缓解功能](../overview/features/z-fighting-mitigation.md)，但在场景中仍会出现 z 向下。 本指南旨在解决这些剩余问题。

### <a name="recommended-steps"></a>建议的步骤

使用以下工作流来缓解 z 反击：

1. 在) 上，用 ARR (的默认设置测试场景

1. 通过其[API](../overview/features/z-fighting-mitigation.md)禁用 z 反击缓解 

1. 将相机附近和远端都更改为更接近的范围

1. 通过下一部分对场景进行故障排除

### <a name="investigating-remaining-z-fighting"></a>调查剩余的 z 反击

如果以上步骤已经用完，并且其余 z 操作不可接受，则需要调查 z 反击的根本原因。 如 [z 反击缓解功能页](../overview/features/z-fighting-mitigation.md)中所述，z 反击的主要原因有两个主要原因：深度精度损失在深度范围的最远位置，以及在处于共面状态时相交的图面。 深度精度损失是数学后果同样，只能通过执行上述步骤3来缓解。 共面图面表示源资产缺陷，并且更好地固定在源数据中。

ARR 具有一项功能，用于确定表面是否可以进行 z 抵抗： [棋盘突出显示](../overview/features/z-fighting-mitigation.md)。 您还可以直观地确定导致 z 反击的原因。 下面的第一个动画显示距离中的深度精度丢失示例，第二个动画显示将近共面图面的示例：

![动画显示了距离中的深度精度丢失示例。](./media/depth-precision-z-fighting.gif)  ![动画显示将近共面图面的示例。](./media/coplanar-z-fighting.gif)

将这些示例与 z 进行比较，以确定原因或按顺序执行以下分步工作流：

1. 将相机置于 z 反击曲面上方，以直接在图面上查看。
1. 慢慢向后移动相机，远离表面。
1. 如果 z 反击在所有时间都可见，则这些表面就是完全共面。 
1. 如果在大多数情况下，z 反击可见，则表面几乎共面。
1. 如果只是从远处看不到 z 反击，则原因是缺少深度精度。

共面图面可以有许多不同的原因：

* 由于错误或不同的工作流方法，导出应用程序复制了对象。

    请查看相应应用程序和应用程序支持的相关问题。

* 曲面将复制并翻转，使其在使用正面或背面剔除的呈现器中呈双面显示。

    通过 [模型转换](../how-tos/conversion/model-conversion.md) 导入确定模型的主体 sidedness。 默认情况下，sidedness 为默认值。 图面将呈现为一个薄壁，其中的两侧都具有物理正确的照明。 Sidedness 可以由源资产中的标志隐含，或在 [模型转换](../how-tos/conversion/model-conversion.md)期间显式强制。 此外，可以根据需要将 [单面模式](../overview/features/single-sided-rendering.md) 设置为 "正常"。

* 对象与源资产相交。

     如果对象的某些表面重叠，转换的对象也会创建 z 向方向。 在 ARR 中导入的场景中，将场景树的一部分转换也可能会造成此问题。

* 表面是有意的，如 decals 或墙壁上的文本。



## <a name="next-steps"></a>后续步骤

* [系统要求](../overview/system-requirements.md)
* [网络要求](../reference/network-requirements.md)
