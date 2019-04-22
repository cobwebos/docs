---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 23587e617c62cfe4aa24256330c5f7673dd1c674
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684400"
---
你的设备都与存储帐户用作 Azure 中的数据目标相关联。 对存储帐户的访问控制按订阅和两个 512 位存储访问密钥与该存储帐户相关联。

当数据框边缘设备访问存储帐户时，其中一个密钥用于身份验证。 另外一个密钥保留，让你能够定期轮换密钥。

出于安全原因，很多数据中心要求密钥轮换。 我们建议遵循密钥轮换的以下最佳实践：

- 存储帐户密钥类似于存储帐户的根密码。 请小心保护你的帐户密钥。 不将密码分发给其他用户，硬编码，或将其保存任意位置以其他人可以访问的纯文本形式。
- [重新生成帐户密钥](../articles/storage/common/storage-account-manage.md#regenerate-access-keys)使用 Azure 门户中，如果你认为可能已遭破坏。
- Azure 管理员应该定期使用 Azure 门户的“存储”部分来直接访问存储帐户，从而更改或重新生成主密钥或辅助密钥。