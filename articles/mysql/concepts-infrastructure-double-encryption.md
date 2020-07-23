---
title: 基础结构双加密-Azure Database for MySQL
description: 了解如何使用基础结构双加密添加使用服务托管密钥的第二层加密。
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 6/30/2020
ms.openlocfilehash: 6532ec33d930ab42a9aa04a92d84ab795f32ebd6
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86034815"
---
# <a name="azure-database-for-mysql-infrastructure-double-encryption"></a>Azure Database for MySQL 基础结构双加密

> [!NOTE]
> 此时，必须请求访问权限才能使用此功能。 为此，请联系 AskAzureDBforMySQL@service.microsoft.com。

Azure Database for MySQL 使用 Microsoft 的托管密钥对数据进行[静态数据存储加密](concepts-security.md#at-rest)。 包括备份在内的数据在磁盘上加密，此加密始终打开且无法禁用。 加密使用 FIPS 140-2 验证的加密模块和 AES 256 位密码进行 Azure 存储加密。

基础结构双加密使用服务托管密钥增加了另一层的加密。 它使用 FIPS 140-2 验证的加密模块，但使用不同的加密算法。 这为静态数据提供额外的保护层。 在基础结构双加密中使用的密钥也由 Azure Database for MySQL 服务管理。 默认情况下不启用基础结构双加密，因为其他加密层可能会影响性能。

> [!NOTE]
> 此功能适用于所有 Azure 区域，其中 Azure Database for MySQL 支持“常规用途”和“内存优化”定价层。

基础结构层加密具有在离存储设备或网络线路最近的层实现的好处。 Azure Database for MySQL 使用服务托管密钥来实现两个加密层。 尽管从技术上讲，在服务层中，它非常接近存储静态数据的硬件。 你仍可以使用预配的 MySQL 服务器的[客户托管密钥](concepts-data-encryption-mysql.md)来启用静态数据加密。 

基础结构层的实现还支持多样性的密钥。 基础结构必须知道计算机和网络的不同群集。 在这种情况下，将使用不同的密钥来最大程度地减少基础结构攻击的冲击半径和各种硬件和网络故障。 

> [!NOTE]
> 使用基础结构双加密会对 Azure Database for MySQL 服务器造成性能影响，因为附加的加密过程。

## <a name="benefits"></a>好处

Azure Database for MySQL 的基础结构双加密具有以下优势：

1. **增加了加密实现的多样性**-计划迁移到基于硬件的加密会通过基于软件的实现提供基于硬件的实现，进一步多元化实现。
2. **实现错误**-基础结构层的两层加密可防止在公开纯文本数据的更高层中进行缓存或内存管理中的任何错误。 此外，这两个层还可以确保通常在加密的实现中出现错误。

这种组合提供了强大的防护机制来防范用于攻击加密的常见威胁和漏洞。

## <a name="supported-scenarios-with-infrastructure-double-encryption"></a>基础结构双加密支持的方案

Azure Database for MySQL 提供的加密功能可以一起使用。 下面是可以使用的各种方案的摘要：

|  ##   | 默认加密 | 基础结构双加密 | 使用客户托管密钥的数据加密  |
|:------|:------------------:|:--------------------------------:|:--------------------------------------------:|
| 1     | *是*              | *否*                             | *否*                                         |
| 2     | *是*              | *是*                            | *否*                                         |
| 3     | *是*              | *否*                             | *是*                                        |
| 4     | *是*              | *是*                            | *是*                                        |
|       |                    |                                  |                                              |

> [!Important]
> - 方案2和4将对 Azure Database for MySQL 服务器带来显著的性能影响，因为这会增加基础结构加密层。
> - 仅允许在服务器创建过程中为 Azure Database for MySQL 配置基础结构双加密。 设置服务器后，将无法更改存储加密。 但是，你仍然可以使用客户管理的密钥来启用使用/不使用基础结构双加密创建的服务器的数据加密。

## <a name="limitations"></a>限制

对于 Azure Database for MySQL，使用服务托管密钥对基础结构进行双重加密的支持具有以下限制：

* 对此功能的支持仅限于**常规用途**和**内存优化**定价层。
* * 此功能仅支持在支持存储高达 16 TB 的区域和服务器上。 有关支持存储高达 16 TB 的 Azure 区域列表，请参阅[存储文档](concepts-pricing-tiers.md#storage)。

    > [!NOTE]
    > - 以上列出的区域中创建的所有**新**MySQL 服务器还支持对客户管理器密钥进行数据加密。 在这种情况下，通过时间点还原（PITR）或读取副本创建的服务器不符合 "new"。
    > - 若要验证预配的服务器是否最多支持 16 TB，可转到门户中的 "定价层" 边栏选项卡，查看 "存储" 滑块是否可以移动到 16 TB。 如果只能将滑块移动到 4 TB，则服务器可能不支持对客户管理的密钥进行加密;但是，始终使用服务托管密钥对数据进行加密。 AskAzureDBforMySQL@service.microsoft.com如果你有任何疑问，请联系。

## <a name="next-steps"></a>后续步骤

了解如何为[Azure database For MySQL 设置基础结构双加密](howto-double-encryption.md)。