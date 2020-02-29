---
title: 利用 Azure Pipelines 部署自定义策略
titleSuffix: Azure AD B2C
description: 了解如何使用 Azure DevOps Services 中 Azure Pipelines 在 CI/CD 管道中部署 Azure AD B2C 自定义策略。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b23b60ae49a4973fa04e6fa5f795f99536e32e7f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78188743"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>利用 Azure Pipelines 部署自定义策略

通过使用在[Azure Pipelines][devops-pipelines]中设置的持续集成和交付（CI/CD）管道，你可以在软件传递和代码控制自动化中包含 Azure AD B2C 自定义策略。 部署到不同的 Azure AD B2C 环境（例如开发、测试和生产环境）时，建议删除手动过程，并使用 Azure Pipelines 执行自动测试。

启用 Azure Pipelines 管理 Azure AD B2C 中的自定义策略需要执行三个主要步骤：

1. 在 Azure AD B2C 租户中创建 web 应用程序注册
1. 配置 Azure 存储库
1. 配置 Azure 管道

> [!IMPORTANT]
> 使用 Azure 管道管理 Azure AD B2C 自定义策略目前使用 Microsoft Graph API `/beta` 终结点上可用的**预览**操作。 不支持在生产应用程序中使用这些 API。 有关详细信息，请参阅[Microsoft Graph REST API beta 终结点引用](https://docs.microsoft.com/graph/api/overview?toc=./ref/toc.json&view=graph-rest-beta)。

## <a name="prerequisites"></a>必备条件

* 使用[B2C IEF 策略管理员](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator)角色在目录中为用户[Azure AD B2C 租户](tutorial-create-tenant.md)和凭据
* 已上传到租户的[自定义策略](custom-policy-get-started.md)
* 已在你的租户中注册[管理应用](microsoft-graph-get-started.md)，其 Microsoft Graph API 权限*策略。 TrustFramework*
* [Azure 管道](https://azure.microsoft.com/services/devops/pipelines/)和对[Azure DevOps Services 项目][devops-create-project]的访问

## <a name="client-credentials-grant-flow"></a>客户端凭据授予流

此处所述的方案使用 OAuth 2.0[客户端凭据授予流](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md)在 Azure Pipelines 和 Azure AD B2C 之间使用服务到服务的调用。 此授权流允许 Azure Pipelines （机密客户端）之类的 web 服务使用其自己的凭据，而不是在调用另一个 web 服务（在本例中 Microsoft Graph 为）时模拟用户进行身份验证。 Azure Pipelines 以非交互方式获取令牌，然后向 Microsoft Graph API 发出请求。

## <a name="register-an-application-for-management-tasks"></a>为管理任务注册应用程序

如[先决条件](#prerequisites)中所述，你需要一个应用程序注册，你的 PowerShell 脚本（由 Azure Pipelines 执行）可用于访问租户中的资源。

如果你已有一个用于自动化任务的应用程序注册，请确保已向其授予对应用注册的**API 权限**中的**Microsoft Graph** > **策略** > **TrustFramework**权限。

有关注册管理应用程序的说明，请参阅[使用 Microsoft Graph 管理 Azure AD B2C](microsoft-graph-get-started.md)。

## <a name="configure-an-azure-repo"></a>配置 Azure 存储库

注册管理应用程序后，可以为策略文件配置存储库。

1. 登录到 Azure DevOps Services 组织。
1. [创建新项目][devops-create-project]或选择现有项目。
1. 在项目中，导航到 "**存储库**"，然后选择 "**文件**" 页。 选择现有存储库，或为此练习创建一个。
1. 创建名为*B2CAssets*的文件夹。 将所需的占位符文件命名为*README.md*并**提交**文件。 以后可以根据需要删除此文件。
1. 将 Azure AD B2C 策略文件添加到*B2CAssets*文件夹。 这包括*trustframeworkbase.xml*、 *trustframeworkextensions.xml*、 *signuporsignin.xml*、 *profileedit.xml*、 *PasswordReset*和你已创建的任何其他策略等。 记录每个 Azure AD B2C 策略文件的文件名，以便在后面的步骤中使用（它们用作 PowerShell 脚本参数）。
1. 在存储库的根目录中创建一个名为 "*脚本*" 的文件夹，将占位符文件命名为 " *DeployToB2c*"。 此时不要提交文件，您将在后面的步骤中执行此操作。
1. 将以下 PowerShell 脚本粘贴到*DeployToB2c*中，然后**提交**该文件。 此脚本从 Azure AD 获取一个令牌，并调用 Microsoft Graph API，将*B2CAssets*文件夹中的策略上传到 Azure AD B2C 租户。

    ```PowerShell
    [Cmdletbinding()]
    Param(
        [Parameter(Mandatory = $true)][string]$ClientID,
        [Parameter(Mandatory = $true)][string]$ClientSecret,
        [Parameter(Mandatory = $true)][string]$TenantId,
        [Parameter(Mandatory = $true)][string]$PolicyId,
        [Parameter(Mandatory = $true)][string]$PathToFile
    )

    try {
        $body = @{grant_type = "client_credentials"; scope = "https://graph.microsoft.com/.default"; client_id = $ClientID; client_secret = $ClientSecret }

        $response = Invoke-RestMethod -Uri https://login.microsoftonline.com/$TenantId/oauth2/v2.0/token -Method Post -Body $body
        $token = $response.access_token

        $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
        $headers.Add("Content-Type", 'application/xml')
        $headers.Add("Authorization", 'Bearer ' + $token)

        $graphuri = 'https://graph.microsoft.com/beta/trustframework/policies/' + $PolicyId + '/$value'
        $policycontent = Get-Content $PathToFile
        $response = Invoke-RestMethod -Uri $graphuri -Method Put -Body $policycontent -Headers $headers

        Write-Host "Policy" $PolicyId "uploaded successfully."
    }
    catch {
        Write-Host "StatusCode:" $_.Exception.Response.StatusCode.value__

        $_

        $streamReader = [System.IO.StreamReader]::new($_.Exception.Response.GetResponseStream())
        $streamReader.BaseStream.Position = 0
        $streamReader.DiscardBufferedData()
        $errResp = $streamReader.ReadToEnd()
        $streamReader.Close()

        $ErrResp

        exit 1
    }

    exit 0
    ```

## <a name="configure-your-azure-pipeline"></a>配置 Azure 管道

当存储库已初始化并填充自定义策略文件后，便可以设置发布管道。

### <a name="create-pipeline"></a>创建管道

1. 登录到 Azure DevOps Services 组织，然后导航到你的项目。
1. 在项目中，选择 "**管道**" >  > **新管道**中**发布**。
1. 在 "**选择模板**" 下，选择 "**空作业**"。
1. 输入**阶段名称**（例如*DeployCustomPolicies*），然后关闭窗格。
1. 选择 "**添加项目**"，然后在 "**源类型**" 下选择 " **Azure 存储库**"。
    1. 选择包含用 PowerShell 脚本填充的*脚本*文件夹的源存储库。
    1. 选择**默认分支**。 如果在上一节中创建了新的存储库，则默认分支为*master*。
    1. 保留*默认分支的 "最新***版本" 默认版本**设置。
    1. 输入存储库的**源别名**。 例如， *policyRepo*。 不要在别名中包含任何空格。
1. 选择“添加”
1. 重命名管道，以反映其意图。 例如，*部署自定义策略管道*。
1. 选择 "**保存**" 以保存管道配置。

### <a name="configure-pipeline-variables"></a>配置管道变量

1. 选择 "**变量**" 选项卡。
1. 将以下变量添加到**管道变量**下，并按指定设置其值：

    | 名称 | 值 |
    | ---- | ----- |
    | `clientId` | 前面注册的应用程序的**应用程序（客户端） ID** 。 |
    | `clientSecret` | 先前创建的**客户端密码**的值。 <br /> 将变量类型更改为**机密**（选择锁定图标）。 |
    | `tenantId` | `your-b2c-tenant.onmicrosoft.com`，其中 *-b2c-租户*是 Azure AD B2C 租户的名称。 |

1. 选择 "**保存**" 以保存变量。

### <a name="add-pipeline-tasks"></a>添加管道任务

接下来，添加一个用于部署策略文件的任务。

1. 选择 "**任务**" 选项卡。
1. 选择 "**代理作业**"，然后选择加号（ **+** ）将任务添加到代理作业。
1. 搜索并选择 " **PowerShell**"。 不要选择 "Azure PowerShell"、"目标计算机上的 PowerShell" 或其他 PowerShell 条目。
1. 选择新添加的 " **PowerShell 脚本**任务"。
1. 为 PowerShell 脚本任务输入以下值：
    * **任务版本**： 2. *
    * **显示名称**：此任务应上传的策略的名称。 例如， *B2C_1A_TrustFrameworkBase*。
    * **类型**：文件路径
    * **脚本路径**：选择省略号（***...***），导航到 "*脚本*" 文件夹，然后选择 " *DeployToB2C* " 文件。
    * **形参**

        输入以下**参数**值。 将 `{alias-name}` 替换为在上一节中指定的别名。

        ```PowerShell
        # Before
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/{alias-name}/B2CAssets/TrustFrameworkBase.xml
        ```

        例如，如果指定的别名为*policyRepo*，则参数行应为：

        ```PowerShell
        # After
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/policyRepo/B2CAssets/TrustFrameworkBase.xml
        ```

1. 选择 "**保存**" 以保存代理作业。

刚添加的任务会将*一个*策略文件上传到 Azure AD B2C。 在继续操作之前，请手动触发作业（**创建发布**），以确保其在创建其他任务之前成功完成。

如果任务成功完成，则通过对每个自定义策略文件执行前面的步骤来添加部署任务。 修改每个策略的 `-PolicyId` 和 `-PathToFile` 参数值。

`PolicyId` 是在 TrustFrameworkPolicy 节点中的 XML 策略文件开始处找到的值。 例如，以下策略 XML 中的 `PolicyId` *B2C_1A_TrustFrameworkBase*：

```XML
<TrustFrameworkPolicy
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:xsd="http://www.w3.org/2001/XMLSchema"
xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
PolicySchemaVersion="0.3.0.0"
TenantId="contoso.onmicrosoft.com"
PolicyId= "B2C_1A_TrustFrameworkBase"
PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

运行代理并上传策略文件时，请确保按以下顺序上载它们：

1. *Trustframeworkbase.xml*
1. *Trustframeworkextensions.xml*
1. *Signuporsignin.xml*
1. *Profileedit.xml*
1. *PasswordReset*

标识体验框架强制执行此顺序，因为文件结构是在层次结构链上构建的。

## <a name="test-your-pipeline"></a>测试管道

测试发布管道：

1. 选择 "**管道**"，然后单击 "**释放**"。
1. 选择前面创建的管道，例如 " *DeployCustomPolicies*"。
1. 选择 "**创建发布**"，然后选择 "**创建**" 以对发布进行排队。

应该会看到一个通知横幅，指出发布已排入队列。 若要查看其状态，请在通知横幅中选择链接，或在 "**发布**" 选项卡上的列表中选择它。

## <a name="next-steps"></a>后续步骤

了解有关以下方面的详细信息：

* [使用客户端凭据的服务到服务调用](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)
* [Azure DevOps Services](https://docs.microsoft.com/azure/devops/user-guide/?view=azure-devops)

<!-- LINKS - External -->
[devops]: https://docs.microsoft.com/azure/devops/?view=azure-devops
[devops-create-project]:  https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops
[devops-pipelines]: https://docs.microsoft.com/azure/devops/pipelines
