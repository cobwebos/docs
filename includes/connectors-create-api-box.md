---
ms.openlocfilehash: d1db175ecc2cf61de0debc15d198d6367aae8bdd
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119970"
---
### <a name="prerequisites"></a>必备组件
* 一个 [Box](http://box.com) 帐户  

可以在逻辑应用中使用 Box 帐户之前，必须先授权该逻辑应用可以连接到 Box 帐户。好在，可以从 Azure 门户中的逻辑应用内轻松地执行此操作。  

下面介绍授权逻辑应用连接到 Box 帐户的步骤：  

1. 要创建与 Box 的连接，请在逻辑应用设计器的下拉列表中选择“显示 Microsoft 托管的 API”，并在搜索框中输入“Box”。 选择要使用的触发器或操作：  
   ![Box 连接创建步骤](./media/connectors-create-api-box/box-1.png)  
2. 如果以前尚未创建任何与 Box 的连接，系统会提示提供 Box 凭据。 这些凭据将用于授权逻辑应用连接到 Box 帐户，并访问该帐户中的数据：  
   ![Box 连接创建步骤](./media/connectors-create-api-box/box-2.png)  
3. 提供 Box 用户名和密码，对逻辑应用进行授权：  
   ![Box 连接创建步骤](./media/connectors-create-api-box/box-3.png)  
4. 使我们可以连接到 Box：  
   ![Box 连接创建步骤](./media/connectors-create-api-box/box-4.png)  
5. 请注意，连接已创建，现在可以随时在逻辑应用中继续执行其他步骤：  
   ![Box 连接创建步骤](./media/connectors-create-api-box/box-5.png)  

