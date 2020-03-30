---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 2/14/2020
ms.author: rohink
ms.openlocfilehash: 3fbbb9d491eef202309c9e64f9a5a839a5d4516d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334657"
---
**公共 DNS 区域**

| 资源 | 限制 |
| --- | --- |
| 每个订阅的公共 DNS 区域数 |250 <sup>1</sup> |
| 每个公共 DNS 区域的记录集数 |10,000 <sup>1</sup> |
| 公共 DNS 区域中每个记录集的记录数 |20 |
| 单个 Azure 资源的别名记录数 |20|
| 每个订阅的专用 DNS 区域 |1000|
| 每个专用 DNS 区域的记录集 |25000|
| 为专用 DNS 区域设置的记录 |20|
| 每个专用 DNS 区域的虚拟网络链接 |1000|
| 启用自动注册的专用 DNS 区域的虚拟网络链接 |100|
| 虚拟网络可以通过启用自动注册连接到的专用 DNS 区域数 |1|
| 虚拟网络可以链接的专用 DNS 区域数 |1000|
| 虚拟机每秒可以发送到 Azure DNS 解析器的 DNS 查询数 |500 <sup>2</sup> |
| 每个虚拟机排队（挂起响应）的最大 DNS 查询数 |200 <sup>2</sup> |

<sup>1</sup>如果需要增加这些限制，请与 Azure 支持部门联系。

<sup>2</sup>这些限制应用于每个单独的虚拟机，而不是在虚拟网络级别。 将删除超出这些限制的 DNS 查询。