---
title: 网络要求
description: 最佳体验的网络要求和最佳网络做法
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: reference
ms.openlocfilehash: 9bb8818d2bb91c14eafc272774e0b633c51e58e1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617874"
---
# <a name="network-requirements"></a>网络要求

与 Azure 数据中心的稳定、低延迟网络连接对于 Azure 远程呈现中的良好用户体验至关重要。 不良网络情况可能导致断开连接、不稳定、抖动或 "跳转"，以及更新服务器端场景图时出现明显的延迟。

## <a name="guidelines-for-network-connectivity"></a>网络连接准则

确切的网络要求取决于您的特定用例，如对远程场景图进行修改的数量和频率，以及呈现的视图的复杂性，但有许多准则可以确保您的体验尽可能好：

* Internet 连接需要为 Azure 远程呈现的单个用户会话至少支持**50 mbps 下游**和**10 mbps 上游**，前提是网络上没有竞争的流量。 建议采用更高的速度，以便获得更好的体验。 对于同一网络中的更多用户，这些要求会相应增加。
* 尽管这两个应用程序都可以正常工作，但使用**5 Ghz wi-fi 波段**通常会产生比 2.4 ghz wi-fi 波段更好的结果。
* 如果附近有其他 Wi-fi 网络，请避免使用这些其他网络使用的 Wi-fi 通道。 可以使用网络扫描工具（如[WifiInfoView](https://www.nirsoft.net/utils/wifi_information_view.html) ）来验证 wi-fi 网络使用的通道是否可以使用竞争性流量。
* 严格**避免使用 wi-fi 中继器**或 powerline 转发。
* 在同一 Wi-fi 网络上，**应避免具有竞争性的带宽密集流量**（如视频或游戏流）。
* **Wi-fi 信号强度良好**非常重要。 如果可能，请靠近 Wi-fi 接入点，避免客户端设备与访问点之间出现障碍。
* 请确保始终连接到[区域](regions.md)最近的**Azure 数据中心**。 数据中心越接近，网络延迟就越低，这对全息影像稳定性有重大影响。

> [!NOTE]
> 下游带宽主要用于视频流，后者又拆分为颜色和深度信息（60 Hz，立体声）。

## <a name="network-performance-tests"></a>网络性能测试

如果你想要初步了解网络连接的质量是否足以运行 Azure 远程渲染，可以使用现有的联机工具。 强烈建议从连接到与你计划在其上运行 Azure 远程呈现客户端应用程序的设备相同 Wi-fi 的相当强大的便携式计算机运行这些联机工具。 在移动电话或 HoloLens2 上运行测试所获得的结果通常不太有用，因为它们已经过证明，可以在低功率终结点设备上显示明显的变化。 放置便携式计算机的位置应该大致为你希望使用运行 Azure 远程呈现客户端应用程序的设备的同一位置。

下面是用于快速测试网络连接的几个简单步骤：

1. **运行 www.speedtest.net 等网络测试工具，以获取网络连接的总体延迟和上游/下游带宽的数据。**
选择最靠近你的服务器并运行测试。 尽管服务器不会是 Azure 远程呈现将连接到的 Azure 数据中心，但生成的数据仍有助于了解 internet 连接和 Wi-fi 的性能。
   * Azure 远程呈现的**最低要求**：约 40 mbps 下游和 5 Mbps 上游。
   * **建议**用于 Azure 远程呈现：约 100 Mbps 下游和 10 mbps 上游。
建议多次运行测试并获得最差的结果。
1. **使用 www.azurespeed.com 之类的工具来度量 Azure 数据中心的延迟**。 选择最靠近你的 Azure 远程呈现支持的 Azure 数据中心（请参阅[支持的区域](regions.md)），并运行**延迟测试**。 如果你看到的数字有变化，请将结果提供一段时间来稳定。
   * Azure 远程呈现的**最低要求**：延迟时间应始终小于100毫秒。
   * **建议**用于 Azure 远程呈现：延迟时间应始终小于70毫秒。

虽然低延迟并不能保证 Azure 远程呈现在你的网络上能够正常运行，但我们通常会发现，在成功通过这些测试的情况下，它会正常运行。
如果在运行 Azure 远程呈现时遇到不稳定、抖动或跳转影像的项目，请参阅[故障排除指南](../resources/troubleshoot.md)。

## <a name="next-steps"></a>后续步骤

* [快速入门：使用 Unity 渲染模型](../quickstarts/render-model.md)
