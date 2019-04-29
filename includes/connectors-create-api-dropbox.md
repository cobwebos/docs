---
ms.openlocfilehash: 3f4430631a664f81f53f9df1f46ebc27c635de36
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60860198"
---
### <a name="prerequisites"></a>必备组件
* 一个 [Dropbox](https://www.Dropbox.com/) 帐户 

可以在逻辑应用中使用 Dropbox 帐户之前，必须先授权该逻辑应用可以连接到 Dropbox 帐户。 好在，可以从 Azure 门户中的逻辑应用内轻松地执行此操作。 

下面介绍授权逻辑应用连接到 Dropbox 帐户的步骤：

1. 要创建与 Dropbox 的连接，请在逻辑应用设计器的下拉列表中选择“显示 Microsoft 托管的 API”，并在搜索框中输入“Dropbox”。 选择要使用的触发器或操作：  
   ![Dropbox 步骤 1](./media/connectors-create-api-dropbox/dropbox-1.png)
2. 如果以前尚未创建任何与 Dropbox 的连接，系统会提示提供 Dropbox 凭据。 这些凭据将用于授权逻辑应用连接到 Dropbox 帐户，并访问该帐户中的数据：  
   ![Dropbox 步骤 2](./media/connectors-create-api-dropbox/dropbox-2.png)
3. 提供 Dropbox 用户名和密码，对逻辑应用进行授权：  
   ![Dropbox 步骤 3](./media/connectors-create-api-dropbox/dropbox-3.png)   
4. 授权逻辑应用使用 Dropbox 帐户：  
   ![Dropbox 步骤 4](./media/connectors-create-api-dropbox/dropbox-4.png)
5. 请注意，连接已创建，现在可以随时在逻辑应用中继续执行其他步骤：  
   ![Dropbox 步骤 5](./media/connectors-create-api-dropbox/dropbox-5.png)   

