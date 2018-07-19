---
title: 用于实现云一致性的 Azure 资源管理器模板 | Microsoft Docs
description: 开发用于实现云一致性的 Azure 资源管理器模板。 创建适用于 Azure Stack 的新模板或更新现有模板。
services: azure-resource-manager
documentationcenter: na
author: marcvaneijk
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/05/2018
ms.author: mavane
ms.openlocfilehash: f1ff151c0b8d89910949d961b732c10901f19293
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38723369"
---
# <a name="develop-azure-resource-manager-templates-for-cloud-consistency"></a>开发用于实现云一致性的 Azure 资源管理器模板

Azure 的主要优势是一致性。 一个位置的开发投入可在另一个位置重复使用。 利用模板可以确保部署在全球 Azure、Azure 主权云和 Azure Stack 等各种环境中保持一致和重复。 若要在各种云中重复使用模板，需要参照本指南的说明，考虑特定于云的依赖关系。

Microsoft 在很多位置提供了面向企业的智能云服务，其中包括：

* 全球 Azure 平台，该平台由全球各区域不断扩大的 Microsoft 托管数据中心的网络提供支持。
* 独立主权云，例如 Azure 德国、Azure 政府和 Azure 中国（Azure 由世纪互联运营）。 主权云提供具有大部分相同的强大功能的一致平台，全球 Azure 客户均有权访问。
* Azure Stack，一种混合云平台，可从组织数据中心提供 Azure 服务。 企业可以在自己的数据中心中设置 Azure Stack，或者使用服务提供商提供的 Azure 服务，在他们的设施（有时称为托管区域）中运行 Azure Stack。

在所有这些云的核心，Azure 资源管理器提供一个 API，用于实现各种用户界面与 Azure 平台的通信。 此 API 提供了强大的基础结构即代码功能。 使用 Azure 资源管理器可以部署和配置 Azure 云平台上提供的任何类型的资源。 只用一个模板即可将完整的应用程序部署和配置为操作结束状态。

![Azure 环境](./media/templates-cloud-consistency/environments.png)

全球 Azure、主权云、托管云以及你的数据中心的云具有一致性，可帮助你从 Azure 资源管理器中受益。 在设置了基于模板的资源部署和配置后，就可以在这些云中重复使用部署投入。

然而，即使全球云、主权云、托管云和混合云提供一致的服务，也不是所有云都相同。 因此，可以创建对仅特定云可以提供的功能具有依赖关系的模板。

本指南的其余部分介绍在计划开发新的适用于 Azure Stack 的 Azure 资源管理器模板或更新现有模板时需要考虑的方面。 一般情况下，注意事项应包括以下各项：

* 验证模板中的函数、终结点、服务和其他资源在目标部署位置是否可用。
* 将嵌套模板和配置项目存储在可访问的位置，确保可以跨云访问。
* 使用动态引用，不要使用硬编码链接和元素。
* 请确保使用的模板参数适用于目标云。
* 验证特定于资源的属性在目标云中是否可用。

有关 Azure 资源管理器模板的简介，请参阅[模板部署](resource-group-overview.md#template-deployment)。

## <a name="ensure-template-functions-work"></a>确保模板函数可用

资源管理器模板的基本语法是 JSON。 模板使用 JSON 的超集，通过表达式和函数扩展语法。 模板语言处理器经常更新以支持附加的模板函数。 有关可用模板函数的详细说明，请参阅 [Azure 资源管理器模板函数](resource-group-template-functions.md)。

Azure 资源管理器中引入的新模板函数在主权云或 Azure Stack 中不会立即可用。 要成功部署模板，模板中引用的所有函数都必须在目标云中可用。 

Azure 资源管理器的功能始终会首先引入到全球 Azure。 使用以下 PowerShell 脚本可以验证新引入的模板函数在 Azure Stack 中是否可用： 

1. 克隆 GitHub 存储库：[https://github.com/marcvaneijk/arm-template-functions](https://github.com/marcvaneijk/arm-template-functions)

1. 本地克隆存储库后，使用 PowerShell 连接到目标的 Azure 资源管理器。

1. 导入 psm1 模块并执行 Test-AzureRmTemplateFunctions cmdlet：

  ```powershell
  # Import the module
  Import-module <path to local clone>\AzureRmTemplateFunctions.psm1

  # Execute the Test-AzureRmTemplateFunctions cmdlet
  Test-AzureRmTemplateFunctions -path <path to local clone>
  ```

该脚本部署多个最小化模板，每个模板仅包含唯一的模板函数。 脚本的输出报告受支持的和不可用的模板函数。

## <a name="working-with-linked-artifacts"></a>使用链接项目

模板可以包含对链接项目的引用以及链接到另一个模板的部署资源。 资源管理器在运行时会检索链接模板（也称为嵌套模板）。 模板也可以包含对虚拟机 (VM) 扩展的项目的引用。 在 VM 内运行的 VM 扩展会检索这些项目，以便在目标部署过程中配置 VM 扩展 

以下部分介绍在开发模板过程中，当该模板包含与主要部署模板不相关的项目时云一致性的注意事项。

### <a name="use-nested-templates-across-regions"></a>跨区域使用嵌套模板

模板可以分解为可重复使用的小模板，每个小模板都有特定用途，可以在各种部署方案中重复使用。 要执行部署，请指定一个模板作为主要模板或主模板。 该模板指定要部署的资源，例如虚拟网络、VM 和 Web 应用。 主要模板还可以包含指向另一个模板的链接，这意味着可以嵌套模板。 同样，嵌套模板可以包含指向其他模板的链接。 嵌套深度可达五级。

以下代码展示了 templateLink 参数如何引用嵌套模板：

```json
"resources": [
  {
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
     "type": "Microsoft.Resources/deployments",
     "properties": {
       "mode": "incremental",
       "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/vNet.json",
          "contentVersion":"1.0.0.0"
       }
     }
  }
]
```

Azure 资源管理器在运行时评估主要模板并检索和评估每个嵌套模板。 检索了所有嵌套模板后，将平展模板并开始进一步处理。

### <a name="make-linked-templates-accessible-across-clouds"></a>使链接模板可跨云访问

请考虑所用的任何链接模板的存储位置和方式。 在运行时，Azure 资源管理器会提取任意链接模板，因此需要该模板的直接访问权限。 常见的一种做法是使用 GitHub 存储嵌套模板。 GitHub 存储库可以包含可通过 URL 公开访问的文件。 虽然此方法适用于公有云和主权云，但 Azure Stack 环境可能位于企业网络或断开连接的远程位置，无任何出站 Internet 访问权限。 在这些情况下，Azure 资源管理器无法检索嵌套模板。 

适合跨云部署的更好做法是将链接模板存储在目标云可访问的位置。 理想情况是在持续集成/持续开发 (CI/CD) 管道中维护和部署所有部署项目。 或者，可以将嵌套模板存储在 blob 存储容器中，Azure 资源管理器可从其中检索它们。 

由于每个云上的 blob 存储使用不同的终结点完全限定的域名 (FQDN)，因此请使用两个参数配置具有链接模板位置的模板。 参数可以接受部署时的用户输入。 模板一般由多人创作和共享，因此最佳做法是对这些参数使用标准名称。 命名约定有助于提高模板在区域、云和作者之间的可重用性。

在以下代码中，`_artifactsLocation` 用于指向一个包含与部署相关的所有项目的位置。 请注意，提供的是默认值。 在部署时，如果没有为 `_artifactsLocation` 指定输入值，则使用默认值。 `_artifactsLocationSasToken` 用作 `sasToken` 的输入。 在 `_artifactsLocation` 未受保护的情况下（例如公共 GitHub 存储库），默认值应为空字符串。

```json
"parameters": {
  "_artifactsLocation": {
    "type": "string",
    "metadata": {
      "description": "The base URI where artifacts required by this template are located."
    },
    "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-custom-script-windows/"
  },
  "_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
      "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
  }
}
```

在整个模板中，将基 URI（来自 `_artifactsLocation` 参数）与项目相对路径和 `_artifactsLocationSasToken` 合并可生成链接。 以下代码演示如何使用 uri 模板函数指定嵌套模板的链接：

```json
"resources": [
  {
    "name": "shared",
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri": "[uri(parameters('_artifactsLocation'), concat('nested/vnet.json', parameters('_artifactsLocationSasToken')))]",
        "contentVersion": "1.0.0.0"
      }
    }
  }
]
```

`_artifactsLocation` 参数的默认值通过上述方式使用。 如果需要从其他位置检索链接模板，则参数输入在部署时可用于重写默认值 - 无需更改模板本身。

### <a name="use-artifactslocation-instead-of-hardcoding-links"></a>使用 _artifactsLocation 取代硬编码链接

`_artifactsLocation` 参数中的 URL 除了用于嵌套模板，还可用作部署模板的所有相关项目的基。 某些 VM 扩展包含存储在模板外的脚本的链接。 对于这些扩展，不应对链接进行硬编码。 例如，自定义脚本和 PowerShell DSC 扩展可能链接到 GitHub 上的外部脚本，如下所示： 

```json
"properties": {
  "publisher": "Microsoft.Compute",
  "type": "CustomScriptExtension",
  "typeHandlerVersion": "1.9",
  "autoUpgradeMinorVersion": true,
  "settings": {
    "fileUris": [
      "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
    ]
  }
}
```

对脚本的链接进行硬编码可能会阻止模板成功部署到另一个位置。 在配置 VM 资源期间，VM 内运行的 VM 代理开始下载 VM 扩展中链接的的所有脚本，然后将脚本存储在 VM 的本地磁盘上。 此方法的作用类似于之前在“跨区域使用嵌套模板”部分介绍的嵌套模板链接。

资源管理器在运行时检索嵌套模板。 对于 VM 扩展，由 VM 代理检索任何外部项目。 除了项目检索的发起程序不同，模板定义中的解决方案都是相同的。 将 _artifactsLocation 参数与存储所有项目（包括 VM 扩展脚本）的基路径的默认值和 sasToken 的输入的 `_artifactsLocationSasToken` 参数结合使用。

```json
"parameters": {
  "_artifactsLocation": {
    "type": "string",
    "metadata": {
      "description": "The base URI where artifacts required by this template are located."
    },
    "defaultValue": "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/"
  },
  "_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
      "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
  }
}
```

要构造项目的绝对 URI，首选方法是使用 uri 模板函数，而不是 concat 模板函数。 通过将 VM 扩展中经过硬编码的脚本链接替换为 uri 模板函数，可配置模板中的此功能以实现云一致性。

```json
"properties": {
  "publisher": "Microsoft.Compute",
  "type": "CustomScriptExtension",
  "typeHandlerVersion": "1.9",
  "autoUpgradeMinorVersion": true,
  "settings": {
    "fileUris": [
      "[uri(parameters('_artifactsLocation'), concat('scripts/configure-music-app.ps1', parameters('_artifactsLocationSasToken')))]"
    ]
  }
}
```

使用此方法，包括配置脚本在内的所有部署项目均可存储在模板本身所在的位置。 若要更改所有链接的位置，只需为 _artifactsLocation 参数指定其他基 URL。

## <a name="factor-in-differing-regional-capabilities"></a>区分区域功能的因素

由于敏捷开发以及各种更新和新服务不断引入 Azure，[区域可能在服务和更新的可用性方面有所差异](https://azure.microsoft.com/regions/services/)。 经过严格的内部测试之后，通常向参与验证计划的小范围受众客户推介新服务或现有服务的更新。 客户验证成功后，就会在部分 Azure 区域中提供服务或更新，然后引入到更多区域，推广到主权云，甚至还可能提供给 Azure Stack 的客户。

明白了 Azure 区域和云在其可用的服务方面可能有所差异，你就可以做出一些模板相关的积极决策。 建议首先开始检查云的可用资源提供程序。 通过资源提供程序可以了解到可用于 Azure 服务的一系列资源和操作。

模板部署和配置资源。 资源类型由资源提供程序提供。 例如，计算资源提供程序 (Microsoft.Compute) 提供多个资源类型，例如 virtualMachines 和 availabilitySets。 每个资源提供程序均可向常见协定定义的 Azure 资源管理器提供一个 API，从而可以跨所有资源提供程序实现一致、统一的创作体验。 但全球 Azure 中可用的资源提供程序在主权云或 Azure Stack 区域中可能不可用。

![资源提供程序](./media/templates-cloud-consistency/resource-providers.png) 

要验证资源提供程序在给定云中是否可用，请在 Azure 命令行界面 ([CLI](/cli/azure/install-azure-cli)) 中运行以下脚本：

```azurecli-interactive
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

此外还可以使用以下 PowerShell cmdlet 查看可用的资源提供程序：

```azurepowershell-interactive
Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

### <a name="verify-the-version-of-all-resource-types"></a>验证所有资源类型的版本

虽然所有资源类型都有一组共用的属性，但每个资源也有自己特定的属性。 有时会通过新的 API 版本向现有资源类型添加新功能和相关属性。 模板中的资源有自己的 API 版本属性 - `apiVersion`。 利用此版本控制可确保模板中的现有资源配置不受平台更改的影响。

引入到全球 Azure 中现有资源类型的新 API 版本在所有区域、主权云或者 Azure Stack 中可能不会立即可用。 要查看可用资源提供程序、资源类型和云的 API 版本的列表，可以在 Azure 门户中使用资源浏览器。 在“所有服务”菜单中搜索资源浏览器。 在资源浏览器中展开提供程序节点，返回该云中所有可用的资源提供程序、资源类型和 API 版本。

要在 Azure CLI 中列出给定云中所有资源类型的可用 API 版本，请运行以下脚本：

```azurecli-interactive
az provider list --query "[].{namespace:namespace, resourceType:resourceType[]}"
```

还可以使用以下 PowerShell cmdlet：

```azurepowershell-interactive
Get-AzureRmResourceProvider | select-object ProviderNamespace -ExpandProperty ResourceTypes | ft ProviderNamespace, ResourceTypeName, ApiVersions
```

### <a name="refer-to-resource-locations-with-a-parameter"></a>使用参数引用资源位置

模板始终部署到驻留在区域中的资源组。 除了部署本身，模板中的每个资源还具有位置属性，可用于指定要部署的区域。 若要开发用于实现云一致性的模板，则需要一种动态方式来引用资源位置，因为每个 Azure Stack 可以包含唯一的位置名称。 资源通常部署在同一区域中作为资源组，但为了支持跨区域应用程序可用性等方案，可以跨区域分布资源。

在模板中指定资源属性时，虽然可以对区域名称进行硬编码，但此方法不保证可以将模板部署到其他 Azure Stack 环境，因为那里很可能不存在区域名称。

为了适应不同的区域，请将输入参数位置添加到具有默认值的模板。 如果在部署期间未指定任何值，则使用默认值。 

模板函数 `[resourceGroup()]` 返回一个对象，其中包含以下键值对：

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

通过引用输入参数的 defaultValue 中对象的位置键，Azure 资源管理器在运行时将 `[resourceGroup().location]` 模板函数替换为模板部署到的资源组的位置。

```json
"parameters": {
  "location": {
    "type": "string",
    "metadata": {
      "description": "Location the resources will be deployed to."
    },
    "defaultValue": "[resourceGroup().location]"
  }
},
"resources": [
  {
    "name": "storageaccount1",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2015-06-15",
    "location": "[parameters('location')]",
    ...
```

使用此模板函数，可以向任意云部署模板，且不必提前知悉区域名称。 此外，模板中特定资源的位置可能不同于资源组位置。 在这种情况下，可以通过对该特定资源使用其他输入参数来进行配置，同时在同一模板中的其他资源仍然使用初始位置输入参数。

### <a name="track-versions-using-api-profiles"></a>使用 API 配置文件跟踪版本

跟踪所有可用资源提供程序和 Azure Stack 中存在的相关 API 版本非常具有挑战性。 例如，在撰写本文时，Azure 中 Microsoft.Compute/availabilitySets 的最新 API 版本为 `2018-04-01`，而 Azure 和 Azure Stack 的通用 API 版本为 `2016-03-30`。 在所有 Azure 和 Azure Stack 位置之间共享的 Microsoft.Storage/storageAccounts 的通用 API 版本为 `2016-01-01`，而在 Azure 中的最新 API 版本为 `2018-02-01`。

为此，资源管理器在模板中引入了 API 配置文件的概念。 使用 API 配置文件，模板中的每个资源都配置了 `apiVersion` 元素，用于描述该特定资源的 API 版本。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location the resources will be deployed to."
            },
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "name": "myavailabilityset",
            "type": "Microsoft.Compute/availabilitySets",
            "apiVersion": "2016-03-30",
            "location": "[parameters('location')]",
            "properties": {
                "platformFaultDomainCount": 2,
                "platformUpdateDomainCount": 2
            }
        }
    ],
    "outputs": {}
}
```

API 配置文件版本充当 Azure 和 Azure Stack 通用的每种资源类型的单个 API 版本的别名。 不应为模板中的每种资源指定 API 版本，而是仅在称为 `apiProfile` 的新根元素中指定 API 配置文件版本，并省略各个资源的 `apiVersion` 元素。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "apiProfile": "2018–03-01-hybrid",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location the resources will be deployed to."
            },
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "name": "myavailabilityset",
            "type": "Microsoft.Compute/availabilitySets",
            "location": "[parameters('location')]",
            "properties": {
                "platformFaultDomainCount": 2,
                "platformUpdateDomainCount": 2
            }
        }
    ],
    "outputs": {}
}
```

API 配置文件可确保 API 版本可跨位置使用，因此不需要手动验证 apiVersions 在特定位置中是否可用。 为了确保 Azure Stack 环境中存在 API 配置文件引用的 API 版本，Azure Stack 操作员必须根据支持策略将解决方案保持最新。 如果系统超过六个月未更新，会被视为不合规，必须更新环境。

模板中 API 配置文件不是必需元素。 即使添加该元素，它也将仅用于未指定 `apiVersion` 的资源。 此元素允许逐步更改，但不要求对现有模板进行任何更改。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "apiProfile": "2018–03-01-hybrid",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location the resources will be deployed to."
            },
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "name": "myavailabilityset",
            "type": "Microsoft.Compute/availabilitySets",
            "location": "[parameters('location')]",
            "properties": {
                "platformFaultDomainCount": 2,
                "platformUpdateDomainCount": 2
            }
        }
    ],
    "outputs": {}
}
```

## <a name="check-endpoint-references"></a>检查终结点引用

资源可以具有对平台上其他服务的引用。 例如，公共 IP 可以分配获得公共 DNS 名称。 公有云、主权云和 Azure Stack 解决方案有自己不同的终结点命名空间。 在大多数情况下，资源仅需要一个前缀作为模板中的输入。 在运行时，Azure 资源管理器将终结点值追加到其中。 某些终结点值需要在模板中显式指定。 

> [!NOTE]
> 为了开发用于实现一致性的模板，请勿对终结点命名空间进行硬编码。

以下两个示例是在创建资源时需要显式指定的常见终结点命名空间：

* 存储帐户（blob、队列、表和文件）
* 数据库和 Redis 缓存的连接字符串

终结点命名空间还可在模板输出中用作部署完成时发送给用户的信息。 以下为常见示例：

* 存储帐户（blob、队列、表和文件）
* 连接字符串（MySql、SQLServer、SQLAzure、Custom、NotificationHub、ServiceBus、EventHub、ApiHub、DocDb、RedisCache、PostgreSQL）
* 流量管理器
* 公共 IP 地址的 domainNameLabel
* 云服务

一般情况下，请避免在模板中使用硬编码终结点。 最佳做法是使用引用模板函数动态检索终结点。 例如，最常进行硬编码的终结点是存储帐户的终结点命名空间。 每个存储帐户均有唯一的 FQDN，它通过连接存储帐户的名称与终结点命名空间来构造。 名为 mystorageaccount1 的 blob 存储帐户会因为云的不同而产生不同的 FQDN：

* 在全球 Azure 云上创建时会产生 mystorageaccount1.blob.core.windows.net。
* 在 Azure 中国云中创建时会产生 mystorageaccount1.blob.core.chinacloudapi.cn。

以下引用模板函数从存储资源提供程序中检索终结点命名空间：

```json
"diskUri":"[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, 'container/myosdisk.vhd')]"
```

通过将存储帐户终结点的硬编码值替换为 `reference` 模板函数，可以使用同一个模板成功部署到不同环境，无需对终结点引用进行任何更改。

### <a name="refer-to-existing-resources-by-unique-id"></a>通过唯一 ID 引用现有资源

还可以从同一或另一个资源组、在同一或另一订阅中以及在同一云的同一租户中引用现有资源。 要检索资源属性，必须使用该资源本身的唯一标识符。 如以下代码所示，`resourceId` 模板函数检索 SQL Server 等资源的唯一 ID： 

```json
"outputs": {
  "resourceId":{
    "type": "string",
    "value": "[resourceId('otherResourceGroup', 'Microsoft.Sql/servers', parameters('serverName'))]"
  }
}
```

然后，可以使用 `reference` 模板函数中的 `resourceId` 函数检索数据库的属性。 返回对象包含保留完整终结点值的 `fullyQualifiedDomainName` 属性。 该值在运行时检索，并提供特定于云环境的终结点命名空间。 若要在不对终结点命名空间硬编码的情况下定义连接字符串，可以直接引用连接字符串中返回对象的属性，如下所示：

```json
"[concat('Server=tcp:', reference(resourceId('sql', 'Microsoft.Sql/servers', parameters('test')), '2015-05-01-preview').fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('database'),';User ID=', parameters('username'), ';Password=', parameters('pass'), ';Encrypt=True;')]"
```

## <a name="consider-resource-properties"></a>考虑资源属性

Azure Stack 环境中的特定资源具有独特属性，必须在模板中考虑这些属性。

### <a name="ensure-vm-images-are-available"></a>确保 VM 映像可用

Azure 提供丰富的精选 VM 映像。 这些映像已创建好，准备供 Microsoft 和合作伙伴部署。 这些映像构成平台上 VM 的基础。 但云一致模板应该仅引用可用的参数 - 尤其是可用于全球 Azure、Azure 主权云或 Azure Stack 的 VM 映像的发布服务器、产品和 SKU。

要检索位置中的可用 VM 映像列表，请运行以下 Azure CLI 命令：

```azurecli-interactive
az vm image list -all
```

可以使用 Azure PowerShell cmdlet [Get-AzureRmVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher) 检索同一列表并使用 `-Location` 参数指定想要的位置。 例如：

```azurepowershell-interactive
Get-AzureRmVMImagePublisher -Location "West Europe" | Get-AzureRmVMImageOffer | Get-AzureRmVMImageSku | Get-AzureRMVMImage
```

此命令需要几分钟时间在全球 Azure 云的西欧区域中返回所有可用的映像。

如果向 Azure Stack 提供这些 VM 映像，则会占用所有可用的存储。 为了可以容纳最小的缩放单元，Azure Stack 允许选择所需的映像添加到环境。

以下代码示例演示了在 Azure 资源管理器模板中引用发布服务器、产品和 SKU 的参数的一致方法：

```json
"storageProfile": {
    "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2016-Datacenter",
    "version": "latest"
    }
}
```

### <a name="check-local-vm-sizes"></a>检查本地 VM 大小

为了开发用于实现云一致性的模板，需要确保所有目标环境可提供所需的 VM 大小。 VM 大小是性能特征和功能的分组。 某些 VM 大小取决于 VM 运行的硬件。 例如，如果要部署 GPU 优化的 VM，则运行虚拟机监控程序的硬件需要具有 GPU 硬件。

当 Microsoft 引入了新的 VM 大小，且该大小具有某种硬件依赖关系时，通常会首先在 Azure 云中的一小部分区域提供该 VM 大小。 随后，向其他区域和云提供。 为了确保该 VM 大小在要部署到的每个云中存在，可以使用以下 Azure CLI 命令检索可用的大小：

```azurecli-interactive
az vm list-sizes --location "West Europe"
```

对于 Azure PowerShell，请使用：

```azurepowershell-interactive
Get-AzureRmVMSize -Location "West Europe"
```

如需可用服务的完整列表，请参阅[可用产品(按区域)](https://azure.microsoft.com/global-infrastructure/services/?cdn=disable)。

### <a name="check-use-of-azure-managed-disks-in-azure-stack"></a>检查 Azure Stack 中 Azure 托管磁盘的使用情况

托管磁盘管理 Azure 租户的存储。 在部署 VM 时，可以使用使用托管磁盘隐式执行这些操作，而不是显式创建存储帐户并指定虚拟硬盘 (VHD) 的 URI。 托管磁盘通过将同一可用性集的 VM 中的所有磁盘都放置在不同的存储单元中来提高可用性。 此外，现有的 VHD 可从标准转换为高级存储，显著减少了停机时间。

虽然托管磁盘已在 Azure Stack 的路线图上，但它们当前不受支持。 到它们可受支持时，你可以在 VM 资源的模板中使用 `vhd` 元素显式指定 VHD，以此方式开发适用于 Azure Stack 的云一致模板，如下所示：

```json
"storageProfile": {
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "[parameters('windowsOSVersion')]",
    "version": "latest"
  },
  "osDisk": {
    "name": "osdisk",
    "vhd": {
      "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
    },
    "caching": "ReadWrite",
    "createOption": "FromImage"
  }
}
```

与此相反，要在模板中指定托管磁盘配置，请从磁盘配置删除 `vhd` 元素。

```json
"storageProfile": {
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "[parameters('windowsOSVersion')]",
    "version": "latest"
  },
  "osDisk": {
    "caching": "ReadWrite",
    "createOption": "FromImage"
  }
}
```

相同的更改也适用于[数据磁盘](../virtual-machines/windows/using-managed-disks-template-deployments.md)。

### <a name="verify-that-vm-extensions-are-available-in-azure-stack"></a>验证 VM 扩展在 Azure Stack 中是否使用

云一致性的另一个注意事项是使用[虚拟机扩展](../virtual-machines/windows/extensions-features.md)配置 VM 内的资源。 并非所有的 VM 扩展在 Azure Stack 均可用 模板可指定专用于 VM 扩展的资源，从而在模板内创建依赖关系和条件。

例如，如果要配置运行 Microsoft SQL Server 的 VM，VM 扩展可以配置 SQL Server 作为模板部署的一部分。 如果部署模板也包含配置为在运行 SQL Server 的 VM 上创建数据库的应用程序服务器，想象一下会发生什么情况。 除了使用应用程序服务器的 VM 扩展外，还可以在成功返回 SQL Server VM 扩展资源时配置应用程序服务器的依赖关系。 此方法可确保在指示应用程序服务器创建数据库时，运行 SQL Server 的 VM 已配置并且可用。

通过模板的声明性方法可以定义资源的最终状态及其相互依赖的关系，同时平台可以处理依赖关系所需的逻辑。

#### <a name="check-that-vm-extensions-are-available"></a>检查 VM 扩展是否可用

存在很多类型的 VM 扩展。 在开发用于实现云一致性的模板时，请务必仅使用在模板的所有目标区域均可用的扩展。

要检索可用于特定区域（在本示例中为 `myLocation`）的 VM 扩展列表，请运行以下 Azure CLI 命令：

```azurecli-interactive
az vm extension image list --location myLocation
```

此外还可执行 Azure PowerShell [Get-AzureRmVmImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher) cmdlet，并使用 `-Location` 指定虚拟机映像的位置。 例如：

```azurepowershell-interactive
Get-AzureRmVmImagePublisher -Location myLocation | Get-AzureRmVMExtensionImageType | Get-AzureRmVMExtensionImage | Select Type, Version
```

#### <a name="ensure-that-versions-are-available"></a>请确保该版本可用

由于 VM 扩展是第一方资源管理器资源，因此它们具有自己的 API 版本。 如以下代码所示，VM 扩展类型是 Microsoft.Compute 资源提供程序中的嵌套资源。

```json
{
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "myExtension",
    "location": "[parameters('location')]",
    ...
```

VM 扩展资源的 API 版本必须存在于你模板中计划的所有目标位置。 位置依赖关系的作用类似于之前在“验证所有资源类型的版本”部分讨论的资源提供程序 API 版本的可用性。

要检索 VM 扩展资源的可用 API 版本列表，请将 [Get-AzureRmResourceProvider](/powershell/module/azurerm.resources/get-azurermresourceprovider) cmdlet 与 Microsoft.Compute 资源提供程序结合使用，如下所示：

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachines/extensions"}
```

在虚拟机规模集中还可以使用 VM 扩展。 适用相同的位置条件。 为了开发用于实现云一致性的模板，请确保 API 版本在计划部署到的所有位置中均可用。 要检索规模集的 VM 扩展资源的 API 版本，请使用与之前相同的 cmdlet，但要指定虚拟机规模集资源类型，如下所示：

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachineScaleSets/extensions"}
```

每个特定的扩展也会进行版本控制。 此版本在 VM 扩展的 `typeHandlerVersion` 属性中显示。 请确保在模板的 VM 扩展的 `typeHandlerVersion` 元素中指定的版本在计划部署模板的位置中可用。 例如，以下代码指定版本 1.7：

```json
{
    "name": "MyCustomScriptExtension",
    "type": "extensions",
    "apiVersion": "2016-03-30",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
    ],
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        ...   
```

要检索特定 VM 扩展的可用版本列表，请使用 [Get AzureRmVMExtensionImage](/powershell/module/azurerm.compute/get-azurermvmextensionimage) cmdlet。 以下示例从 myLocation 检索 PowerShell DSC（所需状态配置）VM 扩展的可用版本：

```azurepowershell-interactive
Get-AzureRmVMExtensionImage -Location myLocation -PublisherName Microsoft.PowerShell -Type DSC | FT
```

要获取发布者列表，请使用 [Get-AzureRmVmImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher) 命令。 要请求类型，请使用 [Get AzureRmVMExtensionImageType](/powershell/module/azurerm.compute/get-azurermvmextensionimagetype) 命令。

## <a name="tips-for-testing-and-automation"></a>用于测试和自动化的提示

在创作模板时跟踪所有相关的设置、功能和限制是一项挑战。 常见方法是针对单个云开发和测试模板，然后再针对其他位置。 然而，在创作过程中越早执行测试，开发团队就会进行越少的故障排除和代码重写。 由于位置依赖关系而失败的部署在故障排除时非常耗时。 因此我们建议在创作周期中尽早执行自动测试。 从根本上讲，你需要使用更少的开发时间和更少的资源，云一致的项目就会变得更有价值。

下图展示了团队使用集成开发环境 (IDE) 的开发过程的典型示例。 在时间线中的不同阶段执行不同的测试类型。 在这里，两名开发者正在处理同一解决方案，但这种情况同样适用于单个开发者或一个大型团队。 每个开发者通常会创建中央存储库的本地副本，这样每个人都可以处理本地副本，并且不会影响可能使用同一文件的其他用户。

![工作流](./media/templates-cloud-consistency/workflow.png) 

请考虑以下用于测试和自动化的提示：

* 务必使用测试工具。 例如，Visual Studio Code 和 Visual Studio 包括 IntelliSense 以及可帮助你验证模板的其他功能。
* 要在本地 IDE 上进行开发期间提高代码质量，请通过单元测试和集成测试执行静态代码分析。 
* 为了在初始开发期间获得更好的体验，单元测试和集成测试应仅在发现问题时发出警告，然后继续进行测试。 这样一来，可以确定要解决的问题并设置更改的优先顺序，也称为测试驱动的部署 (TDD)。
* 请注意，在未连接到 Azure 资源管理器的情况下也可执行某些测试。 其他测试（如测试模板部署）需要资源管理器执行的某些操作无法脱机完成。
* 针对验证 API 测试部署模板不等于实际的部署。 此外，即使从本地文件部署模板，资源管理器也会直接检索对模板中嵌套模板的任何引用，并且在部署的 VM 内运行的 VM 代理也会检索 VM 扩展引用的项目。

## <a name="next-steps"></a>后续步骤

* [Azure 资源管理器模板注意事项](../azure-stack/user/azure-stack-develop-templates.md)
* [Azure 资源管理器模板的最佳做法](resource-group-authoring-templates.md)
