---
title: 视频录制-Azure
description: 在 CCTV 相机的视频管理系统上下文中，视频录制是指从相机捕获视频并通过移动和浏览器应用进行查看的过程。 视频录制可分类为连续视频录制和基于事件的视频录制。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: c03472f577e67d5b2705a2ebe3f53e85e0849819
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260190"
---
# <a name="video-recording"></a>视频录制

在 CCTV 相机的视频管理系统上下文中，视频录制是指从相机捕获视频并通过移动和浏览器应用进行查看的过程。 视频录制可分类为连续视频录制和基于事件的视频录制。 

## <a name="continuous-video-recording"></a>连续视频录制  

连续视频录制（CVR）是指连续记录从视频源捕获的所有视频的过程。 CVR 确保所有视频可用（由[记录策略](#recording-policy)决定），以便在以后的某个时间点进行分析和/或审核。

## <a name="event-based-video-recording"></a>基于事件的视频录制  

基于事件的视频录制 (EVR) 是指由事件触发的视频录制过程。 出现这种事件的原因可能是处理视频信号本身或来自独立源（例如，来自门传感器）。 基于事件的视频录制（EVR）可以节省存储空间，但它需要其他组件来生成事件，并触发视频录制（按照预定义策略）。 换句话说，EVR 需要作出有关应触发视频录制的事件以及与录像（也称为录制策略）相关联的策略的其他决策。 策略的示例如下所示：从事件时间之前30秒开始，记录视频2分钟。 相关事件源自照相机本身的视频处理。 例如，如果在照相机的视区中的已预配置区域内检测到动作，则多个相机支持生成运动检测信号事件。 还可以通过在从相机捕获视频的另一台设备上处理视频并使用简单的图像处理方法或完善的机器学习模型来分析这些事件。 

## <a name="choosing-recording-modes"></a>选择录制模式  

是否使用 CVR 或 EVR 的选择取决于业务目标。 IoT Edge 上的实时视频分析为 CVR 和 EVR 提供平台功能。 可以在[CVR](continuous-video-recording-concept.md)和[EVR](event-based-video-recording-concept.md)方案文章中了解有关此问题的详细信息。

## <a name="recording-policy"></a>记录策略  

录制策略指的是规定记录开始/停止时间（以防出现 EVR）、记录持续时间和录音删除的策略。 记录策略使你可以在存储成本与业务需求之间取得平衡。 记录策略在 CVR 和 EVR 之间有所不同。 对于 CVR，记录策略定义应该存储多少天的视频（例如，过去7天）。 对于 EVR，记录策略定义录制的开始时间和结束时间以及视频的持续时间。 可以在[CVR](continuous-video-recording-concept.md)和[EVR](event-based-video-recording-concept.md)方案文章中了解有关此问题的详细信息。

## <a name="next-steps"></a>后续步骤

* [检测运动，将视频剪辑录制到 Azure 媒体服务](detect-motion-record-video-clips-media-services-quickstart.md)
* [检测动作，将视频剪辑录制到边缘设备](detect-motion-record-video-clips-edge-devices-quickstart.md)

