---
title: 使用 Azure 管道部署自定义策略
titleSuffix: Azure AD B2C
description: 了解如何在 CI/CD 管道中使用 Azure DevOps 服务中的 Azure 管道在 CI/CD 管道中部署 Azure AD B2C 自定义策略。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188743"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>使用 Azure 管道部署自定义策略

通过使用在[Azure 管道][devops-pipelines]中设置的连续集成和传递 （CI/CD） 管道，可以在软件交付和代码控制自动化中包括 Azure AD B2C 自定义策略。 当您部署到不同的 Azure AD B2C 环境（例如开发、测试和生产）时，我们建议您删除手动进程，并使用 Azure 管道执行自动测试。

要使 Azure 管道在 Azure AD B2C 中管理自定义策略，需要三个主要步骤：

1. 在 Azure AD B2C 租户中创建 Web 应用程序注册
1. 配置 Azure 回购
1. 配置 Azure 管道

> [!IMPORTANT]
> 使用 Azure 管道管理 Azure AD B2C**preview**自定义策略当前使用 Microsoft`/beta`图形 API 终结点上可用的预览操作。 不支持在生产应用程序中使用这些 API。 有关详细信息，请参阅 Microsoft[图形 REST API 测试版终结点引用](https://docs.microsoft.com/graph/api/overview?toc=./ref/toc.json&view=graph-rest-beta)。

## <a name="prerequisites"></a>先决条件

* [Azure AD B2C 租户](tutorial-create-tenant.md)，以及具有[B2C IEF 策略管理员](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator)角色的目录中用户的凭据
* 上载到租户的[自定义策略](custom-policy-get-started.md)
* 使用 Microsoft 图形 API 权限策略在租户中注册[的管理应用](microsoft-graph-get-started.md)*。ReadWrite.TrustFramework*
* [Azure 管道](https://azure.microsoft.com/services/devops/pipelines/)和对[Azure DevOps 服务项目][devops-create-project]的访问

## <a name="client-credentials-grant-flow"></a>客户端凭据授予流

此处描述的方案使用 Azure 管道和 Azure AD B2C 之间的服务到服务调用，使用 OAuth 2.0[客户端凭据授予流](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md)。 此授予流允许 Azure 管道（机密客户端）等 Web 服务使用自己的凭据，而不是在调用另一个 Web 服务（本例中为 Microsoft 图形 API）时模拟用户进行身份验证。 Azure 管道以非交互方式获取令牌，然后向 Microsoft 图形 API 发出请求。

## <a name="register-an-application-for-management-tasks"></a>注册管理任务的应用程序

如[先决条件](#prerequisites)中所述，您需要一个应用程序注册，即 PowerShell 脚本（由 Azure 管道执行）可用于访问租户中的资源。

如果您已有用于自动化任务的应用程序注册，请确保已被授予**Microsoft 图形** > **策略** > **策略.ReadWrite.TrustFramework**权限，属于应用注册的**API 权限**。

有关注册管理应用程序的说明，请参阅使用 Microsoft[图形管理 Azure AD B2C](microsoft-graph-get-started.md)。

## <a name="configure-an-azure-repo"></a>配置 Azure 回购

注册管理应用程序后，即可为策略文件配置存储库。

1. 登录到 Azure DevOps 服务组织。
1. [创建新项目][devops-create-project]或选择现有项目。
1. 在项目中，导航到 **"存储库"** 并选择"**文件**"页。 选择现有存储库或为此练习创建一个存储库。
1. 创建名为*B2CAssets 的*文件夹。 README.md命名所需的占位符*文件*并**提交**该文件。 如果您愿意，以后可以删除此文件。
1. 将 Azure AD B2C 策略文件添加到*B2CAssets*文件夹。 这包括*TrustFrameworkBase.xml* *、TrustFrameWork 扩展*.xml、SignUpOrSignin.xml 、*配置文件编辑.xml、PasswordReset.xml*以及您创建的任何其他策略。 *SignUpOrSignin.xml* *PasswordReset.xml* 记录每个 Azure AD B2C 策略文件的文件名，以便在后面的步骤中使用（它们用作 PowerShell 脚本参数）。
1. 在存储库的根目录中创建名为*脚本*的文件夹，命名占位符文件*DeployToB2c.ps1*。 此时不要提交文件，您将在后面的步骤中提交该文件。
1. 将以下 PowerShell 脚本粘贴到*DeployToB2c.ps1*中，然后**提交**文件。 该脚本从 Azure AD 获取令牌，并调用 Microsoft 图形 API 将*B2CAssets*文件夹中的策略上载到 Azure AD B2C 租户。

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

通过使用自定义策略文件初始化并填充存储库，即可设置发布管道。

### <a name="create-pipeline"></a>创建管道

1. 登录到 Azure DevOps 服务组织并导航到项目。
1. 在项目中，选择 **"** > 管道**释放** > **新管道**"。
1. 在 **"选择模板**"下，选择 **"空作业**"。
1. 输入**阶段名称**，例如 *"部署自定义策略*"，然后关闭窗格。
1. 选择 **"添加项目**"，并在 **"源类型**"下选择**Azure 存储库**。
    1. 选择包含使用 PowerShell 脚本填充的*脚本*文件夹的源存储库。
    1. 选择**默认分支**。 如果在上一节中创建了一个新存储库，则默认分支是*主*分支。
    1. *从默认分支保留"最新"的***默认版本**设置。
    1. 输入存储库的**源别名**。 例如，*策略 Repo*。 请勿在别名名称中包含任何空格。
1. 选择 **"添加"**
1. 重命名管道以反映其意图。 例如，*部署自定义策略管道*。
1. 选择 **"保存**"以保存管道配置。

### <a name="configure-pipeline-variables"></a>配置管道变量

1. 选择"**变量"** 选项卡。
1. 在**管道变量**下添加以下变量，并按照指定设置其值：

    | “属性” | “值” |
    | ---- | ----- |
    | `clientId` | 您之前注册的应用程序**的应用程序的应用程序（客户端）ID。** |
    | `clientSecret` | 您之前创建的**客户端机密**的值。 <br /> 将变量类型更改为**机密**（选择锁定图标）。 |
    | `tenantId` | `your-b2c-tenant.onmicrosoft.com`，其中*b2c 租户*是 Azure AD B2C 租户的名称。 |

1. 选择 **"保存"** 以保存变量。

### <a name="add-pipeline-tasks"></a>添加管道任务

接下来，添加任务以部署策略文件。

1. 选择"**任务**"选项卡。
1. 选择 **"代理"作业**，然后选择加号**+**（ ） 以将任务添加到"代理"作业。
1. 搜索并选择**PowerShell**。 不要选择"Azure PowerShell"、"目标计算机上的 PowerShell"或其他 PowerShell 条目。
1. 选择新添加**的 PowerShell 脚本**任务。
1. 输入 PowerShell 脚本任务的以下值：
    * **任务版本**： 2.*
    * **显示名称**：此任务应上载的策略的名称。 例如 *，B2C_1A_TrustFrameworkBase*。
    * **类型**： 文件路径
    * **脚本路径**：选择省略号 ***（...），*** 导航到*脚本*文件夹，然后选择*DeployToB2C.ps1*文件。
    * **参数：**

        输入参数**的**以下值。 替换为`{alias-name}`您在上一节中指定的别名。

        ```PowerShell
        # Before
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/{alias-name}/B2CAssets/TrustFrameworkBase.xml
        ```

        例如，如果您指定的别名是*策略 Repo，* 则参数行应为：

        ```PowerShell
        # After
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/policyRepo/B2CAssets/TrustFrameworkBase.xml
        ```

1. 选择 **"保存**"以保存代理作业。

刚刚添加的任务将*一个*策略文件上载到 Azure AD B2C。 在继续操作之前，手动触发作业 （**创建版本**），以确保在创建其他任务之前成功完成。

如果任务成功完成，则通过为每个自定义策略文件执行上述步骤来添加部署任务。 修改每个`-PolicyId`策略`-PathToFile`的 和 参数值。

是在`PolicyId`TrustFrameworkPolicy 节点中的 XML 策略文件开头找到的值。 例如，`PolicyId`以下策略 XML 中*B2C_1A_TrustFrameworkBase*：

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

运行代理并上载策略文件时，请确保按以下顺序上载它们：

1. *TrustFrameworkBase.xml*
1. *TrustFrameworkExtensions.xml*
1. *注册或Signin.xml*
1. *配置文件编辑.xml*
1. *密码重置.xml*

当文件结构构建在分层链上时，标识体验框架强制执行此顺序。

## <a name="test-your-pipeline"></a>测试管道

要测试发布管道，

1. 选择**管道**，然后**释放**。
1. 选择您之前创建的管道，例如*部署自定义策略*。
1. 选择 **"创建发布**"，然后选择 **"创建"** 以对发布进行排队。

您应该会看到一个通知横幅，该横幅显示发布已排队。 要查看其状态，请在通知横幅中选择链接，或在 **"发布"** 选项卡的列表中选择该链接。

## <a name="next-steps"></a>后续步骤

了解有关以下方面的详细信息：

* [使用客户端凭据的服务到服务调用](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)
* [Azure 开发人员服务](https://docs.microsoft.com/azure/devops/user-guide/?view=azure-devops)

<!-- LINKS - External -->
[devops]: https://docs.microsoft.com/azure/devops/?view=azure-devops
[devops-create-project]:  https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops
[devops-pipelines]: https://docs.microsoft.com/azure/devops/pipelines
