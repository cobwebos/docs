---
title: Azure 数据工厂中的持续集成和交付
description: 了解如何使用持续集成和交付将数据工厂管道从一个环境（开发、测试、生产）移到另一个环境。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: jroth
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 6b091406b15db036007ba6a11049ee63ffe99cf0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91616884"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Azure 数据工厂中的持续集成和交付

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>概述

持续集成是这样一种做法：自动尽早测试对代码库所做的每项更改。 在测试之后进行的持续交付可将更改推送到过渡或生产系统，而测试发生在持续集成期间。

在 Azure 数据工厂中，持续集成和交付 (CI/CD) 是指将数据工厂管道从一个环境（开发、测试、生产）移到另一个环境。 Azure 数据工厂利用 [Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)存储各种 ADF 实体（管道、数据集、数据流等）的配置。 可通过两种建议的方式将数据工厂提升到另一个环境：

-    使用数据工厂与 [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines?view=azure-devops) 的集成进行自动化部署
-    使用数据工厂 UX 与 Azure 资源管理器的集成手动上传资源管理器模板。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>CI/CD 生命周期

下面是配置了 Azure Repos Git 的 Azure 数据工厂中的 CI/CD 生命周期示例概述。 若要详细了解如何配置 Git 存储库，请参阅 [Azure 数据工厂中的源代码管理](source-control.md)。

1.  开发数据工厂是使用 Azure Repos Git 创建和配置的。 所有开发人员都应有权创作数据工厂资源，例如管道和数据集。

1.  开发人员[创建功能分支](source-control.md#creating-feature-branches)以进行更改。 他们使用最近的更改来调试其管道运行。 若要详细了解如何调试管道运行，请参阅[使用 Azure 数据工厂进行迭代开发和调试](iterative-development-debugging.md)。

1.  对所做的更改满意以后，开发人员可以创建一个拉取请求，将请求从其功能分支拉取到主分支或协作分支，让同行来评审他们的更改。

1.  在拉取请求获得批准并已将更改合并到主分支后，更改将发布到开发工厂。

1.  当团队准备好将更改部署到测试或 UAT (用户验收测试) 工厂时，团队会转到其 Azure Pipelines 版本，并将所需版本的开发工厂部署到 UAT。 此部署作为 Azure Pipelines 任务的一部分发生，使用资源管理器模板参数来应用相应的配置。

1.  在测试工厂中验证更改后，将使用下一个管道发布任务部署到生产工厂。

> [!NOTE]
> 只有开发工厂才与 git 存储库相关联。 测试工厂和生产工厂不应有关联的 git 存储库，只能通过 Azure DevOps 管道或资源管理模板更新这些工厂。

下图突出显示了此生命周期的各个步骤。

![与 Azure Pipelines 的持续集成示意图](media/continuous-integration-deployment/continuous-integration-image12.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>使用 Azure Pipelines 发布自动进行持续集成

下面是有关设置 Azure Pipelines 发布以自动将数据工厂部署到多个环境的指导。

### <a name="requirements"></a>要求

-   一个已链接到 Visual Studio Team Foundation Server 或 Azure Repos 并使用 [Azure 资源管理器服务终结点](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager)的 Azure 订阅。

-   一个配置了 Azure Repos Git 集成的数据工厂。

-   一个 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)，其中包含每个环境的机密。

### <a name="set-up-an-azure-pipelines-release"></a>设置 Azure Pipelines 发布

1.  在 [Azure DevOps](https://dev.azure.com/) 中，打开配置了数据工厂的项目。

1.  在页面左侧选择“管道”，然后选择“发布”。 

    ![依次选择“管道”、“发布”](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  选择“新建管道”；如果有现有的管道，请依次选择“新建”、“新建发布管道”。  

1.  选择“空作业”模板。

    ![选择“空作业”](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  在“阶段名称”框中，输入环境的名称。

1.  选择“添加项目”，然后选择配置了开发数据工厂的 git 存储库。 选择“默认分支”对应的存储库的[发布分支](source-control.md#configure-publishing-settings)。 默认情况下，此发布分支为 `adf_publish`。 对于“默认版本”，请选择“默认分支中的最新版本”。 

    ![添加项目](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  添加 Azure 资源管理器部署任务：

    a.  在阶段视图中选择“查看阶段任务”。

    ![阶段视图](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  创建新任务。 搜索 " **ARM 模板部署**"，然后选择 " **添加**"。

    c.  在“部署任务”中，选择目标数据工厂的订阅、资源组和位置。 根据需要提供凭据。

    d.  在“操作”列表中，选择“创建或更新资源组”。 

    e.  选择“模板”框旁边的省略号按钮 ( **...** )。 浏览在配置的 git 存储库的发布分支中生成的 Azure 资源管理器模板。 在 adf_publish 分支的 <FactoryName> 文件夹中查找文件 `ARMTemplateForFactory.json`。

    f.  选择“模板参数”框旁边 的“…”，以便选择参数文件。 在 adf_publish 分支的 <FactoryName> 文件夹中查找文件 `ARMTemplateParametersForFactory.json`。

    g.  在“替代模板参数”框旁边 选择“...”，并输入目标数据工厂的所需参数值。 对于来自 Azure Key Vault 的凭据，请输入机密名称并以双引号将其括住。 例如，如果机密名称为 cred1，请为此值输入 **"$(cred1)"** 。

    h. 选择“增量”作为“部署模式”。 

    > [!WARNING]
    > 在完整部署模式下，资源组中存在但在新资源管理器模板中未指定的资源将被 **删除**。 有关详细信息，请参阅 [Azure 资源管理器部署模式](../azure-resource-manager/templates/deployment-modes.md)

    ![数据工厂生产部署](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  保存发布管道。

1. 若要触发发布，请选择“创建发布”。 若要自动创建发布，请参阅 [Azure DevOps 发布触发器](https://docs.microsoft.com/azure/devops/pipelines/release/triggers?view=azure-devops)

   ![选择“创建发布”](media/continuous-integration-deployment/continuous-integration-image10.png)

> [!IMPORTANT]
> 在 CI/CD 方案中，不同环境中的集成运行时 (IR) 类型必须相同。 例如，如果在开发环境中使用自承载 IR，则同一 IR 在测试和生产等其他环境中的类型也必须是自承载。 同样，如果跨多个阶段共享集成运行时，则必须在所有环境（例如开发、测试和生产）中将集成运行时配置为链接自承载。

### <a name="get-secrets-from-azure-key-vault"></a>获取 Azure Key Vault 中的机密

如果要在 Azure 资源管理器模板中传入机密，建议你将 Azure Key Vault 用于 Azure Pipelines 发布。

可通过两种方式来处理机密：

1.  将机密添加到参数文件。 有关详细信息，请参阅[在部署过程中使用 Azure Key Vault 传递安全参数值](../azure-resource-manager/templates/key-vault-parameter.md)。

    创建上传到发布分支的参数文件的副本。 使用以下格式设置要从 Key Vault 获取的参数的值：

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

    使用此方法时，会自动从密钥保管库拉取机密。

    参数文件也需位于 publish 分支中。

1. 在上一部分所述的 Azure 资源管理器部署任务的前面添加一个 [Azure Key Vault 任务](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault)：

    1.  在“任务”选项卡上创建一个新任务。 搜索并添加 **Azure Key Vault**。

    1.  在 Key Vault 任务中，选择在其中创建了密钥保管库的订阅。 根据需要提供凭据，然后选择该密钥保管库。

    ![添加 Key Vault 任务](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>向 Azure Pipelines 代理授权

如果未设置正确的权限，Azure Key Vault 任务可能失败并出现“拒绝访问”错误。 请下载发布日志，并找到 .ps1 文件，其中包含用于向 Azure Pipelines 代理授予权限的命令。 可以直接运行该命令。 也可以从文件中复制主体 ID，然后在 Azure 门户中手动添加访问策略。 `Get` 和 `List` 是所需的最低权限。

### <a name="updating-active-triggers"></a>更新活动触发器

如果尝试更新活动触发器，部署可能会失败。 若要更新活动触发器，需手动将其停止，并在部署后将其重启。 为此，可以使用 Azure PowerShell 任务：

1.  在此发布版本的“任务”选项卡上，添加一个 **Azure PowerShell** 任务。 选择任务版本 4.*。 

1.  选择工厂所在的订阅。

1.  选择“脚本文件路径”作为脚本类型。 这需要将 PowerShell 脚本保存到存储库中。 以下 PowerShell 脚本可用于停止触发器：

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

部署后，可以完成类似的步骤（使用 `Start-AzDataFactoryV2Trigger` 函数）来重启触发器。

数据工厂团队已在本文的末尾提供了[部署前和部署后示例脚本](#script)。 

## <a name="manually-promote-a-resource-manager-template-for-each-environment"></a>手动提升每个环境的资源管理器模板

1. 在“ARM 模板”列表中，选择“导出 ARM 模板”，以在开发环境中导出数据工厂的资源管理器模板。 

   ![导出资源管理器模板](media/continuous-integration-deployment/continuous-integration-image1.png)

1. 在测试和生产数据工厂中，选择“导入 ARM 模板”。 此操作会将你转到 Azure 门户，可以在其中导入已导出的模板。 选择“在编辑器中生成自己的模板”以打开资源管理器模板编辑器。

   ![生成自己的模板](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. 选择“加载文件”，然后选择生成的资源管理器模板。 此模板是在步骤 1 中导出的 .zip 文件中的 **arm_template.json** 文件。

   ![编辑模板](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. 在设置部分输入配置值，例如链接服务凭据。 完成后，选择“购买”以部署该资源管理器模板。

   ![设置部分](media/continuous-integration-deployment/continuous-integration-image5.png)

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>将自定义参数用于资源管理器模板

如果开发工厂具有关联的 git 存储库，则可替代通过发布或导出模板生成的资源管理器模板的默认资源管理器模板参数。 在以下情况下，你可能想要替代默认的参数化模板：

* 你使用自动化 CI/CD，想要在部署资源管理器期间更改某些属性，但这些属性在默认情况下不会参数化。
* 工厂太大，包含的参数数目超过了允许的最大数目 (256)，以致默认的资源管理器模板无效。

若要重写默认参数化模板，请在 "源代码管理" 部分中转到管理中心，并选择 " **参数化模板** "。 选择 " **编辑模板** "，打开参数化模板代码编辑器。 

![管理自定义参数](media/author-management-hub/management-hub-custom-parameters.png)

创建自定义参数化模板将在 git 分支的根文件夹中创建一个名为 **arm-template-parameters-definition.js** 的文件。 必须使用这个具体的文件名。

![自定义参数文件](media/continuous-integration-deployment/custom-parameters.png)

从协作分支发布时，数据工厂将读取此文件，并使用其配置来生成参数化的属性。 如果找不到文件，则使用默认模板。

导出资源管理器模板时，数据工厂从你当前正在处理的分支读取此文件，而不是协作分支。 可以在某个专用分支中创建或编辑文件，在此文件中，可以通过选择 UI 中的“导出 ARM 模板”来测试更改。 然后，可将该文件合并到协作分支。

> [!NOTE]
> 自定义参数化模板不会更改 ARM 模板参数限制 256。 它允许选择和减少参数化属性的数目。

### <a name="custom-parameter-syntax"></a>自定义参数语法

下面是创建自定义参数文件 **arm-template-parameters-definition.json** 时要遵循的一些准则。 对于下述每个实体类型，该文件都包含一个节：触发器、管道、链接服务、数据集、集成运行时和数据流。

* 输入相关实体类型下的属性路径。
* 将属性名称设置为 `*` 表示要将其下的所有属性参数化（仅参数化到第一个级别，而不是递归性的参数化）。 还可为此配置提供例外情况。
* 将属性值设置为字符串表示你希望参数化该属性。 使用格式 `<action>:<name>:<stype>`。
   *  `<action>` 可以是以下字符之一：
      * `=` 表示将当前值保留为参数的默认值。
      * `-` 表示不保留参数的默认值。
      * `|` 是 Azure Key Vault 中的机密的特例，用于连接字符串或密钥。
   * `<name>` 是参数的名称。 如果为空，将采用属性的名称。 如果值以 `-` 字符开头，则会简写名称。 例如，`AzureStorage1_properties_typeProperties_connectionString` 将简写为 `AzureStorage1_connectionString`。
   * `<stype>` 是参数的类型。 如果 `<stype>` 为空，则默认类型为 `string`。 支持的值：`string`、`bool`、`number`、`object` 和 `securestring`。
* 在定义文件中指定数组表示模板中匹配的属性是一个数组。 数据工厂使用数组的集成运行时对象中指定的定义来循环访问该数组中的所有对象。 第二个对象（一个字符串）成为属性的名称，这用作每次遍历的参数的名称。
* 定义不能特定于资源实例。 任何定义都将应用到该类型的所有资源。
* 默认情况下，会参数化 Key Vault 机密等安全字符串，以及连接字符串、密钥和令牌等安全字符串。
 
### <a name="sample-parameterization-template"></a>示例参数化模板

下面是参数化模板的示例：

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
下面说明如何构造上述模板并按资源类型划分该模板。

#### <a name="pipelines"></a>管道
    
* 路径 `activities/typeProperties/waitTimeInSeconds` 中的任何属性已参数化。 包含名为 `waitTimeInSeconds` 的代码级属性的管道中的任一活动（例如 `Wait` 活动）将以默认名称参数化为数字。 但是，该活动在资源管理器模板中没有默认值。 在部署资源管理器期间，必须输入其默认值。
* 同样，名为 `headers` 的属性（例如，在 `Web` 活动中的该属性）将以类型 `object` (JObject) 参数化。 该属性具有默认值，该值与源工厂的默认值相同。

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* 路径 `typeProperties` 下的所有属性以其各自的默认值参数化。 例如，`IntegrationRuntimes` 类型属性下有两个属性：`computeProperties` 和 `ssisProperties`。 这两个属性类型是使用各自的默认值和类型 (Object) 创建的。

#### <a name="triggers"></a>触发器

* 在 `typeProperties` 下，会参数化两个属性。 第一个属性是 `maxConcurrency`，该属性指定为具有默认值，类型为 `string`。 其默认参数名称为 `<entityName>_properties_typeProperties_maxConcurrency`。
* 另外还会参数化 `recurrence` 属性。 该属性级别下的所有属性均指定为参数化为字符串，并具有默认值和参数名称。 `interval` 属性例外，它将参数化为类型 `number`。 参数名称带有 `<entityName>_properties_typeProperties_recurrence_triggerSuffix` 后缀。 同样，`freq` 属性是字符串，将参数化为字符串。 但是，将参数化 `freq` 属性且不提供默认值。 名称将会简写并带有后缀。 例如，`<entityName>_freq` 。

#### <a name="linkedservices"></a>LinkedServices

* 链接服务是独特的。 由于链接服务和数据集的类型多种多样，你可以提供类型特定的自定义。 在此示例中，对于 `AzureDataLakeStore` 类型的所有链接服务，将应用特定的模板。 对于所有其他链接服务（通过 `*` 获取），将应用不同的模板。
* `connectionString` 属性将参数化为 `securestring` 值。 该属性没有默认值。 它使用带有 `connectionString` 后缀的简写参数名称。
* 属性 `secretAccessKey` 正好是 `AzureKeyVaultSecret`（例如，在 Amazon S3 链接服务中就是如此）。 它自动参数化为 Azure Key Vault 机密，可从配置的密钥保管库提取。 你还可以参数化密钥保管库本身。

#### <a name="datasets"></a>数据集

* 尽管类型特定的自定义可用于数据集，但你无需显式使用 \* 级配置即可提供配置。 在前面的示例中，`typeProperties` 下的所有数据集属性都将参数化。

### <a name="default-parameterization-template"></a>默认参数化模板

下面是当前的默认参数化模板。 如果只需添加少量的参数，直接编辑此模板也许是不错的想法，因为这样不会丢失现有的参数化结构。

```json
{
    "Microsoft.DataFactory/factories": {
        "properties": {
            "globalParameters": {
                "*": {
                    "value": "="
                }
            }
        },
        "location": "="
    },
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
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
                },
                "computeProperties": {
                    "dataFlowProperties": {
                        "externalComputeInfo": [{
                                "accessToken": "-::secureString"
                            }
                        ]
                    }
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
                    "host": "=",
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
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "functionAppUrl":"=",
                    "environmentUrl": "=",
                    "aadResourceId": "=",
                    "sasUri": "|:-sasUri:secureString",
                    "sasToken": "|",
                    "connectionString": "|:-connectionString:secureString",
                    "hostKeyFingerprint": "="
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
        }
    },
    "Microsoft.DataFactory/factories/managedVirtualNetworks/managedPrivateEndpoints": {
        "properties": {
            "*": "="
        }
    }
}
```

### <a name="example-parameterizing-an-existing-azure-databricks-interactive-cluster-id"></a>示例：参数化现有的 Azure Databricks 交互式群集 ID

以下示例演示如何将单个值添加到默认的参数化模板。 我们只想要将 Databricks 链接服务的某个现有 Azure Databricks 交互式群集 ID 添加到参数文件。 请注意，此文件除了在 `Microsoft.DataFactory/factories/linkedServices` 的属性字段下添加了 `existingClusterId` 以外，在其他方面，它与前一个文件相同。

```json
{
    "Microsoft.DataFactory/factories": {
        "properties": {
            "globalParameters": {
                "*": {
                    "value": "="
                }
            }
        },
        "location": "="
    },
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
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
                    "poolName": "=",
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

如果为数据工厂设置了 CI/CD，随着工厂的不断增大，最终可能会超过 Azure 资源管理器模板限制。 例如，有一项限制是资源管理器模板中的最大资源数。 为了容纳较大的工厂，同时为工厂生成完整的资源管理器模板，数据工厂现在会生成链接的资源管理器模板。 借助此功能，整个工厂有效负载将分解为多个文件，这样就不会受到限制的约束。

如果已配置 Git，将会生成链接的模板，并将其连同完整的资源管理器模板一起保存在 adf_publish 分支中名为 linkedTemplates 的新文件夹内：

![链接的资源管理器模板文件夹](media/continuous-integration-deployment/linked-resource-manager-templates.png)

链接的资源管理器模板通常包括一个主模板，以及一组链接到主模板的子模板。 父模板名为 ArmTemplate_master.json，子模板按照 ArmTemplate_0.json、ArmTemplate_1.json ... 的模式命名。 

若要使用链接的模板而不是完整的资源管理器模板，请将 CI/CD 任务更新为指向 ArmTemplate_master.json 而不是 ArmTemplateForFactory.json（完整资源管理器模板）。 资源管理器还要求将链接的模板上传到存储帐户，使 Azure 能够在部署期间访问这些模板。 有关详细信息，请参阅[使用 VSTS 部署链接的资源管理器模板](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/)。

不要忘记在执行部署任务之前和之后在 CI/CD 管道中添加数据工厂脚本。

如果未配置 Git，可以通过“ARM 模板”列表中的“导出 ARM 模板”访问链接的模板。 

## <a name="hotfix-production-environment"></a>修补程序生产环境

将某个工厂部署到生产环境后，如果你发现某个 bug 需要立即予以修复，但无法部署当前协作分支，则你可能需要部署一个修补程序。 此方法称作快速修复工程 (QFE)。

1.    在 Azure DevOps 中，转到已部署至生产环境的版本。 找到已部署的最后一个提交内容。

2.    从提交消息中获取协作分支的提交 ID。

3.    基于该提交内容创建新的修补程序分支。

4.    转到 Azure 数据工厂 UX，切换到修补程序分支。

5.    使用 Azure 数据工厂 UX 修复该 bug。 测试更改。

6.    验证修复结果后，选择“导出 ARM 模板”以获取修补程序资源管理器模板。

7.    手动将此生成签入到 adf_publish 分支。

8.    如果已将发布管道配置为根据 adf_publish 签入内容自动触发，则会自动启动新的发布。 否则，请手动将发布排入队列。

9.    将修补程序版本部署到测试工厂和生产工厂。 此版本包含以前的生产有效负载，以及你在步骤 5 中完成的修复。

10.   将此修补程序中的更改添加到开发分支，使以后的版本不会出现相同的 bug。

## <a name="best-practices-for-cicd"></a>CI/CD 最佳做法

如果你使用数据工厂的 Git 集成，并且某个 CI/CD 管道会将更改从开发环境依次转移到测试和生产环境，则我们建议采用以下最佳做法：

-   **Git 集成**。 仅配置具有 Git 集成的开发数据工厂。 对测试和生产环境所做的更改将通过 CI/CD 进行部署，不需要 Git 集成。

-   **部署前和部署后脚本**。 在 CI/CD 中完成资源管理器部署步骤之前，需要先完成某些任务，例如停止再重启触发器并执行清理。 我们建议在执行部署任务之前和之后使用 PowerShell 脚本。 有关详细信息，请参阅[更新活动触发器](#updating-active-triggers)。 数据工厂团队已在本网页的末尾提供了一个可用的[脚本](#script)。

-   **集成运行时和共享**。 集成运行时不经常更改，在 CI/CD 的所有阶段中都是类似的。 因此，数据工厂预期在 CI/CD 的所有阶段使用相同的集成运行时名称和类型。 若要在所有阶段中共享集成运行时，请考虑使用三元工厂，这只是为了包含共享的集成运行时。 可以在所有环境中将此共享工厂用作链接的集成运行时类型。

-   **托管专用终结点部署**。 如果某个专用终结点在工厂中已存在，并且你尝试部署的 ARM 模板包含具有相同名称但带有修改的属性的专用终结点，则部署将失败。 换句话说，你可以成功部署专用终结点，前提是该终结点具有工厂中已存在的相同属性。 如果环境之间的任何属性不同，可以通过参数化该属性并在部署过程中提供相应的值来重写它。

-   **Key Vault**。 使用其连接信息存储在 Azure Key Vault 中的链接服务时，建议为不同的环境保留不同的密钥保管库。 此外，可为每个密钥保管库单独配置权限级别。 例如，你可能不希望团队成员有权访问生产机密。 如果采用此方法，我们建议在所有阶段中保留相同的机密名称。 如果保留相同的机密名称，则无需在 CI/CD 环境中参数化每个连接字符串，因为只需更改密钥保管库名称，而该名称是一个单独的参数。

-  **资源命名** 由于 ARM 模板约束，如果资源包含名称中的空格，则可能会出现部署中的问题。 Azure 数据工厂团队建议使用 "_" 或 "-" 字符，而不是为资源使用空格。 例如，"Pipeline_1" 将是 "管道 1" 上的首选名称。

## <a name="unsupported-features"></a>不支持的功能

- 按照设计，数据工厂不允许挑拣提交内容或选择性地发布资源。 发布将包含数据工厂中发生的所有更改。

    - 数据工厂实体相互依赖。 例如，触发器依赖于管道，而管道又依赖于数据集和其他管道。 选择性发布资源子集可能会导致意外的行为和错误。
    - 如果需要进行选择性发布（这种情况很少见），请考虑使用修补程序。 有关详细信息，请参阅 [修补程序生产环境](#hotfix-production-environment)。

- Azure 数据工厂团队不建议为数据工厂中的单个实体 (管道、数据集等) 分配 RBAC 控件。 例如，如果开发人员有权访问管道或数据集，则他们应该能够访问数据工厂中的所有管道或数据集。 如果你认为需要在数据工厂中实现许多 RBAC 角色，请查看部署第二个数据工厂。

-   无法从专用分支发布。

-   目前无法在 Bitbucket 上托管项目。

## <a name="sample-pre--and-post-deployment-script"></a><a name="script"></a> 部署前和部署后示例脚本

以下示例脚本可用于在部署之前停止触发器，并在部署之后重启触发器。 此脚本还包括用于删除已移除资源的代码。 请将该脚本保存到 Azure DevOps git 存储库，并使用版本 4.* 通过 Azure PowerShell 任务引用它。

在运行部署前脚本时，需在“脚本参数”字段中指定以下参数的变体。

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $true -deleteDeployment $false`


在运行部署后脚本时，需在“脚本参数”字段中指定以下参数的变体。

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $false -deleteDeployment $true`

![Azure PowerShell 任务](media/continuous-integration-deployment/continuous-integration-image11.png)

下面是部署前和部署后可以使用的脚本。 其中考虑到了已删除的资源以及资源引用。

  
```powershell
param
(
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

function getPipelineDependencies {
    param([System.Object] $activity)
    if ($activity.Pipeline) {
        return @($activity.Pipeline.ReferenceName)
    } elseif ($activity.Activities) {
        $result = @()
        $activity.Activities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.ifFalseActivities -or $activity.ifTrueActivities) {
        $result = @()
        $activity.ifFalseActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        $activity.ifTrueActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.defaultActivities) {
        $result = @()
        $activity.defaultActivities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        if ($activity.cases) {
            $activity.cases | ForEach-Object{ $_.activities } | ForEach-Object{$result += getPipelineDependencies -activity $_ }
        }
        return $result
    } else {
        return @()
    }
}

function pipelineSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]$pipeline,
    [Hashtable] $pipelineNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$pipeline.Name] -eq $true) {
        return;
    }
    $visited[$pipeline.Name] = $true;
    $pipeline.Activities | ForEach-Object{ getPipelineDependencies -activity $_ -pipelineNameResourceDict $pipelineNameResourceDict}  | ForEach-Object{
        pipelineSortUtil -pipeline $pipelineNameResourceDict[$_] -pipelineNameResourceDict $pipelineNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($pipeline)

}

function Get-SortedPipelines {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $pipelines = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $ppDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $pipelines | ForEach-Object{ $ppDict[$_.Name] = $_ }
    $pipelines | ForEach-Object{ pipelineSortUtil -pipeline $_ -pipelineNameResourceDict $ppDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function triggerSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]$trigger,
    [Hashtable] $triggerNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$trigger.Name] -eq $true) {
        return;
    }
    $visited[$trigger.Name] = $true;
    if ($trigger.Properties.DependsOn) {
        $trigger.Properties.DependsOn | Where-Object {$_ -and $_.ReferenceTrigger} | ForEach-Object{
            triggerSortUtil -trigger $triggerNameResourceDict[$_.ReferenceTrigger.ReferenceName] -triggerNameResourceDict $triggerNameResourceDict -visited $visited -sortedList $sortedList
        }
    }
    $sortedList.Push($trigger)
}

function Get-SortedTriggers {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $triggers = Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $triggerDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $triggers | ForEach-Object{ $triggerDict[$_.Name] = $_ }
    $triggers | ForEach-Object{ triggerSortUtil -trigger $_ -triggerNameResourceDict $triggerDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function Get-SortedLinkedServices {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $linkedServices = Get-AzDataFactoryV2LinkedService -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $LinkedServiceHasDependencies = @('HDInsightLinkedService', 'HDInsightOnDemandLinkedService', 'AzureBatchLinkedService')
    $Akv = 'AzureKeyVaultLinkedService'
    $HighOrderList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $RegularList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $AkvList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]

    $linkedServices | ForEach-Object {
        if ($_.Properties.GetType().Name -in $LinkedServiceHasDependencies) {
            $HighOrderList.Add($_)
        }
        elseif ($_.Properties.GetType().Name -eq $Akv) {
            $AkvList.Add($_)
        }
        else {
            $RegularList.Add($_)
        }
    }

    $SortedList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]($HighOrderList.Count + $RegularList.Count + $AkvList.Count)
    $SortedList.AddRange($HighOrderList)
    $SortedList.AddRange($RegularList)
    $SortedList.AddRange($AkvList)
    $SortedList
}

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersInTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNamesInTemplate = $triggersInTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}

$triggersDeployed = Get-SortedTriggers -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

$triggersToStop = $triggersDeployed | Where-Object { $triggerNamesInTemplate -contains $_.Name } | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.Name
        TriggerType = $_.Properties.GetType().Name 
    }
}
$triggersToDelete = $triggersDeployed | Where-Object { $triggerNamesInTemplate -notcontains $_.Name } | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.Name
        TriggerType = $_.Properties.GetType().Name 
    }
}
$triggersToStart = $triggersInTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.name.Substring(37, $_.name.Length-40)
        TriggerType = $_.Properties.type
    }
}

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers`n"
    $triggersToStop | ForEach-Object {
        if ($_.TriggerType -eq "BlobEventsTrigger") {
            Write-Host "Unsubscribing" $_.Name "from events"
            $status = Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            while ($status.Status -ne "Disabled"){
                Start-Sleep -s 15
                $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            }
        }
        Write-Host "Stopping trigger" $_.Name
        Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-SortedPipelines -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #dataflows
    $dataflowsADF = Get-AzDataFactoryV2DataFlow -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $dataflowsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/dataflows" }
    $dataflowsNames = $dataflowsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataflow = $dataflowsADF | Where-Object { $dataflowsNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-SortedLinkedServices -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
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
    $triggersToDelete | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            if ($_.TriggerType -eq "BlobEventsTrigger") {
                Write-Host "Unsubscribing trigger" $_.Name "from events"
                $status = Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
                while ($status.Status -ne "Disabled"){
                    Start-Sleep -s 15
                    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
                }
            }
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting dataflows"
    $deleteddataflow | ForEach-Object { 
        Write-Host "Deleting dataflow " $_.Name
        Remove-AzDataFactoryV2DataFlow -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
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

    #Start active triggers - after cleanup efforts
    Write-Host "Starting active triggers"
    $triggersToStart | ForEach-Object { 
        if ($_.TriggerType -eq "BlobEventsTrigger") {
            Write-Host "Subscribing" $_.Name "to events"
            $status = Add-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            while ($status.Status -ne "Enabled"){
                Start-Sleep -s 15
                $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            }
        }
        Write-Host "Starting trigger" $_.Name
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force
    }
}
```
