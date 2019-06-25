---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: b609a708a987194398c53bdf83f0d6e1f281808d
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173443"
---
默认情况下，可匿名调用移动应用后端中的 API。 接下来，需限制为仅可访问已验证的客户端。  

* **Node.js 后端（通过 Azure 门户）** ：  

    在移动应用设置中，单击“简易表”  并选择相应的表。 单击“更改权限”  ，为所有权限选择“仅限已验证的访问”  ，并单击“保存”  。
* **.NET 后端 (C#)** :  

    在服务器项目中，导航到“控制器” > “TodoItemController.cs”。   将 `[Authorize]` 属性添加到“TodoItemController”类，如下所示。  若要限制为仅可访问特定方法，还可只向这些方法应用此属性（而非类）。 重新发布服务器项目。

        [Authorize]
        public class TodoItemController : TableController<TodoItem>

* **Node.js 后端（通过 Node.js 代码）** ：  

    若要访问表时需验证身份，请向 Node.js 服务器脚本添加以下行：

        table.access = 'authenticated';

    有关更多详细信息，请参阅[如何：要求对表的访问进行身份验证](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth)。 若要了解如何从您的网站下载快速入门代码项目，请参阅[如何：使用 Git 将 Node.js 后端快速入门代码项目下载](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart)。
