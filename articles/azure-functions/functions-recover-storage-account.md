---
title: 故障排除错误：无法到达 Azure 函数运行时
description: 了解如何排查存储帐户无效的问题。
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 8fcd0661e2c7cab505121cf0d4d7b4c1d29017f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063775"
---
# <a name="troubleshoot-error-azure-functions-runtime-is-unreachable"></a>故障排除错误："无法到达 Azure 函数运行时"

本文可帮助您排除 Azure 门户中显示的以下错误字符串：

> 错误：无法到达 Azure 函数运行时。 单击此处了解有关存储配置的详细信息。

当 Azure 函数运行时无法启动时，将出现此问题。 此问题的最常见原因是功能应用无法访问其存储帐户。 有关详细信息，请参阅[存储帐户要求](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements)。

本文的其余部分可帮助您排除此错误的以下原因，包括如何识别和解决每个情况。

## <a name="storage-account-was-deleted"></a>存储帐户已删除

每个 Function App 都需要存储帐户才能运行。 如果该帐户被删除，您的功能将不起作用。

首先，在应用程序设置中查找存储帐户名称。 或`AzureWebJobsStorage``WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`包含在连接字符串中包装的存储帐户的名称。 有关详细信息，请参阅[Azure 函数的应用设置参考](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)。

在 Azure 门户中搜索存储帐户，以查看它是否仍然存在。 如果已删除，请重新创建存储帐户并替换存储连接字符串。 函数代码丢失，您需要重新部署它。

## <a name="storage-account-application-settings-were-deleted"></a>已删除存储帐户应用程序设置

在上一步中，如果找不到存储帐户连接字符串，则可能将其删除或覆盖。 删除应用程序设置最常发生在使用部署槽或 Azure 资源管理器脚本设置应用程序设置时。

### <a name="required-application-settings"></a>必需的应用程序设置

* 必需：
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* 消费计划功能所需的：
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

有关详细信息，请参阅[Azure 函数的应用设置参考](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)。

### <a name="guidance"></a>指南

* 不要检查这些设置中的"插槽设置"。 如果交换部署槽，函数应用将中断。
* 不要作为自动部署的一部分修改这些设置。
* 必须在创建时提供这些设置并使其有效。 不包含这些设置的自动部署会导致函数应用无法运行，即使以后添加了这些设置也是如此。

## <a name="storage-account-credentials-are-invalid"></a>存储帐户凭据无效

如果重新生成存储密钥，则必须更新前面讨论的存储帐户连接字符串。 有关存储密钥管理的详细信息，请参阅创建[Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)。

## <a name="storage-account-is-inaccessible"></a>无法访问存储帐户

您的功能应用必须能够访问存储帐户。 阻止函数应用访问存储帐户的常见问题包括：

* 功能应用部署到应用服务环境，无需正确的网络规则以允许流量往来和从存储帐户。

* 存储帐户防火墙已启用，未配置为允许流量与函数通信。 有关详细信息，请参阅[配置 Azure 存储防火墙和虚拟网络](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

## <a name="daily-execution-quota-is-full"></a>每日执行配额已满

如果配置了每日执行配额，则函数应用将暂时禁用，从而导致许多门户控件不可用。 

要验证[Azure 门户](https://portal.azure.com)中的配额，请在函数应用中选择**平台** > **功能应用设置**。 如果您已超出已设置**的每日使用配额**，将显示以下消息：

  > "功能应用已达到每日使用配额，并一直停止，直到下 24 小时的时间框架。

要解决此问题，请删除或增加每日配额，然后重新启动应用。 否则，应用的执行将一直阻止到第二天。

## <a name="app-is-behind-a-firewall"></a>应用受防火墙保护

由于以下任一原因，可能无法到达函数运行时：

* 您的功能应用托管在[内部负载平衡的应用服务环境中](../app-service/environment/create-ilb-ase.md)，它配置为阻止入站互联网流量。

* 您的功能应用具有已配置为阻止 Internet 访问的[入站 IP 限制](functions-networking-options.md#inbound-ip-restrictions)。 

Azure 门户直接调用正在运行的应用以获取函数列表，并且对 Kudu 终结点进行 HTTP 调用。 **"平台功能**"选项卡下的平台级设置仍然可用。

要验证应用服务环境配置，请进行以下操作：
1. 转到应用服务环境所在的子网的网络安全组 （NSG）。
1. 验证入站规则，以允许来自您访问应用程序的计算机的公共 IP 的流量。 
   
还可以使用连接到运行应用的虚拟网络的计算机或虚拟网络中运行的虚拟机的门户。 

有关入站规则配置的详细信息，请参阅[应用服务环境网络注意事项](https://docs.microsoft.com/azure/app-service/environment/network-info#network-security-groups)的"网络安全组"部分。

## <a name="next-steps"></a>后续步骤

了解如何监视功能应用：

> [!div class="nextstepaction"]
> [监视 Azure Functions](functions-monitoring.md)
