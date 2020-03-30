---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 8840e6649058837207c75dbdf7baa5c0b11ef387
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75467010"
---
您的设备与用作 Azure 中数据目标的存储帐户相关联。 对存储帐户的访问由订阅和与该存储帐户关联的两个 512 位存储访问密钥控制。

当数据框边缘设备访问存储帐户时，其中一个密钥用于身份验证。 另一个键保留在保留中，因此您可以定期旋转键。

出于安全原因，许多数据中心需要密钥轮换。 我们建议遵循密钥轮换的以下最佳实践：

- 存储帐户密钥类似于存储帐户的根密码。 小心保护您的帐户密钥。 不要将密码分发给其他用户，不要对其进行硬编码，或将其保存在其他人可访问的纯文本中的任何位置。
- 如果您认为帐户密钥可能受到威胁，则通过 Azure 门户重新生成帐户密钥。 有关详细信息，请参阅[管理存储帐户访问密钥](../articles/storage/common/storage-account-keys-manage.md)。
- Azure 管理员应通过使用 Azure 门户的"存储"部分直接访问存储帐户，定期更改或重新生成主密钥或辅助密钥。