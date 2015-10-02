<properties
   pageTitle="使用 Azure 资源管理器模板部署应用程序"
   services="azure-resource-manager"
   description="使用 Azure 资源管理器向 Azure 部署应用程序模板是一个 JSON 文件，可以从门户、PowerShell、适用于 Mac、Linux 和 Windows 的 Azure 命令行界面以及 REST 使用。"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.date="08/20/2015"
   wacn.date=""/>

# 使用 Azure 资源管理器模板部署应用程序

本主题介绍如何使用 Azure 资源管理器模板向 Azure 部署应用程序。其中将会说明如何使用 Azure PowerShell、Azure CLI、REST API 或 Microsoft Azure 预览门户部署应用程序。

使用 Azure 资源管理器模板，可以通过声明性 JSON 在 Azure 中快速轻松地设置应用程序。在单个 JSON 模板中可以部署多个服务，例如虚拟机、虚拟网络、存储、应用程序服务和数据库。在应用程序生命周期中的每个阶段，你可以使用同一个模板反复、一致地部署应用程序。

为了简化应用程序管理，可以将共享相同生命周期的所有资源组织到单个资源组中。使用资源组可以轻松地将所有相关资源一起部署、更新和删除。在大多数情况下，资源组将映射到单个应用程序或应用程序层（对于大型应用程序）。通过模板部署的资源将驻留在单个资源组中，但可以包含其他资源组中的依赖项。

在资源组中，你可以跟踪部署的执行，以及查看部署状态和执行模板后的输出。

在使用模板部署应用程序时，可以提供参数值来自定义如何创建资源。以内联方式或者在参数文件中指定这些参数的值。

## 概念

- 资源组 - 共享相同生命周期的实体的集合
- 资源管理器模板 - 用于定义部署目标状态的声明性 JSON 文件
- 部署 - 用于跟踪模板资源管理器执行情况的操作
- 参数 - 执行部署的用户提供的值，用于自定义部署的资源
- 参数文件 - 用于存储参数名称和值的 JSON 文件

## 方案

使用资源管理器模板可以：

- 部署复杂的多层应用程序，如 Microsoft SharePoint。
- 反复、一致地部署应用程序。
- 为开发、测试和生产环境提供支持。
- 查看部署的状态。
- 使用部署审核日志排查部署错误。

## 使用预览门户进行部署

你猜会怎样？ 库中的每个应用程序都将得到 Azure 资源管理器模板的支持！ 只需通过门户创建虚拟机、虚拟网络、存储帐户、应用程序服务或数据库，就能享用 Azure 资源管理器的优势，且不用额外地增加工作量。

![新建](./media/resource-group-template-deploy/new.png)

有关将门户与 Azure 资源管理器配合使用的详细信息，请参阅[使用 Azure 预览门户管理 Azure 资源](resource-group-portal.md)。

## 使用 PowerShell 进行部署

如果你以前没有对资源管理器使用过 Azure PowerShell，请参阅[将 Azure PowerShell 与 Azure 资源管理器配合使用](/documentation/articles/powershell-azure-resource-manager)。

1. 登录到你的 Azure 帐户。提供凭据后，该命令将返回有关你的帐户的信息。

        PS C:\> Add-AzureAccount

        Id                             Type       ...
        --                             ----
        someone@example.com            User       ...

2. 如果你有多个订阅，请提供要用于部署的订阅 ID。

        PS C:\> Select-AzureSubscription -SubscriptionID <YourSubscriptionId>

3. 切换到 Azure 资源管理器模块。

        PS C:\> Switch-AzureMode AzureResourceManager

4. 如果目前没有资源组，请创建新的资源组。提供资源组的名称，以及解决方案所需的位置。将返回新资源组的摘要。

        PS C:\> New-AzureResourceGroup -Name ExampleResourceGroup -Location "China North"

        ResourceGroupName : ExampleResourceGroup
        Location          : chinanorth
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

5. 若要为资源组创建新部署，请运行 **New-AzureResourceGroupDeployment** 命令并提供所需的参数。参数包括部署的名称、资源组的名称、所创建模板的路径，以及方案所需的任何其他参数。

     可以使用以下选项提供参数值：

     - 使用内联参数。

            PS C:\> New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"

     - 使用参数对象。

            PS C:\> $parameters = @{"<ParameterName>"="<Parameter Value>"}
            PS C:\> New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterObject $parameters

     - 使用参数文件。有关模板文件的信息，请参阅[参数文件](#parameter-file)。

            PS C:\> New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterFile <PathOrLinkToParameterFile>

     部署资源组后，你将看到部署摘要。

          DeploymentName    : ExampleDeployment
          ResourceGroupName : ExampleResourceGroup
          ProvisioningState : Succeeded
          Timestamp         : 4/14/2015 7:00:27 PM
          Mode              : Incremental
          ...

6. 获取有关部署错误的信息。

        PS C:\> Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed

7. 获取有关部署错误的详细信息。

        PS C:\> Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed -DetailedOutput

## 使用适用于 Mac、Linux 和 Windows 的 Azure CLI 进行部署

如果你以前没有对资源管理器使用过 Azure CLI，请参阅[将适用于 Mac、Linux 和 Windows 的 Azure CLI 与 Azure 资源管理配合使用](xplat-cli-azure-resource-manager)。

1. 登录到你的 Azure 帐户。提供凭据后，该命令将返回你的登录结果。

        azure login

        ...
        info:    login command OK

2. 如果你有多个订阅，请提供要用于部署的订阅 ID。

        azure account set <YourSubscriptionNameOrId>

3. 切换到 Azure 资源管理器模块。你将收到新模式确认。

        azure config mode arm

        info:     New mode is arm

4. 如果目前没有资源组，请创建新的资源组。提供资源组的名称，以及解决方案所需的位置。将返回新资源组的摘要。

        azure group create -n ExampleResourceGroup -l "China North"

        info:    Executing command group create
        + Getting resource group ExampleResourceGroup
        + Creating resource group ExampleResourceGroup
        info:    Created resource group ExampleResourceGroup
        data:    Id:                  /subscriptions/####/resourceGroups/ExampleResourceGroup
        data:    Name:                ExampleResourceGroup
        data:    Location:            chinanorth
        data:    Provisioning State:  Succeeded
        data:    Tags:
        data:
        info:    group create command OK

5. 若要为资源组创建新部署，请运行以下命令并提供所需的参数。参数包括部署的名称、资源组的名称、所创建模板的路径，以及方案所需的任何其他参数。

     可以使用以下选项提供参数值：

     - 使用内联参数和本地模板。

             azure group deployment create -f <PathToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

     - 使用内联参数和模板链接。

             azure group deployment create --template-uri <LinkToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

     - 使用参数文件。有关模板文件的信息，请参阅[参数文件](#parameter-file)。

             azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

     部署资源组后，你将看到部署摘要。

           info:    Executing command group deployment create
           + Initializing template configurations and parameters
           + Creating a deployment
           ...
           info:    group deployment create command OK


6. 获取有关最新部署的信息。

         azure group log show -l ExampleResourceGroup

7. 获取有关部署错误的详细信息。

         azure group log show -l -v ExampleResourceGroup

## 使用 REST API 进行部署
1. 设置[常见参数和标头](https://msdn.microsoft.com/zh-cn/library/azure/8d088ecc-26eb-42e9-8acc-fe929ed33563#bk_common)，包括身份验证令牌。
2. 如果目前没有资源组，请创建新的资源组。提供订阅 ID、新资源组的名称，以及解决方案所需的位置。有关详细信息，请参阅[创建资源组](https://msdn.microsoft.com/zh-cn/library/azure/dn790525.aspx)。

         PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
           <common headers>
           {
             "location": "China North",
             "tags": {
               "tagname1": "tagvalue1"
             }
           }

3. 创建新的资源组部署。提供你的订阅 ID、要部署的资源组的名称、部署的名称以及模板的位置。有关模板文件的信息，请参阅[参数文件](#parameter-file)。有关使用 REST API 创建资源组的详细信息，请参阅[创建模板部署](https://msdn.microsoft.com/zh-cn/library/azure/dn790564.aspx)。

         PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
            <common headers>
            {
              "properties": {
                "templateLink": {
                  "uri": "http://mystorageaccount.blob.core.chinacloudapi.cn/templates/template.json",
                  "contentVersion": "1.0.0.0",
                },
                "mode": "Incremental",
                "parametersLink": {
                  "uri": "http://mystorageaccount.blob.core.chinacloudapi.cn/templates/parameters.json",
                  "contentVersion": "1.0.0.0",
                }
              }
            }

4. 获取模板部署的状态。有关详细信息，请参阅[获取有关模板部署的信息](https://msdn.microsoft.com/zh-cn/library/azure/dn790565.aspx)。

         GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
           <common headers>

## 参数文件

如果要在部署期间使用参数文件将参数值传递给模板，你需要使用类似于下例的格式创建一个 JSON 文件。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "webSiteName": {
                "value": "ExampleSite"
            },
            "webSiteHostingPlanName": {
                "value": "DefaultPlan"
            },
            "webSiteLocation": {
                "value": "China North"
            }
       }
    }

参数文件的大小不能超过 64 KB。

## 后续步骤
- 有关通过.NET 客户端库部署资源的示例，请参阅[使用 .NET 库和模板部署资源](/documentation/articles/arm-template-deployment)
- 有关进一步了解部署应用程序的示例，请参阅[按可预见的方式在 Azure 中预配和部署微服务](/documentation/articles/app-service-web/app-service-deploy-complex-application-predictably)
- 若要了解 Azure 资源管理器模板的区段，请参阅[创作模板](/documentation/articles/resource-group-authoring-templates)。
- 有关可在 Azure 资源管理器模板中使用的函数列表，请参阅[模板函数](/documentation/articles/resource-group-template-functions)。

 

<!---HONumber=71-->