---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 5c3277394350036b8863185e83b7cfcd4975be1c
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095171"
---
它允许你通过创建的后端服务从通知中心注册和取消注册。 

当指定了一个操作，并且应用位于前台时，将显示一个警报。 否则，通知中心将显示通知。

> [!NOTE]
> 通常，会在应用程序生命周期中的相应时间点（或在首次运行体验过程中）执行注册（和取消注册）操作，无需进行显式用户注册/取消注册输入。 但是，此示例将需要显式用户输入，以便能够更轻松地探索和测试此功能。
