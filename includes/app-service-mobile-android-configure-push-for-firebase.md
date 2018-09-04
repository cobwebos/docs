---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: bb03e4b5b04a0272d8fa9b032da5adb50878b620
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2018
ms.locfileid: "42809470"
---
1. 在[Azure 门户](https://portal.azure.com/)中，单击“浏览全部” > “应用服务”，并单击移动应用后端。 在“设置”下，单击“应用服务推送”，并单击通知中心名称。
2. 转到“Google (GCM)”，输入上一步骤中从 Firebase 获取的“服务器密钥”值，并单击“保存”。

    ![在门户中设置 API 密钥](./media/app-service-mobile-android-configure-push/mobile-push-api-key.png)

移动应用后端现在已配置为使用 Firebase Cloud Messaging。 这样便可通过使用通知中心将推送通知发送到 Android 设备上运行的应用。
