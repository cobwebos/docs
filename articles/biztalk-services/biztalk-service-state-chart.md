---
title: 在 BizTalk 服务中不同状态下可执行的任务 | Microsoft Docs
description: 不同 MABS 状态下允许执行的操作：停止、启动、重启、挂起、恢复、删除、缩放、更新配置和备份
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: aea738f3-ec76-4099-a41b-e17fea9e252f
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2016
ms.author: mandia
ms.openlocfilehash: 05470e75fc7b46603c8fce3a98c66ac6a24758a8
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2017
ms.locfileid: "24102736"
---
# <a name="what-you-can-and-cant-do-using-the-biztalk-service-state"></a>使用 BizTalk 服务状态可以和不可以执行的操作

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

根据 BizTalk 服务的当前状态，有一些操作不一定能够在 BizTalk 服务上完成。

例如，预配新的 BizTalk 服务。 在其成功完成后，BizTalk 服务处于 `active` 状态。 在活动状态下，可以停止、挂起和删除 BizTalk 服务。 如果停止 BizTalk 服务并且停止失败，则 BizTalk 服务将转到 `StopFailed` 状态。 在 `StopFailed` 状态下，可以重新启动 BizTalk 服务。 如果尝试执行某一不允许的操作，例如恢复，会发生以下错误：

`Operation not allowed`

## <a name="view-the-possible-states"></a>查看可能的状态

下表列出了在 BizTalk 服务处于某一特定状态时可执行的操作。 ✔ 意味着处于该状态时允许执行该操作。 空白条目表示处于该状态时不能执行的操作。

| 服务状态 | 开始 | 停止 | 重新启动 | 挂起 | 恢复 | 删除 | 缩放 | 更新 <br/> 配置 | 备份 |
| --- | --- | --- | --- | --- | --- | --- |--- | --- | --- |
| 活动 |  | ✔ | ✔ | ✔ |  | ✔ |✔ |✔ |✔ |
| 已禁用 |  |  |  |  |  | ✔ | |  |  | 
| 已挂起 |  |  |  |  | ✔ | ✔ | |  | ✔ |
| 已停止 | ✔ |  | ✔ |  |  | ✔ | |  | ✔ |
| 服务更新失败 |  |  |  |  |  | ✔ | |  |  | 
| 禁用失败 |  |  |  |  |  | ✔ | |  |  | 
| 启用失败 |  |  |  |  |  | ✔ | |  |  | 
| 启动失败 <br/> 停止失败 <br/> 重新启动失败 | ✔ | ✔ | ✔ |  |  | ✔ | | ✔ | |
| 挂起失败 <br/> 恢复失败|  |  |  | ✔ | ✔ | ✔ | |  |  | 
| 创建失败 <br/> 还原失败 |  |  |  |  |  | ✔ | |  |  | 
| 配置更新失败  |  |  | ✔ |  |  | ✔ | |✔ | |
| 缩放失败 |  |  |  |  |  | ✔ |✔ | |  |  | 



## <a name="see-also"></a>另请参阅
* [可在 BizTalk 服务中的“仪表板”、“监视”和“缩放”选项卡中执行的操作](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [使用 BizTalk 服务中的开发人员版、基本版、标准版和高级版可获取的内容](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [如何备份和还原 BizTalk 服务](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [BizTalk 服务中所述的限制](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
* [为 BizTalk 服务检索服务总线和访问控制颁发者名称以及颁发者密钥值](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
* [如何开始使用 Azure BizTalk 服务 SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)

