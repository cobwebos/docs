---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 0e55c372c6f5dc3484bd64cf4f328479d2d0b245
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60461309"
---
## <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>密钥事务 (每个区域保管库在 10 秒内，每个允许的最大事务<sup>1</sup>):

|密钥类型|HSM 密钥<br>创建密钥|HSM 密钥<br>所有其他事务|软件密钥<br>创建密钥|软件密钥<br>所有其他事务|
|:---|---:|---:|---:|---:|
|RSA 2048 位|5|1,000|10|2,000|
|RSA 3072 位|5|250|10|500|
|RSA 4096 位|5|125|10|250|
|ECC P-256|5|1,000|10|2,000|
|ECC P-384|5|1,000|10|2,000|
|ECC P-521|5|1,000|10|2,000|
|ECC SECP256K1|5|1,000|10|2,000|

> [!NOTE]
> 在上表中，我们看到，对于 RSA 2048 位软件密钥，允许每 10 秒 2,000 个获取事务。 对于 RSA 2048 位的 HSM 密钥，允许每 10 秒的 1,000 个获取事务。
>
> 限制阈值时权重，并强制位于其总和。 例如上, 表中所示，请执行 GET 操作在 RSA HSM 密钥时它是八倍更昂贵，若要使用 4096 位密钥相比 2,048 位密钥。 这是因为 1,000/125 = 8。
>
> 在给定的 10 秒间隔内，Azure 密钥保管库客户端可以执行*只有一个*的以下操作之前遇到`429`限制 HTTP 状态代码：
> - 2,000 个 RSA 2048 位软件密钥获取事务
> - 1,000 个 RSA 2048 位 HSM 密钥获取事务
> - 125 RSA 4096 位 HSM 密钥获取事务
> - 124 RSA 4096 位的 HSM 密钥获取事务和 8 RSA 2048 位 HSM 密钥获取事务

## <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>机密、 托管的存储帐户密钥和保管库事务：
| 事务类型 | 每个区域保管库在 10 秒内，每个允许的最大事务<sup>1</sup> |
| --- | --- |
| 所有事务 |2,000 |

有关如何处理限制时超出这些限制的信息，请参阅[Azure Key Vault 限制指南](../articles/key-vault/key-vault-ovw-throttling.md)。

<sup>1</sup>的所有事务类型的订阅范围限制为五次每个密钥保管库的限制。 例如，每个订阅的 HSM-其他事务在 10 秒内每个订阅仅限于 5,000 个事务。
