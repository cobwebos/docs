---
title: 网络要求
description: 网络要求和最佳网络实践，实现最佳体验
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: reference
ms.openlocfilehash: 9bb8818d2bb91c14eafc272774e0b633c51e58e1
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617874"
---
# <a name="network-requirements"></a>网络要求

与 Azure 数据中心的稳定、低延迟网络连接对于 Azure 远程呈现中良好的用户体验至关重要。 网络条件不佳可能导致连接断开、不稳定、抖动或"跳跃"全息图，并在更新服务器端场景图时出现明显的延迟。

## <a name="guidelines-for-network-connectivity"></a>网络连接指南

确切的网络要求取决于您的特定用例，例如对远程场景图的修改次数和频率以及渲染视图的复杂性，但有许多准则可确保您的体验尽可能好：

* 假定网络上没有竞争流量，您的互联网连接需要始终如一地支持**下游 50 Mbps**和**10 Mbps 的上行**Azure 远程呈现单个用户会话。 我们建议提高费率以提供更好的体验。 随着同一网络上的用户越来越多，这些要求相应地扩展。
* 使用**5-GHz Wi-Fi 频段**通常比 2.4 GHz Wi-Fi 频段产生更好的效果，尽管两者都应正常工作。
* 如果附近还有其他 Wi-Fi 网络，请避免使用这些其他网络使用的 Wi-Fi 通道。 您可以使用[WifiInfoView](https://www.nirsoft.net/utils/wifi_information_view.html)等网络扫描工具来验证您的 Wi-Fi 网络使用的频道是否没有竞争流量。
* 严禁**使用 Wi-Fi 中继器**或 LAN 过电源线转发。
* 避免在同一 Wi-Fi 网络上**出现争用带宽密集流量**（如视频或游戏流）。
* **良好的 Wi-Fi 信号强度**至关重要。 如果可能，请靠近 Wi-Fi 接入点，避免客户端设备和接入点之间的障碍物。
* 请确保始终连接到[区域](regions.md)最近的 Azure**数据中心**。 数据中心越近，网络延迟越低，对全息图稳定性的影响很大。

> [!NOTE]
> 下游带宽主要由视频流消耗，而视频流又在颜色和深度信息（60 Hz，立体声）之间分裂。

## <a name="network-performance-tests"></a>网络性能测试

如果要初步了解网络连接的质量是否足以运行 Azure 远程呈现，则可以使用现有的联机工具。 我们强烈建议使用与计划运行 Azure 远程呈现客户端应用程序的设备相同的强大便携式计算机运行这些联机工具。 在移动电话或 HoloLens2 上运行测试的结果通常不太有用，因为它们已证明在低功率端点设备上表现出显著的变化。 放置便携式计算机的位置应大致位于使用运行 Azure 远程呈现客户端应用程序的设备时所期望的位置。

以下是快速测试网络连接的几个简单步骤：

1. **运行网络测试工具，如www.speedtest.net，获取网络连接的总体延迟和上下游带宽数据。**
选择离您最近的服务器并运行测试。 虽然服务器不是 Azure 远程呈现将连接到的 Azure 数据中心，但生成的数据对于了解 Internet 连接和 Wi-Fi 的性能仍然很有用。
   * Azure 远程呈现**的最低要求**：下游约 40 Mbps 和 5 Mbps 上行。
   * **建议**用于 Azure 远程呈现：下游约 100 Mbps 和上行 10 Mbps。
我们建议多次运行测试并获取最差结果。
1. **使用www.azurespeed.com等工具测量 Azure 数据中心的延迟**。 选择 Azure 远程呈现支持的 Azure 数据中心（请参阅[支持的区域](regions.md)），然后运行**延迟测试**。 如果看到的数字有变化，给结果一些时间来稳定。
   * Azure 远程呈现**的最低要求**：延迟应始终小于 100 毫秒。
   * **建议**用于 Azure 远程呈现：延迟应始终小于 70 毫秒。

虽然低延迟不能保证 Azure 远程呈现在您的网络上运行良好，但我们通常看到，在这些测试成功通过的情况下，它运行良好。
如果在运行 Azure 远程渲染时遇到不稳定、抖动或跳跃全息图等伪影，请参阅[故障排除指南](../resources/troubleshoot.md)。

## <a name="next-steps"></a>后续步骤

* [快速入门：使用 Unity 渲染模型](../quickstarts/render-model.md)
