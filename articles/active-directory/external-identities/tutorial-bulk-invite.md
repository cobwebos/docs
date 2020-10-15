---
title: 有关批量邀请 B2B 协作用户的教程 - Azure AD
description: 在本教程中，你将学习如何使用 PowerShell 和 CSV 文件向外部 Azure AD B2B 协作用户批量发送邀请。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 05/07/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0f88b310bc00881e66ee8e8b5f2d40616d60315
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87906813"
---
# <a name="tutorial-bulk-invite-azure-ad-b2b-collaboration-users"></a>教程：批量邀请 Azure AD B2B 协作用户

如果使用 Azure Active Directory (Azure AD) B2B 协作功能与外部合作伙伴进行协作，可同时邀请多名来宾用户加入你的组织。 本教程介绍如何使用 Azure 门户向外部用户批量发送邀请。 具体操作如下：

> [!div class="checklist"]
> * 使用“批量邀请用户”准备一个包含用户信息和邀请首选项的逗号分隔值 (.csv) 文件
> * 将 .csv 文件上传到 Azure AD
> * 验证是否已将用户添加到目录中

如果没有 Azure Active Directory，请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="understand-the-csv-template"></a>了解 CSV 模板

下载并填写“批量上传”CSV 模板，该模板可帮助你成功批量邀请 Azure AD 来宾用户。 下载的 CSV 模板可能如下例所示：

![用于上传和调出的电子表格，说明了每一行和每一列的用途和值](media/tutorial-bulk-invite/understand-template.png)

### <a name="csv-template-structure"></a>CSV 模板结构

下载的 CSV 模板中的行如下所示：

- **版本号**：包含版本号的第一行必须包含在上传 CSV 中。
- **列标题**：列标题的格式为：&lt;项名称&gt; [PropertyName] &lt;必需或空白&gt;。 例如，`Email address to invite [inviteeEmail] Required`。 某些较旧版本的模板可能会略有不同。
- **示例行**：我们已经在模板中包含了一行示例，展示了每个列的可接受值。 你必须删除示例行并将其替换为你自己的项。

### <a name="additional-guidance"></a>其他指南

- 不得删除或修改上传模板的前两行，否则无法处理上传。
- 所需的列会先列出。
- 建议不要将新列添加到模板。 所添加的任何其他列都会被忽略，不进行处理。
- 建议尽可能频繁地下载 CSV 模板的最新版本。

## <a name="prerequisites"></a>先决条件

需要两个或更多可向其发送邀请的测试电子邮件帐户。 这些帐户必须来自组织外部。 可使用任意类型的帐户，包括 gmail.com 或 outlook.com 地址等社交帐户。

## <a name="invite-guest-users-in-bulk"></a>批量邀请来宾用户

1. 使用组织中的用户管理员的帐户登录到 Azure 门户。
2. 在导航窗格中选择“Azure Active Directory”。
3. 在“管理”下，选择“用户” > “批量邀请”。  
4. 在“批量邀请用户”页上，选择“下载”以获取一个包含邀请属性的有效 .csv 模板。

    ![批量邀请的下载按钮](media/tutorial-bulk-invite/bulk-invite-button.png)

5. 打开 .csv 模板，为每个来宾用户添加一行。 必需的值为：

   * **要邀请的电子邮件地址** - 会收到邀请的用户

   * **重定向 url** - 一个 URL，受邀请的用户在接受邀请后会被系统定向到该 URL

    ![CSV 文件的示例，其中已输入来宾用户](media/tutorial-bulk-invite/bulk-invite-csv.png)

   > [!NOTE]
   > 请勿在**自定义的邀请消息**中使用逗号，因为这样会妨碍系统对消息成功进行分析。

6. 保存文件。
7. 在“批量邀请用户”页中，在“上传 csv 文件”下，浏览找到该文件。 选择该 .csv 文件后，对其的验证就会开始。 
8. 验证文件内容后，会看到“文件上传成功”消息。 如果有错误，必须修正错误，然后才能提交作业。
9. 文件通过验证以后，请选择“提交”，开始用于添加邀请的 Azure 批量操作。 
10. 若要查看作业状态，请选择“单击此处查看每项操作的状态”。 也可在“活动”部分中选择“批量操作结果”。  若要了解批量操作中每个行项的详细信息，请选择“成功数”、“失败数”或“请求总数”列下的值。   如果失败，则会列出失败原因。

    ![批量操作结果的示例](media/tutorial-bulk-invite/bulk-operation-results.png)

11. 作业完成后，会显示一条通知，指出批量操作成功。

## <a name="verify-guest-users-in-the-directory"></a>验证目录中的来宾用户

通过 Azure 门户或 PowerShell 进行检查，看已添加的来宾用户是否存在于目录中。

### <a name="view-guest-users-in-the-azure-portal"></a>在 Azure 门户中查看来宾用户

1. 使用组织中的用户管理员的帐户登录到 Azure 门户。
2. 在导航窗格中选择“Azure Active Directory”。
3. 在“管理”下，选择“用户” 。
4. 在“显示”下选择“仅来宾用户”，验证添加的来宾用户是否已列出。 

### <a name="view-guest-users-with-powershell"></a>使用 PowerShell 查看来宾用户

运行以下命令：

```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```

应会看到已列出受邀用户，其中用户主体名称 (UPN) 采用 emailaddress#EXT#\@domain 的格式。 例如，lstokes_fabrikam.com#EXT#\@contoso.onmicrosoft.com，其中 contoso.onmicrosoft.com 是你从其发送邀请的组织。

## <a name="clean-up-resources"></a>清理资源

不再需要目录中的测试用户帐户时，可在 Azure 门户的“用户”页上将其删除，方法是：选择来宾用户旁边的复选框，然后选择“删除”。 

也可运行以下 PowerShell 命令来删除用户帐户：

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```

例如： `Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`

## <a name="next-steps"></a>后续步骤

在本教程中，你向组织外部的来宾用户批量发送了邀请。 接下来，将学习如何执行邀请兑换过程。

> [!div class="nextstepaction"]
> [了解 Azure AD B2B 协作邀请兑换过程](redemption-experience.md)
