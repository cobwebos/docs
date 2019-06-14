---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/09/2019
ms.author: crdun
ms.openlocfilehash: 63c54f8af91b6b4a76ba49d5e6fc7b3cda9f5b98
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66240308"
---
1. 使用“导入项目（Eclipse ADT、Gradle 等）”打开使用“Android Studio”的项目。   请确保使用此导入选项以避免任何 JDK 错误。

2. 打开此文件夹（ZUMOAPPNAME/app/src/main/java/com/example/zumoappname）中的文件 `ToDoActivity.java`。 应用程序名称为 `ZUMOAPPNAME`。

3. 转到 [Azure 门户](https://portal.azure.com/)，并导航到已创建的移动应用。 在 `Overview` 边栏选项卡上，查找作为移动应用公共终结点的 URL。 示例 - 我的应用名称“test123”的站点名将为 https://test123.azurewebsites.net 。

4. 在 `onCreate()` 方法中，将 `ZUMOAPPURL` 参数替换为上面的公共终结点。
    
    `new MobileServiceClient("ZUMOAPPURL", this).withFilter(new ProgressFilter());` 
    
    变为
    
    `new MobileServiceClient("https://test123.azurewebsites.net", this).withFilter(new ProgressFilter());`
    
5. 按“运行‘应用’”  按钮以生成项目，并在 Android 模拟器中启动应用。

4. 在应用中键入有意义的文本（例如“完成教程”  ），并单击“添加”图标。 这会将一个 POST 请求发送到之前部署的 Azure 后端。 后端将请求中的数据插入到 TodoItem SQL 表中，并将有关新存储的项的信息返回给移动应用。 移动应用会在列表中显示此数据。
    ![Android 快速入门](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)