---
title: 使用自定义 DNS 服务器
titleSuffix: Azure Machine Learning
description: 如何将自定义 DNS 服务器配置为使用 Azure 机器学习工作区和专用终结点。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 10/05/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 1d215c9564d89e5bd410e68839807f5c2c752356
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91828372"
---
# <a name="how-to-use-your-workspace-with-a-custom-dns-server"></a>如何将工作区用于自定义 DNS 服务器

将 Azure 机器学习与虚拟网络一起使用时， [可以通过多种方式来处理 DNS 名称解析](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances)。 默认情况下，Azure 会自动处理工作区和专用终结点的名称解析。 但是， __在使用你自己的自定义 DNS 服务器时__，你必须为工作区手动创建 DNS 条目。

> [!IMPORTANT]
> 本文仅介绍如何查找 (FQDN) 和 IP 地址的完全限定域名，而不提供有关为这些项配置 DNS 记录的信息。 有关如何添加记录的信息，请参阅 DNS 软件的文档。

## <a name="prerequisites"></a>先决条件

- 使用 [自己的 DNS 服务器](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)的 Azure 虚拟网络。

- 具有专用终结点的 Azure 机器学习工作区。 有关详细信息，请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。

- 熟悉 [在定型 & 推理期间使用网络隔离](how-to-enable-virtual-network.md)。

- （可选） [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 或 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)。

## <a name="find-the-ip-addresses"></a>查找 IP 地址

以下列表包含工作区和专用终结点使用 (FQDN) 的完全限定的域名：

* `<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `<workspace-GUID>.studio.workspace.<region>.api.azureml.ms`
* `cert-<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
* `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
* `<workspace-GUID>.workspace.<region>.aether.ms`
* `ml-<workspace-name>-<region>-<workspace-guid>.notebooks.azure.ml`
* 如果创建计算实例，还必须为添加一个条目 `<instance-name>.<region>.instances.azureml.ms` 。

若要在 VNet 中查找 Fqdn 的内部 IP 地址，请使用以下方法之一：

> [!NOTE]
> 根据您的配置，完全限定的域名和 IP 地址将有所不同。 例如，域名中的 GUID 值将特定于你的工作区。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az network private-endpoint show --endpoint-name <endpoint> --resource-group <resource-group> --query 'customDnsConfigs[*].{FQDN: fqdn, IPAddress: ipAddresses[0]}' --output table
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
$workspaceDns=Get-AzPrivateEndpoint -Name <endpoint> -resourcegroupname <resource-group>
$workspaceDns.CustomDnsConfigs | format-table
```

# <a name="azure-portal"></a>[Azure 门户](#tab/azure-portal)

1. 在 [Azure 门户](https://portal.azure.com)中，选择 Azure 机器学习 __工作区__。
1. 从 " __设置__ " 部分中，选择 " __专用终结点连接__"。
1. 选择显示的 " __专用终结点__ " 列中的链接。
1. 页面底部的工作区专用终结点 (FQDN) 和 IP 地址的完全限定域名列表。

:::image type="content" source="./media/how-to-custom-dns/private-endpoint-custom-dns.png" alt-text="门户中的 Fqdn 列表":::

---

从所有方法返回的信息是相同的;资源的 FQDN 和专用 IP 地址的列表。

| FQDN | IP 地址 |
| ----- | ----- |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.studio.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `cert-fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net` | `10.1.0.6` |

> [!IMPORTANT]
> 有些 Fqdn 不显示在私有终结点列出的中，而是由工作区所需的。 下表列出了这些 Fqdn，还必须将其添加到 DNS 服务器：
>
> * `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
> * `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
> * `<workspace-GUID>.workspace.<region>.aether.ms`
> * 如果你有一个计算实例，请使用 `<instance-name>.<region>.instances.azureml.ms` ，其中 `<instance-name>` 是你的计算实例的名称。
>
> 对于所有这些 IP 地址，请使用与 `*.api.azureml.ms` 前面步骤中返回的条目相同的地址。

## <a name="next-steps"></a>后续步骤

有关使用虚拟网络 Azure 机器学习的详细信息，请参阅 [虚拟网络概述](how-to-network-security-overview.md)。