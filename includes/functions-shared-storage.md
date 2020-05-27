---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.openlocfilehash: 82d122ed236dc72ced7ebafe2301ef5f1143897f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "76963643"
---
若要最大程度地提高性能，请对每个函数应用使用单独的存储帐户。 如果有 Durable Functions 或事件中心触发的函数，则请注意，这两种函数都会产生大量存储事务，这一点特别重要。 当应用程序逻辑与 Azure 存储交互时，无论是直接（使用存储 SDK）交互还是通过某个存储绑定进行交互，都应使用专用存储帐户。 例如，如果有事件中心触发的函数将一些数据写入 blob 存储，请使用两个存储帐户&mdash;一个用于函数应用，另一个用于由函数存储的 blob。