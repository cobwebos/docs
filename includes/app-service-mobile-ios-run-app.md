---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: a5bde1a56bf6a1f5fca4b775c7c8e9bb7477eb6b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240273"
---
1. 打开下载的客户端项目使用 Xcode。

2. 转到[Azure 门户](https://portal.azure.com/)并导航到你创建的移动应用。 在`Overview`边栏选项卡，查找这是你的移动应用的公共终结点的 URL。 示例-将为我的应用程序名称"test123"sitename https://test123.azurewebsites.net。

3. 对于 Swift 项目中，打开文件`ToDoTableViewController.swift`此文件夹-ZUMOAPPNAME/ZUMOAPPNAME/ToDoTableViewController.swift 中。 应用程序名称是`ZUMOAPPNAME`。

4. 在中`viewDidLoad()`方法中，替换`ZUMOAPPURL`参数具有更高版本的公共终结点。

    `let client = MSClient(applicationURLString: "ZUMOAPPURL")`

    将成为
    
    `let client = MSClient(applicationURLString: "https://test123.azurewebsites.net")`
    
5. 对于 Objective C 项目中，打开文件`QSTodoService.m`此文件夹-ZUMOAPPNAME/ZUMOAPPNAME 中。 应用程序名称是`ZUMOAPPNAME`。

6. 在中`init`方法中，替换`ZUMOAPPURL`参数具有更高版本的公共终结点。

    `self.client = [MSClient clientWithApplicationURLString:@"ZUMOAPPURL"];`

    将成为
    
    `self.client = [MSClient clientWithApplicationURLString:@"https://test123.azurewebsites.net"];`

7. 按“运行”  按钮生成项目，并在 iOS 模拟器中启动应用。

8. 在应用中，  单击加号 (+) 图标，键入有意义的文本（例如 Complete the tutorial）  ，然后单击“保存”按钮。 这会将一个 POST 请求发送到之前部署的 Azure 后端。 后端将请求中的数据插入到 TodoItem SQL 表中，并将有关新存储的项的信息返回给移动应用。 移动应用会在列表中显示此数据。

   ![在 iOS 上运行的快速入门应用](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure portal]: https://portal.azure.com/
