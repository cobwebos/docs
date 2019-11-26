---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 2d16febd4676ca7ba763eb7bc6dcecda4608ebb5
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224357"
---
#### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>密钥事务数（每个区域的每个保管库在 10 秒内允许的事务数上限<sup>1</sup>）：

|密钥类型|HSM 密钥<br>CREATE 密钥|HSM 密钥<br>所有其他事务|软件密钥<br>CREATE 密钥|软件密钥<br>所有其他事务|
|:---|---:|---:|---:|---:|
|RSA 2,048 位|5|1,000|10|2,000|
|RSA 3,072 位|5|250|10|500|
|RSA 4,096 位|5|125|10|250|
|ECC P-256|5|1,000|10|2,000|
|ECC P-384|5|1,000|10|2,000|
|ECC P-521|5|1,000|10|2,000|
|ECC SECP256K1|5|1,000|10|2,000|

> [!NOTE]
> 在上表中，我们发现，对于 RSA 2048 位软件密钥，允许2000每10秒获取事务数。 对于 RSA 2048 位 HSM-密钥，允许1000每10秒获取事务数。
>
> 限制阈值为加权，并强制执行。 例如，如上表所示，在 RSA HSM 密钥上执行 GET 操作时，使用4096位密钥比2048位密钥的8倍。 这是因为 1000/125 = 8。
>
> 在给定的10秒的时间间隔内，Azure Key Vault 客户端在遇到 `429` 限制 HTTP 状态代码之前只能执行以下操作*之一*：
> - 2000 RSA 2048 位软件-密钥获取事务
> - 1000 RSA 2048-位 HSM-密钥获取事务
> - 125 RSA 4096-位 HSM-密钥获取事务
> - 124 RSA 4096-位 HSM-密钥获取事务和 8 RSA 2048 位 HSM-密钥获取事务

#### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>机密、托管存储帐户密钥，以及保管库事务：
| 事务类型 | 每个区域的每个保管库在 10 秒内允许的事务数上限<sup>1</sup> |
| --- | --- |
| 所有事务 |2,000 |

有关超出这些限制时如何处理限制的信息，请参阅 [Azure Key Vault 限制指南](../articles/key-vault/key-vault-ovw-throttling.md)。

<sup>1</sup> 所有事务类型的订阅范围限制是每个密钥保管库限制的 5 倍。 例如，每个订阅的 HSM-其他事务数限制5000为每个订阅10秒的事务数。
