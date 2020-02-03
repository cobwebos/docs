---
title: 如何排查 Azure Functions 运行时无法访问的问题。
description: 了解如何排查存储帐户无效的问题。
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 910b582cb40b9f8aff6a553621b4677d6b019826
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963880"
---
# <a name="how-to-troubleshoot-functions-runtime-is-unreachable"></a>如何排查“Azure Functions 运行时无法访问”的问题

本文旨在解决 "Azure 门户中显示的" 函数运行时无法访问 "错误消息。 发生此错误时，会在门户中看到以下错误字符串。

`Error: Azure Functions Runtime is unreachable. Click here for details on storage configuration`

如果 Azure Functions 运行时无法启动，则会发生这种情况。 出现此错误最常见的原因是，Function App 无法访问其存储帐户。 若要了解详细信息，请参阅[存储帐户要求](storage-considerations.md#storage-account-requirements)。

本文的其余部分将帮助你解决此错误的以下原因，其中包括如何识别和解决每种情况。

+ [存储帐户已删除](#storage-account-deleted)
+ [存储帐户应用程序设置已删除](#storage-account-application-settings-deleted)
+ [存储帐户凭据无效](#storage-account-credentials-invalid)
+ [无法访问存储帐户](#storage-account-inaccessible)
+ [超出每日执行配额](#daily-execution-quota-full)
+ [你的应用程序位于防火墙后面](#app-is-behind-a-firewall)


## <a name="storage-account-deleted"></a>存储帐户已删除

每个 Function App 都需要存储帐户才能运行。 如果该帐户已删除，则你的 Function 将无法使用。

### <a name="how-to-find-your-storage-account"></a>如何查找存储帐户

首先在“应用程序设置”中查找存储帐户名称。 `AzureWebJobsStorage` 或 `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` 将包含在连接字符串中包装的存储帐户名称。 有关详细信息，请参阅[此处的应用程序设置参考](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)。

在 Azure 门户中搜索存储帐户，以确定它是否仍然存在。 如果它已被删除，则需要重新创建存储帐户，并替换存储连接字符串。 你的函数代码将丢失，需要再次重新部署它。

## <a name="storage-account-application-settings-deleted"></a>存储帐户应用程序设置已删除

在上一步中，如果没有存储帐户连接字符串，则它可能会被删除或覆盖。 当使用部署槽位或 Azure 资源管理器脚本来设置应用程序设置时，通常会删除应用程序设置。

### <a name="required-application-settings"></a>必需的应用程序设置

* 需要
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* 消耗计划函数需要
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

[有关这些应用程序设置的信息，请参阅此处](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)。

### <a name="guidance"></a>指南

* 对于这些设置中的任何一个，请勿选中 "槽设置"。 交换部署槽位时，函数应用中断。
* 请勿在自动部署过程中修改这些设置。
* 必须在创建时提供这些设置并使其有效。 不包含这些设置的自动部署会导致不会运行的 function app，即使稍后添加了设置也是如此。

## <a name="storage-account-credentials-invalid"></a>存储帐户凭据无效

如果重新生成存储密钥，则必须更新上面的“存储帐户”连接字符串。 [有关存储密钥管理的详细信息，请参阅此处](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)。

## <a name="storage-account-inaccessible"></a>无法访问存储帐户

函数应用必须能够访问存储帐户。 阻止 Function 访问存储帐户的常见问题是：

+ 部署到应用服务环境（ASE）的函数应用，没有正确的网络规则，以允许与存储帐户通信。

+ 存储帐户防火墙已启用，并且未配置为允许进出函数的流量。 若要了解详细信息，请参阅[配置 Azure 存储防火墙和虚拟网络](../storage/common/storage-network-security.md)。

## <a name="daily-execution-quota-full"></a>每日执行配额已满

如果已配置每日执行配额，则会暂时禁用函数应用，这会导致许多门户控件变得不可用。 

+ 若要在[Azure 门户](https://portal.azure.com)中进行验证，请在函数应用中打开**平台功能** > **Function App 设置**。 如果你已设置**每日使用配额**，你会看到以下消息：

    `The function app has reached daily usage quota and has been stopped until the next 24 hours time frame.`

+ 若要解决此问题，请删除或增加每日配额，然后重启应用。 否则，应用程序的执行将被阻止，直到下一天。

## <a name="app-is-behind-a-firewall"></a>应用位于防火墙后面

如果函数应用托管在[内部负载平衡应用服务环境](../app-service/environment/create-ilb-ase.md)中，并且配置为阻止入站 internet 流量，或具有配置为阻止 internet 访问的[入站 IP 限制](functions-networking-options.md#inbound-ip-restrictions)，则函数运行时将无法访问。 Azure 门户直接调用正在运行的应用程序以提取函数列表，并对 KUDU 终结点进行 HTTP 调用。 "`Platform Features`" 选项卡下的平台级别设置仍可用。

若要验证 ASE 配置，请导航到 ASE 所在子网的 NSG，并验证入站规则，以允许来自要访问应用程序的计算机的公共 IP 的流量。 你还可以从连接到运行应用的虚拟网络的计算机或虚拟网络中运行的虚拟机使用门户。 [在此处阅读有关入站规则配置的详细信息](../app-service/environment/network-info.md#network-security-groups)

## <a name="next-steps"></a>后续步骤

了解如何监视函数应用：

> [!div class="nextstepaction"]
> [监视 Azure Functions](functions-monitoring.md)
