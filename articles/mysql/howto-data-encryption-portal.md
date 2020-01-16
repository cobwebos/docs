---
title: 使用门户 Azure Database for MySQL 的数据加密
description: 了解如何使用 Azure 门户设置和管理 Azure Database for MySQL 的数据加密。
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 5bd100a5858660d80f3704844ed974f2bc9257b4
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028941"
---
# <a name="data-encryption-for-azure-database-for-mysql-server-using-azure-portal"></a>使用 Azure 门户 Azure Database for MySQL 服务器的数据加密

在本文中，你将了解如何设置和管理，以使用 Azure 门户设置 Azure Database for MySQL 的数据加密。

## <a name="prerequisites-for-cli"></a>CLI 先决条件

* 必须有一个 Azure 订阅，并且是该订阅的管理员。
* 创建用于客户托管密钥的 Azure Key Vault 和密钥。
* Key Vault 必须具有以下属性以用作客户托管的密钥：
  * [软删除](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [清除保护](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* 密钥必须具有以下用于客户托管密钥的属性。
  * 无过期日期
  * 未禁用
  * 能够执行“获取”、“包装密钥”和“解包密钥”操作

## <a name="setting-the-right-permissions-for-key-operations"></a>为密钥操作设置正确的权限

1. 在 Azure Key Vault 上，选择 "**访问策略**"，然后单击 "**添加访问策略**"：

   ![访问策略概述](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. 选择 "**密钥" 权限**，然后选择 "**获取**"、"**换行**"、"**解包**" 和 "**主体**"，即 MySQL 服务器的名称。 如果在现有主体列表中找不到你的服务器主体，你将需要在第一次尝试设置数据加密时进行注册，否则将会失败。

   ![访问策略概述](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. 保存设置。

## <a name="setting-data-encryption-for-azure-database-for-mysql"></a>为 Azure Database for MySQL 设置数据加密

1. 在**Azure Database for MySQL**上，选择要设置客户管理的密钥设置的**数据加密**。

   ![设置数据加密](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. 您可以选择**Key Vault**和**密钥**对，也可以传递**密钥标识符**。

   ![设置 Key Vault](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. 保存设置。

4. 若要确保所有文件（包括**临时文件**）都是完全加密的，**需要** **重新启动**服务器。

## <a name="restoring-or-creating-replica-of-the-server-which-has-data-encryption-enabled"></a>还原或创建已启用数据加密的服务器副本

使用存储在 Key Vault 中的客户托管密钥对 Azure Database for MySQL 进行加密后，可以通过本地或异地还原操作或使用副本（本地/跨区域）操作对服务器的任何新创建的副本进行加密。 因此，对于加密的 MySQL 服务器，你可以执行以下步骤来创建加密还原服务器。

1. 在服务器上，选择 "**概述**"，然后选择 "**还原**"。

   ![启动-还原](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   对于启用了复制的服务器，在 "**设置**" 标题下选择 "**复制**"，如下所示：

   ![Initiate-副本](media/concepts-data-access-and-security-data-encryption/mysql-replica.png)

2. 还原操作完成后，将使用用于对主服务器进行加密的密钥来加密创建的新服务器。 但服务器上的功能和选项已禁用，并且服务器被标记为**不可访问**的状态。 此行为旨在防止任何数据操作，因为尚未向新服务器的标识授予访问 Key Vault 的权限。

   ![标记服务器不可访问](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. 若要修复不可访问状态，需要重新验证还原的服务器上的密钥。 选择 "**数据加密**" 窗格，然后选择 "重新**验证密钥**" 按钮。

   > [!NOTE]
   > 第一次尝试重新验证将会失败，因为需要为新服务器的服务主体授予对密钥保管库的访问权限。 若要生成服务主体，请选择 "重新**验证密钥**"，此密钥将生成错误，但会生成服务主体。 之后，请参阅上述第[2 部分中的](#setting-the-right-permissions-for-key-operations)步骤。

   ![重新验证服务器](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   你将必须向 Key Vault 授予对新服务器的访问权限。

4. 注册服务主体后，你将需要再次重新验证密钥，并使服务器恢复其正常功能。

   ![正常服务器已还原](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>后续步骤

 若要了解有关数据加密的详细信息，请参阅[什么是 Azure 数据加密](concepts-data-encryption-mysql.md)。
