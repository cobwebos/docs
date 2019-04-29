---
ms.openlocfilehash: 975d6625f46a6c10417793764b28d0a529a5c0cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60921232"
---
### <a name="prerequisites"></a>必备组件
* 一个 [ProjectOnline](https://products.office.com/Project/project-online-with-project-for-office-365) 帐户 

可以在逻辑应用中使用 ProjectOnline 帐户之前，必须先授权该逻辑应用可以连接到 ProjectOnline 帐户。 好在，可以从 Azure 门户中的逻辑应用内轻松地执行此操作。 

下面介绍授权逻辑应用连接到 ProjectOnline 帐户的步骤：

1. 要创建与 ProjectOnline 的连接，请在逻辑应用设计器的下拉列表中选择“显示 Microsoft 托管的 API”，并在搜索框中输入“ProjectOnline”。 选择要使用的触发器或操作：  
   ![ProjectOnline 步骤 1](./media/connectors-create-api-projectonline/projectonline-1.png)
2. 如果以前尚未创建任何与 ProjectOnline 的连接，系统会提示提供 ProjectOnline 凭据。 这些凭据将用于授权逻辑应用连接到 ProjectOnline 帐户，并访问该帐户中的数据：  
   ![ProjectOnline 步骤 2](./media/connectors-create-api-projectonline/projectonline-2.png)
3. 提供 ProjectOnline 用户名和密码，对逻辑应用进行授权：  
   ![ProjectOnline 步骤 3](./media/connectors-create-api-projectonline/projectonline-3.png)   
4. 请注意，连接已创建，现在可以随时在逻辑应用中继续执行其他步骤：  
   ![ProjectOnline 步骤 4](./media/connectors-create-api-projectonline/projectonline-4.png)   

