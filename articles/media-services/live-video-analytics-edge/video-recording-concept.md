---
title: 视频录制 - Azure
description: 在使用 CCTV 相机的视频管理系统的情况下，视频录制是指从相机捕获视频并录制它以通过移动应用和浏览器应用进行观看的过程。 视频录制可分为连续视频录制和基于事件的视频录制两类。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: c03472f577e67d5b2705a2ebe3f53e85e0849819
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84260190"
---
# <a name="video-recording"></a>视频录制

在使用 CCTV 相机的视频管理系统的情况下，视频录制是指从相机捕获视频并录制它以通过移动应用和浏览器应用进行观看的过程。 视频录制可分为连续视频录制和基于事件的视频录制两类。 

## <a name="continuous-video-recording"></a>连续视频录制  

连续视频录制 (CVR) 是指连续录制从视频源捕获的所有视频的过程。 CVR 可确保所有视频都可在以后的某个时间点进行分析和/或审核（由[录制策略](#recording-policy)指定）。

## <a name="event-based-video-recording"></a>基于事件的视频录制  

基于事件的视频录制 (EVR) 是指由事件触发的视频录制过程。 相关事件可能源于视频信号本身的处理，也可能来自独立源（例如，来自门传感器）。 基于事件的视频录制 (EVR) 可以节省存储空间，但需要额外的组件来生成事件并触发视频录制（根据预定义的策略）。 换句话说，EVR 需要对那些应触发视频录制的事件以及与视频录制相关的策略（也称为录制策略）做出附加决策。 策略的示例如下所示：从事件时间之前的 30 秒开始录制 2 分钟的视频。 相关事件可能源自相机本身的视频处理。 例如，在相机视区中预先配置的感兴趣区域内检测到运动时，多个相机支持生成运动检测信号事件。 事件也可以通过在另一个设备上处理视频来生成，该设备从相机捕获视频并使用简单的图像处理技术或复杂的机器学习模型对其进行分析。 

## <a name="choosing-recording-modes"></a>选择录制模式  

选择使用 CVR 还是使用 EVR 取决于业务目标。 IoT Edge 上的实时视频分析同时为 CVR 和 EVR 提供平台功能。 有关详细信息，可参阅 [CVR](continuous-video-recording-concept.md) 和 [EVR](event-based-video-recording-concept.md) 方案文章。

## <a name="recording-policy"></a>录制策略  

录制策略是指规定录制开始/停止时间（如果是 EVR）、录制持续时间和录制删除操作的策略。 录制策略使你能够均衡存储成本和业务需求。 录制策略在 CVR 和 EVR 中有所不同。 对于 CVR，录制策略定义应存储多少天的视频（例如，过去 7 天的视频）。 对于 EVR，录制策略定义录制开始时间和结束时间以及视频的持续时间。 有关详细信息，可参阅 [CVR](continuous-video-recording-concept.md) 和 [EVR](event-based-video-recording-concept.md) 方案文章。

## <a name="next-steps"></a>后续步骤

* [检测运动，将视频剪辑录制到 Azure 媒体服务](detect-motion-record-video-clips-media-services-quickstart.md)
* [检测运动，将视频剪辑录制到边缘设备](detect-motion-record-video-clips-edge-devices-quickstart.md)

