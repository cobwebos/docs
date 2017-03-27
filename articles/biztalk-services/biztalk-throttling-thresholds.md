---
title: "了解 BizTalk 服务中的限制 | Microsoft Docs"
description: "了解 BizTalk 服务的限制阈值以及导致的运行时行为。 限制以内存使用量和消息数为基础。 MABS, WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: f6663cf2-cda4-4bac-855e-27d2ad5c4fa4
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 71f9dd111ebdbe885f33d162b2ea320dfaa167bb
ms.openlocfilehash: e14f42497d5ee0c89fe1fa0824431e2d82e6555a


---
# <a name="biztalk-services-throttling"></a>BizTalk 服务：限制
Azure BizTalk 服务基于两个条件实现服务限制：内存使用量和同时处理的消息数目。 本主题列出限制阈值，并介绍发生限制条件时的运行时行为。

## <a name="throttling-thresholds"></a>限制阈值
下表列出限制源和阈值：

|  | 说明 | 低阈值 | 高阈值 |
| --- | --- | --- | --- |
| 内存 |可用系统内存总量的百分比/页面文件字节数。 <p><p>可用的总页面文件字节数大约是系统内存的 2 倍。 |60% |70% |
| 消息处理 |同时处理的消息数 |40 * 内核数 |100 * 内核数 |

当达到高阈值时，Azure BizTalk 服务开始限制。 当达到低阈值时，将停止限制。 例如，服务使用 65% 的系统内存。 在此情况下，服务不进行限制。 服务开始使用 70% 的系统内存。 在此情况下，服务将进行限制并持续限制，直到服务使用 60%（低阈值）的系统内存。

Azure BizTalk 服务跟踪限制状态（正常状态与受限制状态)以及限制持续时间。

## <a name="runtime-behavior"></a>运行时行为
当 Azure BizTalk 服务进入限制状态时，将发生以下事件：

* 限制按每个角色实例发生。 例如：<br/>
  RoleInstanceA 正在进行限制。 RoleInstanceB 未进行限制。 在这种情况下，RoleInstanceB 中的消息将按预期方式处理。 RoleInstanceA 中的消息被丢弃并出现故障，同时显示以下错误：<br/><br/>
  **服务器正忙。请重试。**<br/><br/>
* 任何请求源都不会轮询或下载消息。 例如：<br/>
  管道从外部 FTP 源请求消息。 执行请求的角色实例会进入限制状态。 在此情况下，管道停止下载其他消息，直到角色实例停止限制。
* 系统将向客户端发送响应，以便客户端能够重新提交消息。
* 你必须等待，直到限制得以解决。 具体而言，你必须等待，直到达到低阈值。

## <a name="important-notes"></a>重要事项
* 无法禁用限制。
* 无法修改限制阈值。
* 限制是在系统范围内实施的。
* Azure SQL 数据库 Server 还具有内置限制。

## <a name="additional-azure-biztalk-services-topics"></a>其他 Azure BizTalk 服务主题
* [安装 Azure BizTalk 服务 SDK](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [教程：Azure BizTalk 服务](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [如何开始使用 Azure BizTalk 服务 SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Azure BizTalk 服务](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>另请参阅
* [BizTalk 服务：开发人员版、基本版、标准版和高级版图表](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [BizTalk 服务：使用 Azure 经典门户预配](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
* [BizTalk 服务：预配状态图表](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [Biztalk 服务：“仪表板”、“监视”和“缩放”选项卡](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [BizTalk 服务：备份和还原](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [BizTalk 服务：颁发者名称和颁发者密钥](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>




<!--HONumber=Nov16_HO3-->


