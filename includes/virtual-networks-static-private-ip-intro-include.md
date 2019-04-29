---
author: rockboyfor
ms.service: virtual-network
ms.topic: include
origin.date: 02/27/2019
ms.date: 04/01/2019
ms.author: v-yeche
ms.openlocfilehash: 63853ee34d99516b139b0356f609f41637ac2b39
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60516420"
---
系统会从指定的范围自动为虚拟机 (VM) 分配专用 IP 地址，具体取决于部署时所在的子网。 该地址会由某个 VM 保留，直到该 VM 被删除。 Azure 从你创建 VM 时所在的子网动态分配下一个可用的专用 IP 地址。 如果需要已分配给 VM 的子网中的特定 IP 地址，请分配静态 IP 地址。