---
title: 查看和管理 Microsoft Azure StorSimple 虚拟阵列警报 | Microsoft 文档
description: 介绍 StorSimple 虚拟阵列警报条件和严重性以及如何使用 StorSimple Manager 服务管理警报。
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 97ee25a1-0ec3-4883-9a0a-54b722598462
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/12/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7d4d680e3460fbeff73c2f334c6461da7967374d
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2018
ms.locfileid: "27786401"
---
# <a name="use-storsimple-device-manager-to-manage-alerts-for-the-storsimple-virtual-array"></a>使用 StorSimple Device Manager 管理 StorSimple 虚拟阵列的警报

## <a name="overview"></a>概述

StorSimple Device Manager 服务中的警报功能提供了一种实时查看和清除与 StorSimple 虚拟阵列相关的警报的方法。 可以使用“服务摘要”边栏选项卡上的警报来集中监视 StorSimple 虚拟阵列和整个 Microsoft Azure StorSimple 解决方案的运行状况问题。

本教程介绍如何配置警报通知、常见的警报条件、警报严重性级别以及如何查看和跟踪警报。 此外，还介绍了警报快速参考表，可在其中快速找到特定的警报并做出适当的响应。

![“警报”页](./media/storsimple-virtual-array-manage-alerts/alerts1.png)

## <a name="configure-alert-settings"></a>配置警报设置

可以选择是否想要通过电子邮件接收有关每个 StorSimple 虚拟阵列的警报条件的通知。 此外，可以指定其他警报通知收件人，方法是在“其他电子邮件收件人”框中输入其电子邮件地址并用分号分隔。

> [!NOTE]
> 最多可为每个虚拟阵列输入 20 个电子邮件地址。


为虚拟阵列启用电子邮件通知后，每次发生关键警报时，通知列表成员都将收到电子邮件消息。 消息将从 *storsimple-alerts-noreply@mail.windowsazure.com* 发出，并描述警报条件。 收件人可以单击“取消订阅”将自己从电子邮件通知列表中删除。

#### <a name="to-enable-email-notification-for-alerts"></a>为警报启用电子邮件通知

1. 转到 StorSimple Device Manager 服务，在“管理”部分中，选择和单击“设备”。 从显示的设备列表中，选择并单击设备。
   
    ![警报设置](./media/storsimple-virtual-array-manage-alerts/alerts2.png)
2. 此时会打开“设置”边栏选项卡。 在“设备设置”部分中，选择“常规”。 此时会打开“常规设置”边栏选项卡。
   
    ![警报通知配置](./media/storsimple-virtual-array-manage-alerts/alerts4.png)
3. 在“常规设置”边栏选项卡中，转到“警报设置”部分，并设置以下项：
   
   1. 在“启用电子邮件通知”字段中，选择“是”。
   2. 如果希望服务管理员和所有协同管理员接收警报通知，请在“电子邮件服务管理员”字段中选择“是”。
   3. 在“其他电子邮件收件人”字段中，输入应接收警报通知的所有其他收件人的电子邮件地址。 以 *someone@somewhere.com* 格式输入名称。使用分号分隔电子邮件地址。 对于每个虚拟设备，最多可以配置 20 个电子邮件地址。
      
       ![警报通知配置](./media/storsimple-virtual-array-manage-alerts/alerts6.png)
   4. 若要发送测试电子邮件通知，请单击“发送测试电子邮件”。 StorSimple Device Manager 服务在转发测试通知时会显示状态消息。
      
       ![警报测试通知电子邮件已发送](./media/storsimple-virtual-array-manage-alerts/alerts7.png)
      
      > [!NOTE]
      > 如果无法发送测试通知消息，StorSimple Device Manager 服务会显示相应的消息。 请单击“确定”，并在几分钟后重试发送测试通知消息。
      > 
      > 
   5. 在页面底部，单击“保存”保存配置。 出现确认提示时，单击“是”。
      
      ![警报测试通知电子邮件已发送](./media/storsimple-virtual-array-manage-alerts/alerts10.png)

## <a name="common-alert-conditions"></a>常见警报条件

StorSimple 虚拟阵列可在响应各种条件时生成警报。 以下是最常见类型的警报条件：

* **连接问题** - 传输数据遇到困难时，会生成这些警报。 在通过 Azure 存储帐户往返传输数据时，可能会遇到通信问题，另外通信问题也可能是由于虚拟设备与 StorSimple Device Manager 服务之间没有连接而导致。 由于故障点众多，通信问题属于修复难度最大的问题。 在继续进行更高级的故障排除之前，始终应该先验证网络连接和 Internet 访问是否可用。 有关端口和防火墙设置的信息，请转到 [StorSimple 虚拟阵列系统要求](storsimple-ova-system-requirements.md)。 有关故障排除的帮助，请转到[使用 Test-Connection cmdlet 进行故障排除](storsimple-troubleshoot-deployment.md)。
* **性能问题** - 当系统未在最佳状态下运行时（例如在高负载下运行），会导致这些警报。

此外，还可能会看到与安全、更新或作业失败相关的警报。

## <a name="alert-severity-levels"></a>警报严重级别

警报具有不同的严重级别，具体取决于警报情况将产生的影响，以及对警报响应的需要。 严重级别包括：

* **关键** - 当出现影响系统成功运行的条件时，将生成此类警报。 必须采取措施，确保 StorSimple 服务不会中断。
* **警告** - 如果得不到解决，此类条件可能变成关键级别。 应针对这种情况进行调查，并采取任何必要的措施来解决问题。
* **信息** - 此类警报包含可用于跟踪和管理系统的信息。

## <a name="view-and-track-alerts"></a>查看和跟踪警报

在 StorSimple Device Manager 服务摘要边栏选项卡上，可快速浏览虚拟设备上按严重级别排列的警报的数量。

![“警报”仪表板](./media/storsimple-virtual-array-manage-alerts/alerts14.png)

单击严重性级别可打开“警报”边栏选项卡。 结果仅包括与该严重级别匹配的警报。

![警报报告已划归到警报类型](./media/storsimple-virtual-array-manage-alerts/alerts15.png)

单击列表中的警报可获取有关该警报的其他详细信息，包括上次报告警报的时间、警报在设备上出现的次数、解决警报问题的建议操作。

![警报列表和详细信息](./media/storsimple-virtual-array-manage-alerts/alerts16.png)

如果需要将信息发送到 Microsoft 支持部门，可将警报详细信息复制到文本文件。 遵循建议消除本地警报条件后，应从列表中清除该警报。 从列表中选择该警报，并单击“清除”。 要清除多个警报，请选择每个警报，单击除“警报”列以外的所有列，并在选择要清除的所有警报之后单击“清除”。

单击“清除”时，可以提供有关警报的注释，以及采用的解决问题的步骤。 

![警报注释](./media/storsimple-virtual-array-manage-alerts/alerts17.png)

如果新信息触发另一个事件，系统将清除某些事件。 

## <a name="sort-and-review-alerts"></a>排序和查看警报

“警报”边栏选项卡最多可显示 250 个警报。 如果超过该警报数目，默认视图中不一定会显示所有警报。 可以结合以下字段来自定义要显示的通知：

* **状态** - 可以显示“活动”或“已清除”的警报。 活动警报仍在系统上触发，而已清除的警报已由管理员手动清除，或由于系统以新信息更新警报条件而以编程方式清除。
* **严重性** - 可以显示所有严重级别（关键、警告、信息）的警报，或只显示特定严重性的警报，例如仅显示关键警报。
* **源** - 可以显示来自所有源的警报，或只显示来自服务或者某一个或所有虚拟设备的警报。
* **时间范围** - 可以指定“从”和“到”日期时间戳，查看感兴趣的时段内的警报。

## <a name="alerts-quick-reference"></a>警报快速参考

下表列出了可能会遇到的一些 StorSimple 警报，以及适用的其他信息和建议。 StorSimple 虚拟阵列警报属于以下类别之一：

* [云连接警报](#cloud-connectivity-alerts)
* [配置警报](#configuration-alerts)
* [作业失败警报](#job-failure-alerts)
* [性能警报](#performance-alerts)
* [安全警报](#security-alerts)

### <a name="cloud-connectivity-alerts"></a>云连接警报

| 警报文本 | 事件 | 详细信息/建议的操作 |
|:--- |:--- |:--- |
| 设备 *<device name>* 未连接到云。 |已命名的设备无法连接到云。 |无法连接到云。 这可能由以下原因之一造成：<ul><li>设备上的网络设置可能出现问题。</li><li>存储帐户凭据可能出现问题。</li></ul>有关排查连接问题的详细信息，请转到设备的[本地 web UI](storsimple-ova-web-ui-admin.md)。 |

### <a name="configuration-alerts"></a>配置警报

| 警报文本 | 事件 | 详细信息/建议的操作 |
|:--- |:--- |:--- |
| 本地虚拟设备配置不受支持。 |降低性能。 |当前配置可能导致性能降低。 确保服务器满足最低配置要求。 有关详细信息，请转到 [StorSimple 虚拟阵列要求](storsimple-ova-system-requirements.md)。 |
| <*device name*> 上的预配磁盘空间不足。 |磁盘空间警告。 |预配磁盘空间不足。 要释放空间，请考虑将工作负荷移动到另一个卷或共享，或者删除数据。 |

### <a name="job-failure-alerts"></a>作业失败警报

| 警报文本 | 事件 | 详细信息/建议的操作 |
|:--- |:--- |:--- |
| 无法完成 <*device name*> 的备份。 |备份作业失败。 |无法创建备份。 考虑以下操作之一：<ul><li>连接问题可能正在阻止备份操作成功完成。 请确保没有任何连接问题。 有关排查连接问题的详细信息，请转到虚拟设备的[本地 Web UI](storsimple-ova-web-ui-admin.md)。</li><li>已达到可用存储限制。 若要释放空间，请考虑删除不再需要的所有备份。</li></ul> 解决问题、清除警报并重试操作。 |
| 无法完成 <*device name*> 的克隆。 |克隆作业失败。 |无法创建克隆。 考虑以下操作之一：<ul><li>备份列表可能无效。 刷新列表以验证它是否仍然有效。</li><li>连接问题可能正在阻止克隆操作成功完成。 请确保没有任何连接问题。</li><li>已达到可用存储限制。 若要释放空间，请考虑删除不再需要的所有备份。</li></ul>解决问题、清除警报并重试操作。 |

### <a name="networking-alerts"></a>网络警报
| 警报文本 | 事件 | 详细信息/建议的操作 |
|:--- |:--- |:--- |
| 无法连接到身份验证服务。 |数据路径错误 |用于身份验证的 URL 不可访问。 请确保防火墙规则包括为 StorSimple 设备指定的 URL 模式。 有关 Azure 门户中 URL 模式的详细信息，请参阅 [StorSimple 虚拟数组网络需求](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules)。|

### <a name="performance-alerts"></a>性能警报

| 警报文本 | 事件 | 详细信息/建议的操作 |
|:--- |:--- |:--- |
| 在数据传输中遇到意外延迟。 |数据传输较慢。 |当超出存储服务的可伸缩性目标时，会发生限制错误。 存储服务这样做是为了确保没有单个客户端或租户可以在损害其他客户端或租户的情况下使用服务。 有关 Azure 存储帐户疑难解答的详细信息，请转到[监视、诊断和排查 Microsoft Azure 存储问题](../storage/common/storage-monitoring-diagnosing-troubleshooting.md)。 |
| <*device name*> 上的本地预留磁盘空间不足。 |响应时间较长。 |在本地设备上预留 <*device name*> 的总预配大小的 10%，现在预留空间不足。 <*device name*> 上的工作负荷正在生成更高的改动率，或者最近迁移了大量数据。 这可能导致性能降低。 若要解决此问题，请考虑采取以下措施之一：<ul><li>增加此设备的云带宽。</li><li>减少工作负荷或将工作负荷移到其他卷或共享。</li></ul> |

### <a name="security-alerts"></a>安全警报

| 警报文本 | 事件 | 详细信息/建议的操作 |
|:--- |:--- |:--- |
| <*device name*> 的密码将在 <*number*> 天后到期。 |密码警告。 |密码将在 <number< 天后到期。 请考虑更改密码。 有关详细信息，请转到 [更改 StorSimple 虚拟阵列设备管理员密码](storsimple-virtual-array-change-device-admin-password.md)。 |

## <a name="next-steps"></a>后续步骤

* [了解 StorSimple 虚拟阵列](storsimple-ova-overview.md)。

