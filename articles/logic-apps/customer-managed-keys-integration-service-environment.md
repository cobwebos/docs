---
title: 设置客户管理的密钥以加密 ISEs 中静态数据
description: 创建和管理自己的加密密钥，以在 Azure 逻辑应用中保护集成服务环境 （ISEs） 的静态数据
services: logic-apps
ms.suite: integration
ms.reviewer: klam, rarayudu, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: fa39c8f65b00283044ef31dc7577a4668b3e634b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127649"
---
# <a name="set-up-customer-managed-keys-to-encrypt-data-at-rest-for-integration-service-environments-ises-in-azure-logic-apps"></a>设置客户管理的密钥，以加密 Azure 逻辑应用中的集成服务环境 （ISEs） 静态数据

Azure 逻辑应用依赖于 Azure 存储来存储和自动[加密静态数据](../storage/common/storage-service-encryption.md)。 此加密可保护您的数据，并帮助您履行组织安全和合规性承诺。 默认情况下，Azure 存储使用 Microsoft 管理的密钥来加密数据。 有关 Azure 存储加密的工作原理的详细信息，请参阅[静态数据的 Azure 存储加密](../storage/common/storage-service-encryption.md)和[静态 Azure 数据加密](../security/fundamentals/encryption-atrest.md)。

当您为托管逻辑应用创建[集成服务环境 （ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)时，希望对 Azure 存储使用的加密密钥进行更多控制时，可以使用[Azure 密钥保管库](../key-vault/key-vault-overview.md)设置、使用和管理自己的密钥。 此功能也称为"自带密钥"（BYOK），密钥称为"客户管理密钥"。

本主题演示如何使用逻辑应用 REST API 设置和指定自己的加密密钥，以便使用 ISE 时使用。 有关通过逻辑应用 REST API 创建 ISE 的一般步骤，请参阅[使用逻辑应用 REST API 创建集成服务环境 （ISE）。](../logic-apps/create-integration-service-environment-rest-api.md)

## <a name="considerations"></a>注意事项

* 此时，ISE 的客户管理的关键支持仅在这些 Azure 区域可用：美国西部 2、美国东部和美国中南部

* *只有在创建 ISE 时，才能*指定客户管理的密钥，而不是之后。 创建 ISE 后，无法禁用此密钥。 目前，没有用于旋转 ISE 客户管理密钥的支持。

* 要支持客户管理的密钥，ISE 需要启用其[系统分配的托管标识](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)。 此标识允许 ISE 对其他 Azure 活动目录 （Azure AD） 租户中的资源进行身份验证访问，这样您就不必使用凭据登录。

* 目前，要创建支持客户托管密钥并启用系统分配标识的 ISE，必须使用 HTTPS PUT 请求调用逻辑应用 REST API。

* 在发送创建 ISE 的 HTTPS PUT 请求后的*30 分钟内*，必须[授予密钥保管库对 ISE 系统分配的身份的访问权限](#identity-access-to-key-vault)。 否则，ISE 创建将失败并引发权限错误。

## <a name="prerequisites"></a>先决条件

* [启用 ISE 访问](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)[的先决条件](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites)和要求与在 Azure 门户中创建 ISE 时相同

* 启用 **"软删除**"和 **"不清除"** 属性的 Azure 密钥保管库

  有关启用这些属性的详细信息，请参阅[Azure 密钥保管库软删除概述](../key-vault/key-vault-ovw-soft-delete.md)，以及[使用 Azure 密钥保管库配置客户管理的密钥](../storage/common/storage-encryption-keys-portal.md)。 如果您是 Azure 密钥保管库的新增功能，请了解如何使用 Azure 门户或使用 Azure PowerShell 命令["New-AzKeyVault"](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault)[创建密钥保管库](../key-vault/quick-create-portal.md#create-a-vault)。

* 在密钥保管库中，使用以下属性值创建的密钥：

  | properties | “值” |
  |----------|-------|
  | **密钥类型** | RSA |
  | **RSA 密钥大小** | 2048 |
  | **启用** | 是 |
  |||

  ![创建客户托管的加密密钥](./media/customer-managed-keys-integration-service-environment/create-customer-managed-key-for-encryption.png)

  有关详细信息，请参阅使用 Azure 密钥保管库或 Azure PowerShell 命令["添加 AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/Add-AzKeyVaultKey)"[配置客户管理的密钥](../storage/common/storage-encryption-keys-portal.md)。

* 一种工具，可用于使用 HTTPS PUT 请求调用逻辑应用 REST API 来创建 ISE。 例如，您可以使用[Postman](https://www.getpostman.com/downloads/)，也可以生成执行此任务的逻辑应用。

<a name="enable-support-key-system-identity"></a>

## <a name="create-ise-with-key-vault-and-managed-identity-support"></a>使用密钥保管库和托管标识支持创建 ISE

要通过调用逻辑应用 REST API 创建 ISE，请发出此 HTTPS PUT 请求：

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> 逻辑应用 REST API 2019-05-01 版本要求您对 ISE 连接器发出自己的 HTTP PUT 请求。

部署通常需要两个小时才能完成。 有时，部署可能需要长达四个小时。 要检查部署状态，请在 Azure 工具栏上的[Azure 门户](https://portal.azure.com)中，选择通知图标，该图标将打开通知窗格。

> [!NOTE]
> 如果部署失败或删除 ISE，Azure 在释放子网之前可能需要长达一个小时。 此延迟意味着您可能需要等待，然后再在另一个 ISE 中重用这些子网。
>
> 如果删除虚拟网络，Azure 通常会在释放子网之前最多需要两个小时，但此操作可能需要更长时间。 
> 删除虚拟网络时，请确保没有资源仍然连接。 
> 请参阅[删除虚拟网络](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)。

### <a name="request-header"></a>请求标头

在请求标头中，包括以下属性：

* `Content-type`：将此属性值设置为`application/json`。

* `Authorization`：为有权访问要使用的 Azure 订阅或资源组的客户设置此属性值。

### <a name="request-body"></a>请求正文

在请求正文中，通过在 ISE 定义中提供它们的信息来支持这些附加项：

* ISE 用于访问密钥保管库的系统分配的托管标识
* 要使用密钥保管库和客户管理的密钥

#### <a name="request-body-syntax"></a>请求正文语法

下面是请求正文语法，它描述了创建 ISE 时要使用的属性：

```json
{
   "id": "/subscriptions/{Azure-subscription-ID/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.KeyVault/vaults/{key-vault-name}",
            },
            "keyName": "{customer-managed-key-name}",
            "keyVersion": "{key-version-number}"
         }
      }
   }
}
```

#### <a name="request-body-example"></a>请求正文示例

此示例请求正文显示示例值：

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "identity": {
      "type": "SystemAssigned"
   },
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.KeyVault/vaults/FabrikamKeyVault",
            },
            "keyName": "Fabrikam-Encryption-Key",
            "keyVersion": "********************"
         }
      }
   }
}
```

<a name="identity-access-to-key-vault"></a>

## <a name="grant-access-to-your-key-vault"></a>授予对 Key Vault 的访问权限

在发送 HTTP PUT 请求以创建 ISE 后*30 分钟内*，必须为 ISE 的系统分配标识向密钥保管库添加访问策略。 否则，为 ISE 创建失败，并且您会收到权限错误。 

对于此任务，可以使用 Azure PowerShell[集-AzKeyVaultAccessPolicy 命令](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)，也可以在 Azure 门户中执行以下步骤：

1. 在[Azure 门户](https://portal.azure.com)中，打开 Azure 密钥保管库。

1. 在密钥保管库菜单上，选择 **"访问策略** > **添加访问策略**"，例如：

   ![为系统分配的托管标识添加访问策略](./media/customer-managed-keys-integration-service-environment/add-ise-access-policy-key-vault.png)

1. 打开 **"添加访问策略"** 窗格后，按照以下步骤操作：

   1. 选择以下选项：

      | 设置 | 值 |
      |---------|--------|
      | **从模板（可选）列表配置** | 密钥管理 |
      | **密钥权限** | - **密钥管理操作**：获取、列表 <p><p>- **加密操作**： 解包密钥， 包装密钥 |
      |||

      ![选择"密钥管理">"密钥权限"](./media/customer-managed-keys-integration-service-environment/select-key-permissions.png)

   1. 对于 **"选择主体**"，请选择 **"无选择**"。 打开 **"主体"** 窗格后，在搜索框中查找并选择 ISE。 完成后，**选择"** > **添加**"。

      ![选择 ISE 作为主体](./media/customer-managed-keys-integration-service-environment/select-service-principal-ise.png)

   1. 完成 **"访问策略"** 窗格后，选择"**保存**"。

有关详细信息，请参阅[使用托管标识提供密钥保管库身份验证](../key-vault/managed-identity.md#grant-your-app-access-to-key-vault)。

## <a name="next-steps"></a>后续步骤

* 详细了解 [Azure Key Vault](../key-vault/key-vault-overview.md)