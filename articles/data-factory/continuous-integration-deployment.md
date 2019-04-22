---
title: 在 Azure 数据工厂中进行持续集成和交付 | Microsoft Docs
description: 了解如何使用持续集成和交付将数据工厂管道从一个环境（开发、测试、生产）移到另一个环境。
services: data-factory
documentationcenter: ''
author: gauravmalhot
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/17/2019
ms.author: gamal
manager: craigg
ms.openlocfilehash: 2edd4e28a0dd67be3c06159bce2e968d681b7f70
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "58905250"
---
# <a name="continuous-integration-and-delivery-cicd-in-azure-data-factory"></a>在 Azure 数据工厂中进行持续集成和交付 (CI/CD)

持续集成是这样一种做法：自动地尽早测试对代码库所做的每项更改。 在测试之后进行的持续交付可将更改推送到过渡或生产系统，而测试发生在持续集成期间。

对于 Azure 数据工厂，持续集成和交付意味着将数据工厂管道从一个环境（开发、测试、生产）移到另一个环境。 若要进行持续集成和交付，可以将数据工厂 UI 集成与 Azure 资源管理器模板配合使用。 选择“ARM 模板”选项时，数据工厂 UI 可以生成资源管理器模板。 选择“导出 ARM 模板”时，门户会为数据工厂生成资源管理器模板，并生成一个包含所有连接字符串和其他参数的配置文件。 然后，需为每个环境（开发、测试、生产）创建一个配置文件。 所有环境的主资源管理器模板文件始终相同。

有关此功能的 9 分钟介绍和演示，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-resource-manager-template-for-each-environment"></a>为每个环境创建资源管理器模板
选择“导出 ARM 模板”，在开发环境中导出数据工厂的资源管理器模板。

![](media/continuous-integration-deployment/continuous-integration-image1.png)

然后转到测试性数据工厂和生产性数据工厂，选择“导入 ARM 模板”。

![](media/continuous-integration-deployment/continuous-integration-image2.png)

此操作会将你转到 Azure 门户，可以在其中导入已导出的模板。 依次选择“在编辑器中生成自己的模板”、“加载文件”、生成的资源管理器模板。 提供设置，然后数据工厂和整个管道就会导入到生产环境中。

![](media/continuous-integration-deployment/continuous-integration-image3.png)

![](media/continuous-integration-deployment/continuous-integration-image4.png)

选择“加载文件”，以便选择导出的资源管理器模板并提供所有配置值（例如，链接服务）。

![](media/continuous-integration-deployment/continuous-integration-image5.png)

**连接字符串**。 可以在关于各个连接器的文章中找到创建连接字符串所需的信息。 例如，对于 Azure SQL 数据库，请参阅[使用 Azure 数据工厂向/从 Azure SQL 数据库复制数据](connector-azure-sql-database.md)。 若要验证正确的连接字符串（例如，针对链接的服务），还可以在数据工厂 UI 中打开资源的代码视图。 但是，在代码视图中，连接字符串的密码或帐户密钥部分已删除。 若要打开代码视图，请选择下面的屏幕截图中突出显示的图标。

![打开代码视图来查看连接字符串](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="continuous-integration-lifecycle"></a>持续集成生命周期
下面是持续集成和交付的整个生命周期。使用此类集成和交付的前提是在数据工厂 UI 中启用 Azure Repos Git 集成：

1.  使用 Azure Repos 设置开发数据工厂，以便所有开发人员都能创作数据工厂资源，例如管道、数据集，等等。

1.  然后，开发人员就可以修改管道之类的资源。 进行修改后，开发人员可以选择“调试”来查看在使用最新更改的情况下管道的运行情况。

1.  对所做的更改满意以后，开发人员可以创建一个拉取请求，将请求从其分支拉取到 master 分支（或 collaboration 分支），让同行来审核他们的更改。

1.  将更改置于 master 分支中以后，即可选择“发布”，将其发布到开发工厂。

1.  准备将更改提升到测试工厂和生产工厂时，团队可以将资源管理器模板从 master 分支导出，或者在 master 分支需要对实时开发数据工厂提供支持的情况下，从任何其他分支导出。

1.  可以使用不同的参数文件，将导出的资源管理器模板部署到测试工厂和生产工厂。

## <a name="automate-continuous-integration-with-azure-pipelines-releases"></a>使用 Azure Pipelines 发布自动完成持续集成

可以使用下面的步骤来设置 Azure Pipelines 发布，以便将数据工厂自动部署到多个环境。

![与 Azure Pipelines 的持续集成示意图](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>要求

-   一项使用  [*Azure 资源管理器服务终结点*](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm)链接到 Team Foundation Server 或 Azure Repos 的 Azure 订阅。

-   配置了 Azure Repos Git 集成的数据工厂。

-   一个包含机密的  [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 。

### <a name="set-up-an-azure-pipelines-release"></a>设置 Azure Pipelines 发布

1.  转到使用数据工厂配置的项目中的 Azure Repos 页。

1.  单击顶部菜单“Azure Pipelines”&gt;“发布”&gt;“创建发布定义”。

    ![](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  选择“空进程”模板。

1.  输入环境的名称。

1.  添加一个 Git 项目，选择使用数据工厂配置的存储库。 选择 `adf_publish` 作为使用最新默认版本的默认分支。

    ![](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  添加 Azure 资源管理器部署任务：

    a.  创建新的任务，然后搜索并添加“Azure 资源组部署”。

    b.  在部署任务中选择目标数据工厂对应的订阅、资源组和位置，然后根据需要提供凭据。

    c.  选择“创建或更新资源组”操作。

    d.  在“替代模板参数”字段旁边 选择“…”。 以浏览方式查找在门户中通过发布操作创建的资源管理器模板 (*ARMTemplateForFactory.json*)。 在 `adf_publish` 分支的文件夹 `<FactoryName>` 中查找该文件。

    e.  针对参数文件执行相同的操作。 选择正确的文件，具体取决于你是创建了副本，还是使用默认的 *ARMTemplateParametersForFactory.json* 文件。

    f.  在“替代模板参数”字段旁边 选择“…”，然后填充目标数据工厂的信息。 对于来自密钥保管库的凭据，请按以下格式使用机密的名称：假设机密的名称为 `cred1`，请输入 `"$(cred1)"`（使用引号）。

    ![](media/continuous-integration-deployment/continuous-integration-image9.png)

    g. 选择“增量”部署模式。

    > [!WARNING]
    > 如果选择“完成”部署模式，则可能会删除现有资源，包括资源管理器模板中未定义的目标资源组中的所有资源。

1.  保存发布管道。

1.  根据此发布管道创建新发布。

    ![](media/continuous-integration-deployment/continuous-integration-image10.png)

### <a name="optional---get-the-secrets-from-azure-key-vault"></a>可选 - 获取 Azure Key Vault 中的机密

如果有要传入 Azure 资源管理器模板的机密，我们建议将 Azure Key Vault 用于 Azure Pipelines 发布。

可以通过两种方式来处理机密：

1.  将机密添加到参数文件。 有关详细信息，请参阅[在部署过程中使用 Azure Key Vault 传递安全参数值](../azure-resource-manager/resource-manager-keyvault-parameter.md)。

    -   创建上传到 publish 分支的参数文件的副本，并使用以下格式设置需要从密钥保管库获取的参数的值：

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    -   使用此方法时，会自动从密钥保管库拉取机密。

    -   参数文件也需位于 publish 分支中。

1.  在上一部分中介绍的 Azure 资源管理器部署之前添加 [Azure Key Vault 任务](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault)：

    -   选择“任务”选项卡，创建新的任务，然后搜索并添加“Azure Key Vault”。

    -   在 Key Vault 任务中，选择在其中创建了密钥保管库的订阅，根据需要提供凭据，然后选择密钥保管库。

    ![](media/continuous-integration-deployment/continuous-integration-image8.png)

### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>向 Azure Pipelines 代理授权
Azure Key Vault 任务可能会失败并出现拒绝访问错误的 fIntegration 运行时时间。 请下载此发行版的日志，并使用此命令找到 `.ps1` 文件，以便向 Azure Pipelines 代理授权。 可以直接运行此命令，也可以从文件中复制主体 ID，然后在 Azure 门户中手动添加访问策略。 （“获取”和“列出”是所需的最小权限）。

### <a name="update-active-triggers"></a>更新活动触发器
如果尝试更新活动触发器，部署可能会失败。 若要更新活动触发器，需手动将其停止，在部署后再将其启动。 可以为此添加 Azure Powershell 任务，如以下示例所示：

1.  在此发布的“任务”选项卡中，搜索并添加“Azure Powershell”。

1.  选择“Azure 资源管理器”作为连接类型，然后选择订阅。

1.  选择“内联脚本”作为脚本类型，然后提供代码。 以下示例停止触发器：

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

    ![](media/continuous-integration-deployment/continuous-integration-image11.png)

可以在部署后按照类似的步骤并使用类似的代码（通过 `Start-AzDataFactoryV2Trigger` 函数）来重启触发器。

> [!IMPORTANT]
> 在持续集成和部署方案中，不同环境之间的集成运行时类型必须相同。 例如，如果在开发环境中有自承载集成运行时 (IR)，则在测试和生产等其他环境中同一 IR 的类型必须为自承载。 同样，如果跨多个阶段共享集成运行时，则必须在所有环境（如开发、测试和生产）中将集成运行时配置为“链接自承载”。

## <a name="sample-deployment-template"></a>示例部署模板

下面是一个可以在 Azure Pipelines 中导入的示例部署模板。

```json
{
    "source": 2,
    "id": 1,
    "revision": 51,
    "name": "Data Factory Prod Deployment",
    "description": null,
    "createdBy": {
        "displayName": "Sample User",
        "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "uniqueName": "sampleuser@microsoft.com",
        "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
    },
    "createdOn": "2018-03-01T22:57:25.660Z",
    "modifiedBy": {
        "displayName": "Sample User",
        "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "uniqueName": "sampleuser@microsoft.com",
        "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
    },
    "modifiedOn": "2018-03-14T17:58:11.643Z",
    "isDeleted": false,
    "path": "\\",
    "variables": {},
    "variableGroups": [],
    "environments": [{
        "id": 1,
        "name": "Prod",
        "rank": 1,
        "owner": {
            "displayName": "Sample User",
            "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "uniqueName": "sampleuser@microsoft.com",
            "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
        },
        "variables": {
            "factoryName": {
                "value": "sampleuserprod"
            }
        },
        "variableGroups": [],
        "preDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 1
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 1
            }
        },
        "deployStep": {
            "id": 2
        },
        "postDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 3
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 2
            }
        },
        "deployPhases": [{
            "deploymentInput": {
                "parallelExecution": {
                    "parallelExecutionType": "none"
                },
                "skipArtifactsDownload": false,
                "artifactsDownloadInput": {
                    "downloadInputs": []
                },
                "queueId": 19,
                "demands": [],
                "enableAccessToken": false,
                "timeoutInMinutes": 0,
                "jobCancelTimeoutInMinutes": 1,
                "condition": "succeeded()",
                "overrideInputs": {}
            },
            "rank": 1,
            "phaseType": 1,
            "name": "Run on agent",
            "workflowTasks": [{
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "param\n(\n    [parameter(Mandatory = $false)] [String] $rootFolder=\"C:\\Users\\sampleuser\\Downloads\\arm_template\",\n    [parameter(Mandatory = $false)] [String] $armTemplate=\"$rootFolder\\arm_template.json\",\n    [parameter(Mandatory = $false)] [String] $armTemplateParameters=\"$rootFolder\\arm_template_parameters.json\",\n    [parameter(Mandatory = $false)] [String] $domain=\"microsoft.onmicrosoft.com\",\n    [parameter(Mandatory = $false)] [String] $TenantId=\"72f988bf-86f1-41af-91ab-2d7cd011db47\",\n    [parame",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $true",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": "5.*"
                }
            }, {
                "taskId": "1e244d32-2dd4-4165-96fb-b7441ca9331e",
                "version": "1.*",
                "name": "Azure Key Vault: sampleuservault",
                "refName": "secret1",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "KeyVaultName": "sampleuservault",
                    "SecretsFilter": "*"
                }
            }, {
                "taskId": "94a74903-f93f-4075-884f-dc11f34058b4",
                "version": "2.*",
                "name": "Azure Deployment:Create Or Update Resource Group action on sampleuser-datafactory",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "action": "Create Or Update Resource Group",
                    "resourceGroupName": "sampleuser-datafactory",
                    "location": "East US",
                    "templateLocation": "Linked artifact",
                    "csmFileLink": "",
                    "csmParametersFileLink": "",
                    "csmFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateForFactory.json",
                    "csmParametersFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateParametersForFactory.json",
                    "overrideParameters": "-factoryName \"$(factoryName)\" -linkedService1_connectionString \"$(linkedService1-connectionString)\" -linkedService2_connectionString \"$(linkedService2-connectionString)\"",
                    "deploymentMode": "Incremental",
                    "enableDeploymentPrerequisites": "None",
                    "deploymentGroupEndpoint": "",
                    "project": "",
                    "deploymentGroupName": "",
                    "copyAzureVMTags": "true",
                    "outputVariable": "",
                    "deploymentOutputs": ""
                }
            }, {
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "# You can write your azure powershell scripts inline here. \n# You can also pass predefined and custom variables to this script using arguments",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $false",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }]
        }],
        "environmentOptions": {
            "emailNotificationType": "OnlyOnFailure",
            "emailRecipients": "release.environment.owner;release.creator",
            "skipArtifactsDownload": false,
            "timeoutInMinutes": 0,
            "enableAccessToken": false,
            "publishDeploymentStatus": true,
            "badgeEnabled": false,
            "autoLinkWorkItems": false
        },
        "demands": [],
        "conditions": [{
            "name": "ReleaseStarted",
            "conditionType": 1,
            "value": ""
        }],
        "executionPolicy": {
            "concurrencyCount": 1,
            "queueDepthCount": 0
        },
        "schedules": [],
        "retentionPolicy": {
            "daysToKeep": 30,
            "releasesToKeep": 3,
            "retainBuild": true
        },
        "processParameters": {
            "dataSourceBindings": [{
                "dataSourceName": "AzureRMWebAppNamesByType",
                "parameters": {
                    "WebAppKind": "$(WebAppKind)"
                },
                "endpointId": "$(ConnectedServiceName)",
                "target": "WebAppName"
            }]
        },
        "properties": {},
        "preDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "postDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "badgeUrl": "https://sampleuser.vsrm.visualstudio.com/_apis/public/Release/badge/19749ef3-2f42-49b5-9696-f28b49faebcb/1/1"
    }, {
        "id": 2,
        "name": "Staging",
        "rank": 2,
        "owner": {
            "displayName": "Sample User",
            "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "uniqueName": "sampleuser@microsoft.com",
            "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
        },
        "variables": {
            "factoryName": {
                "value": "sampleuserstaging"
            }
        },
        "variableGroups": [],
        "preDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 4
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 1
            }
        },
        "deployStep": {
            "id": 5
        },
        "postDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 6
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 2
            }
        },
        "deployPhases": [{
            "deploymentInput": {
                "parallelExecution": {
                    "parallelExecutionType": "none"
                },
                "skipArtifactsDownload": false,
                "artifactsDownloadInput": {
                    "downloadInputs": []
                },
                "queueId": 19,
                "demands": [],
                "enableAccessToken": false,
                "timeoutInMinutes": 0,
                "jobCancelTimeoutInMinutes": 1,
                "condition": "succeeded()",
                "overrideInputs": {}
            },
            "rank": 1,
            "phaseType": 1,
            "name": "Run on agent",
            "workflowTasks": [{
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "# You can write your azure powershell scripts inline here. \n# You can also pass predefined and custom variables to this script using arguments",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $true",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }, {
                "taskId": "1e244d32-2dd4-4165-96fb-b7441ca9331e",
                "version": "1.*",
                "name": "Azure Key Vault: sampleuservault",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "KeyVaultName": "sampleuservault",
                    "SecretsFilter": "*"
                }
            }, {
                "taskId": "94a74903-f93f-4075-884f-dc11f34058b4",
                "version": "2.*",
                "name": "Azure Deployment:Create Or Update Resource Group action on sampleuser-datafactory",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "action": "Create Or Update Resource Group",
                    "resourceGroupName": "sampleuser-datafactory",
                    "location": "East US",
                    "templateLocation": "Linked artifact",
                    "csmFileLink": "",
                    "csmParametersFileLink": "",
                    "csmFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateForFactory.json",
                    "csmParametersFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateParametersForFactory.json",
                    "overrideParameters": "-factoryName \"$(factoryName)\" -linkedService1_connectionString \"$(linkedService1-connectionString)\" -linkedService2_connectionString \"$(linkedService2-connectionString)\"",
                    "deploymentMode": "Incremental",
                    "enableDeploymentPrerequisites": "None",
                    "deploymentGroupEndpoint": "",
                    "project": "",
                    "deploymentGroupName": "",
                    "copyAzureVMTags": "true",
                    "outputVariable": "",
                    "deploymentOutputs": ""
                }
            }, {
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "16a37943-8b58-4c2f-a3d6-052d6f032a07",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "param(\n$x,\n$y,\n$z)\nwrite-host \"----------\"\nwrite-host $x\nwrite-host $y\nwrite-host $z | ConvertTo-SecureString\nwrite-host \"----------\"",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $false",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }]
        }],
        "environmentOptions": {
            "emailNotificationType": "OnlyOnFailure",
            "emailRecipients": "release.environment.owner;release.creator",
            "skipArtifactsDownload": false,
            "timeoutInMinutes": 0,
            "enableAccessToken": false,
            "publishDeploymentStatus": true,
            "badgeEnabled": false,
            "autoLinkWorkItems": false
        },
        "demands": [],
        "conditions": [{
            "name": "ReleaseStarted",
            "conditionType": 1,
            "value": ""
        }],
        "executionPolicy": {
            "concurrencyCount": 1,
            "queueDepthCount": 0
        },
        "schedules": [],
        "retentionPolicy": {
            "daysToKeep": 30,
            "releasesToKeep": 3,
            "retainBuild": true
        },
        "processParameters": {
            "dataSourceBindings": [{
                "dataSourceName": "AzureRMWebAppNamesByType",
                "parameters": {
                    "WebAppKind": "$(WebAppKind)"
                },
                "endpointId": "$(ConnectedServiceName)",
                "target": "WebAppName"
            }]
        },
        "properties": {},
        "preDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "postDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "badgeUrl": "https://sampleuser.vsrm.visualstudio.com/_apis/public/Release/badge/19749ef3-2f42-49b5-9696-f28b49faebcb/1/2"
    }],
    "artifacts": [{
        "sourceId": "19749ef3-2f42-49b5-9696-f28b49faebcb:a6c88f30-5e1f-4de8-b24d-279bb209d85f",
        "type": "Git",
        "alias": "Dev",
        "definitionReference": {
            "branches": {
                "id": "adf_publish",
                "name": "adf_publish"
            },
            "checkoutSubmodules": {
                "id": "",
                "name": ""
            },
            "defaultVersionSpecific": {
                "id": "",
                "name": ""
            },
            "defaultVersionType": {
                "id": "latestFromBranchType",
                "name": "Latest from default branch"
            },
            "definition": {
                "id": "a6c88f30-5e1f-4de8-b24d-279bb209d85f",
                "name": "Dev"
            },
            "fetchDepth": {
                "id": "",
                "name": ""
            },
            "gitLfsSupport": {
                "id": "",
                "name": ""
            },
            "project": {
                "id": "19749ef3-2f42-49b5-9696-f28b49faebcb",
                "name": "Prod"
            }
        },
        "isPrimary": true
    }],
    "triggers": [{
        "schedule": {
            "jobId": "b5ef09b6-8dfd-4b91-8b48-0709e3e67b2d",
            "timeZoneId": "UTC",
            "startHours": 3,
            "startMinutes": 0,
            "daysToRelease": 31
        },
        "triggerType": 2
    }],
    "releaseNameFormat": "Release-$(rev:r)",
    "url": "https://sampleuser.vsrm.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_apis/Release/definitions/1",
    "_links": {
        "self": {
            "href": "https://sampleuser.vsrm.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_apis/Release/definitions/1"
        },
        "web": {
            "href": "https://sampleuser.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_release?definitionId=1"
        }
    },
    "tags": [],
    "properties": {
        "DefinitionCreationSource": {
            "$type": "System.String",
            "$value": "ReleaseNew"
        }
    }
}
```

## <a name="sample-script-to-stop-and-restart-triggers-and-clean-up"></a>用来停止和重启触发器以及进行清理的示例脚本

下面是一个示例脚本，用于在部署之前停止触发器并随后重启触发器。 此脚本还包括用于删除已移除资源的代码。 若要安装最新版本的 Azure PowerShell，请参阅[使用 PowerShellGet 在 Windows 上安装 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)。

```powershell
param
(
    [parameter(Mandatory = $false)] [String] $rootFolder,
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
$triggersTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNames = $triggersTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$activeTriggerNames = $triggersTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$deletedtriggers = $triggersADF | Where-Object { $triggerNames -notcontains $_.Name }
$triggerstostop = $triggerNames | where { ($triggersADF | Select-Object name).name -contains $_ }

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers"
    $triggerstostop | ForEach-Object { 
        Write-host "Disabling trigger " $_
        Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-AzDataFactoryV2LinkedService -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $deletedtriggers | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start Active triggers - After cleanup efforts
    Write-Host "Starting active triggers"
    $activeTriggerNames | ForEach-Object { 
        Write-host "Enabling trigger " $_
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>将自定义参数用于资源管理器模板

如果处于 GIT 模式下时，您可以在 Resource Manager 模板中的模板和已硬编码的属性设置将参数化的属性重写默认属性。 你可能想要重写这些方案中的默认参数化模板：

* 使用自动化的 CI/CD 和你想要在资源管理器部署过程中更改某些属性，但属性在默认情况下不参数化。
* 您的工厂是很大的默认资源管理器模板无效，因为它有多个参数 (256) 允许的最大。

上述情况下，若要重写默认的参数化模板，创建名为的文件 *arm 模板参数 definition.json* 存储库的根文件夹中。 必须完全匹配的文件的名称。 数据工厂会尝试读取此文件，从目前正在使用 Azure 数据工厂门户中的任何一个分支，而不只是从协作分支。 可以创建或编辑其中使用可测试所做的更改将文件从一个私有分支**导出 ARM 模板**在 UI 中。 然后，可以将文件合并到协作分支。 如果未找到文件，则使用默认模板。


### <a name="syntax-of-a-custom-parameters-file"></a>自定义参数文件的语法

以下是一些指导原则创作的自定义参数文件时使用。 该文件由每个实体类型的部分： 触发器、 管道、 链接服务、 数据集、 integrationruntime，等等。
* 输入下的相关实体类型的属性路径。
* 属性名称设置为\*'，指示您希望参数化下它 （仅到第一个级别，不以递归方式） 的所有属性。 你还可以提供任何例外情况。
* 将属性的值设置为字符串时，表示你希望参数化该属性。 使用格式 `<action>:<name>:<stype>`。
   *  `<action>` 可以是下列字符之一：
      * `=` 方法将当前值保留为参数的默认值。
      * `-` 表示不保留参数的默认值。
      * `|` 是一种特殊情况的连接字符串或密钥从 Azure 密钥保管库机密。
   * `<name>` 为参数的名称。 如果为空，它将属性的名称。 如果值开头`-`字符，缩短名称。 例如，`AzureStorage1_properties_typeProperties_connectionString`将缩写为`AzureStorage1_connectionString`。
   * `<stype>` 是参数的类型。 如果 `<stype>` 是保留为空，默认类型是`string`。 支持的值： `string`， `bool`， `number`， `object`，和`securestring`。
* 当您在定义文件中指定数组时，则指示在模板中的匹配属性数组。 数据工厂循环访问数组中的所有对象使用的集成运行时对象的数组中指定的定义。 第二个对象（一个字符串）成为属性的名称，这用作每次遍历的参数的名称。
* 不能有一个特定的资源实例的定义。 任何定义适用于该类型的所有资源。
* 默认情况下，所有的安全字符串，例如密钥保管库机密和安全字符串，例如连接字符串、 密钥和令牌，将参数化。
 
## <a name="sample-parameterization-template"></a>示例参数化模板

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::number",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:number",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```

### <a name="explanation"></a>说明:

#### <a name="pipelines"></a>管道
    
* 路径的活动/typeProperties/waitTimeInSeconds 中的任何属性已参数化。 这意味着，具有一个名为的代码级别属性的管道中的任何活动`waitTimeInSeconds`(例如，`Wait`活动) 为数字，使用默认名称参数化。 但是，它不会在资源管理器模板中具有默认值。 它将在资源管理器部署期间是必需的输入。
* 同样，属性，称为`headers`(例如，在`Web`活动) 使用类型参数化`object`(JObject)。 它具有默认值，这是与的值相同源工厂。

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* 仅属性和所有属性，在路径下的`typeProperties`被参数化，使用其各自的默认值。 例如，截至今天的架构中，有两个属性下的**IntegrationRuntimes**类型属性：`computeProperties`和`ssisProperties`。 使用其各自的默认值和类型 （对象） 创建这两种属性类型。

#### <a name="triggers"></a>触发器

* 下`typeProperties`，两个属性进行参数化。 第一个参数是`maxConcurrency`，其指定为具有默认值和该类型将`string`。 它具有的默认参数名称`<entityName>_properties_typeProperties_maxConcurrency`。
* `recurrence`属性也参数化。 在其下的该级别的所有属性都指定为字符串，默认值和参数名称与参数化。 例外情况是`interval`属性，这是作为数字类型，参数化，使用参数名称带有后缀`<entityName>_properties_typeProperties_recurrence_triggerSuffix`。 同样，`freq`属性是一个字符串，作为一个字符串，参数化。 但是，`freq`属性没有默认值参数化。 名称是缩短，作为后缀。 例如，`<entityName>_freq`。

#### <a name="linkedservices"></a>LinkedServices

* 链接的服务是唯一的。 由于链接的服务和数据集可以属于多个类型，您可以提供特定于类型的自定义项。 例如，您可能会说所有链接类型的服务`AzureDataLakeStore`，特定的模板将应用，并对所有其他人 (通过\*) 将应用不同的模板。
* 在前面的示例中，`connectionString`属性将作为参数化`securestring`值，它不会具有默认值，其中包含带有后缀的缩短了的参数名称`connectionString`。
* 属性`secretAccessKey`，但是，碰巧`AzureKeyVaultSecret`(例如，`AmazonS3`链接服务)。 因此，自动参数化为 Azure 密钥保管库机密，并且从在源工厂中使用配置的密钥保管库提取。 您可以还参数化密钥保管库本身。

#### <a name="datasets"></a>数据集

* 即使特定于类型的自定义不适用于数据集，可以无显式提供配置\*-级别配置。 在前面的示例中下的所有数据集属性`typeProperties`参数化。

可以更改默认参数化模板，但这是当前的模板。 这会十分有用，如果您只需添加一个附加属性作为参数，而且还如果不想丢失现有的参数化并且需要重新创建它们。


```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }

        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

**示例**：添加一个 Databricks 交互式群集 ID （从 Databricks 链接服务） 的参数文件：

```
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```


## <a name="linked-resource-manager-templates"></a>链接的资源管理器模板

如果你为数据工厂设置了持续集成和部署 (CI/CD)，则可能会注意到，随着工厂变得越来越大，你会达到资源管理器模板限制，例如，资源管理器模板中的最大资源数或最大有效负载。 对于这类情况，除了为工厂生成完整的资源管理器模板外，数据工厂现在还会生成链接的资源管理器模板。 因此，这将工厂有效负载拆分为多个文件，以便不会达到上面提到的限制。

如果已配置了 Git，则会在 `adf_publish` 分支中在名为 `linkedTemplates` 的新文件夹下生成并保存链接的模板以及完整的资源管理器模板。

![链接的资源管理器模板文件夹](media/continuous-integration-deployment/linked-resource-manager-templates.png)

链接的资源管理器模板通常有一个主模板和一组链接到主模板的子模板。 父模板名为 `ArmTemplate_master.json`，子模板以如下模式命名：`ArmTemplate_0.json`、`ArmTemplate_1.json`，依此类推。 若要从使用完整的资源管理器模板转变为使用链接的模板，请更新 CI/CD 任务以指向 `ArmTemplate_master.json` 而非指向 `ArmTemplateForFactory.json`（即完整的资源管理器模板）。 资源管理器还要求将链接的模板上传到存储帐户，以便它们在部署期间可供 Azure 访问。 有关详细信息，请参阅[通过 VSTS 部署链接的 ARM 模板](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/)。

不要忘记在执行部署任务之前和之后在 CI/CD 管道中添加数据工厂脚本。

如果没有配置 Git，则可以通过**导出 ARM 模板**操作访问链接的模板。

## <a name="best-practices-for-cicd"></a>CI/CD 最佳做法

如果你使用数据工厂的 Git 集成，并且某个 CI/CD 管道会将更改从“开发”环境依次转移到“测试”和“生产”环境，则我们建议采用以下最佳做法：

-   **Git 集成**。 只需使用 Git 集成配置开发数据工厂。 对测试和生产做出的更改将通过 CI/CD 部署，不需要采用 Git 集成。

-   **数据工厂 CI/CD 脚本**。 在执行 CI/CD 中的资源管理器部署步骤之前，必须处理好停止触发器、执行不同类型的工厂清理等任务。 我们建议使用[此脚本](#sample-script-to-stop-and-restart-triggers-and-clean-up)，它可以处理所有这些任务。 使用相应的标志，在部署之前和之后各运行该脚本一次。

-   **集成运行时和共享**。 集成运行时是数据工厂中的基础结构组件之一，它们不经常会发生更改，在 CI/CD 的各个阶段都是类似的。 因此，数据工厂预期集成运行时在 CI/CD 的所有阶段具有相同的名称和类型。 若要在所有阶段共享集成运行时 - 例如，自承载集成运行时 - 共享方法之一是将自承载 IR 托管在仅用于包含共享的集成运行时的三元工厂中。 然后，可以在开发/测试/生产环境中将这些集成运行时用作链接的 IR 类型。

-   **Key Vault**。 使用建议的基于 Azure Key Vault 的链接服务时，可以通过为开发/测试/生产环境保留独立的 Key Vault，来进一步发挥 Key Vault 的优势。此外，可为每个 Key Vault 单独配置权限级别。 你可能不希望团队成员有权访问生产机密。 此外，我们建议在所有阶段保留相同的机密名称。 如果保留相同的名称，则无需在 CI/CD 中更改资源管理器模板，因为唯一需要更改的设置是 Key Vault 名称，这是一个资源管理器模板参数。

## <a name="unsupported-features"></a>不支持的功能

-   无法发布单个资源，因为数据工厂实体相互依赖。 例如，触发器依赖于管道，管道依赖于数据集和其他管道，等等。很难跟踪更改依赖项。 即使可以选择手动发布的资源，也可能只能选择整个更改集中的某个子集，导致发布后出现意外的行为。

-   无法从专用分支发布。

-   无法在 Bitbucket 上托管项目。
