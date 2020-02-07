---
title: 疑难解答错误：无法访问 Azure Functions 运行时
description: 了解如何排查存储帐户无效的问题。
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 8fcd0661e2c7cab505121cf0d4d7b4c1d29017f8
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063775"
---
# <a name="troubleshoot-error-azure-functions-runtime-is-unreachable"></a>疑难解答错误： "无法访问 Azure Functions 运行时"

本文将帮助你解决 Azure 门户中出现的以下错误字符串：

> "错误：无法访问 Azure Functions 运行时。 单击此处了解存储配置的详细信息。 "

如果 Azure Functions 运行时无法启动，则会出现此问题。 此问题的最常见原因是函数应用无法访问其存储帐户。 有关详细信息，请参阅[存储帐户要求](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements)。

本文的其余部分将帮助你解决此错误的以下原因，其中包括如何识别和解决每种情况。

## <a name="storage-account-was-deleted"></a>存储帐户已被删除

每个 Function App 都需要存储帐户才能运行。 如果删除了该帐户，则函数将不起作用。

首先，在应用程序设置中查找存储帐户名称。 `AzureWebJobsStorage` 或 `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` 包含在连接字符串中封装的存储帐户的名称。 有关详细信息，请参阅[应用设置参考 Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)。

在 Azure 门户中搜索存储帐户，以查看它是否仍然存在。 如果已将其删除，请重新创建存储帐户并替换存储连接字符串。 函数代码丢失，需要重新部署它。

## <a name="storage-account-application-settings-were-deleted"></a>已删除存储帐户应用程序设置

在前面的步骤中，如果找不到存储帐户连接字符串，则很可能会将其删除或覆盖。 删除应用程序设置最常见的情况是，使用部署槽位或 Azure 资源管理器脚本来设置应用程序设置。

### <a name="required-application-settings"></a>必需的应用程序设置

* 必需：
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* 对于消耗计划函数是必需的：
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

有关详细信息，请参阅[应用设置参考 Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)。

### <a name="guidance"></a>指南

* 对于这些设置中的任何一个，请勿选中 "槽设置"。 如果交换部署槽位，函数应用将中断。
* 请勿在自动部署过程中修改这些设置。
* 必须在创建时提供这些设置并使其有效。 不包含这些设置的自动部署会导致不会运行的 function app，即使稍后添加了设置也是如此。

## <a name="storage-account-credentials-are-invalid"></a>存储帐户凭据无效

如果重新生成存储密钥，则必须更新前面讨论过的存储帐户连接字符串。 有关存储密钥管理的详细信息，请参阅[创建 Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)。

## <a name="storage-account-is-inaccessible"></a>无法访问存储帐户

函数应用必须能够访问存储帐户。 阻止函数应用访问存储帐户的常见问题包括：

* 函数应用将部署到你的应用服务环境，而无需正确的网络规则以允许与存储帐户通信。

* 存储帐户防火墙已启用，并且未配置为允许进出函数的流量。 有关详细信息，请参阅[配置 Azure 存储防火墙和虚拟网络](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

## <a name="daily-execution-quota-is-full"></a>每日执行配额已满

如果已配置每日执行配额，则会暂时禁用函数应用，这会导致许多门户控件变得不可用。 

若要验证[Azure 门户](https://portal.azure.com)中的配额，请在函数应用中选择 "**平台功能**" > **Function App "设置**"。 如果你使用的是已设置的**每日使用配额**，则会显示以下消息：

  > "Function App 已达到每日使用量配额，已停止到下一个24小时的时间范围内。"

若要解决此问题，请删除或增加每日配额，然后重启应用。 否则，应用程序的执行将被阻止，直到下一天。

## <a name="app-is-behind-a-firewall"></a>应用位于防火墙后面

由于以下任一原因，可能无法访问函数运行时：

* 函数应用托管在[内部负载平衡应用服务环境](../app-service/environment/create-ilb-ase.md)中，并配置为阻止入站 internet 流量。

* Function app 具有配置为阻止 internet 访问的[入站 IP 限制](functions-networking-options.md#inbound-ip-restrictions)。 

Azure 门户直接调用正在运行的应用程序以提取函数列表，并向 Kudu 终结点发出 HTTP 调用。 "**平台功能**" 选项卡下的平台级别设置仍可用。

验证应用服务环境配置：
1. 中转到应用服务环境所在子网的网络安全组（NSG）。
1. 验证入站规则，以允许来自要访问应用程序的计算机的公共 IP 的流量。 
   
你还可以从连接到运行应用的虚拟网络的计算机或虚拟网络中运行的虚拟机使用门户。 

有关入站规则配置的详细信息，请参阅[应用服务环境的网络注意事项](https://docs.microsoft.com/azure/app-service/environment/network-info#network-security-groups)部分中的 "网络安全组" 部分。

## <a name="next-steps"></a>后续步骤

了解如何监视函数应用：

> [!div class="nextstepaction"]
> [监视 Azure Functions](functions-monitoring.md)
