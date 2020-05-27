---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 2cfd376f595ae70daf9ab468d464dd9c8ff13d74
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335020"
---
| 资源 | 限制 |
| --- | --- |
| 每个云服务的[虚拟机数](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)<sup>1</sup> |50 |
| 每个云服务的输入终结点数<sup>2</sup> |150 |

<sup>1</sup>使用经典部署模型而非 Azure 资源管理器创建的虚拟机自动存储在云服务中。 可以向该云服务添加更多虚拟机以获得负载均衡和可用性。 

<sup>2</sup>输入终结点允许从某个虚拟机的云服务外部与该虚拟机通信。 位于同一云服务或虚拟网络中的虚拟机可以自动相互通信。 有关详细信息，请参阅[如何对虚拟机设置终结点](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。 
