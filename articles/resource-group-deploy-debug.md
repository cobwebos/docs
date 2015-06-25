<properties
   pageTitle="Azure 中的资源组部署疑难解答"
   description="介绍在 Azure 中部署资源时遇到的常见问题，并说明如何使用 Azure 门户、适用于 Mac、Linux 和 Windows 的 Azure 命令行界面 (Azure CLI) 和 PowerShell 来检查部署及检测问题。"
   services="virtual-machines"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.date="04/25/2015"
   wacn.date=""/>

# Azure 中的资源组部署疑难解答

提前检查一些要素可以更轻松地防止部署错误，但部署有时会因许多原因而失败。本文档介绍了一些用于防止简单错误、下载模板文件和检查部署日志的工具和操作。此外，还介绍了在部署日志中查看失败原因时要考虑的主要方面。

## 与 Azure 交互的有用工具
AzureResourceManager 模块包含一些 cmdlet，从命令行处理 Azure 资源时，你可以使用这些 cmdlet 来收集用于帮助完成工作的信息。Azure 资源组模板为 JSON 文件，而 Azure 资源管理 API 接受并返回 JSON，因此 JSON 分析工具可用来帮助你浏览资源的相关信息，以及设计模板和模板参数文件或与之交互。

### Mac、Linux 和 Windows 工具
如果你使用适用于 Mac、Linux 和 Windows 的 Azure 命令行界面，则可能熟悉标准下载工具（例如 **[curl](http://curl.haxx.se/)** 和 **[wget](https://www.gnu.org/software/wget/)** 或 **[Resty](https://github.com/beders/Resty)**）和 JSON 实用工具（例如 **[jq](http://stedolan.github.io/jq/download/)**、**[jsawk](https://github.com/micha/jsawk)**，以及还能处理 JSON 的语言库）。（其中的许多工具也有针对 Windows 的端口，例如 [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)；事实上，还可以通过多种方法获取 Linux 以及在 Windows 上运行的其他开源软件工具）。

本主题包括一些你可以与 **jq** 配合使用的 Azure CLI 命令，以更有效地准确获取所需的信息。你应该选择熟悉的工具集，以帮助了解 Azure 资源的使用情况。

### Windows PowerShell

Windows PowerShell 提供了一些基本命令用于执行相同的过程。

- 使用 **[Invoke-WebRequest cmdlet](https://technet.microsoft.com/zh-cn/library/hh849901%28v=wps.640%29)** 可下载资源组模板或参数 JSON 文件等文件。
- 使用 **[ConvertFrom-Json](https://technet.microsoft.com/zh-cn/library/hh849898%28v=wps.640%29.aspx)** cmdlet 可将 JSON 字符串转换成自定义对象 ([PSCustomObject](https://msdn.microsoft.com/zh-cn/library/windows/desktop/system.management.automation.pscustomobject%28v=vs.85%29.aspx))，该对象具有 JSON 字符串中每个字段的属性。

## 防止适用于 Mac、Linux 和 Windows 的 Azure CLI 中的错误

Azure CLI 提供多个命令用于帮助防止出错，以及检测发生的问题。

- **azure location list**。此命令获取支持每种资源类型（例如虚拟机的提供程序）的位置。输入资源的位置之前，请使用此命令验证该位置是否支持此资源类型。

    因为位置列表可能很长，而且有多个提供程序，所以你可以先使用工具来检查提供程序和位置，再使用尚未使用的位置。以下脚本使用 **jq** 发现 Azure 虚拟机资源提供程序的可用位置。()

        azure location list --json | jq '.[] | select(.name == "Microsoft.Compute/virtualMachines")'
        {
          "name": "Microsoft.Compute/virtualMachines",
          "location": "China East,China North"
        }

- **azure group template validate <resource group>**。此命令在你使用模板和模板参数之前对其进行验证。输入自定义或库模板，以及您打算使用的模板参数值。此 cmdlet 测试该模板是否内部一致以及参数值的设置是否与该模板匹配。

    以下示例演示如何验证模板和所有必需参数；Azure CLI 将提示你输入必需的参数值。

        azure group template validate \
        > --template-uri "https://contoso.com/templates/azuredeploy.json" \
        > resource-group
        info:    Executing command group template validate
        info:    Supply values for the following parameters
        adminUserName: UserName
        adminPassword: PassWord
        + Initializing template configurations and parameters
        + Validating the template
        info:    group template validate command OK

## 获取信息以修复 Azure CLI 部署问题

- **azure group log show <resource group>**：此命令获取资源组中每个部署的日志中的条目。如果出现问题，则从检查部署日志开始。

        info:    Executing command group log show
        info:    Getting group logs
        data:    ----------
        data:    EventId:              <guid>
        data:    Authorization:
        data:                          action: Microsoft.Network/networkInterfaces/write
        data:                          role:   Subscription Admin
        data:                          scope:  /subscriptions/xxxxxxxxxxx/resourcegroups/templates/
                                               providers/Microsoft.Network/
                                               networkInterfaces/myNic
        data:    ResourceUri:          /subscriptions/xxxxxxxxxxxx/resourcegroups/templates/providers/
                                       Microsoft.Network/networkInterfaces/myNic
        data:    SubscriptionId:       <guid>
        data:    EventTimestamp (UTC): Wed Apr 22 2015 05:53:31 GMT+0000 (UTC)
        data:    OperationName:        Microsoft.Network/networkInterfaces/write
        data:    OperationId:          <guid>
        data:    Status:               Started
        data:    SubStatus:
        data:    Caller:
        data:    CorrelationId:        <guid>
        data:    Description:
        data:    HttpRequest:          clientRequestId: <guid>
                                       clientIpAddress: 000.000.00.000
                                       method:          PUT

        data:    Level:                Informational
        data:    ResourceGroup:        templates
        data:    ResourceProvider:     Microsoft.Network
        data:    EventSource:          Microsoft Resources
        data:    Properties:           requestbody: {"location":"China North","properties
                                       ":{"ipConfigurations":[{"
                                       name":"ipconfig1","properties":{"
                                       privateIPAllocationMethod

                                       ":"Dynamic","publicIPAddress":{"id":"/
                                       subscriptions/
                                       <guid>/
                                       resourceGroups/
                                       templates/providers/Microsoft.Network/
                                       publicIPAddresses/
                                       myPublicIP"},"subnet":{"idThe AzureResourceManager module includes cmdlets that ":"/subscriptions/
                                       <guid>/resourceGroups/templates/
                                       providers/
                                       Microsoft.Network/virtualNetworks/myVNET/subnets/
                                       Subnet-1
                                       "}}}]}}

        Use the **--last-deployment** option to retrieve only the log for the most recent deployment. The following script uses the **--json** option and **jq** to search the log for deployment failures.

        azure group log show templates --json | jq '.[] | select(.status.value == "Failed")'

        {
          "claims": {
            "aud": "https://management.core.chinacloudapi.cn/",
            "iss": "https://sts.chinacloudapi.cn/<guid>/",
            "iat": "1429678549",
            "nbf": "1429678549",
            "exp": "1429682449",
            "ver": "1.0",
            "http://schemas.microsoft.com/identity/claims/tenantid": "<guid>",
            "http://schemas.microsoft.com/identity/claims/objectidentifier": "<guid>",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "ahmet@contoso.partner.onmschina.cn",
            "puid": "XXXXXXXXXXXXXX",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "<hash string>",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "ahmet",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "",
            "name": "Friendly Name",
            "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
            "groups": "<guid>",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "ahmet@contoso.partner.onmschina.cn",
            "appid": "<guid>",
            "appidacr": "0",
            "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
            "http://schemas.microsoft.com/claims/authnclassreference": "1"
          },
          "properties": {},
          "authorization": {
            "action": "Microsoft.Resources/subscriptions/resourcegroups/deployments/write",
            "role": "Subscription Admin",
            "scope": "/subscriptions/<guid>/resourcegroups/templates/deployments/basic-vm-version-0.1"
          },
          "eventChannels": "Operation",
          "eventDataId": "<guid>",
          "correlationId": "<guid>",
          "eventName": {
            "value": "EndRequest",
            "localizedValue": "End request"
          },
          "eventSource": {
            "value": "Microsoft.Resources",
            "localizedValue": "Microsoft Resources"
          },
          "level": "Error",
          "resourceGroupName": "templates",
          "resourceProviderName": {
            "value": "Microsoft.Resources",
            "localizedValue": "Microsoft Resources"
          },
          "resourceUri": "/subscriptions/<guid>/resourcegroups/templates/deployments/basic-vm-version-0.1",
          "operationId": "<guid>",
          "operationName": {
            "value": "Microsoft.Resources/subscriptions/resourcegroups/deployments/write",
            "localizedValue": "Update deployment"
          },
          "status": {
            "value": "Failed",
            "localizedValue": "Failed"
          },
          "subStatus": {},
          "eventTimestamp": "2015-04-22T05:53:40.8150293Z",
          "submissionTimestamp": "2015-04-22T05:54:00.6728843Z",10037FFE8E80BB65
          "subscriptionId": "<guid>"
        }


- **--verbose 和 -vv 选项**：使用 **--verbose** 选项可将模式设为详细，以显示操作在 `stdout` 上经历的步骤。如果完整请求历史记录包括 **--verbose** 所启用的步骤，请使用 **-vv** 选项。这些消息经常提供有关任何失败原因的重要线索。

- **你的 Azure 凭据尚未设置，或已过期**：若要刷新 Azure CLI 会话中的凭据，只需键入 `azure login`。若要获取有关身份验证错误的帮助，请确保[正确配置 Azure CLI](xplat-cli-connect)。

## 防止 Windows PowerShell 中的错误

AzureResourceManager 模块包括帮助您防止错误的 cmdlet。


- **Get-AzureLocation**：此 cmdlet 获取支持每种资源类型的位置。输入资源的位置之前，请使用此 cmdlet 验证该位置是否支持此资源类型。


- **Test-AzureResourceGroupTemplate**：在你使用模板和模板参数之前对其进行测试。输入自定义或库模板，以及您打算使用的模板参数值。此 cmdlet 测试该模板是否内部一致以及参数值的设置是否与该模板匹配。

## 获取信息以修复 Windows PowerShell 中的部署问题

- **Get-AzureResourceGroupLog**：此 cmdlet 获取资源组中每个部署的日志中的条目。如果出现问题，则从检查部署日志开始。

- **详细和调试**：AzureResourceManager 模块中的 cmdlet 调用执行实际工作的 REST API。若要查看 API 返回的消息，请将 $DebugPreference 变量设置为“Continue”，并在您的命令中使用 Verbose 通用参数。这些消息经常提供有关任何失败原因的重要线索。

- **你的 Azure 凭据尚未设置，或已过期**：若要刷新 Windows PowerShell 会话中的凭据，请使用 Add-AzureAccount cmdlet。发布设置文件中的凭据不足以满足 AzureResourceManager 模块中的 cmdlet 的需要。

## 身份验证、订阅、角色和配额问题

可能有一个或多个问题防止部署成功，包含身份验证和授权以及 Azure Active Directory。无论你如何管理 Azure 资源组，用于登录帐户的标识都必须是 Azure Active Directory 对象或服务主体（也称为工作或学校帐户或组织 ID）。

但是 Azure Active Directory 可让你或你的系统管理员非常精确地控制哪些标识可以访问哪些资源。如果你的部署失败，请检查请求本身是否存在身份验证或授权问题的迹象，并检查资源组的部署日志。你可能会发现，当你拥有某些资源的权限时，就没有其他资源的权限。使用 Azure CLI 可以通过 `azure ad` 命令检查 Azure Active Directory 租户和用户。（有关 Azure CLI 命令的完整列表，请参阅[将适用于 Mac、Linux 和 Windows 的 Azure CLI 与 Azure 资源管理配合使用](azure-cli-arm-commands)。）

当部署达到默认配额（可能是根据资源组、订阅、帐户和其他范围指定的）时，你也可能会遇到问题。确认你有可正确部署的资源。有关完整的配额信息，请参阅 [Azure 订阅和服务限制、配额与约束](azure-subscription-service-limits)。


## Azure CLI 和 PowerShell 模式问题

你可能遇到过这种问题：使用资源管理 API 或 Azure 门户时看不到使用服务管理 API 或使用经典门户部署的 Azure 资源。请务必使用用于创建资源的相同管理 API 或门户来管理资源。如果资源消失，请使用其他管理 API 或门户确认资源是否可用。

## Azure 资源提供程序注册问题

资源由资源提供程序管理，你可以启用帐户或订阅来使用特定的提供程序。如果你可以使用某个提供程序，则还必须注册该提供程序才能使用。Azure 门户或正在使用的命令行界面会自动注册大多数提供程序；但非全部。

若要使用 Azure CLI 查看是否已注册提供程序供使用，请使用 `azure provider list` 命令（以下是截断的输出示例）。

        azure provider list
        info:    Executing command provider list
        + Getting ARM registered providers
        data:    Namespace                        Registered
        data:    -------------------------------  -------------
        data:    Microsoft.Compute                Registered
        data:    Microsoft.Network                Registered  
        data:    Microsoft.Storage                Registered
        data:    microsoft.visualstudio           Registered
        data:    Microsoft.Authorization          Registered
        data:    Microsoft.Automation             NotRegistered
        data:    Microsoft.Backup                 NotRegistered
        data:    Microsoft.BizTalkServices        NotRegistered
        data:    Microsoft.Features               Registered
        data:    Microsoft.Search                 NotRegistered
        data:    Microsoft.ServiceBus             NotRegistered
        data:    Microsoft.Sql                    Registered
        info:    provider list command OK

    Again, if you want more information about providers, including their regional availability, type `azure provider list --json`. The following selects only the first one in the list to view:

        azure provider list --json | jq '.[0]'
        {
          "resourceTypes": [
            {
              "apiVersions": [
                "2014-02-14"
              ],
              "locations": [
                "China East",
                "China North",
              ],
              "properties": {},
              "name": "service"
            }
          ],
          "id": "/subscriptions/<guid>/providers/Microsoft.ApiManagement",
          "namespace": "Microsoft.ApiManagement",
          "registrationState": "Registered"
        }


如果提供程序需要注册，请使用 `azure provider register <namespace>` 命令，其中 *namespace* 值来自前面的列表。

## 了解自定义模板的部署何时成功

如果你正在使用自己创建的模板，请务必了解 Azure 资源管理系统会在成功从部署返回所有提供程序时报告部署成功。这意味着已部署所有模板项供你使用。

不过，这不一定表示你的资源组**处于活动状态且可供用户使用**。例如，大多数部署会请求部署下载升级文件、等待其他非模板资源，或者安装复杂的脚本或 Azure 不知道的其他某个可执行活动（因为它不是提供程序正在跟踪的活动）。在这些情况下，可能要在一段时间后，你的资源才可供实际使用。因此，你应该预料到部署状态成功一段时间后，才能使用部署。

不过，创建自定义模板的自定义脚本（例如，使用 [CustomScriptExtension](http://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)），即可防止 Azure 报告部署成功，而此自定义脚本知道如何监视整个部署以了解系统是否准备就绪，并只在用户可以与整个部署交互时才会成功返回。如果你想要确保最后才运行你的扩展，请在模板中使用 **dependsOn** 属性。[此处](https://msdn.microsoft.com/zh-cn/library/azure/dn790564.aspx)提供了一个示例。

## 合并模板

你有时可能需要合并两个模板，或者需要从父级启动子系模板。通过使用主模板内的部署资源来部署子模板，即可实现此目的。


    {
            "name": "instance01",
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2015-01-01",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "https://mystore.blob.chinacloudapi.cn/azurermtemplates/my-child-template.json",
                    "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "storageAccountName": { "value": "[variables('stgAcctName1')]" },
                    "adminUsername": { "value": "[parameters('adminUsername')]" },
                    "adminPassword": { "value": "[parameters('adminPassword')]" }
                }
            }
    }


## 交叉资源组

在很多情况下，你可能希望使用部署模板的当前资源组外部的资源。此行为的最常见情况是在替代资源组中使用存储帐户或虚拟网络。通常需要执行此操作，以便删除包含虚拟机的资源组不会删除 VHD Blob 或多个资源组使用的 VNet。以下示例演示了如何轻松使用外部资源组中的资源：


    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "virtualNetworkName": {
              "type": "string"
          },
          "virtualNetworkResourceGroup": {
              "type": "string"
          },
          "subnet1Name": {
              "type": "string"
          },
          "nicName": {
              "type": "string"
          }
      },
      "variables": {
          "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
          "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
      },
      "resources": [
      {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[parameters('nicName')]",
          "location": "[parameters('location')]",
          "properties": {
              "ipConfigurations": [{
                  "name": "ipconfig1",
                  "properties": {
                      "privateIPAllocationMethod": "Dynamic",
                      "subnet": {
                          "id": "[variables('subnet1Ref')]"
                      }
                  }
              }]
           }
      }]

    }



## 后续步骤

若要掌握如何创建模板，请阅读[创作 Azure 资源管理器模板](resource-group-authoring-templates)，并浏览 [AzureRMTemplates 存储库](https://github.com/azurermtemplates/azurermtemplates)中的可部署示例。**dependsOn** 属性的示例是[使用入站 NAT 规则模板的负载平衡器](https://github.com/azurermtemplates/azurermtemplates/blob/master/101-create-internal-loadbalancer/azuredeploy.json)。

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/
[yah]: http://search.yahoo.com/
[msn]: http://search.msn.com/

<!---HONumber=61-->