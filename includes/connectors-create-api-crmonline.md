---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 53c683dacbb3b94e34bd8662743673c3a0490d94
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "58113885"
---
#### <a name="prerequisites"></a>必备组件
* Azure 帐户；可以创建[免费帐户](https://azure.microsoft.com/free)
* 一个 [Dynamics CRM Online](https://www.microsoft.com/en-us/dynamics/crm-free-trial-overview.aspx) 帐户 

在逻辑应用中使用 Dynamics 帐户之前，请先授权该逻辑应用可以连接到 CRM Online 帐户。 可以在 Azure 门户中的逻辑应用内轻松地执行此操作。 

按照以下步骤操作，即可授权逻辑应用连接到 CRM Online 帐户：

1. 创建逻辑应用。 在逻辑应用设计器的下拉列表中选择“显示 Microsoft 托管的 API”，并在搜索框中输入“dynamics”。 选择其中一个触发器或操作：  
   ![](./media/connectors-create-api-crmonline/dynamics-triggers.png)
2. 如果以前尚未创建任何与 Dynamics 的连接，系统会提示使用 Dynamics 凭据进行登录：  
   ![](./media/connectors-create-api-crmonline/dynamics-signin.png)
3. 选择“登录”，并输入用户名和密码。 选择“登录”。 
   
    这些凭据将用于授权逻辑应用连接到你的 Dynamics 帐户，并访问该帐户中的数据。 
4. 请注意，连接已创建。 现在，继续在逻辑应用中执行其他步骤：  
   ![](./media/connectors-create-api-crmonline/dynamics-properties.png)

