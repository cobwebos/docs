---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 3295c7e677e4c9bf7bff27614fe5fa4fa3200570
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334693"
---
| 资源 | 限制 |
| --- | --- |
| [每个部署的 Web 角色或辅助角色数](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |
| 每个部署的[实例输入终结点数](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) |25 |
| 每个部署的[输入终结点数](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) |25 |
| 每个部署的[内部终结点数](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) |25 |
| 每个部署的[托管服务证书数](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) |199 |

<sup>1</sup>每个具有 Web 角色或辅助角色的 Azure 云服务可以有两个部署，一个用于生产，一个用于过渡。 此限制针对不同角色的数目，即配置。 此限制并非针对每个角色的实例数，即缩放。

