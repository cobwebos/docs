---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: c86210208b9f747cbef1d9231528fa6cedbdb5d2
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2018
ms.locfileid: "42814917"
---
1. 右键单击 Windows 应用商店项目，单击“设为启动项目”，并按 F5 键运行 Windows 应用商店应用。

    该应用启动后，已注册该设备以接收推送通知。
2. 停止 Windows 应用商店应用并对 Windows Phone 应用商店应用重复上一步操作。

    此时，这两个设备会注册以接收推送通知。

3. 重新运行 Windows 应用商店应用，在“插入 TodoItem”中键入文本，并单击“保存”。

    请注意，完成插入后，Windows Store 和 Windows Phone 应用会从 WNS 收到一条推送通知。 即使在此应用未运行时，也在 Windows Phone 上显示此通知。

    ![](./media/app-service-mobile-windows-universal-test-push/mobile-quickstart-push5-wp8.png)
