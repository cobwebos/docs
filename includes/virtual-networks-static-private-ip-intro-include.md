---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 02/14/2020
ms.author: genli
ms.openlocfilehash: 3d0c475cebedf28b41f16fbde312a1aac14ef207
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "80060475"
---
系统会从指定的范围自动为虚拟机 (VM) 分配专用 IP 地址，具体取决于 VM 部署时所在的子网。 VM 会保留地址，直到该 VM 被删除。 Azure 从你创建 VM 时所在的子网动态分配下一个可用的专用 IP 地址。 如果想要已分配给 VM 的子网中的特定 IP 地址，请分配静态 IP 地址。
