---
author: rothja
ms.service: cloud-services
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 390460cf6a22c164ee41a9b6679c7b7f2979d8ec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85838976"
---
| 资源 | 限制 |
| --- | --- |
| [每个部署的 Web 角色或辅助角色数](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |
| 每个部署的[实例输入终结点数](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) |25 |
| 每个部署的[输入终结点数](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) |25 |
| 每个部署的[内部终结点数](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) |25 |
| 每个部署的[托管服务证书数](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) |199 |

<sup>1</sup>每个具有 Web 角色或辅助角色的 Azure 云服务可以有两个部署，一个用于生产，一个用于过渡。 此限制针对不同角色的数目，即配置。 此限制并非针对每个角色的实例数，即缩放。

