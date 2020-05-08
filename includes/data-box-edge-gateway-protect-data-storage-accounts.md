---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 9ac9865afe37916f1777d92eab8637884eba0c08
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562112"
---
设备与在 Azure 中用作数据目标的存储帐户相关联。 对存储帐户的访问由与该存储帐户关联的订阅和 2 512 位存储访问密钥控制。

当 Azure Stack Edge 设备访问存储帐户时，将使用其中一个密钥进行身份验证。 其他密钥保留，因此你可以定期轮换密钥。

出于安全原因，很多数据中心要求密钥轮换。 我们建议遵循密钥轮换的以下最佳实践：

- 存储帐户密钥类似于存储帐户的根密码。 小心保护帐户密钥。 不要将密码分发给其他用户、对其进行硬编码或将其以纯文本形式保存在其他用户可以访问的位置。
- 如果你认为帐户密钥可能已泄漏，请通过 Azure 门户重新生成帐户密钥。 有关详细信息，请参阅[管理存储帐户访问密钥](../articles/storage/common/storage-account-keys-manage.md)。
- Azure 管理员应定期更改或重新生成主密钥或辅助密钥，方法是使用 Azure 门户的 "存储" 部分直接访问存储帐户。