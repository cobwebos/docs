---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 07/21/2020
ms.openlocfilehash: 06a503e800309d8689735417931820d9f16a6e75
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87102483"
---
## <a name="prerequisites"></a>先决条件

* [OneDrive for](https://OneDrive.com) business 帐户 

在将你的 OneDrive for business 帐户用于逻辑应用之前，必须授权逻辑应用连接到 Azure 门户中的 OneDrive for business 帐户。

请按照以下步骤授权逻辑应用连接到你的 OneDrive for Business 帐户：  

1. 登录到 Azure 门户。 

1. 在 " **Azure 服务**" 下，选择 " **逻辑应用**"。 然后从列表中选择逻辑应用的名称。

1. 在逻辑应用的菜单中，选择 "**开发工具**" 下的 "**逻辑应用设计器**"。

1. 在逻辑应用设计器的下拉列表中，选择 " **显示 Microsoft 托管的 api** "，并在搜索框中输入 " *OneDrive for* business"。 选择要使用的触发器或操作：  

   ![逻辑应用设计器的屏幕截图，显示具有 OneDrive for business API 操作的重复触发器。](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-1.png)

2. 如果以前尚未创建任何与 OneDrive for business 的连接，请按照提示提供 OneDrive for business 凭据。 这些凭据用于授权逻辑应用访问 OneDrive for Business 帐户的数据：  

   ![逻辑应用设计器的屏幕截图，显示 OneDrive for business 的登录提示。](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-2.png)

3. 提供 OneDrive for business 用户名和密码以授权你的逻辑应用：  

   ![OneDrive for business 登录页的屏幕截图，显示登录提示。](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-3.png)   

4. 此连接现在列在步骤中。 选择 "保存"，然后继续创建逻辑应用。 

   ![逻辑应用设计器的屏幕截图，其中列出了 "已列出 OneDrive for business 连接"。](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-4.png)   
