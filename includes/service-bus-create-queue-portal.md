---
title: include 文件
description: include 文件
services: service-bus-messaging
author: sethmanheim
ms.service: service-bus-messaging
ms.topic: include
ms.date: 06/29/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: bad7e87cb194fb7d8846e7e675881658a1d33b1e
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37138154"
---
请确保已创建服务总线命名空间，如[此处][namespace-how-to]所示。

1. 登录到 [Azure 门户][azure-portal]。
2. 在门户左侧的导航窗格中，单击“服务总线”（如果未看到“服务总线”，请单击“所有服务”）。
3. 单击要在其中创建队列的命名空间。 在此示例中，它是“sbnstest1”。
   
    ![创建队列][createqueue1]
4. 在命名空间窗口中单击“队列”，然后在“队列”窗口中单击“+ 队列”。
   
    ![选择“队列”][createqueue2]
5. 输入队列**名称**，其他值则保留默认值。
   
    ![选择“新建”][createqueue3]
6. 在窗口底部，单击“创建”。

[createqueue1]: ./media/service-bus-create-queue-portal/create-queue1.png
[createqueue2]: ./media/service-bus-create-queue-portal/create-queue2.png
[createqueue3]: ./media/service-bus-create-queue-portal/create-queue3.png

[namespace-how-to]: ../articles/service-bus-messaging/service-bus-create-namespace-portal.md
[azure-portal]: https://portal.azure.com
