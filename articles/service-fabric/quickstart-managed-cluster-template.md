---
title: 使用 Azure 资源管理器部署 Service Fabric 托管群集（预览版）
description: 了解如何使用 Azure 资源管理器模板创建 Service Fabric 托管群集
ms.topic: quickstart
ms.date: 09/28/2020
ms.custom: references_regions
ms.openlocfilehash: 079e8e3e39bd306332abb43fa2ae1f8cc04e305b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91410379"
---
# <a name="quickstart-deploy-a-service-fabric-managed-cluster-preview-with-an-azure-resource-manager-template"></a>快速入门：使用 Azure 资源管理器模板部署 Service Fabric 托管群集（预览版）

Service Fabric 托管群集是从 Azure Service Fabric 群集资源模型演变而来，用于简化部署和群集管理体验。 Service Fabric 托管群集是一种完全封装的资源，可用于部署单个 Service Fabric 群集资源，而不必部署构成 Service Fabric 群集的所有基础资源。 本文介绍如何使用 Azure 资源管理器模板（ARM 模板）在 Azure 中部署 Service Fabric 托管群集以进行测试。

本教程中部署的三节点基本 SKU 群集仅供教学使用（不能用于生产工作负荷）。 有关详细信息，请参阅 [Service Fabric 托管群集 SKU](overview-managed-cluster.md#service-fabric-managed-cluster-skus)。

## <a name="prerequisites"></a>先决条件

在开始学习本快速入门之前：

* 如果没有 Azure 订阅，请创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 示例 - Service Fabric 群集模板](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Basic-SKU-1-NT)。

## <a name="create-a-client-certificate"></a>创建客户端证书

Service Fabric 托管群集使用客户端证书作为访问控制的密钥。 如果已有要用于群集访问控制的客户端证书，则可以跳过此步骤。

如果需要创建新的客户端证书，请按照[设置和检索 Azure 密钥保管库中的证书](../key-vault/certificates/quick-create-portal.md)中的步骤进行操作。

记下证书指纹，因为下一步需要使用它来部署模板。

## <a name="deploy-the-template"></a>部署模板

1. 选择下图登录到 Azure 并打开一个模板。

      [![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fservice-fabric-cluster-templates%2Fmaster%2FSF-Managed-Basic-SKU-1-NT%2Fazuredeploy.json)

2. 选择或输入以下值

    在本快速入门中，为以下模板参数提供你自己的值：

    * 订阅：选择 Azure 订阅。
    * **资源组**：选择“新建”。 输入资源组的唯一名称（例如 *myResourceGroup*），然后选择“确定”。
    * 位置：选择一个位置，例如 eastus2。 Service Fabric 托管群集预览版支持的区域包括 `centraluseuap`、`eastus2euap`、`eastasia`、`northeurope`、`westcentralus` 和 `eastus2`。
    * **群集名称**：输入群集的唯一名称，例如 mysfcluster。
    * **管理员用户名**：输入要在群集中的基础 VM 上用于 RDP 的管理员名称。
    * **管理员密码**：输入要在群集中的基础 VM 上用于 RDP 的管理员密码。
    * 客户端证书指纹：提供要用于访问群集的客户端证书的指纹。 如果没有证书，请按照[设置和检索证书](../key-vault/certificates/quick-create-portal.md)来创建自签名证书。
    * **节点类型名称**：输入节点类型的唯一名称，例如 nt1。
    * **我同意上述条款和条件**：选中此框表示同意。 

3. 选择“购买”。

4. 部署 Service Fabric 托管群集需要几分钟时间。 等待部署成功完成，然后再继续执行后续步骤。

## <a name="validate-the-deployment"></a>验证部署

### <a name="review-deployed-resources"></a>查看已部署的资源

部署完成后，在输出中找到 Service Fabric Explorer 值，然后在 Web 浏览器中打开该地址，以便在 Service Fabric Explorer 中查看群集。 当系统提示你提供证书时，请使用在模板中为其提供了客户端指纹的证书。

> [!NOTE]
> 可以在 Azure 门户的资源组部署选项卡下找到部署的输出。

## <a name="clean-up-resources"></a>清理资源

如果不再需要 Service Fabric 托管群集的资源组，请将其删除。 要通过门户删除资源组，请执行以下操作：

1. 在门户顶部的“搜索”框中输入资源组的名称。 在搜索结果中看到在本快速入门中使用的资源组后，将其选中。
2. 选择“删除资源组”。
3. 在“键入资源组名称:”框中，键入资源组的名称，然后选择“删除” 。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你部署了 Service Fabric 托管群集。 若要详细了解如何扩展群集，请参阅：

> [!div class="nextstepaction"]
> [横向扩展 Service Fabric 托管群集](tutorial-managed-cluster-scale.md)
