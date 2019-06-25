---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 70ca28fbcd6feea75788cd58677ca44080e47ae5
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67202775"
---
### <a name="prerequisites"></a>必备组件
* 一个 [Salesforce](https://salesforce.com) 帐户  

可以在逻辑应用中使用 Salesforce 帐户之前，必须先授权该逻辑应用可以连接到 Salesforce 帐户。好在，可以从 Azure 门户中的逻辑应用内轻松地执行此操作。  

下面介绍授权逻辑应用连接到 Salesforce 帐户的步骤：  

1. 要创建与 Salesforce 的连接，请在逻辑应用设计器的下拉列表中选择“显示 Microsoft 托管的 API”  ，并在搜索框中输入“Salesforce”  。 选择要使用的触发器或操作：  
   ![Salesforce 连接图像 1](./media/connectors-create-api-salesforce/salesforce-1.png)  
2. 如果以前尚未创建任何与 Salesforce 的连接，系统会提示提供 Salesforce 凭据。 这些凭据用于授权逻辑应用连接到 Salesforce 帐户，然后访问该帐户中的数据：  
   ![Salesforce 连接图像 2](./media/connectors-create-api-salesforce/salesforce-2.png)  
3. 提供 Salesforce 用户名和密码，对逻辑应用进行授权：  
   ![Salesforce 连接图像 3](./media/connectors-create-api-salesforce/salesforce-3.png)  
4. 使我们可以连接到 Salesforce：  
   ![Salesforce 连接图像 4](./media/connectors-create-api-salesforce/salesforce-4.png)  
5. 请注意，连接已创建，现在可以随时在逻辑应用中继续执行其他步骤：  
   ![Salesforce 连接图像 5](./media/connectors-create-api-salesforce/salesforce-5.png)  

