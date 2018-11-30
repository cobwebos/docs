---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: e81cdb478a63e1e584aef2c32754bd321d245365
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2018
ms.locfileid: "52440000"
---
## <a name="start-your-powershell-session"></a>启动 PowerShell 会话
首先，需要安装并运行最新的 [Azure PowerShell](https://msdn.microsoft.com/library/mt619274.aspx)。 有关详细信息，请参阅 [如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs)。

> [!NOTE]
> 由于本主题中的示例使用的是 [Azure 资源管理器部署模型](../articles/azure-resource-manager/resource-group-overview.md)，因此示例将使用 [Azure 资源管理器 cmdlet](https://msdn.microsoft.com/library/azure/mt125356.aspx)。 
> 
> 

运行 [Connect-AzureRmAccount](https://docs.microsoft.com/powershell/module/azurerm.profile/connect-azurermaccount) cmdlet，然后便会看到可输入凭据的登录屏幕。 使用与登录 Azure 门户相同的凭据。

    Connect-AzureRmAccount

如果有多个订阅，请使用 [**Set-AzureRmContext**](https://docs.microsoft.com/powershell/module/azurerm.profile/set-azurermcontext) cmdlet 选择 PowerShell 会话应使用的订阅。 若要查看当前 PowerShell 会话正在使用哪个订阅，请运行 [**Get-AzureRmContext**](https://docs.microsoft.com/powershell/module/azurerm.profile/get-azurermcontext)。 若要查看所有订阅，请运行 [**Get-AzureRmSubscription**](https://docs.microsoft.com/powershell/module/servicemanagement/azurerm.profile/get-azurermsubscription)。

    Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'

