---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 8146489a913ce863cee7534331231a248a3ea7ac
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2019
ms.locfileid: "56890953"
---
1. 在 Mac 上访问 [Azure 门户]。 单击“所有服务” > “应用程序服务”> 刚创建的后端。 在移动应用设置中，选择首选语言：

    - Objective-C &ndash;“快速入门” > “iOS (Objective-C)”
    - Swift &ndash;“快速入门” > “iOS (Swift)”

    在“3. 定义操作组”下，配置客户端应用程序”下，单击“下载”。 这会下载预配置为连接到后端的完整 Xcode 项目。 使用 Xcode 打开项目。

1. 按“运行”  按钮生成项目，并在 iOS 模拟器中启动应用。

1. 在应用中，单击加号 (+) 图标，键入有意义的文本（例如 Complete the tutorial），然后单击“保存”按钮。 这会将一个 POST 请求发送到之前部署的 Azure 后端。 后端将请求中的数据插入到 TodoItem SQL 表中，并将有关新存储的项的信息返回给移动应用。 移动应用会在列表中显示此数据。

   ![在 iOS 上运行的快速入门应用](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure 门户]: https://portal.azure.com/
