---
ms.openlocfilehash: b06c57f50f759677770839ae06cb05e4de36f84c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "62103665"
---
### <a name="prerequisites"></a>必备组件
* 一个 [SendGrid](https://www.SendGrid.com/) 帐户 

可以在逻辑应用中使用 SendGrid 帐户之前，必须先授权该逻辑应用可以连接到 SendGrid 帐户。 好在，可以从 Azure 门户中的逻辑应用内轻松地执行此操作。 

下面介绍授权逻辑应用连接到 SendGrid 帐户的步骤：

1. 要创建与 SendGrid 的连接，请在逻辑应用设计器的下拉列表中选择“显示 Microsoft 托管的 API”，并在搜索框中输入“SendGrid”。 选择要使用的触发器或操作：  
   ![SendGrid 步骤 1](./media/connectors-create-api-sendgrid/sendgrid-1.png)
2. 如果以前尚未创建任何与 SendGrid 的连接，系统会提示提供 SendGrid 凭据。 这些凭据将用于授权逻辑应用连接到 SendGrid 帐户，并访问该帐户中的数据：  
   ![SendGrid 步骤 2](./media/connectors-create-api-sendgrid/sendgrid-2.png)
3. 请注意，连接已创建，现在可以随时在逻辑应用中继续执行其他步骤：  
   ![SendGrid 步骤 3](./media/connectors-create-api-sendgrid/sendgrid-3.png)   

