---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: da3793c428c624ce3a224cbd7606ab26c4a50803
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66140248"
---
Azure 应用服务的移动应用功能使用 [Azure 通知中心]发送推送内容，因此用户需为移动应用配置通知中心。

1. 在 [Azure 门户]中，转到“应用服务”  ，并选择应用后端。 在“设置”  下，选择“推送”  。
2. 若要将通知中心资源添加到应用中，请选择“连接”  。 可以创建一个中心，也可以连接到一个现有的中心。

    ![配置中心](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

现在已将通知中心连接到移动应用后端项目。 稍后需对此通知中心进行配置，以便连接到将内容推送到设备的平台通知系统 (PNS)。

[Azure 门户]: https://portal.azure.com/
[Azure 通知中心]: https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/
