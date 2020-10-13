---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 994a7726adec07f2f6533d460d05469a0f3c7bf3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87102494"
---
| 资源 | 限制 |
| --- | --- |
| 每个云服务的虚拟机数 <sup>1</sup> |50 |
| 每个云服务的输入终结点数 <sup>2</sup> |150 |

<sup>1</sup> 通过使用经典部署模型（而不是 Azure 资源管理器）创建的虚拟机自动存储在云服务中。 可以向该云服务添加更多虚拟机以获得负载均衡和可用性。 

<sup>2</sup> 输入终结点允许从某个虚拟机的云服务外部与该虚拟机通信。 位于同一云服务或虚拟网络中的虚拟机可以自动相互通信。  
