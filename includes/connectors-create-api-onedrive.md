---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 8b5f72f18ee5bf9c1e4dbaaa87d6705f55b09bc5
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86524195"
---
## <a name="prerequisites"></a>必备条件

* Azure 帐户；可以创建[免费帐户](https://azure.microsoft.com/free)
* 一个 [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) 帐户 

可以在逻辑应用中使用 OneDrive 帐户之前，请先授权该逻辑应用可以连接到 OneDrive 帐户。  可以在 Azure 门户中的逻辑应用内轻松地执行此操作。 

按照以下步骤操作，即可授权逻辑应用连接到 OneDrive 帐户：

1. 创建逻辑应用。 在逻辑应用设计器的下拉列表中选择“显示 Microsoft 托管的 API”  ，并在搜索框中输入“onedrive”。 选择其中一个触发器或操作：  
   ![标题为 "显示 Microsoft 托管的 Api" 的对话框包含一个包含 "onedrive" 的搜索框。 下面是四个触发器的列表。 列表中的第一个选项是 "OneDrive-创建文件时"。](./media/connectors-create-api-onedrive/onedrive-1.png)
2. 如果以前尚未创建任何与 OneDrive 的连接，系统会提示使用 OneDrive 凭据进行登录：  
   ![标题为 "OneDrive-创建文件时" 的对话框有一个名为 "登录" 的按钮。](./media/connectors-create-api-onedrive/onedrive-2.png)
3. 选择“登录”****，并输入用户名和密码。 选择**登录**：  
   ![标题为 "登录" 的对话框指示你 "使用 Microsoft 帐户"。 它有两个标记为 "电子邮件或电话" 和 "密码" 的文本框，它还具有一个标记为 "使我保持登录" 的复选框，以及一个标记为 "登录" 的按钮。](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    这些凭据将用于授权逻辑应用连接到你的 OneDrive 帐户，并访问该帐户中的数据。 
4. 选择“是”**** 即可授权逻辑应用使用 OneDrive 帐户：  
   ![标题为 "让此应用访问你的信息？" 的对话框 要求提供以下四项的权限：1） "自动登录"，2） "访问你的电子邮件地址"，3） "访问你的信息的任何时间"，3） "访问 OneDrive 文件"。 提供权限的 "是" 按钮和 "否" 按钮拒绝此权限。 有一个链接可更改这些应用程序权限。](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. 请注意，连接已创建。 现在，继续在逻辑应用中执行其他步骤：  
   ![标题为 "创建文件时" 的对话框有一个标题为 "文件夹" 的文本框，其中包含关联的浏览按钮。](./media/connectors-create-api-onedrive/onedrive-5.png)

