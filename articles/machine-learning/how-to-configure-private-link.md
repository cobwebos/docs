---
title: 配置专用终结点
titleSuffix: Azure Machine Learning
description: 使用 Azure 专用链接从虚拟网络安全地访问 Azure 机器学习工作区。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/30/2020
ms.openlocfilehash: 4ba7ec73ac70723e21b6acad571d62d14edd250a
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91828119"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace"></a>为 Azure 机器学习工作区配置 Azure 专用链接

本文档介绍如何将 Azure 专用链接与 Azure 机器学习工作区配合使用。 有关创建 Azure 机器学习虚拟网络的信息，请参阅 [虚拟网络隔离和隐私概述](how-to-network-security-overview.md)

使用 Azure 专用链接，可以通过专用终结点连接到工作区。 专用终结点是虚拟网络中的一组专用 IP 地址。 然后，你可以限制工作区访问权限，只允许通过专用 IP 地址访问你的工作区。 专用链接有助于降低数据外泄风险。 若要详细了解专用终结点，请参阅 [Azure 专用链接](/azure/private-link/private-link-overview)一文。

> [!IMPORTANT]
> Azure 专用链接不影响 Azure 控制平面（管理操作），例如删除工作区或管理计算资源。 例如，创建、更新或删除计算目标。 这些操作像往常一样通过公共 Internet 执行。 数据平面操作（如使用 Azure 机器学习 studio、Api (包括) 的已发布管道）或 SDK 使用专用终结点。
>
> 如果使用的是 Mozilla Firefox，则在尝试访问工作区的专用终结点时可能会遇到问题。 此问题可能与 Mozilla 中的 HTTPS 上的 DNS 有关。 建议使用 Google Chrome 的 Microsoft Edge 作为解决方法。

## <a name="prerequisites"></a>先决条件

如果计划将启用了专用链接的工作区与客户托管的密钥配合使用，则必须使用支持票证请求此功能。 有关详细信息，请参阅 [管理和增加配额](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases)。

## <a name="limitations"></a>限制

使用具有专用链接的 Azure 机器学习工作区在 Azure 政府区域或 Azure 中国世纪互联区域中不可用。

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>创建使用专用终结点的工作区

使用以下方法之一创建具有专用终结点的工作区：

> [!TIP]
> 如果需要，Azure 资源管理器模板可以创建一个新的虚拟网络。 其他方法都需要现有虚拟网络。

# <a name="resource-manager-template"></a>[Resource Manager 模板](#tab/azure-resource-manager)

中的 Azure 资源管理器模板 [https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) 提供了一种简单的方法来创建具有专用终结点和虚拟网络的工作区。

有关使用此模板（包括专用终结点）的信息，请参阅 [使用 Azure 资源管理器模板创建 Azure 机器学习的工作区](how-to-create-workspace-template.md)。

# <a name="python"></a>[Python](#tab/python)

Azure 机器学习 Python SDK 提供了 [PrivateEndpointConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.privateendpointconfig?view=azure-ml-py) 类，该类可用于 [工作区。创建 ( # B1 ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) 来创建具有专用终结点的工作区。 此类需要现有虚拟网络。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[机器学习的 Azure CLI 扩展](reference-azure-machine-learning-cli.md)提供了[az ml workspace create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext_azure_cli_ml_az_ml_workspace_create)命令。 此命令的以下参数可用于创建具有专用网络的工作区，但它需要现有虚拟网络：

* `--pe-name`：创建的专用终结点的名称。
* `--pe-auto-approval`：是否应自动批准与工作区的专用终结点连接。
* `--pe-resource-group`：要在其中创建专用终结点的资源组。 必须是包含虚拟网络的同一个组。
* `--pe-vnet-name`：要在其中创建专用终结点的现有虚拟网络。
* `--pe-subnet-name`：要在其中创建专用终结点的子网的名称。 默认值为 `default`。

# <a name="portal"></a>[Portal](#tab/azure-portal)

使用 Azure 机器学习 studio 中的 " __网络__ " 选项卡可以配置专用终结点。 但是，它需要现有虚拟网络。 有关详细信息，请参阅 [在门户中创建工作区](how-to-manage-workspace.md)。

---

## <a name="using-a-workspace-over-a-private-endpoint"></a>通过专用终结点使用工作区

由于仅允许从虚拟网络到工作区的通信，因此，使用该工作区的任何开发环境都必须是虚拟网络的成员。 例如，虚拟网络中的虚拟机。

> [!IMPORTANT]
> 为了避免暂时中断连接，Microsoft 建议你在启用专用链接后在连接到工作区的计算机上刷新 DNS 缓存。 

有关 Azure 虚拟机的信息，请参阅[虚拟机文档](/azure/virtual-machines/)。


## <a name="next-steps"></a>后续步骤

* 有关保护 Azure 机器学习工作区的详细信息，请参阅 [虚拟网络隔离和隐私概述](how-to-network-security-overview.md) 一文。

* 如果你计划在虚拟网络中使用自定义 DNS 解决方案，请参阅 [如何通过自定义 dns 服务器使用工作区](how-to-custom-dns.md)。
