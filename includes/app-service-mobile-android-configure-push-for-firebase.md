---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 51a75ee7bf87c38e3916bdbc8d85abcfb14dca8b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66140337"
---
1. 在[Azure 门户](https://portal.azure.com/)中，单击“浏览全部”   > “应用服务”  ，并单击移动应用后端。 在“设置”  下，单击“应用服务推送”  ，然后单击通知中心名称。
2. 转到“Google (GCM)”  ，输入上一步骤中从 Firebase 获取的“服务器密钥”  值，并单击“保存”  。

    ![在门户中设置 API 密钥](./media/app-service-mobile-android-configure-push/mobile-push-api-key.png)

移动应用后端现在已配置为使用 Firebase Cloud Messaging。 这样便可以使用通知中心向 Android 设备上运行的应用发送推送通知。
