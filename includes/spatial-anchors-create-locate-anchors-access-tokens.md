---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 63725d55e2b2935ec6a899789249259b096865c3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "67172645"
---
### <a name="access-tokens"></a>访问令牌

访问令牌是一种更可靠的方法，用于使用 Azure 空间定位点进行身份验证。 为生产部署准备应用程序时，它尤其有用。 概括而言，此方法设置客户端应用程序可以用来安全地进行身份验证的后端服务。 后端服务在运行时与 AAD 进行交互并与 Azure 空间定位点安全令牌服务进行交互来请求访问令牌。 然后，该令牌将被传递给客户端应用程序，并在 SDK 中用来通过 Azure 空间定位点进行身份验证。
