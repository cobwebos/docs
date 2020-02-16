---
title: 有关批量邀请 B2B 协作用户的教程 - Azure AD
description: 在本教程中，你将学习如何使用 PowerShell 和 CSV 文件向外部 Azure AD B2B 协作用户批量发送邀请。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 2/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: c429648adeb0c81799bff2dca1650de965395a60
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77166441"
---
# <a name="tutorial-bulk-invite-azure-ad-b2b-collaboration-users-preview"></a>教程：批量邀请 Azure AD B2B 协作用户（预览）

|     |
| --- |
| 本文介绍 Azure Active Directory 的公共预览版功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

> [!NOTE]
> 从 2019/12/22 起，批量邀请用户（预览）功能已暂时禁用。
> 目前尚不知道何时在 Azure 门户中重新启用此功能。 若要使用 PowerShell 批量邀请来宾用户，请参阅 [B2B 批量邀请教程](bulk-invite-powershell.md)或 [B2B 代码和 PowerShell 示例](code-samples.md)。

如果使用 Azure Active Directory (Azure AD) B2B 协作功能与外部合作伙伴进行协作，可同时邀请多名来宾用户加入你的组织。 本教程介绍如何使用 Azure 门户向外部用户批量发送邀请。 具体操作如下：

> [!div class="checklist"]
> * 通过“批量邀请用户(预览)”  准备一个包含用户信息和邀请首选项的逗号分隔值 (.csv) 文件
> * 将 .csv 文件上传到 Azure AD
> * 验证是否已将用户添加到目录中

如果没有 Azure Active Directory，请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

## <a name="prerequisites"></a>必备条件

需要两个或更多可向其发送邀请的测试电子邮件帐户。 这些帐户必须来自组织外部。 可使用任意类型的帐户，包括 gmail.com 或 outlook.com 地址等社交帐户。

## <a name="invite-guest-users-in-bulk"></a>批量邀请来宾用户

1. 使用组织中的用户管理员的帐户登录到 Azure 门户。
2. 在导航窗格中选择“Azure Active Directory”。 
3. 在“管理”下，选择“用户” > “批量邀请”。   
4. 在“批量邀请用户(预览)”  页上选择“下载”，获取一个有效的包含邀请属性的 .csv 文件。 

    ![批量邀请的下载按钮](media/tutorial-bulk-invite/bulk-invite-button.png)

5. 打开 .csv 文件，为每个来宾用户添加一行。 必需的值为：

   * **要邀请的电子邮件地址** - 会收到邀请的用户

   * **重定向 url** - 一个 URL，受邀请的用户在接受邀请后会被系统定向到该 URL

    ![CSV 文件的示例，其中已输入来宾用户](media/tutorial-bulk-invite/bulk-invite-csv.png)

   > [!NOTE]
   > 请勿在**自定义的邀请消息**中使用逗号，因为这样会妨碍系统对消息成功进行分析。

6. 保存文件。
7. 在“批量邀请用户(预览)”  页的“上传 csv 文件”下，浏览到该文件。  选择该 .csv 文件后，对其的验证就会开始。 
8. 验证文件内容后，会看到“文件上传成功”消息。  如果有错误，必须修正错误，然后才能提交作业。
9. 文件通过验证以后，请选择“提交”，开始用于添加邀请的 Azure 批量操作。  
10. 若要查看作业状态，请选择“单击此处查看每项操作的状态”。  也可在“活动”部分选择“批量操作结果(预览)”。   若要了解批量操作中每个行项的详细信息，请选择“成功数”、“失败数”或“请求总数”列下的值。    如果失败，则会列出失败原因。

    ![批量操作结果的示例](media/tutorial-bulk-invite/bulk-operation-results.png)

11. 作业完成后，会显示一条通知，指出批量操作成功。

## <a name="verify-guest-users-in-the-directory"></a>验证目录中的来宾用户

通过 Azure 门户或 PowerShell 进行检查，看已添加的来宾用户是否存在于目录中。

### <a name="view-guest-users-in-the-azure-portal"></a>在 Azure 门户中查看来宾用户

1. 使用组织中的用户管理员的帐户登录到 Azure 门户。
2. 在导航窗格中选择“Azure Active Directory”。 
3. 在“管理”下，选择“用户”   。
4. 在“显示”下选择“仅来宾用户”，验证添加的来宾用户是否已列出。  

### <a name="view-guest-users-with-powershell"></a>使用 PowerShell 查看来宾用户

运行以下命令：

```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```

应会看到已列出受邀用户，其中用户主体名称 (UPN) 采用 emailaddress  #EXT#\@domain  的格式。 例如，lstokes_fabrikam.com#EXT#\@contoso.onmicrosoft.com，其中 contoso.onmicrosoft.com 是你从其发送邀请的组织  。

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
