---
title: 网络要求
description: 网络要求和获得最佳体验的网络最佳做法
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: reference
ms.openlocfilehash: fe684d15e2ce9b8d302db4eb6bd31dd1416abf21
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196549"
---
# <a name="network-requirements"></a>网络要求

若要在 Azure 远程渲染中获得良好的用户体验，与 Azure 数据中心建立低延迟的稳定网络连接至关重要。 网络状况不佳可能会导致连接断开、全息影像不稳定、抖动或“跳转”，并且可能会导致在更新服务器端场景图时出现明显延迟。

## <a name="guidelines-for-network-connectivity"></a>网络连接准则

确切的网络要求视特定用例而定（如对远程场景图的修改次数和频率，以及所渲染视图的复杂度），但仍有许多准则可以确保尽可能获得良好的用户体验：

* 对于单个 Azure 远程渲染用户会话，Internet 连接至少需要始终如一地支持 40 Mbps 下游速度和 5 Mbps 上游速度（假设网络上没有竞争流量）。 为了获得更好的体验，建议提高速度。 随着同一网络上的用户越来越多，这些要求也相应地纵向扩展。
* 使用 5 GHz Wi-Fi 频段通常会比使用 2.4 GHz Wi-Fi 频段产生更好的效果，不过这两种频段都可以使用。
* 如果附近有其他 Wi-Fi 网络，应避免使用其他这些网络正在使用的 Wi-Fi 通道。 可以使用网络扫描工具（如 [WifiInfoView](https://www.nirsoft.net/utils/wifi_information_view.html)）来验证你的 Wi-Fi 网络使用的通道是否没有竞争流量。
* 请务必避免使用 Wi-Fi 中继器或电力线 LAN 上网转发。
* 避免在同一 Wi-Fi 网络上出现带宽密集型竞争流量（如视频或游戏流）。
* 拥有良好的 Wi-Fi 信号强度至关重要。 如果可以，尽量靠近 Wi-Fi 接入点，避免客户端设备与接入点之间的障碍。
* 请务必始终连接到距离你所在[区域](regions.md)最近的 Azure 数据中心。 距离数据中心越近，网络延迟就越低，这对全息影像的稳定性有很大的影响。

> [!NOTE]
> 下游带宽主要为视频流所占用，而视频流又被拆分为颜色和深度信息（都是 60 Hz 的立体声）。

## <a name="network-performance-tests"></a>网络性能测试

若要初步了解网络连接质量是否足以运行 Azure 远程渲染，可以使用现有的联机工具。 强烈建议在一台功能相当强大的笔记本电脑上运行这些联机工具，这台笔记本电脑所连接到的 Wi-Fi 应与你计划在其上运行 Azure 远程渲染客户端应用的设备所连接到的 Wi-Fi 相同。 在移动电话或 HoloLens2 上的测试结果通常不太有用，因为已证明在低功率终结点设备上的测试结果的差异显著。 笔记本电脑的放置位置应与运行 Azure 远程渲染客户端应用的设备的使用位置大致相同。

下面是几个用于快速测试网络连接的简单步骤：

1. 运行 www.speedtest.net 等网络测试工具，以获取关于网络连接的总体延迟和上游/下游带宽的数据。
选择距离你最近的服务器，并运行测试。 尽管服务器不是 Azure 远程渲染将连接到的 Azure 数据中心，但生成的数据对于了解 Internet 连接和 Wi-Fi 的性能仍然很有用。
   * 针对 Azure 远程渲染的最低要求：约 40 Mbps 下游速度和 5 Mbps 上游速度。
   * 针对 Azure 远程渲染的建议要求：约 100 Mbps 下游速度和 10 Mbps 上游速度。
建议多次运行测试，并获得最差测试结果。
1. 使用 www.azurespeed.com 等工具来度量 Azure 数据中心的延迟。 选择 Azure 远程渲染支持的距离你最近的 Azure 数据中心（请参阅[支持的区域](regions.md)），然后运行延迟测试。 如果看到的数字有变化，请等待一段时间，让结果趋于稳定。
   * 针对 Azure 远程渲染的最低要求：延迟应始终短于 100 毫秒。
   * 针对 Azure 远程渲染的建议要求：延迟应始终短于 70 毫秒。

虽然低延迟并不能保证 Azure 远程渲染将在你的网络上运行良好，但在成功通过这些测试的情况下，应用通常都能运行良好。
如果在运行 Azure 远程渲染时遇到全息影像不稳定、抖动或跳转等问题，请参阅[故障排除指南](../resources/troubleshoot.md)。

## <a name="next-steps"></a>后续步骤

* [快速入门：使用 Unity 渲染模型](../quickstarts/render-model.md)
