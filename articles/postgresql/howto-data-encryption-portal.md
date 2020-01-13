---
title: 使用门户 Azure Database for PostgreSQL 单一服务器的数据加密
description: 了解如何使用 Azure 门户设置和管理 Azure Database for PostgreSQL 单一服务器的数据加密
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: 51c99ca0788900397c922e31e44f121a7ae9caa6
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75904258"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-using-portal"></a>使用门户 Azure Database for PostgreSQL 单一服务器的数据加密

本文介绍如何设置和管理，以使用 Azure 门户设置 Azure Database for PostgreSQL 单一服务器的数据加密。

## <a name="prerequisites-for-powershell"></a>PowerShell 先决条件

* 必须有一个 Azure 订阅，并且是该订阅的管理员。
* 必须安装并运行 Azure PowerShell。
* 创建用于客户托管密钥的 Azure Key Vault 和密钥。
* Key vault 必须具有以下属性以用作客户托管密钥
    * [软删除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)

        ```azurecli-interactive
        az resource update --id $(az keyvault show --name \ <key_valut_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
        ```
    
    * [清除保护](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection)

        ```azurecli-interactive
        az keyvault update --name <key_valut_name> --resource-group <resource_group_name>  --enable-purge-protection true
        ```

* 密钥必须具有以下用于客户托管密钥的属性。
    * 无过期日期
    * 未禁用
    * 能够执行 get、wrap 键、解包键操作

## <a name="setting-the-right-permissions-for-key-operations"></a>为密钥操作设置正确的权限

1. 在 Azure Key Vault 上，选择 "**访问策略**" 和 "**添加访问策略**" 

   ![访问策略概述](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. 在 "**权限**" 下，选择 "**获取**"、"**换行**"、"**解包**" 和 "**主体**"，这是 PostgreSQL 服务器的名称。

   ![访问策略概述](media/concepts-data-access-and-security-data-encryption/access-policy-warp-unwrap.png)

3. 保存设置。

## <a name="setting-data-encryption-for-azure-database-for-postgresql-single-server"></a>为 Azure Database for PostgreSQL 单个服务器设置数据加密

1. 在**Azure Database for PostgreSQL**上，选择要设置客户管理的密钥设置的**数据加密**。

   ![设置数据加密](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. 您可以选择**Key Vault**和**密钥**对，也可以传递**密钥标识符**。

   ![设置 Key Vault](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. 保存设置。

4. 若要确保所有文件（包括临时文件）都是完全加密的，需要重新启动服务器。

## <a name="restoring-or-creating-replica-of-the-server-which-has-data-encryption-enabled"></a>还原或创建已启用数据加密的服务器副本

Azure Database for PostgreSQL 单服务器使用存储在 Key Vault 中的客户托管密钥进行加密后，可以通过本地或异地还原操作或使用副本（本地/跨区域）操作，为服务器创建的任何新副本。 因此，对于加密的 PostgreSQL 服务器，你可以执行以下步骤来创建加密还原服务器。

1. 在服务器上，选择 "**概述**"，然后选择 "**还原**"。

   ![启动-还原](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   对于已启用复制的服务器，在 "**设置**" 标题下选择 "**复制**"

   ![Initiate-副本](media/concepts-data-access-and-security-data-encryption/postgresql-replica.png)

2. 还原操作完成后，新创建的服务器就是用主服务器的密钥对数据进行加密。 但服务器上的功能和选项已禁用，并且服务器被标记为**不可访问**的状态。 这是为了防止任何数据操作，因为尚未向新服务器的标识授予对 Key Vault 的访问权限。

   ![标记服务器不可访问](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)


3. 若要修复不可访问状态，需要重新验证还原的服务器上的密钥。

   ![重新验证服务器](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   你将必须向 Key Vault 授予对新服务器的访问权限。 

4. 重新验证密钥后，服务器将恢复其正常功能。

   ![正常服务器已还原](media/concepts-data-access-and-security-data-encryption/restore-successful.png)


## <a name="next-steps"></a>后续步骤

 若要了解有关数据加密的详细信息，请参阅[什么是 Azure 数据加密](concepts-data-encryption-postgresql.md)。