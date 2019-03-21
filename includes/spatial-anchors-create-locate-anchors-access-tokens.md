---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 0e4c0b6d622fe0d8d20692ec6c0c889779e4601a
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2019
ms.locfileid: "56751672"
---
### <a name="access-tokens"></a>访问令牌

访问令牌是更可靠的方法来使用 Azure 空间的定位点进行身份验证。 尤其是当你准备进行生产部署时应用程序。 此方法的摘要是设置你的客户端应用程序可以安全地进行身份验证与后端服务。 您的后端服务接口与 AAD 在运行时和 Azure 空间的定位点 STS 服务可以请求访问令牌。 此令牌是然后传送到客户端应用程序，用于在 SDK 中使用 Azure 空间的定位点进行身份验证。
