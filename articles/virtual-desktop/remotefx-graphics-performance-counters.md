---
title: 诊断远程桌面中的图形性能问题-Azure
description: 本文介绍如何使用远程桌面协议会话中的 RemoteFX 图形计数器诊断 Windows 虚拟桌面中图形的性能问题。
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 05/23/2019
ms.author: v-chjenk
ms.openlocfilehash: 8cd24861b9d7432a582d1b635b8ffcf0d8d2b9e6
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68233626"
---
# <a name="diagnose-graphics-performance-issues-in-remote-desktop"></a>诊断远程桌面中的图形性能问题

若要诊断远程会话的体验质量问题, 请在性能监视器的 "RemoteFX 图形" 部分下提供计数器。 本文可帮助你在使用这些计数器的远程桌面协议 (RDP) 会话期间查找并修复与图形相关的性能瓶颈。

## <a name="find-your-remote-session-name"></a>查找远程会话名称

你需要远程会话名称来标识图形性能计数器。 按照此部分中的说明标识每个计数器的实例。

1. 从远程会话打开 Windows 命令提示符。
2. 运行**qwinsta**命令并找到你的会话名称。
    - 如果会话托管在多会话虚拟机 (VM) 中:每个计数器的实例都用与你的会话名称后缀相同的数作为后缀, 如 "rdp-tcp 37"。
    - 如果会话托管在支持虚拟图形处理单元 (vGPU) 的 VM 中, 请执行以下操作:每个计数器的实例存储在服务器上而不是 VM 中。 计数器实例包含 VM 名称, 而不是会话名称中的数字, 如 "Win8 Enterprise VM"。

>[!NOTE]
> 虽然计数器在其名称中具有 RemoteFX, 但它们也包含 vGPU 方案中的远程桌面图形。

## <a name="access-performance-counters"></a>访问性能计数器

确定远程会话名称后, 请按照以下说明收集远程会话的 RemoteFX 图形性能计数器。

1. 选择 "**启动** > **管理工具** > **性能监视器**"。
2. 在 "**性能监视器**" 对话框中, 展开 "**监视工具**", 选择 "**性能监视器**", 然后选择 "**添加**"。
3. 在 "**添加计数器**" 对话框的 "**可用计数器**" 列表中, 展开 "RemoteFX 图形" 部分。
4. 选择要监视的计数器。
5. 在 "**所选对象的实例**" 列表中, 选择要为所选计数器监视的特定实例, 然后选择 "**添加**"。 若要选择所有可用的计数器实例, 请选择 "**所有实例**"。
6. 添加计数器后, 选择 **"确定"** 。

所选性能计数器将出现在 "性能监视器" 屏幕上。

>[!NOTE]
>主机上的每个活动会话都有其自己的每个性能计数器的实例。

## <a name="diagnose-issues"></a>诊断问题

图形相关性能问题通常分为四个类别:

- 低帧速率
- 随机停止
- 高输入延迟
- 帧质量差

### <a name="addressing-low-frame-rate-random-stalls-and-high-input-latency"></a>解决帧速率较低、随机停止和高输入延迟

首先选中 "输出帧/秒" 计数器。 它测量客户端提供的帧数。 如果此值小于输入帧/秒计数器, 则跳过帧。 若要识别瓶颈, 请使用 "跳过的帧数/秒" 计数器。

有三种类型的帧跳过/秒计数器:

- 每秒跳过的帧数 (服务器资源不足)
- 每秒跳过的帧数 (网络资源不足)
- 每秒跳过的帧数 (客户端资源不足)

"跳过的帧数/秒" 计数器的值太大, 这意味着问题与计数器跟踪的资源相关。 例如, 如果客户端没有以服务器提供框架的相同速率对帧进行解码和呈现, 则跳过的帧数/秒 (客户端资源不足) 计数器将会很高。

如果输出帧/秒计数器与输入帧/秒计数器匹配, 但仍注意到异常延迟或停止, 则平均编码时间可能是原因。 编码是在单会话 (vGPU) 方案中的服务器上和多会话方案中的 VM 上发生的同步过程。 平均编码时间应小于33毫秒。 如果平均编码时间低于33毫秒, 但仍存在性能问题, 则您正在使用的应用或操作系统可能存在问题。

有关诊断与应用相关的问题的详细信息, 请参阅[用户输入延迟性能计数器](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters)。

由于 RDP 支持的平均编码时间为33毫秒, 因此它支持输入帧速率最多为30帧/秒。 请注意, 33 毫秒是受支持的最大帧速率。 在许多情况下, 用户遇到的帧速率将更低, 具体取决于源将帧提供给 RDP 的频率。 例如, 观看视频等任务需要30帧/秒的输入帧速率为30帧/秒, 但计算密集型任务 (如不经常编辑文档) 会导致输入帧/秒的值大大降低, 用户的体验质量。

### <a name="addressing-poor-frame-quality"></a>解决帧质量差问题

使用 "帧质量" 计数器诊断帧质量问题。 此计数器将输出帧的质量表示为源帧质量的百分比。 质量损失可能是由于 RemoteFX 导致的, 也可能是图形源所固有的。 如果 RemoteFX 导致质量损失, 则问题可能是缺少网络或服务器资源来发送更高保真的内容。

## <a name="mitigation"></a>缓解措施

如果服务器资源导致瓶颈, 请尝试以下方法之一来提高性能:

- 减少每个主机的会话数。
- 增加服务器上的内存和计算资源。
- 丢弃连接的分辨率。

如果网络资源导致瓶颈, 请尝试以下方法之一来提高每个会话的网络可用性:

- 减少每个主机的会话数。
- 使用较高带宽的网络。
- 丢弃连接的分辨率。

如果客户端资源导致瓶颈, 请尝试以下方法之一来提高性能:

- 安装最新的远程桌面客户端。
- 增加客户端计算机上的内存和计算资源。

> [!NOTE]
> 当前不支持 "源帧/秒" 计数器。 目前, Source Frames/Second 计数器将始终显示0。

## <a name="next-steps"></a>后续步骤

- 若要创建 GPU 优化的 Azure 虚拟机, 请参阅[配置 Windows 虚拟桌面预览环境的图形处理单元 (GPU) 加速](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu)。
- 有关疑难解答和升级跟踪的概述, 请参阅[故障排除概述、反馈和支持](https://docs.microsoft.com/azure/virtual-desktop/troubleshoot-set-up-overview)。
- 若要了解有关预览服务的详细信息, 请参阅[Windows 桌面预览环境](https://docs.microsoft.com/azure/virtual-desktop/environment-setup)。
