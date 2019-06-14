---
title: 远程桌面-Azure 中的诊断图形性能问题
description: 本文介绍如何使用远程桌面协议会话中的 RemoteFX 图形计数器来诊断性能问题的 Windows 虚拟桌面图片。
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 05/23/2019
ms.author: v-chjenk
ms.openlocfilehash: 0b4113f1e0024415135aa99d1fb4e881efe448a3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66499259"
---
# <a name="diagnose-graphics-performance-issues-in-remote-desktop"></a>诊断中远程桌面的图形性能问题

当系统不会按预期执行时，务必确定问题的根源。 本文可帮助您确定和解决远程桌面协议 (RDP) 会话期间与图形相关的性能瓶颈。

## <a name="find-your-remote-session-name"></a>查找你远程会话的名称

你将需要在远程会话名称以标识图形性能计数器。 按照本部分中的说明来标识你的 Windows 虚拟桌面预览远程会话名称。

1. 远程会话中打开 Windows 命令提示符。
2. 运行**qwinsta**命令。
    - 如果在多会话虚拟机 (VM) 中承载你的会话：对于每个计数器名称后缀是相同的后缀中你的会话名称，例如"rdp-tcp 37。"
    - 如果支持虚拟图形处理单元 (vGPU) 的 VM 中托管你的会话：计数器存储在服务器而不是在 VM 中。 计数器实例的会话名称，包括 VM 名称而不是数如下所述"Win8 Enterprise VM。"

>[!NOTE]
> 计数器名称中含有 RemoteFX，尽管它们在 vGPU 情况下包括远程桌面图形。

## <a name="access-performance-counters"></a>访问性能计数器

RemoteFX 图形中的性能计数器帮助您通过帮助您跟踪帧编码时间等内容来检测瓶颈，并跳过的帧。

确定你的远程会话名称后，按照以下说明来收集远程会话的 RemoteFX 图形性能计数器。

1. 选择**启动** > **管理工具** > **性能监视器**。
2. 在中**性能监视器**对话框框中，展开**监视工具**，选择**性能监视器**，然后选择**添加**。
3. 在中**添加计数器**对话框中，从**可用的计数器**列表中，展开的 RemoteFX 图形性能计数器对象。
4. 选择要监视的计数器。
5. 在中**选定对象的实例**列表中，选择要监视的所选的计数器，然后选择的特定实例**添加**。 若要选择的所有可用的计数器实例，请选择**所有实例**。
6. 添加计数器以后, 选择**确定**。

所选的性能计数器将显示在性能监视器屏幕上。

>[!NOTE]
>在主机上的每个活动会话具有自己的每个性能计数器实例。

## <a name="diagnosis"></a>诊断

与图形相关的性能问题通常分为四个类别：

- 较低的帧速率
- 随机停止
- 高输入的延迟
- 较差的帧质量

首先解决较低的帧速率、 随机将停止，并输入的延迟较高。 下一节将告诉您哪些性能计数器来测量每个类别。

### <a name="performance-counters"></a>性能计数器

本部分可帮助识别瓶颈。

首先检查输出 Frames/Second 计数器。 它测量可供客户端的帧数。 如果此值小于输入 Frames/Second 计数器，正在跳过的帧。 若要标识瓶颈，请使用帧已跳过每秒计数器。

有三种类型的框架已跳过每秒计数器：

- 帧每秒跳过 （没有足够的网络资源）
- 帧每秒跳过 （没有足够的客户端资源）
- 帧每秒跳过 （服务器资源不足）

已跳过每秒计数器意味着问题与资源的帧的任何高价值的计数器跟踪。 例如，如果客户端不会对进行解码，相同的费率服务器存在帧提供帧的帧已跳过/秒 （客户端资源不足） 计数器将很高。

如果输出 Frames/Second 计数器与匹配输入 Frames/Second 计数器，仍有不寻常的延隔时间尚未或停滞，则问题可能由编码的平均时间。 编码是发生在单个会话 (vGPU) 方案中的服务器上并在多会话方案中 VM 上一个同步过程。 编码的平均时间应为低于 33 毫秒。 如果编码的平均时间是低于 33 毫秒，但仍有性能问题，可能有问题的应用程序或操作系统使用。

有关诊断应用相关的问题的详细信息，请参阅[用户输入延迟性能计数器](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters)。

由于 RDP 支持 33 毫秒平均编码时间，因此它支持最多 30 输入的帧速率帧/秒。 请注意，33 毫秒的受支持的最大帧速率。 在许多情况下，用户感受到的帧速率较低，具体取决于何种频率帧提供源通过 rdp 连接。 例如，任务喜欢观看视频需要完整的输入的帧速率为 30 帧/秒，但更少资源密集型任务，如不常编辑 word 文档不需要此类较高的每秒输入帧速率，以获得良好的用户体验。

使用的帧质量计数器来诊断帧质量问题。 此计数器表示源帧质量占输出帧的质量。 质量丢失可能是由于 RemoteFX，也可能是固有的图形的源。 如果 RemoteFX 导致质量丢失，则问题可能由缺少网络或服务器资源发送提高保真度的内容。

## <a name="mitigation"></a>缓解措施

如果服务器资源导致瓶颈的原因，请尝试以提高性能的以下内容之一：

- 减少每个主机的会话数。
- 增加内存和计算资源在服务器上。
- 删除连接的解决方法。

如果网络资源导致瓶颈的原因，请尝试以下操作来改进网络可用性，每个会话之一：

- 减少每个主机的会话数。
- 删除连接的解决方法。
- 使用更高带宽网络。

如果客户端资源导致瓶颈的原因，请执行一个或两个需要提高性能的以下事项：

- 安装最新的远程桌面客户端。
- 增加内存和计算客户端计算机上的资源。

> [!NOTE]
> 我们当前不支持源 Frames/Second 计数器。 现在，源 Frames/Second 计数器将始终设置为 0。

## <a name="next-steps"></a>后续步骤

- 若要创建 GPU 优化 Azure 虚拟机，请参阅[配置的图形处理单元 (GPU) 加速 Windows 虚拟桌面预览环境](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu)。
- 有关故障排除和升级轨道的概述，请参阅[故障排除概述、 反馈和支持](https://docs.microsoft.com/azure/virtual-desktop/troubleshoot-set-up-overview)。
- 若要了解有关预览服务的详细信息，请参阅[Windows Desktop 预览环境](https://docs.microsoft.com/azure/virtual-desktop/environment-setup)。
