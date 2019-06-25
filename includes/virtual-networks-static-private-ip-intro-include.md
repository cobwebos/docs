---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 02/27/2019
ms.author: genli
ms.openlocfilehash: 63853ee34d99516b139b0356f609f41637ac2b39
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172861"
---
系统会从指定的范围自动为虚拟机 (VM) 分配专用 IP 地址，具体取决于部署时所在的子网。 该地址会由某个 VM 保留，直到该 VM 被删除。 Azure 从你创建 VM 时所在的子网动态分配下一个可用的专用 IP 地址。 如果需要已分配给 VM 的子网中的特定 IP 地址，请分配静态 IP 地址。

