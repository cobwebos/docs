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
ms.date: 04/30/2020
ms.openlocfilehash: 54ff58735b6831bb45a9477360ffca3439d2f6b4
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124714"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Azure 数据工厂中的持续集成和交付

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>概述

持续集成是指自动并尽快测试对基本代码所做的每项更改。持续交付遵循在持续集成期间进行的测试，并将更改推送到过渡或生产系统。

在 Azure 数据工厂中，持续集成和交付（CI/CD）意味着将数据工厂管道从一个环境（开发、测试、生产）移到另一个环境。 Azure 数据工厂利用[azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)来存储各种 ADF 实体（管道、数据集、数据流等）的配置。 有两种建议的方法可将数据工厂升级到另一个环境：

-    使用数据工厂与[Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines?view=azure-devops)集成的自动化部署
-    使用数据工厂 UX 与 Azure 资源管理器集成，手动上载资源管理器模板。

有关此功能的九分钟介绍和演示，请观看此视频：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>CI/CD 生命周期

下面是 Azure 数据工厂中使用 Git Azure Repos 配置的 CI/CD 生命周期的示例概述。 有关如何配置 Git 存储库的详细信息，请参阅[Azure 数据工厂中的源代码管理](source-control.md)。

1.  开发数据工厂使用 Azure Repos Git 创建和配置。 所有开发人员都应该有权创作数据工厂资源，如管道和数据集。

1.  开发人员[创建功能分支](source-control.md#creating-feature-branches)以进行更改。 它们使用最近的更改来调试其管道运行。 有关如何调试管道运行的详细信息，请参阅[使用 Azure 数据工厂进行迭代开发和调试](iterative-development-debugging.md)。

1.  当开发人员对更改感到满意后，他们会创建一个从其功能分支到主分支或协作分支的拉取请求，以获取对等方所做的更改。

1.  在拉取请求获得批准并将更改合并到主分支后，更改将发布到开发工厂。

1.  当团队准备好将更改部署到测试或 UAT 工厂时，团队会转到其 Azure Pipelines 版本，并将所需版本的开发工厂部署到 UAT。 此部署将作为 Azure Pipelines 任务的一部分发生，并使用资源管理器模板参数应用适当的配置。

1.  在测试工厂中验证更改后，使用管道版本的下一个任务将部署到生产工厂。

> [!NOTE]
> 只有开发工厂与 git 存储库相关联。 测试工厂和生产工厂不应有关联的 git 存储库，只应通过 Azure DevOps 管道或资源管理模板进行更新。

下图突出显示了此生命周期的各个步骤。

![与 Azure Pipelines 的持续集成示意图](media/continuous-integration-deployment/continuous-integration-image12.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>使用 Azure Pipelines 版本实现持续集成

下面是设置用于自动将数据工厂部署到多个环境的 Azure Pipelines 版本的指南。

### <a name="requirements"></a>要求

-   链接到 Visual Studio 的 Azure 订阅 Team Foundation Server 或使用 [Azure 资源管理器服务终结点](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager)Azure Repos。

-   使用 Azure Repos Git 集成配置的数据工厂。

-   一个 [Azure 密钥保管库](https://azure.microsoft.com/services/key-vault/)，其中包含每个环境的机密。

### <a name="set-up-an-azure-pipelines-release"></a>设置 Azure Pipelines 发布

1.  在[Azure DevOps](https://dev.azure.com/)中，打开配置有数据工厂的项目。

1.  在页面左侧，选择 "**管道**"，然后选择 "**发布**"。

    ![选择管道、发布](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  选择 "**新建管道**"，如果有现有管道，请依次选择 "**新建**" 和新的 "**发布管道**"。

1.  选择**空作业**模板。

    ![选择空作业](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  在 "**阶段名称**" 框中，输入环境的名称。

1.  选择 "**添加项目**"，然后选择配置了开发数据工厂的 git 存储库。 选择**默认分支**的存储库的 "[发布" 分支](source-control.md#configure-publishing-settings)。 默认情况下，此发布分支是 `adf_publish` 。 对于**默认版本**，请**从 "默认分支" 中选择 "最新**"。

    ![添加项目](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  添加 Azure 资源管理器部署任务：

    a.  在 "阶段" 视图中，选择 "**查看阶段任务**"。

    ![阶段视图](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  创建新任务。 搜索 " **Azure 资源组部署**"，然后选择 "**添加**"。

    c.  在 "部署任务" 中，选择目标数据工厂的 "订阅"、"资源组" 和 "位置"。 如有必要，请提供凭据。

    d.  在 "**操作**" 列表中，选择 "**创建或更新资源组**"。

    e.  选择**模板**框旁边的省略号按钮（**...**）。 浏览在配置的 git 存储库的发布分支中生成的 Azure 资源管理器模板。 `ARMTemplateForFactory.json`在 adf_publish 分支的文件夹中查找文件 <FactoryName> 。

    f.  选择 **...** 在 "**模板参数**" 框旁边，选择参数文件。 `ARMTemplateParametersForFactory.json`在 adf_publish 分支的文件夹中查找文件 <FactoryName> 。

    g.  选择 **...** 在 "**替代模板参数**" 框旁边，为目标数据工厂输入所需的参数值。 对于来自 Azure Key Vault 的凭据，请在双引号之间输入密钥的名称。 例如，如果密码的名称为 cred1，请输入此值的 **"$ （cred1）"** 。

    h.如果该值不存在，请单击“添加行”。 选择**部署模式**的 "**增量**"。

    > [!WARNING]
    > 如果为**部署模式**选择 "**完成**"，则可能会删除现有资源，其中包括未在资源管理器模板中定义的目标资源组中的所有资源。

    ![数据工厂生产部署](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  保存发布管道。

1. 若要触发发布，请选择 "**创建发布**"。 若要自动创建发布，请参阅[Azure DevOps release 触发器](https://docs.microsoft.com/azure/devops/pipelines/release/triggers?view=azure-devops)

   ![选择 "创建发布"](media/continuous-integration-deployment/continuous-integration-image10.png)

> [!IMPORTANT]
> 在 CI/CD 方案中，不同环境中的集成运行时（IR）类型必须相同。 例如，如果在开发环境中具有自承载 IR，则同一 IR 还必须在其他环境（如测试和生产）中是自承载类型。 同样，如果你要跨多个阶段共享集成运行时，则必须在所有环境（例如开发、测试和生产）中将集成运行时配置为链接自承载。

### <a name="get-secrets-from-azure-key-vault"></a>从 Azure Key Vault 获取机密

如果你有权传入 Azure 资源管理器模板中的机密，则建议你将 Azure Key Vault 与 Azure Pipelines 版本结合使用。

可以通过两种方式来处理机密：

1.  将机密添加到参数文件。 有关详细信息，请参阅[在部署过程中使用 Azure Key Vault 传递安全参数值](../azure-resource-manager/templates/key-vault-parameter.md)。

    创建上载到发布分支的参数文件的副本。 使用以下格式设置要从 Key Vault 获取的参数的值：

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

1. 在上一部分中介绍的 Azure 资源管理器部署任务之前添加[Azure Key Vault 任务](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault)：

    1.  在 "**任务**" 选项卡上，创建一个新任务。 搜索并添加**Azure Key Vault** 。

    1.  在 Key Vault 任务中，选择在其中创建密钥保管库的订阅。 提供凭据（如有必要），然后选择密钥保管库。

    ![添加 Key Vault 任务](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>向 Azure Pipelines 代理授权

如果未设置正确的权限，Azure Key Vault 任务可能会失败并出现 "拒绝访问" 错误。 下载版本的日志，并找到包含命令的 ps1 文件，以便向 Azure Pipelines 代理提供权限。 可以直接运行该命令。 或者，你可以从文件复制主体 ID 并在 Azure 门户中手动添加访问策略。 `Get`和 `List` 是所需的最小权限。

### <a name="updating-active-triggers"></a>更新活动触发器

如果尝试更新活动触发器，部署可能会失败。 若要更新活动触发器，需要手动将其停止，然后在部署后重新启动它们。 为此，可以使用 Azure PowerShell 任务：

1.  在版本的 "**任务**" 选项卡上，添加一个**Azure PowerShell**任务。 选择 "任务版本 4. *"。 

1.  选择工厂所在的订阅。

1.  选择 "**脚本文件路径**" 作为脚本类型。 这要求你将 PowerShell 脚本保存到存储库中。 以下 PowerShell 脚本可用于停止触发器：

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

部署后，可以完成类似的步骤 `Start-AzDataFactoryV2Trigger` 来重新启动触发器。

数据工厂团队已经在本文底部提供了一个[示例预先部署脚本和后期部署脚本](#script)。 

## <a name="manually-promote-a-resource-manager-template-for-each-environment"></a>为每个环境手动升级资源管理器模板

1. 在**ARM 模板**列表中，选择 "**导出 ARM 模板**"，为开发环境中的数据工厂导出资源管理器模板。

   ![导出资源管理器模板](media/continuous-integration-deployment/continuous-integration-image1.png)

1. 在测试和生产数据工厂中，选择 "**导入 ARM 模板**"。 此操作会将你转到 Azure 门户，可以在其中导入已导出的模板。 选择 **"在编辑器中生成自己的模板**" 以打开 "资源管理器模板编辑器"。

   ![构建您自己的模板](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. 选择 "**加载文件**"，然后选择生成的资源管理器模板。 这是在步骤1中导出的 .zip 文件中的**arm_template**文件。

   ![编辑模板](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. 在 "设置" 部分中，输入配置值，如链接的服务凭据。 完成后，选择 "**购买**" 以部署资源管理器模板。

   ![设置部分](media/continuous-integration-deployment/continuous-integration-image5.png)

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>将自定义参数用于资源管理器模板

如果你的开发工厂具有关联的 git 存储库，则可以覆盖通过发布或导出该模板生成的资源管理器模板的默认资源管理器模板参数。 在以下情况下，你可能希望重写默认参数化模板：

* 你使用自动 CI/CD，并且想要在资源管理器部署过程中更改某些属性，但默认情况下不会对属性进行参数化。
* 由于默认资源管理器模板超过允许的最大参数（256），因此您的工厂非常大。

若要重写默认参数化模板，请在 git 分支的根文件夹中创建一个名为 " **arm-模板-参数-json** " 的文件。 必须使用正确的文件名。

   ![自定义参数文件](media/continuous-integration-deployment/custom-parameters.png)

从协作分支进行发布时，数据工厂将读取此文件，并使用其配置来生成参数化的属性。 如果未找到任何文件，则使用默认模板。

导出资源管理器模板时，数据工厂从你当前正在处理的分支读取此文件，而不是只从协作分支读取此文件。 您可以通过在用户界面中选择 "**导出 ARM 模板**"，从私有分支创建或编辑该文件。 然后，可以将文件合并到协作分支。

> [!NOTE]
> 自定义参数化模板不会将 ARM 模板参数限制更改为256。 它允许选择并减少参数化属性的数目。

### <a name="custom-parameter-syntax"></a>自定义参数语法

下面是在创建自定义参数文件时需要遵循的一些准则： **arm 模板-参数定义。** 该文件由每个实体类型的部分组成：触发器、管道、链接服务、数据集、集成运行时和数据流。

* 输入相关实体类型下的属性路径。
* 将属性名称设置为，  `*` 表示要将其下的所有属性参数化（仅向下到第一级，而不是以递归方式）。 你还可以为此配置提供例外情况。
* 将属性的值设置为字符串表示你要参数化该属性。 使用格式  `<action>:<name>:<stype>` 。
   *  `<action>` 可以是下列字符之一：
      * `=` 表示保留当前值作为参数的默认值。
      * `-` 表示不要保留参数的默认值。
      * `|` 是 Azure Key Vault 用于连接字符串或键的密码的特例。
   * `<name>` 参数的名称。 如果为空，则采用属性的名称。 如果值以 `-` 字符开头，则将缩短名称。 例如，将 `AzureStorage1_properties_typeProperties_connectionString` 缩短为 `AzureStorage1_connectionString` 。
   * `<stype>` 参数的类型。 如果  `<stype>`   为空，则默认类型为 `string` 。 支持的值： `string` 、 `bool` 、 `number` 、 `object` 和 `securestring` 。
* 如果在定义文件中指定数组，则表明模板中的匹配属性是数组。 数据工厂使用数组的 integration runtime 对象中指定的定义来循环访问数组中的所有对象。 第二个对象（一个字符串）成为属性的名称，这用作每次遍历的参数的名称。
* 定义不能特定于资源实例。 任何定义都适用于该类型的所有资源。
* 默认情况下，所有安全字符串（如 Key Vault 机密）和安全字符串（如连接字符串、密钥和令牌）都是参数化的。
 
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
下面说明如何构造上述模板，按资源类型细分。

#### <a name="pipelines"></a>管道
    
* 路径中的任何属性 `activities/typeProperties/waitTimeInSeconds` 都已参数化。 管道中具有名为（例如，活动）的代码级别属性的任何活动 `waitTimeInSeconds` `Wait` 均被参数化为数字，并且具有默认名称。 但资源管理器模板中没有默认值。 在资源管理器部署过程中，它将是必需的输入。
* 同样，名为的属性 `headers` （例如，在 `Web` 活动中）使用 type `object` （JObject）进行参数化。 它有一个默认值，该值与源工厂的值相同。

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* 路径下的所有属性 `typeProperties` 均由其各自的默认值参数化。 例如，在 "类型属性" 下有两个属性 `IntegrationRuntimes` ： `computeProperties` 和 `ssisProperties` 。 这两个属性类型都是用各自的默认值和类型（对象）创建的。

#### <a name="triggers"></a>触发器

* 在下 `typeProperties` ，参数化两个属性。 第一个是 `maxConcurrency` ，它被指定为具有默认值，并且的类型为 `string` 。 它具有默认的参数名称 `<entityName>_properties_typeProperties_maxConcurrency` 。
* 此 `recurrence` 属性也是参数化的。 在该级别下，将指定该级别的所有属性指定为字符串，并将默认值和参数名称指定为参数。 异常是 `interval` 参数为类型的属性 `number` 。 参数名称的后缀为 `<entityName>_properties_typeProperties_recurrence_triggerSuffix` 。 同样，此 `freq` 属性是一个字符串，参数化为字符串。 但是， `freq` 属性的参数化没有默认值。 名称将被缩短并带有后缀。 例如，`<entityName>_freq`。

#### <a name="linkedservices"></a>Linkedservices.json

* 链接服务是唯一的。 由于链接服务和数据集具有各种类型，因此你可以提供特定于类型的自定义。 在此示例中，对于所有类型的链接服务 `AzureDataLakeStore` ，将应用特定模板。 对于其他所有内容（通过 `*` ），将应用不同的模板。
* `connectionString`属性将参数化为 `securestring` 值。 它没有默认值。 它将具有一个带有后缀的短参数名称 `connectionString` 。
* 属性 `secretAccessKey` 碰巧为 `AzureKeyVaultSecret` （例如，在 Amazon S3 链接服务中）。 它自动参数化为 Azure Key Vault 机密，并从配置的密钥保管库提取。 还可以参数化密钥保管库本身。

#### <a name="datasets"></a>数据集

* 尽管特定于类型的自定义可用于数据集，但你可以提供配置，而无需显式设置 \* 级别配置。 在前面的示例中，所有位于下的数据集属性 `typeProperties` 都是参数化的。

### <a name="default-parameterization-template"></a>默认参数化模板

下面是当前默认参数化模板。 如果只需要添加几个参数，则直接编辑此模板可能是个好主意，因为不会丢失现有的参数化结构。

```json
{
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

### <a name="example-parameterizing-an-existing-azure-databricks-interactive-cluster-id"></a>示例：参数化现有 Azure Databricks 交互式群集 ID

下面的示例演示如何将单个值添加到默认参数化模板。 我们只想要将 Databricks 链接服务的现有 Azure Databricks 交互式群集 ID 添加到参数文件。 请注意，此文件与上一个文件相同，只是 `existingClusterId` 在的属性字段下添加了 `Microsoft.DataFactory/factories/linkedServices` 。

```json
{
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

如果已为数据工厂设置 CI/CD，则可能会超出 Azure 资源管理器模板限制，因为工厂会增大。 例如，一个限制是资源管理器模板中的最大资源数。 为了适应大型工厂，同时生成工厂的完整资源管理器模板，数据工厂现在会生成链接资源管理器模板。 利用此功能，整个工厂有效负载分解为多个文件，因此不受限制的限制。

如果已配置 Git，则会在名为 linkedTemplates 的新文件夹中，生成和保存链接的模板，并将其与 adf_publish 分支中的完整资源管理器模板一起保存：

![链接的资源管理器模板文件夹](media/continuous-integration-deployment/linked-resource-manager-templates.png)

链接的资源管理器模板通常由主模板和链接到主模板的一组子模板组成。 父模板称为 ArmTemplate_master json，子模板按模式 ArmTemplate_0 json、ArmTemplate_1 json 等进行命名。 

若要使用链接模板而不是完整资源管理器模板，请将 CI/CD 任务更新为指向 ArmTemplate_master 而不是 ArmTemplateForFactory （完整资源管理器模板）。 资源管理器还要求将链接的模板上传到存储帐户，以便 Azure 可以在部署过程中访问它们。 有关详细信息，请参阅[通过 VSTS 部署链接的资源管理器模板](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/)。

不要忘记在执行部署任务之前和之后在 CI/CD 管道中添加数据工厂脚本。

如果未配置 Git，则可以通过**Arm 模板**列表中的 "**导出 ARM 模板**" 访问链接模板。

## <a name="exclude-azure-ssis-integration-runtimes-from-cicd"></a>从 CI/CD 中排除 Azure SSIS 集成运行时

如果你的开发工厂包含 Azure SSIS 集成运行时，则可以在以下情况下从 CI/CD 进程中排除所有 Azure SSIS 集成运行时：

- Azure-SSIS IR 基础结构是很复杂的，每个环境都是不同的。  
- 为具有相同名称的每个环境手动设置 Azure-SSIS IR。 否则，如果存在活动，则发布将失败，具体取决于 Azure-SSIS IR。

排除 Azure SSIS 集成运行时：

1. 将 publish_config 的 json 文件添加到协作分支的根文件夹中（如果不存在）。
1. 将以下设置添加到 publish_config。 json： 

```json
{
    " excludeIRs": "true"
}
```

从协作分支进行发布时，将从生成的资源管理器模板中排除 Azure SSIS 集成运行时。

## <a name="hotfix-production-branch"></a>修补程序生产分支

如果将工厂部署到生产环境，并意识到需要立即固定的 bug，但无法部署当前的协作分支，则可能需要部署修补程序。 此方法称为快速修补工程或 QFE。

1.    在 Azure DevOps 中，请参阅已部署到生产的版本。 查找已部署的上次提交。

2.    从提交消息获取协作分支的提交 ID。

3.    从该提交创建新的修补程序分支。

4.    转到 Azure 数据工厂 UX，并切换到修补程序分支。

5.    使用 Azure 数据工厂 UX，修复错误。 测试更改。

6.    验证修复后，选择 "**导出 ARM 模板**" 以获取修补程序资源管理器模板。

7.    手动将此生成检查到 adf_publish 分支。

8.    如果已将发布管道配置为根据 adf_publish 签入自动触发，则新版本会自动启动。 否则，手动将发布排队。

9.    将修补程序版本部署到测试工厂和生产工厂。 此版本包含之前的生产负载以及您在步骤5中所做的修复。

10.   将此修补程序中的更改添加到开发分支，以便以后版本不包含相同的 bug。

## <a name="best-practices-for-cicd"></a>CI/CD 最佳做法

如果对数据工厂使用 Git 集成，并提供 CI/CD 管道将所做的更改从开发环境移到测试，然后再移至生产环境，我们建议采用以下最佳做法：

-   **Git 集成**。 仅配置带有 Git 集成的开发数据工厂。 通过 CI/CD 部署对测试和生产的更改，无需 Git 集成。

-   **预先部署脚本和后期部署脚本**。 在 CI/CD 中资源管理器部署步骤之前，需要完成一些任务，如停止和重新启动触发器并执行清理。 建议在部署任务前后使用 PowerShell 脚本。 有关详细信息，请参阅[Update active trigger](#updating-active-triggers)。 数据工厂团队[提供了一个脚本](#script)，该脚本可用于此页面的底部。

-   **集成运行时和共享**。 集成运行时不经常更改，并且在 CI/CD 中的所有阶段都是类似的。 因此，数据工厂要求在 CI/CD 的所有阶段都具有相同的集成运行时名称和类型。 如果要在所有阶段共享集成运行时，请考虑使用三元工厂，只包含共享集成运行时。 可以在所有环境中将此共享工厂用作链接集成运行时类型。

-   **Key Vault**。 使用连接信息存储在 Azure Key Vault 中的链接服务时，建议为不同环境保留单独的密钥保管库。 你还可以为每个密钥保管库配置单独的权限级别。 例如，你可能不希望团队成员拥有生产机密的权限。 如果遵循此方法，我们建议你在所有阶段保留相同的机密名称。 如果保留相同的机密名称，则无需在 CI/CD 环境中参数化每个连接字符串，因为唯一更改的是密钥保管库名称，这是一个单独的参数。

## <a name="unsupported-features"></a>不支持的功能

- 按照设计，数据工厂不允许挑拣提交或选择性地发布资源。 发布将包含数据工厂中所做的所有更改。

    - 数据工厂实体相互依赖。 例如，触发器依赖于管道，而管道依赖于数据集和其他管道。 资源子集的选择性发布可能会导致意外的行为和错误。
    - 在极少数情况下，需要选择性发布时，请考虑使用修补程序。 有关详细信息，请参阅[修补程序生产分支](#hotfix-production-branch)。

-   无法从专用分支发布。

-   当前不能在 Bitbucket 上托管项目。

## <a name="sample-pre--and-post-deployment-script"></a><a name="script"></a>示例预先部署脚本和后期部署脚本

下面的示例脚本可用于在部署之前停止触发器并在以后重新启动。 此脚本还包括用于删除已移除资源的代码。 将该脚本保存在 Azure DevOps git 存储库中，并通过使用版本 4. * 的 Azure PowerShell 任务引用它。

在运行预先部署脚本时，需要在 "**脚本参数**" 字段中指定以下参数的变体。

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $true -deleteDeployment $false`


运行后期部署脚本时，需要在 "**脚本参数**" 字段中指定以下参数的变体。

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $false -deleteDeployment $true`

![Azure PowerShell 任务](media/continuous-integration-deployment/continuous-integration-image11.png)

下面是可以用于预先部署和后期部署的脚本。 它用于删除资源和资源引用。

  
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
    $trigger.Properties.DependsOn | Where-Object {$_ -and $_.ReferenceTrigger} | ForEach-Object{
        triggerSortUtil -trigger $triggerNameResourceDict[$_.ReferenceTrigger.ReferenceName] -triggerNameResourceDict $triggerNameResourceDict -visited $visited -sortedList $sortedList
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
$triggersADF = Get-SortedTriggers -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
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
        Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force
    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    while ($status.Status -ne "Disabled"){
            Start-Sleep -s 15
            $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    }
    Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
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
    $activeTriggerNames | ForEach-Object { 
        Write-host "Enabling trigger " $_
        Add-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force
    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    while ($status.Status -ne "Enabled"){
            Start-Sleep -s 15
            $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    }
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```
