---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: acf5a7e8d7feebaac3d82629c5b561a22112de0f
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553306"
---
| 资源 | 默认限制 | 最大限制 |
| --- | --- | --- |
| [每个部署的 web 或辅助角色](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [实例输入终结点](https://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint)每个部署 |25 |25 |
| [输入终结点](https://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint)每个部署 |25 |25 |
| [内部终结点](https://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint)每个部署 |25 |25 |
| 每个部署的[托管服务证书数](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) |199 |199 |

<sup>1</sup>每个 Azure 云服务中使用 web 或辅助角色可以有两个部署，一个用于生产，一个用于过渡。 此限制是指不同的角色，即配置数量。 此限制不是指每个角色，实例数，即缩放。

