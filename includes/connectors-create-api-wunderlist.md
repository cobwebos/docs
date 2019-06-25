---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: ad543ca2cd92895b9042ba795591523f00feb70a
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67202702"
---
### <a name="prerequisites"></a>必备组件
* 一个 Wunderlist 帐户  

可以在逻辑应用中使用 Wunderlist 帐户之前，必须先授权该逻辑应用可以连接到 Wunderlist 帐户。 好在，可以从 Azure 门户中的逻辑应用内轻松地执行此操作。 

下面介绍授权逻辑应用连接到 Wunderlist 帐户的步骤：

1. 要创建与 Wunderlist 的连接，请在逻辑应用设计器的下拉列表中选择“显示 Microsoft 托管的 API”  ，并在搜索框中输入“Wunderlist”  。 选择要使用的触发器或操作：  
   ![](./media/connectors-create-api-wunderlist/wunderlist-0.png)
2. 如果以前尚未创建任何与 Wunderlist 的连接，系统会提示提供 Wunderlist 凭据。 这些凭据用于授权逻辑应用连接到 Wunderlist 帐户，然后访问该帐户中的数据：   
   ![](./media/connectors-create-api-wunderlist/wunderlist-1.png)  
3. 提供凭据，然后选择登录按钮  
   ![](./media/connectors-create-api-wunderlist/wunderlist-2.png)  
4. 然后会告知你，逻辑应用具有哪些用于操作 Wunderlist 帐户的权限。 如果你同意，则选择按钮来指示你的协议。 
   ![](./media/connectors-create-api-wunderlist/wunderlist-4.png)  
5. 最后，选择“授权”  按钮  
   ![](./media/connectors-create-api-wunderlist/wunderlist-5.png)  

