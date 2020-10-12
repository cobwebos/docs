---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: eb55d993ad8960f821c2b72f0a53602166b7cc7e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "69900618"
---
对于静态数据：

- 对共享中存储的数据的访问受到限制。

    - 访问共享数据的 SMB 客户端需要与共享关联的用户凭据。 这些凭据是在创建共享时定义的。
    - 需要在创建共享时添加访问共享的 NFS 客户端的 IP 地址。
