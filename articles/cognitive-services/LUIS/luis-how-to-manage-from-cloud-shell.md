---
title: 使用情况数据 - Cloud Shell
titleSuffix: Language Understanding - Azure Cognitive Services
description: 了解如何在 Azure Cloud Shell 中获取 LUIS 的终结点命中次数使用情况信息。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/18/2018
ms.author: diberry
ms.openlocfilehash: 703332ece0208856bfbedb852b4b1e985d157dc9
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/19/2018
ms.locfileid: "53605937"
---
# <a name="usage-data-for-luis-service-from-azure-cloud-shell"></a>从 Azure Cloud Shell 获取 LUIS 服务的使用情况数据

了解如何在 Azure Cloud Shell 中获取 LUIS 的终结点命中次数使用情况信息。

通过 Azure 门户可使用 PowerShell cmdlet 处理 LUIS 资源。 

使用这些 cmdlet 可以[创建](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/new-azurermcognitiveservicesaccount?view=azurermps-6.0.0) LUIS 订阅、获取有关订阅的信息（包括[使用情况](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/get-azurermcognitiveservicesaccountusage?view=azurermps-6.0.0)）和[删除](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/remove-azurermcognitiveservicesaccount?view=azurermps-6.0.0)订阅。 

## <a name="cloud-shell-storage-account-and-authentication"></a>Cloud Shell 存储帐户和身份验证

若要在 Azure 门户 [cloud shell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell) 中使用 PowerShell，需要具有 Azure 存储帐户。 如果没有[存储帐户](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage)，系统将提示你创建一个。 存储帐户允许你在 Cloud Shell 中保存PowerShell 脚本。  

你还需要在 cloud shell 中向 Azure 进行身份验证才能访问任何资源。 

拥有存储帐户并进行身份验证后，便可以运行 PowerShell cmdlet。

## <a name="open-cloud-shell"></a>打开 Cloud Shell

使用 Azure门户 cloud shell 时，你始终使用最新的 PowerShell 版本。 

使用“启动 Cloud Shell”按钮打开 Cloud Shell 或使用 [https://shell.azure.com](https://shell.azure.com) 打开浏览器。 选择 Power Shell 作为环境。 如果没有 Azure 存储帐户，则需创建一个。 

<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><image src="https://shell.azure.com/images/launchcloudshell.png" alt="Start powershell" /></a>

## <a name="luis-endpoint-usage-information"></a>LUIS 终结点使用情况信息

PowerShell 6.x cmdlet `Get-AzureRmCognitiveServicesAccountUsage` 提供 Microsoft 认知服务（包括 LUIS）的使用情况信息。 [Get-AzureRmCognitiveServicesAccountUsage](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/get-azurermcognitiveservicesaccountusage?view=azurermps-6.0.0) 需要服务的资源组和资源名称。 

命令语法为：

```powershell
Get-AzureRmCognitiveServicesAccountUsage -ResourceGroupName my-resource-group -Name my-luis-service-name
```

在以下示例中，资源组名称为 `luis-westus-rg`，LUIS 服务订阅名称为 `luis-westus-1`。 创建 LUIS 服务时，将选择这两个名称。 

该 cmdlet 返回于 6 月 7 日结束的 30 天期限内使用的 16 个终结点命中（共 10,000 个终结点命中）的使用情况信息：

```powershell
CurrentValue  : 16
Name          : LUIS.Calls
Limit         : 10000
Status        : Included
Unit          : Count
QuotaPeriod   : 30.00:00:00
NextResetTime : 2018-06-07T18:28:52Z
```

在与 cloud shell 关联的 Azure 存储帐户中将该命令保存为 PowerShell文件 *.ps1，并随时执行。 

![从存储运行脚本](./media/luis-how-to-manage-from-powershell/run-script-from-storage.png)

将脚本保存在云驱动器上后，即可从 Azure 手机应用的 cloud shell 中运行 PowerShell 脚本。 

![从手机应用的存储中运行脚本](./media/luis-how-to-manage-from-powershell/phone-app.png)