---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.openlocfilehash: 8d7731480b6239c572d39f52b6a0217d2ac48d25
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66240229"
---
1. 导航到客户端项目 (.sln) 中的解决方案文件并使用 Visual Studio 将其打开。

2. 在 Visual Studio 中，从启动箭头旁边的下拉列表中选择解决方案平台（Android、iOS 或 Windows）。 单击下拉列表中的绿色箭头，选择特定的部署设备或仿真程序。 可以使用默认 Android 平台和 Ripple 仿真程序。 更高级的教程（例如推送通知）要求选择支持的设备或仿真程序。

3. 打开此文件夹（ZUMOAPPNAME/app/src/main/java/com/example/zumoappname）中的文件 `ToDoActivity.java`。 应用程序名称为 `ZUMOAPPNAME`。

4. 转到 [Azure 门户](https://portal.azure.com/)，并导航到已创建的移动应用。 在 `Overview` 边栏选项卡上，查找作为移动应用公共终结点的 URL。 示例 - 我的应用名称“test123”的站点名将为 https://test123.azurewebsites.net 。

5. 转到 ZUMOAPPNAME/www/js/index.js 中的 `index.js` 文件，在 `onDeviceReady()` 方法中，将 `ZUMOAPPURL` 参数替换为上面的公共终结点。

    `client = new WindowsAzure.MobileServiceClient('ZUMOAPPURL');`
    
    变为
    
    `client = new WindowsAzure.MobileServiceClient('https://test123.azurewebsites.net');`
    
6. 若要生成并运行 Cordova 应用，请按 F5 或单击绿色箭头。 如果在仿真程序中看到要求访问网络的安全性对话，请接受。

7. 在设备或模拟器上启动应用后，在“输入新文本”中键入有意义的文本，例如“完成教程”，然后单击“添加”按钮    。

后端会将请求中的数据插入 SQL 数据库的 TodoItem 表中，并将新存储项的相关信息返回到移动应用。 移动应用会在列表中显示此数据。

可以针对其他平台重复步骤 3 至 5。