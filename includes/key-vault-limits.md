---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: efa367157a8fd896cdc9680bf2ab6ba6a9e3dbb0
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2018
ms.locfileid: "53429923"
---
密钥事务数（每个区域的每个保管库在 10 秒内允许的事务数上限<sup>1</sup>）：

|密钥类型|HSM-key<br>CREATE 密钥|HSM-key<br>所有其他事务|Software-key<br>CREATE 密钥|Software-key<br>所有其他事务|
|:---|---:|---:|---:|---:|
|RSA 2048 位|5|1000|10|2000|
|RSA 3072 位|5|250|10|500|
|RSA 4096 位|5|125|10|250|
|ECC P-256|5|1000|10|2000|
|ECC P-384|5|1000|10|2000|
|ECC P-521|5|1000|10|2000|
|ECC SECP256K1|5|1000|10|2000|
|

> [!NOTE]
> 如果查看下表，你会看到：对于软件支持的密钥，我们允许每 10 秒完成 2000 个事务，而对于 HSM 支持的密钥，我们允许每 10 秒完成 1000 个事务。 3072 密钥与 2048 密钥的软件支持事务的比率为 500/2000 或0.4。 这意味着，如果客户在 10 秒内完成 500 个 3072 密钥事务，那么它们将达到最大限制，而不能执行任何其他密钥操作。 
   
|密钥类型  | 软件密钥 |HSM-key  |
|---------|---------|---------|
|RSA 2048 位     |    2000     |   1000    |
|RSA 3072 位     |     500    |    250     |
|RSA 4096 位     |    125     |    250     |
|ECC P-256     |    2000     |  1000     |
|ECC P-384     |    2000     |  1000     |
|ECC P-521     |    2000     |  1000     |
|ECC SECP256K1     |    2000     |  1000     |


机密、托管存储帐户密钥，以及保管库事务：
| 事务类型 | 每个区域的每个保管库在 10 秒内允许的事务数上限<sup>1</sup> |
| --- | --- |
| 所有事务 |2000 |
|

有关超出这些限制时如何处理限制的信息，请参阅 [Azure Key Vault 限制指南](../articles/key-vault/key-vault-ovw-throttling.md)。

<sup>1</sup> 所有事务类型都没有订阅范围的限制，即每个密钥保管库限制的 5 倍。 例如，每个订阅的 HSM- 其他事务数限制为 10 秒内 5000 个事务。
