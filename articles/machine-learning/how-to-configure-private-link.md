---
title: " (预览配置专用终结点) "
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
ms.date: 07/28/2020
ms.openlocfilehash: bdb7ba30d9fa2d0bd1eff9368d6e30e516b53895
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192718"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace-preview"></a> (预览版为 Azure 机器学习工作区配置 Azure 专用链接) 

本文档介绍如何将 Azure 专用链接与 Azure 机器学习工作区配合使用。 

> [!IMPORTANT]
> 将 Azure Private Link 与 Azure 机器学习工作区结合使用目前为公共预览版。 此功能仅在 **美国东部** 和 **美国西部 2** 区域提供。 此预览版在提供时没有服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

使用 Azure 专用链接，可以通过专用终结点连接到工作区。 专用终结点是虚拟网络中的一组专用 IP 地址。 然后，你可以限制工作区访问权限，只允许通过专用 IP 地址访问你的工作区。 专用链接有助于降低数据外泄风险。 若要详细了解专用终结点，请参阅 [Azure 专用链接](/azure/private-link/private-link-overview)一文。

> [!IMPORTANT]
> Azure 专用链接不影响 Azure 控制平面（管理操作），例如删除工作区或管理计算资源。 例如，创建、更新或删除计算目标。 这些操作像往常一样通过公共 Internet 执行。
>
> 在已启用专用链接的工作区中，不支持 Azure 机器学习计算实例预览。
>
> 如果使用的是 Mozilla Firefox，则在尝试访问工作区的专用终结点时可能会遇到问题。 此问题可能与 Mozilla 中的 HTTPS 上的 DNS 有关。 建议使用 Google Chrome 的 Microsoft Edge 作为解决方法。

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>创建使用专用终结点的工作区

> [!IMPORTANT]
> 目前，我们仅支持在创建新的 Azure 机器学习工作区时启用专用终结点。

中的模板 [https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) 可用于创建具有专用终结点的工作区。

有关使用此模板（包括专用终结点）的信息，请参阅 [使用 Azure 资源管理器模板创建 Azure 机器学习的工作区](how-to-create-workspace-template.md)。

## <a name="using-a-workspace-over-a-private-endpoint"></a>通过专用终结点使用工作区

由于仅允许从虚拟网络到工作区的通信，因此，使用该工作区的任何开发环境都必须是虚拟网络的成员。 例如，虚拟网络中的虚拟机。

> [!IMPORTANT]
> 为了避免暂时中断连接，Microsoft 建议你在启用专用链接后在连接到工作区的计算机上刷新 DNS 缓存。 

有关 Azure 虚拟机的信息，请参阅[虚拟机文档](/azure/virtual-machines/)。


## <a name="using-azure-storage"></a>使用 Azure 存储

若要保护工作区使用的 Azure 存储帐户，请将其置于虚拟网络中。

若要了解如何将存储帐户置于虚拟网络中，请参阅[对工作区使用存储帐户](how-to-enable-virtual-network.md#use-a-storage-account-for-your-workspace)。

> [!WARNING]
> Azure 机器学习不支持使用启用了专用链接的 Azure 存储帐户。

## <a name="using-azure-key-vault"></a>使用 Azure Key Vault

若要保护工作区使用的 Azure Key Vault，可以将其置于虚拟网络中，也可以为其启用专用链接。

若要了解如何将密钥保管库置于虚拟网络中，请参阅[将密钥保管库实例与工作区配合使用](how-to-enable-virtual-network.md#key-vault-instance)。

若要了解如何为密钥保管库启用专用链接，请参阅[将 Key Vault 与 Azure 专用链接集成](/azure/key-vault/private-link-service)。

## <a name="using-azure-kubernetes-services"></a>使用 Azure Kubernetes 服务

若要保护工作区使用的 Azure Kubernetes 服务，请将其置于虚拟网络中。 有关详细信息，请参阅[将 Azure Kubernetes 服务与工作区配合使用](how-to-enable-virtual-network.md#aksvnet)。

Azure 机器学习现在支持使用启用了专用链接的 Azure Kubernetes 服务。
若要创建专用 AKS 群集，请[在此处](https://docs.microsoft.com/azure/aks/private-clusters)执行文档

## <a name="azure-container-registry"></a>Azure 容器注册表

若要了解如何在虚拟网络中保护 Azure 容器注册表，请参阅[使用 Azure 容器注册表](how-to-enable-virtual-network.md#azure-container-registry)。

> [!IMPORTANT]
> 如果对 Azure 机器学习工作区使用专用链接，并在虚拟网络中放置工作区的 Azure 容器注册表，则还必须应用以下 Azure 资源管理器模板。 借助此模板，工作区可以通过专用链接与 ACR 通信。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "keyVaultArmId": {
      "type": "string"
      },
      "workspaceName": {
      "type": "string"
      },
      "containerRegistryArmId": {
      "type": "string"
      },
      "applicationInsightsArmId": {
      "type": "string"
      },
      "storageAccountArmId": {
      "type": "string"
      },
      "location": {
      "type": "string"
      }
  },
  "resources": [
      {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2019-11-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "identity": {
          "type": "SystemAssigned"
      },
      "sku": {
          "tier": "enterprise",
          "name": "enterprise"
      },
      "properties": {
          "sharedPrivateLinkResources":
  [{"Name":"Acr","Properties":{"PrivateLinkResourceId":"[concat(parameters('containerRegistryArmId'), '/privateLinkResources/registry')]","GroupId":"registry","RequestMessage":"Approve","Status":"Pending"}}],
          "keyVault": "[parameters('keyVaultArmId')]",
          "containerRegistry": "[parameters('containerRegistryArmId')]",
          "applicationInsights": "[parameters('applicationInsightsArmId')]",
          "storageAccount": "[parameters('storageAccountArmId')]"
      }
      }
  ]
}
```

## <a name="next-steps"></a>后续步骤

若要详细了解如何保护 Azure 机器学习工作区，请参阅[企业安全](concept-enterprise-security.md)一文。