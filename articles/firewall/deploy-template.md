---
title: 使用模板部署 Azure 防火墙
description: 使用模板部署 Azure 防火墙。 创建的网络有一个 VNet，具有三个子网。 部署了两个双核 Windows 服务器虚拟机。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: b9e82998283bf44eede88148a12d88aae521b1ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74169198"
---
# <a name="deploy-azure-firewall-using-a-template"></a>使用模板部署 Azure 防火墙

[创建 AzureFirewall 沙盒设置模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox)创建具有防火墙的测试网络环境。 该网络有一个虚拟网络 （VNet） 与三个子网： *Azure 防火墙子网*，*服务器子网*， 和*跳转盒子网*. ServersSubnet** 和 JumpboxSubnet** 子网均包含一个单个、双核 Windows Server 虚拟机。

防火墙位于 Azure*防火墙子网*子网中，并且具有一个应用程序规则集合，该集合具有允许访问`www.microsoft.com`的单个规则。

用户定义的一个路由，它引导来自 ServersSubnet** 子网的网络流量穿过应用了防火墙规则的防火墙。

有关 Azure 防火墙的详细信息，请参阅[使用 Azure 门户部署和配置 Azure 防火墙](tutorial-firewall-deploy-portal.md)。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-the-template-to-deploy-azure-firewall"></a>使用模板部署 Azure 防火墙

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

**使用模板安装和部署 Azure 防火墙：**

1. 访问 中的[https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox)模板。
   
1. 阅读简介，并准备好部署后，选择“部署到 Azure”****。
   
1. 如有必要，登录到 Azure 门户。 

1. 在门户中的“创建 AzureFirewall 的沙盒设置”**** 页上，键入或选择以下值：
   
   - **资源组**：选择 **"创建新**"，键入资源组的名称，然后选择 **"确定**"。 
   - **虚拟网络名称**：键入新 VNet 的名称。 
   - **管理员用户名**：键入管理员用户帐户的用户名。
   - **管理员密码**：键入管理员密码。 
   
1. 阅读条款和条件，然后选择**我同意上述条款和条件**。
   
1. 选择“购买”。****
   
   创建资源可能需要几分钟。 
   
1. 浏览使用防火墙创建的资源。 

若要了解模板中防火墙的 JSON 语法和属性，请参阅 [Microsoft.Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls)。

## <a name="clean-up-resources"></a>清理资源

不再需要这些资源时，可以通过运行 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) PowerShell 命令来删除资源组、防火墙和所有相关资源。 若要删除名为 MyResourceGroup** 的资源组，请运行： 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```
如果计划继续学习防火墙监视教程，请先不要删除资源组和防火墙。 

## <a name="next-steps"></a>后续步骤

接下来，可以监视 Azure 防火墙日志：

> [!div class="nextstepaction"]
> [教程：监视 Azure 防火墙日志](./tutorial-diagnostics.md)
