---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 63725d55e2b2935ec6a899789249259b096865c3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66110638"
---
### <a name="access-tokens"></a>访问令牌

访问令牌是更可靠的方法来使用 Azure 空间的定位点进行身份验证。 尤其是当你准备进行生产部署时应用程序。 此方法的摘要是设置你的客户端应用程序可以安全地进行身份验证与后端服务。 您的后端服务接口与 AAD 在运行时和 Azure 空间的定位点安全令牌服务来请求访问令牌。 此令牌是然后传送到客户端应用程序，用于在 SDK 中使用 Azure 空间的定位点进行身份验证。
