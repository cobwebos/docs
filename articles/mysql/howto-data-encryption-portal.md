---
title: 使用 Azure 门户 Azure Database for MySQL 的数据加密
description: 了解如何使用 Azure 门户设置和管理 Azure Database for MySQL 的数据加密。
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: ba1ad7c533ea1dc9e88c8337373e607db653ac24
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048547"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-portal"></a>使用 Azure 门户 Azure Database for MySQL 的数据加密

了解如何使用 Azure 门户设置和管理 Azure Database for MySQL 的数据加密。

## <a name="prerequisites-for-azure-cli"></a>Azure CLI 的先决条件

* 必须有一个 Azure 订阅，并且是该订阅的管理员。
* 在 Azure Key Vault 中，创建密钥保管库和用于客户托管密钥的密钥。
* Key vault 必须具有以下属性以用作客户管理的密钥：
  * [软删除](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [清除保护](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* 此密钥必须具有以下属性以用作客户管理的密钥：
  * 无过期日期
  * 未禁用
  * 能够执行 get、wrap 键、解包键操作

## <a name="set-the-right-permissions-for-key-operations"></a>为密钥操作设置正确的权限

1. 在 Key Vault 中，选择 "**访问策略**" > "**添加访问策略**"。

   ![突出显示了 "访问策略" 和 "添加访问策略" Key Vault 屏幕截图](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. 选择 "**密钥权限**"，然后选择 "**获取**"、"**换行**"、"**解包**" 和 "**主体**"，即 MySQL 服务器的名称。 如果在现有主体列表中找不到你的服务器主体，则需要注册它。 当你首次尝试设置数据加密时，系统将提示你注册服务器主体，并且失败。

   ![访问策略概述](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. 选择“保存”。

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>设置 Azure Database for MySQL 的数据加密

1. 在 Azure Database for MySQL 中，选择 "**数据加密**" 设置客户管理的密钥。

   ![Azure Database for MySQL 的屏幕截图，其中突出显示了数据加密](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. 可以选择密钥保管库和密钥对，也可以输入密钥标识符。

   ![Azure Database for MySQL 的屏幕截图，其中突出显示了数据加密选项](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. 选择“保存”。

4. 若要确保所有文件（包括临时文件）都是完全加密的，请重新启动服务器。

## <a name="restore-or-create-a-replica-of-the-server"></a>还原或创建服务器的副本

使用存储在 Key Vault 中的客户托管密钥对 Azure Database for MySQL 进行加密后，任何新创建的服务器副本也会加密。 你可以通过本地或异地还原操作或通过副本（本地/跨区域）操作进行此新复制。 因此，对于加密的 MySQL 服务器，你可以使用以下步骤来创建加密还原服务器。

1. 在服务器上，选择 "**概述** > **还原**"。

   ![Azure Database for MySQL 的屏幕截图，其中突出显示了概述和还原](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   对于启用了复制的服务器，在 "**设置**" 标题下选择 "**复制**"。

   ![Azure Database for MySQL 的屏幕截图，其中突出显示了复制](media/concepts-data-access-and-security-data-encryption/mysql-replica.png)

2. 还原操作完成后，创建的新服务器将用主服务器的密钥进行加密。 但服务器上的功能和选项已禁用，并且服务器不可访问。 这会阻止任何数据操作，因为尚未向新服务器的标识授予访问密钥保管库的权限。

   ![突出显示状态为 "不可访问" 的 Azure Database for MySQL 屏幕截图](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. 若要使服务器可访问，请重新验证已还原服务器上的密钥。 选择 "**数据加密**" > 重新**验证密钥**。

   > [!NOTE]
   > 重新验证的第一次尝试将失败，因为需要为新服务器的服务主体授予对密钥保管库的访问权限。 若要生成服务主体，请选择 "重新**验证密钥**"，它将显示错误，但会生成服务主体。 之后，请参阅本文前面的[步骤](#set-the-right-permissions-for-key-operations)。

   ![突出显示重新验证步骤的 Azure Database for MySQL 屏幕截图](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   你将需要为密钥保管库访问新服务器。

4. 注册服务主体后再次重新验证密钥，然后服务器恢复其正常功能。

   ![显示已还原功能的 Azure Database for MySQL 屏幕截图](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>后续步骤

 若要了解有关数据加密的详细信息，请参阅[Azure Database for MySQL 通过客户托管的密钥进行数据加密](concepts-data-encryption-mysql.md)。
