---
title: 了解环境中的数据保留-Azure 时序见解 |Microsoft Docs
description: 本文介绍控制 Azure 时序见解环境中的数据保留的两项设置。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/21/2019
ms.custom: seodec18
ms.openlocfilehash: f479433ef486c12a00f74b220d46f84f5f979e7c
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863455"
---
# <a name="understand-data-retention-in-azure-time-series-insights"></a>了解 Azure 时序见解中的数据保留

本文介绍了影响 Azure 时序见解环境中的数据保留的两项主要设置。

## <a name="video"></a>视频

### <a name="the-following-video-summarizes-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>以下视频汇总了时序见解数据保留和如何对其进行规划。</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

每个 Azure 时序见解环境都有控制**数据保留时间**的设置。 该值的范围为 1 到 400 天。 根据环境存储容量或保持期（以先达到的时间为准）删除数据。

此外，Azure 时序见解环境的 "**存储限制超出**了" 行为设置。 它控制在达到环境的最大容量时的入口和清除行为。 配置时，有两种方法可供选择：

- 清除旧数据（默认行为）  
- 暂停传入

> [!NOTE]
> 创建新环境时，保留规则默认配置为“清除旧数据”。 使用 Azure 门户在时序见解环境的 "**配置**" 页上，可以根据需要切换此设置。
> * 有关如何配置保留策略的信息，请参阅[配置时序见解中的保留期](time-series-insights-how-to-configure-retention.md)。

下面更详细地介绍了这两个数据保留策略。

## <a name="purge-old-data"></a>清除旧数据

- **清除旧数据**是 Azure 时序见解环境的默认设置。  
- 如果用户希望在其时序见解环境中始终具有*最新的数据*，则可以**清除旧数据**。
- "**清除旧数据**" 设置在达到环境限制（保留时间、大小或计数，第一个为准）后会*清除*数据。 默认情况下，保留时间设置为 30 天。
- 将首先清除最早的引入数据（"先进先出" 的方法）。

### <a name="example-one"></a>示例一

假设某个示例环境的保留行为是“持续传入和清除旧数据”：

**数据保持时间**设置为400天。 “容量”设置为 S1 单位，包含 30 GB 总容量。 假设每天的入站数据累积平均为 500 MB。 根据给定的入站数据率，此环境只能保留 60 天的数据，因为在 60 天后会达到最大容量。 入站数据累积为：每天 500 MB x 60 天 = 30 GB。

在第61日，环境会显示最晚的数据，但会清除超过60天的最早的数据。 清除操作能够为流入的新数据腾出空间，因此能够继续浏览新数据。 如果用户想要将数据保留更长时间，可以通过添加更多的单位或推送更少的数据来增加环境的大小。  

### <a name="example-two"></a>示例二

假设某个环境的保留行为也配置为“继续传入和清除旧数据”。 在此示例中，“数据保留时间”设置为一个较小值：180 天。 “容量”设置为 S1 单位，包含 30 GB 总容量。 若要存储 180 天的数据，每天的传入量不能超过 0.166 GB (166 MB)。  

只要此环境的每日传入率超过 0.166 GB，就无法将数据存储 180 天，因为某些数据将被清除。 在繁忙的时段，可以考虑使用这种环境。 假设环境的传入率可能会增大到每日平均 0.189 GB。 在繁忙时段，将会保留大约 158 天的数据（30 GB/0.189 = 保留期 158.73 天）。 此时间小于所需的数据保留时间范围。

## <a name="pause-ingress"></a>暂停传入

- "**暂停入口**" 设置旨在确保在其保持期之前达到大小和计数限制时不清除数据。  
- **暂停入口**为用户提供了额外的时间，以便用户在由于破坏保留期而清除数据之前增加其环境的容量。
- 如果入口暂停超出了事件源的保留期，则它可帮助防止数据丢失，但可能会导致最近的数据丢失。
- 但是，一旦达到了环境的最大容量，环境就会暂停数据进入，直到发生以下附加操作：

   - 根据[如何缩放时序见解环境](time-series-insights-how-to-scale-your-environment.md)中所述，增加环境的最大容量以添加更多的扩展单元。
   - 已达到数据保持期，并清除数据，从而使环境低于其最大容量。

### <a name="example-three"></a>示例三

假设某个环境的保留行为配置为“暂停传入”。 在此示例中，“数据保留期”配置为 60 天。 **容量**设置为 S1 的三（3）个单位。 假设此环境的每日数据传入量为 2-GB。 在此环境中，一旦达到最大容量，传入就会暂停。

此时，环境会显示相同的数据集，直到进入 "正在运行" 或 "**继续**进入" 状态（这会清除旧数据以便为新数据腾出空间）。

当传入恢复时：

- 数据会根据事件源的接收顺序流入
- 根据事件的时间戳为事件编制索引，除非超出了针对事件源的保留策略。 有关事件源保留配置的详细信息，请参阅[事件中心常见问题解答](../event-hubs/event-hubs-faq.md)

> [!IMPORTANT]
> 应设置警报，以提供通知来帮助避免传入暂停。 由于 Azure 事件源的默认保留期为 1 天，因此可能丢失数据。 一旦传入暂停，除非采取其他措施，否则可能丢失最近的数据。 必须增大容量，或者将行为切换到“清除旧数据”才能避免潜在的数据丢失。

在受影响的事件中心，请考虑调整“消息保留”属性，以最大程度地减少在时序见解中发生传入暂停时丢失数据的情况。

[![事件中心消息保留。](media/time-series-insights-concepts-retention/event-hub-retention.png)](media/time-series-insights-concepts-retention/event-hub-retention.png#lightbox)

如果未在事件源（`timeStampPropertyName`）上配置任何属性，则时序见解默认为作为 X 轴的事件中心到达的时间戳。 如果 `timeStampPropertyName` 配置为其他对象，则在分析事件时，环境将在数据包中查找配置的 `timeStampPropertyName`。

了解[如何缩放时序见解环境](time-series-insights-how-to-scale-your-environment.md)以缩放环境以适应额外的容量或增加保留的长度。

## <a name="next-steps"></a>后续步骤

- 有关配置或更改数据保持期设置的信息，请参阅[在时序见解中配置保留期](time-series-insights-how-to-configure-retention.md)。

- 了解[Azure 时序见解中的缓解延迟](time-series-insights-environment-mitigate-latency.md)。
