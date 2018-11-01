---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 505eac0996129a17b6b68e8ab4ea2d4fc80fd473
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134339"
---
1. 访问 [Azure 门户]。 依次单击“浏览全部” > “移动应用”> 刚创建的后端。 在移动应用设置中，依次单击“快速启动” > “Android”。 在“配置客户端应用程序”下，单击“下载”。 这样可以为预配置为连接到后端的应用下载完整的 Android 项目。 
2. 使用“导入项目（Eclipse ADT、Gradle 等）”打开使用“Android Studio”的项目。 请确保使用此导入选项以避免任何 JDK 错误。
3. 按“运行‘应用’”  按钮以生成项目，并在 Android 模拟器中启动应用。
4. 在应用中键入有意义的文本（例如“完成教程”），并单击“添加”图标。 这会将一个 POST 请求发送到之前部署的 Azure 后端。 后端将请求中的数据插入到 TodoItem SQL 表中，并将有关新存储的项的信息返回给移动应用。 移动应用会在列表中显示此数据。 
   
    ![](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)

[Azure 门户]: https://portal.azure.com/
