---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: d90ef1737f0107e5e323c1e90e3f9021efd4e4f7
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "58115903"
---
### <a name="prerequisites"></a>必备组件
* 一个 [SMTP](https://wikipedia.org/wiki/Simple_Mail_Transfer_Protocol) 帐户  

可以在逻辑应用中使用 SMTP 帐户之前，必须先授权该逻辑应用可以连接到 SMTP 帐户。好在，可以从 Azure 门户中的逻辑应用内轻松地执行此操作。  

下面介绍授权逻辑应用连接到 SMTP 帐户的步骤：  

1. 要创建与 SMTP 的连接，请在逻辑应用设计器的下拉列表中选择“显示 Microsoft 托管的 API”，并在搜索框中输入“SMTP”。 选择要使用的触发器或操作：  
   ![](./media/connectors-create-api-smtp/smtp-1.png)  
2. 如果以前尚未创建任何与 SMTP 的连接，系统会提示提供 SMTP 凭据。 这些凭据用于授权逻辑应用连接到 SMTP 帐户，然后访问该帐户中的数据：  
   ![](./media/connectors-create-api-smtp/smtp-2.png)  
3. 请注意，连接已创建，现在可以随时在逻辑应用中继续执行其他步骤：  
   ![](./media/connectors-create-api-smtp/smtp-3.png)  

