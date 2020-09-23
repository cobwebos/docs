---
title: 如何确保验证 Azure Database for MySQL 数据
description: 了解如何使用客户托管密钥验证 Azure Database for MySQL 数据加密的加密。
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 04/28/2020
ms.openlocfilehash: e7b747bd2babeeccb210bb30c3eb28f22d4befe7
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90905796"
---
# <a name="validating-data-encryption-for-azure-database-for-mysql"></a>验证 Azure Database for MySQL 的数据加密

本文将帮助你验证使用客户托管密钥进行的数据加密 Azure Database for MySQL 按预期方式工作。

## <a name="check-the-encryption-status"></a>检查加密状态

### <a name="from-portal"></a>从门户

1. 如果要验证客户的密钥是否用于加密，请执行以下步骤：

    * 在 Azure 门户中，导航到**Azure Key Vault**  ->  **键**
    * 选择用于服务器加密的密钥。
    * 将 " **已启用** 密钥" 的状态设置为 " **否**"。
  
       经过一段时间后 (**~ 15 分钟**) ，Azure Database for MySQL 服务器 **状态** 应为 " **不可访问**"。 对服务器所做的任何 i/o 操作都将失败，验证服务器确实已用客户密钥进行了加密，并且密钥当前无效。
    
       为了使服务器 **可用于** ，可以重新验证密钥。 
    
    * 将 Key Vault 中的项的状态设置为 **"是"**。
    * 在 "服务器 **数据加密**" 中，选择 "重新 **验证密钥**"。
    * 成功重新验证密钥后，服务器 **状态** 将更改为 " **可用**"。

2. 在 Azure 门户上，如果你可以确保设置加密密钥，则使用 Azure 门户中使用的客户密钥对数据进行加密。

  :::image type="content" source="media/concepts-data-access-and-security-data-encryption/byok-validate.png" alt-text="访问策略概述":::

### <a name="from-cli"></a>从 CLI

1. 我们可以使用 *AZ CLI* 命令验证 Azure Database for MySQL 服务器所使用的关键资源。

    ```azurecli-interactive
   az mysql server key list --name  '<server_name>'  -g '<resource_group_name>'
    ```

    对于没有设置数据加密的服务器，此命令会生成空集 []。

### <a name="azure-audit-reports"></a>Azure 审核报告

还可以查看[审核报告](https://servicetrust.microsoft.com)，以提供与数据保护标准和法规要求有关的信息。

## <a name="next-steps"></a>后续步骤

若要了解有关数据加密的详细信息，请参阅 [Azure Database for MySQL 通过客户托管的密钥进行数据加密](concepts-data-encryption-mysql.md)。