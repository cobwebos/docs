---
title: 教程 - 配置 Azure Analysis Services 服务器管理员和用户角色教程课程 | Microsoft Docs
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 05/10/2018
ms.author: owend
ms.reviewer: owend
ms.openlocfilehash: fcedba0a5b8a9684216f181a6d16f86044edb961
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34651257"
---
# <a name="tutorial-configure-server-administrator-and-user-roles"></a>教程：配置服务器管理员和用户角色

 本教程介绍如何使用 SQL Server Management Studio (SSMS) 连接到 Azure 中的服务器，以便配置服务器管理员和模型数据库角色。 此外还介绍[表格模型脚本语言 (TMSL)](https://docs.microsoft.com/sql/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200)。 TMSL 是基于 JSON 的脚本语言，适用于 1200 和更高兼容级别的表格模型。 它可以用来自动执行许多表格建模任务。 TMSL 通常与 PowerShell 配合使用，但在本教程中，请在 SSMS 中使用 XMLA 查询编辑器。 使用本教程时，请完成以下任务： 
  
> [!div class="checklist"]
> * 从门户获取服务器名称
> * 使用 SSMS 连接到服务器
> * 将用户或组添加到服务器管理员角色 
> * 将用户或组添加到模型数据库管理员角色
> * 添加新的模型数据库角色并添加用户或组

若要详细了解 Azure Analysis Services 中的用户安全性，请参阅[身份验证和用户权限](../analysis-services-manage-users.md)。 

## <a name="prerequisites"></a>先决条件

- 订阅中的一个 Azure Active Directory。
- 已在订阅中创建 [Azure Analysis Services 服务器](../analysis-services-create-server.md)。
- 具有[服务器管理员](../analysis-services-server-admins.md)权限。
- 向服务器[添加 adventureworks 示例模型](../analysis-services-create-sample-model.md)。
- [安装最新版本的 SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)。

## <a name="log-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到[门户](https://portal.azure.com/)。

## <a name="get-server-name"></a>获取服务器名称
若要从 SSMS 连接到服务器，首先需要服务器名称。 可以从门户获取服务器名称。

在 **Azure 门户**中，单击“服务器”>“概述” > “服务器名称”，并复制服务器名称。
   
   ![在 Azure 中获取服务器名称](./media/analysis-services-tutorial-roles/aas-copy-server-name.png)

## <a name="connect-in-ssms"></a>在 SSMS 中连接

对于余下的任务，请使用 SSMS 连接到服务器并对其进行管理。

1. 在 SSMS >“对象资源管理器”中，单击“连接” > “Analysis Services”。

    ![连接](./media/analysis-services-tutorial-roles/aas-ssms-connect.png)

2. 在“连接到服务器”对话框的“服务器名称”中，粘贴从门户复制的服务器名称。 在“身份验证”中选择“具有 MFA 支持的 Active Directory 通用版”，输入用户帐户，然后按“连接”。
   
    ![在 SSMS 中连接](./media/analysis-services-tutorial-roles/aas-connect-ssms-auth.png)

    > [!TIP]
    > 建议选择“具有 MFA 支持的 Active Directory 通用版”。 这种身份验证类型支持[非交互式多重身份验证](../../sql-database/sql-database-ssms-mfa-authentication.md)。 

3. 在“对象资源管理器”中，以展开方式查看服务器对象。 右键单击即可查看服务器属性。
   
    ![在 SSMS 中连接](./media/analysis-services-tutorial-roles/aas-connect-ssms-objexp.png)

## <a name="add-a-user-account-to-the-server-administrator-role"></a>将用户帐户添加到服务器管理员角色

在此任务中，请将用户或组帐户从 Azure AD 添加到服务器管理员角色。

1. 在“对象资源管理器”中，右键单击服务器名称，然后单击“属性”。 
2. 在“Analysis Server 属性”窗口中，单击“安全性” > “添加”。
3. 在“选择用户或组”窗口的 Azure AD 中输入用户或组帐户，然后单击“添加”。 
   
     ![添加服务器管理员](./media/analysis-services-tutorial-roles/aas-add-server-admin.png)

4. 单击“确定”，关闭“Analysis Server 属性”。

    > [!TIP]
    > 也可在门户中使用“Analysis Services 管理员”来添加服务器管理员。 

## <a name="add-a-user-to-the-model-database-administrator-role"></a>将用户添加到模型数据库管理员角色

在此任务中，请将用户或组帐户添加到已存在于模型中的“Internet 销售管理员”角色。 此角色具有 adventureworks 示例模型数据库的完全控制（管理员）权限。 此任务在为你创建的脚本中使用 [CreateOrReplace](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/createorreplace-command-tmsl) TMSL 命令。

1. 在“对象资源管理器”中，展开“数据库” > “adventureworks” > “角色”。 
2. 右键单击“Internet 销售管理员”，然后单击“充当的脚本角色” > “CREATE OR REPLACE TO” > “新建查询编辑器窗口”。

    ![新建查询编辑器窗口](./media/analysis-services-tutorial-roles/aas-add-db-admin.png)

3. 在“XMLAQuery”中将“memberName:”的值更改为 Azure AD 中的某个用户或组帐户。 默认情况下，已经包括登录时使用的帐户；但是，你不需添加自己的帐户，因为你已经是服务器管理员。

    ![XMLA 查询中的 TMSL 脚本](./media/analysis-services-tutorial-roles/aas-add-db-admin-script.png)

4. 按 **F5** 执行脚本。


## <a name="add-a-new-model-database-role-and-add-a-user-or-group"></a>添加新的模型数据库角色并添加用户或组

在此任务中，请使用 TMSL 脚本中的 [Create](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/create-command-tmsl?view=sql-analysis-services-2017) 命令创建一个新的 Internet 销售全局角色，为该角色指定读取权限，然后从 Azure AD 添加用户或组帐户。

1. 在“对象资源管理器”中右键单击“adventureworks”，然后单击“新建查询” > “XMLA”。 
2. 将以下 TMSL 脚本复制并粘贴到查询编辑器中：

    ```JSON
    {
    "create": {
      "parentObject": {
        "database": "adventureworks",
       },
       "role": {
         "name": "Internet Sales Global",
         "description": "All users can query model data",
         "modelPermission": "read",
         "members": [
           {
             "memberName": "globalsales@adventureworks.com",
             "identityProvider": "AzureAD"
           }
         ]
       }
      }
    }
    ```

3. 将 **"memberName": \"globalsales@adventureworks.com\"** 对象值更改为 Azure AD 中的用户或组帐户。
4. 按 **F5** 执行脚本。

## <a name="verify-your-changes"></a>验证所做的更改

1. 在“对象资源管理器”中单击服务器名，然后单击“刷新”或按 **F5**。
2. 展开“数据库” > “adventureworks” > “角色”。 验证在前述任务中添加的用户帐户和新角色更改是否显示。   

    ![在对象资源管理器中验证](./media/analysis-services-tutorial-roles/aas-connect-ssms-verify.png)

## <a name="clean-up-resources"></a>清理资源

不再需要用户或组帐户和角色时，请将其删除。 为此，请通过“角色属性” > “成员身份”删除用户帐户，或者右键单击某个角色，然后单击“删除”。


## <a name="next-steps"></a>后续步骤
本教程介绍了如何连接到 Azure AS 服务器并浏览 SSMS 中的 adventureworks 示例模型数据库和属性。 此外还介绍了如何使用 SSMS 和 TMSL 脚本将用户或组添加到现有的和新的角色。 为服务器和示例模型数据库配置用户权限以后，你和其他用户即可使用客户端应用程序（例如 Power BI）连接到该服务器。 若要了解详细信息，请继续阅读下一个教程。 

> [!div class="nextstepaction"]
> [教程：使用 Power BI Desktop 进行连接](analysis-services-tutorial-pbid.md)

