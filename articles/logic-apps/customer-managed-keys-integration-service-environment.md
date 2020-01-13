---
title: 设置客户管理的密钥，以便在 ISEs 中加密静态数据
description: 创建并管理自己的加密密钥，以便在 Azure 逻辑应用中保护静态数据和集成服务环境（ISEs）
services: logic-apps
ms.suite: integration
ms.reviewer: klam, rarayudu, logicappspm
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: 6f4e0744aad5f053cdda0a52b382ad3c86982c2f
ms.sourcegitcommit: d48afd9a09f850b230709826d4a5cd46e57d19fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2020
ms.locfileid: "75904975"
---
# <a name="set-up-customer-managed-keys-to-encrypt-data-at-rest-for-integration-service-environments-ises-in-azure-logic-apps"></a>设置客户管理的密钥，以便在 Azure 逻辑应用中为集成服务环境（ISEs）加密静态数据

Azure 逻辑应用依赖 Azure 存储来存储和自动[加密静态数据](../storage/common/storage-service-encryption.md)。 此加密可保护你的数据，并可帮助你满足组织的安全性和符合性承诺。 默认情况下，Azure 存储使用 Microsoft 托管的密钥来加密数据。 有关 Azure 存储加密的工作原理的详细信息，请参阅[静态数据的 Azure 存储加密](../storage/common/storage-service-encryption.md)和[静态数据加密](../security/fundamentals/encryption-atrest.md)。

创建用于托管逻辑应用的[integration service 环境（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)并希望更好地控制 Azure 存储使用的加密密钥时，可以使用[Azure Key Vault](../key-vault/key-vault-overview.md)设置、使用和管理你自己的密钥。 此功能也称为 "创建自己的密钥" （BYOK），你的密钥称为 "客户管理的密钥"。

本主题说明如何设置和指定你自己的加密密钥，以便在创建 ISE 时使用。 

## <a name="considerations"></a>注意事项

* 目前，对 ISE 的客户托管密钥支持仅在以下 Azure 区域提供：美国西部2、美国东部和美国中南部

* *只能在创建 ISE 时*指定客户托管的密钥，而不能在以后创建 ISE。 创建 ISE 后，不能禁用此密钥。 目前，不支持轮换 ISE 的客户托管密钥。

* 若要支持客户管理的密钥，ISE 要求需要启用[系统分配的托管标识](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)。 此标识允许 ISE 验证对其他 Azure Active Directory （Azure AD）租户中的资源的访问权限，这样就无需使用凭据进行登录。

* 目前，若要创建支持客户管理的密钥的 ISE 并启用系统分配的标识，则必须使用 HTTPS PUT 请求调用逻辑应用 REST API。

* 发送用于创建 ISE 的 HTTPS PUT 请求后*30 分钟*内，必须[为 ise 的系统分配的标识授予密钥保管库访问权限](#identity-access-to-key-vault)。 否则，ISE 创建失败并引发权限错误。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 已启用**软删除**且**未清除**属性的 Azure 密钥保管库

  有关启用这些属性的详细信息，请参阅[Azure Key Vault 软删除概述](../key-vault/key-vault-ovw-soft-delete.md)和[配置 Azure Key Vault 的客户托管密钥](../storage/common/storage-encryption-keys-portal.md)。 如果你不熟悉 Azure Key Vault，请参阅[如何使用 Azure 门户创建密钥保管库](../key-vault/quick-create-portal.md#create-a-vault)，或使用 Azure PowerShell 命令[AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault)。

* 在密钥保管库中，使用以下属性值创建的密钥：

  | 属性 | 值 |
  |----------|-------|
  | **键类型** | RSA |
  | **RSA 密钥大小** | 2048 |
  | **已启用** | 是 |
  |||

  ![创建客户管理的加密密钥](./media/customer-managed-keys-integration-service-environment/create-customer-managed-key-for-encryption.png)

  有关详细信息，请参阅[配置客户管理的密钥与 Azure Key Vault](../storage/common/storage-encryption-keys-portal.md)或 Azure PowerShell 命令[AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/Add-AzKeyVaultKey)。

* 一种工具，可用于通过调用具有 HTTPS PUT 请求 REST API 的逻辑应用来创建 ISE。 例如，可以使用[Postman](https://www.getpostman.com/downloads/)，也可以生成执行此任务的逻辑应用。

<a name="enable-support-key-system-identity"></a>

## <a name="create-ise-with-key-vault-and-managed-identity-support"></a>创建具有 key vault 和托管标识支持的 ISE

若要通过调用逻辑应用 REST API 创建 ISE，请发出此 HTTPS PUT 请求：

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> 逻辑应用 REST API 2019-05-01 版本要求你为 ISE 连接器自行发出 HTTP PUT 请求。

### <a name="request-header"></a>请求标头

在请求标头中，包括以下属性：

* `Content-type`：将此属性值设置为 `application/json`。

* `Authorization`：将此属性值设置为有权访问要使用的 Azure 订阅或资源组的客户的持有者令牌。

### <a name="request-body"></a>请求正文

在请求正文中，通过在 ISE 定义中提供其信息来启用对这些附加项的支持：

* 你的 ISE 用于访问密钥保管库的系统分配的托管标识
* 你要使用的密钥保管库和客户托管的密钥

#### <a name="request-body-syntax"></a>请求正文语法

下面是请求正文语法，描述创建 ISE 时要使用的属性：

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

发送 HTTP PUT 请求以创建 ISE 之后，在*30 分钟*内，必须将访问策略添加到 ise 系统分配的密钥保管库。 否则，你的 ISE 创建会失败，并会出现权限错误。 

对于此任务，您可以使用 Azure PowerShell [AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)命令，也可以在 Azure 门户中执行以下步骤：

1. 在[Azure 门户](https://portal.azure.com)中，打开 Azure 密钥保管库。

1. 在 key vault 菜单上，选择 "**访问策略**" > **添加访问策略**，例如：

   ![为系统分配的托管标识添加访问策略](./media/customer-managed-keys-integration-service-environment/add-ise-access-policy-key-vault.png)

1. 在 "**添加访问策略**" 窗格打开后，请执行以下步骤：

   1. 选择以下选项：

      | 设置 | 值 |
      |---------|--------|
      | **"从模板配置（可选）" 列表** | 密钥管理 |
      | **关键权限** | - **密钥管理操作**：获取、列出 <p><p>- **加密操作**：解包密钥、环绕键 |
      |||

      ![选择 "密钥管理" > "密钥权限"](./media/customer-managed-keys-integration-service-environment/select-key-permissions.png)

   1. 对于 "**选择主体**"，请选择 "**未**选择"。 **主体**窗格打开后，在 "搜索" 框中，找到并选择你的 ISE。 完成后，选择 "**选择** > **添加**"。

      ![选择要用作主体的 ISE](./media/customer-managed-keys-integration-service-environment/select-service-principal-ise.png)

   1. 完成 "**访问策略**" 窗格后，请选择 "**保存**"。

有关详细信息，请参阅[使用托管标识提供 Key Vault 身份验证](../key-vault/managed-identity.md#grant-your-app-access-to-key-vault)。

## <a name="next-steps"></a>后续步骤

* 详细了解[Azure Key Vault](../key-vault/key-vault-overview.md)