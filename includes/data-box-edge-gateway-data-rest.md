---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: d8570c0be6b64d4e289575ce3f3f1721c4a65074
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684405"
---
对数据的静态：

- 对数据的静态，BitLocker XTS AES-256 加密用于保护的本地数据。
- 对于驻留在共享中的数据，对共享的访问权限受到限制。

    - 对于访问共享数据的 SMB 客户端，它们需要与共享相关联的用户凭据。 在共享创建时定义这些凭据。
    - 对于访问共享的 NFS 客户端，需要在创建共享时添加客户端的 IP 地址。