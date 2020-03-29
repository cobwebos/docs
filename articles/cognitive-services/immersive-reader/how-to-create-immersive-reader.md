---
title: 创建沉浸式阅读器资源
titleSuffix: Azure Cognitive Services
description: 本文将向您展示如何使用自定义子域创建新的沉浸式读取器资源，然后在 Azure 租户中配置 Azure AD。
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: 41efe4592c65ae3cdd85ce1b212554e50691905a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330713"
---
# <a name="create-an-immersive-reader-resource-and-configure-azure-active-directory-authentication"></a>创建沉浸式读取器资源并配置 Azure 活动目录身份验证

在本文中，我们将提供一个脚本，该脚本将创建沉浸式读取器资源并配置 Azure 活动目录 （Azure AD） 身份验证。 每次创建沉浸式读取器资源时，无论是使用此脚本还是在门户中，都必须使用 Azure AD 权限配置它。 此脚本将帮助您。

该脚本旨在一步创建和配置所有必要的沉浸式读取器和 Azure AD 资源。 但是，您也可以为现有的沉浸式读取器资源配置 Azure AD 身份验证（例如，如果恰好已在 Azure 门户中创建了一个身份验证）。

对于某些客户，可能需要创建多个沉浸式读取器资源，用于开发与生产，或者对于服务部署在多个不同区域。 对于这些情况，您可以返回并使用脚本多次创建不同的沉浸式读取器资源，并使用它们配置 Azure AD 权限。

该脚本设计为灵活。 它将首先在订阅中查找现有的沉浸式读取器和 Azure AD 资源，并且仅在它们不存在时才根据需要创建这些资源。 如果这是您第一次创建沉浸式阅读器资源，脚本将执行所需的一切操作。 如果只想使用它为门户中创建的现有沉浸式读取器资源配置 Azure AD，它也将执行此操作。 它还可用于创建和配置多个沉浸式读取器资源。

## <a name="set-up-powershell-environment"></a>设置 PowerShell 环境

1. 首先打开[Azure 云外壳](https://docs.microsoft.com/azure/cloud-shell/overview)。 确保云外壳设置为左上角下拉或键入`pwsh`PowerShell。

1. 复制以下代码段并将其粘贴到 shell 中。

    ```azurepowershell-interactive
    function Create-ImmersiveReaderResource(
        [Parameter(Mandatory=$true, Position=0)] [String] $SubscriptionName,
        [Parameter(Mandatory=$true)] [String] $ResourceName,
        [Parameter(Mandatory=$true)] [String] $ResourceSubdomain,
        [Parameter(Mandatory=$true)] [String] $ResourceSKU,
        [Parameter(Mandatory=$true)] [String] $ResourceLocation,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupName,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupLocation,
        [Parameter(Mandatory=$true)] [String] $AADAppDisplayName="ImmersiveReaderAAD",
        [Parameter(Mandatory=$true)] [String] $AADAppIdentifierUri,
        [Parameter(Mandatory=$true)] [String] $AADAppClientSecret
    )
    {
        $unused = ''
        if (-not [System.Uri]::TryCreate($AADAppIdentifierUri, [System.UriKind]::Absolute, [ref] $unused)) {
            throw "Error: AADAppIdentifierUri must be a valid URI"
        }

        Write-Host "Setting the active subscription to '$SubscriptionName'"
        $subscriptionExists = Get-AzSubscription -SubscriptionName $SubscriptionName
        if (-not $subscriptionExists) {
            throw "Error: Subscription does not exist"
        }
        az account set --subscription $SubscriptionName

        $resourceGroupExists = az group exists --name $ResourceGroupName
        if ($resourceGroupExists -eq "false") {
            Write-Host "Resource group does not exist. Creating resource group"
            $groupResult = az group create --name $ResourceGroupName --location $ResourceGroupLocation
            if (-not $groupResult) {
                throw "Error: Failed to create resource group"
            }
            Write-Host "Resource group created successfully"
        }

        # Create an Immersive Reader resource if it doesn't already exist
        $resourceId = az cognitiveservices account show --resource-group $ResourceGroupName --name $ResourceName --query "id" -o tsv
        if (-not $resourceId) {
            Write-Host "Creating the new Immersive Reader resource '$ResourceName' (SKU '$ResourceSKU') in '$ResourceLocation' with subdomain '$ResourceSubdomain'"
            $resourceId = az cognitiveservices account create `
                            --name $ResourceName `
                            --resource-group $ResourceGroupName `
                            --kind ImmersiveReader `
                            --sku $ResourceSKU `
                            --location $ResourceLocation `
                            --custom-domain $ResourceSubdomain `
                            --query "id" `
                            -o tsv

            if (-not $resourceId) {
                throw "Error: Failed to create Immersive Reader resource"
            }
            Write-Host "Immersive Reader resource created successfully"
        }

        # Create an Azure Active Directory app if it doesn't already exist
        $clientId = az ad app show --id $AADAppIdentifierUri --query "appId" -o tsv
        if (-not $clientId) {
            Write-Host "Creating new Azure Active Directory app"
            $clientId = az ad app create --password $AADAppClientSecret --display-name $AADAppDisplayName --identifier-uris $AADAppIdentifierUri --query "appId" -o tsv

            if (-not $clientId) {
                throw "Error: Failed to create Azure Active Directory app"
            }
            Write-Host "Azure Active Directory app created successfully"
        }

        # Create a service principal if it doesn't already exist
        $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv
        if (-not $principalId) {
            Write-Host "Creating new service principal"
            az ad sp create --id $clientId | Out-Null
            $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv

            if (-not $principalId) {
                throw "Error: Failed to create new service principal"
            }
            Write-Host "New service principal created successfully"
        }

        # Sleep for 5 seconds to allow the new service principal to propagate
        Write-Host "Sleeping for 5 seconds"
        Start-Sleep -Seconds 5

        Write-Host "Granting service principal access to the newly created Immersive Reader resource"
        $accessResult = az role assignment create --assignee $principalId --scope $resourceId --role "Cognitive Services User"
        if (-not $accessResult) {
            throw "Error: Failed to grant service principal access"
        }
        Write-Host "Service principal access granted successfully"

        # Grab the tenant ID, which is needed when obtaining an Azure AD token
        $tenantId = az account show --query "tenantId" -o tsv

        # Collect the information needed to obtain an Azure AD token into one object
        $result = @{}
        $result.TenantId = $tenantId
        $result.ClientId = $clientId
        $result.ClientSecret = $AADAppClientSecret
        $result.Subdomain = $ResourceSubdomain

        Write-Host "Success! " -ForegroundColor Green -NoNewline
        Write-Host "Save the following JSON object to a text file for future reference:"
        Write-Output (ConvertTo-Json $result)
    }
    ```

1. 运行函数`Create-ImmersiveReaderResource`，根据需要提供参数。

    ```azurepowershell-interactive
    Create-ImmersiveReaderResource
      -SubscriptionName '<SUBSCRIPTION_NAME>' `
      -ResourceName '<RESOURCE_NAME>' `
      -ResourceSubdomain '<RESOURCE_SUBDOMAIN>' `
      -ResourceSKU '<RESOURCE_SKU>' `
      -ResourceLocation '<RESOURCE_LOCATION>' `
      -ResourceGroupName '<RESOURCE_GROUP_NAME>' `
      -ResourceGroupLocation '<RESOURCE_GROUP_LOCATION>' `
      -AADAppDisplayName '<AAD_APP_DISPLAY_NAME>' `
      -AADAppIdentifierUri '<AAD_APP_IDENTIFIER_URI>' `
      -AADAppClientSecret '<AAD_APP_CLIENT_SECRET>'
    ```

    | 参数 | 注释 |
    | --- | --- |
    | SubscriptionName |用于沉浸式读取器资源的 Azure 订阅的名称。 您必须具有订阅才能创建资源。 |
    | ResourceName |  必须为字母数字，并且可能包含"-"，只要"-"不是第一个或最后一个字符。 长度不得超过 63 个字符。|
    | 资源子域 |沉浸式阅读器资源需要自定义子域。 SDK 在调用沉浸式读取器服务以启动读取器时使用子域。 子域必须全局唯一。 子域必须是字母数字，并且可能包含"-"，只要"-"不是第一个或最后一个字符。 长度不得超过 63 个字符。 如果资源已存在，则此参数是可选的。 |
    | 资源库 |选项： `S0`. 请访问我们的[认知服务定价页面](https://azure.microsoft.com/pricing/details/cognitive-services/immersive-reader/)，了解有关每个可用的 SKU 的更多信息。 如果资源已存在，则此参数是可选的。 |
    | ResourceLocation |选项： `eastus` `eastus2`、 `southcentralus` `westus`、 `westus2` `australiaeast`、 `southeastasia` `centralindia`、 `japaneast` `northeurope`、 `uksouth` `westeurope`、 、 、 、 、 、 、 、 、 、 如果资源已存在，则此参数是可选的。 |
    | ResourceGroupName |资源在订阅中的资源组中创建。 提供现有资源组的名称。 如果资源组不存在，将创建具有此名称的新资源组。 |
    | ResourceGroupLocation |如果资源组不存在，则需要提供一个位置来创建该组。 要查找位置列表，运行`az account list-locations`。 使用返回的结果*的名称*属性（无空格）。 如果资源组已存在，则此参数是可选的。 |
    | AADApp显示名称 |Azure 活动目录应用程序显示名称。 如果未找到现有的 Azure AD 应用程序，将创建具有此名称的新应用程序。 如果 Azure AD 应用程序已存在，则此参数是可选的。 |
    | AADApp标识符 |Azure AD 应用的 URI。 如果未找到现有的 Azure AD 应用，将创建具有此 URI 的新应用。 例如，`https://immersivereaderaad-mycompany` 。 |
    | AADAppClient秘密 |您创建的密码，稍后将用于获取令牌以启动沉浸式读取器时进行身份验证。 密码必须至少为 16 个字符长，至少包含 1 个特殊字符，并且至少包含 1 个数字字符。 |

1. 将 JSON 输出复制到文本文件中，供以后使用。 输出应如下所示。

    ```json
    {
      "TenantId": "...",
      "ClientId": "...",
      "ClientSecret": "...",
      "Subdomain": "..."
    }
    ```

## <a name="next-steps"></a>后续步骤

* 查看 [Node.js 快速入门](./quickstart-nodejs.md)，了解通过 Node.js 使用沉浸式阅读器 SDK 还可以做什么
* 查看 [Python 教程](./tutorial-python.md)，了解通过 Python 使用沉浸式阅读器 SDK 还可以做什么
* 查看[Swift 教程](./tutorial-ios-picture-immersive-reader.md)，了解使用 Swift 的沉浸式读取器 SDK 还可以执行哪些操作
* 浏览[沉浸式阅读器 SDK ](https://github.com/microsoft/immersive-reader-sdk)和[沉浸式阅读器 SDK 参考](./reference.md)




