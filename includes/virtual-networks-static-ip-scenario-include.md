---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 11/09/2018
ms.author: genli
ms.openlocfilehash: d40f8fb263c5838d523108a1df7be99aba587354
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172859"
---
## <a name="scenario"></a>场景
为了更好地说明如何为 VM 配置静态 IP 地址，本文档使用以下方案。

![VNet 方案](./media/virtual-networks-static-ip-scenario-include/static-ip-scenario.png)

在此方案中，会在 **FrontEnd** 子网中创建一个名为 **DNS01** 的 VM，并将它设置为使用静态 IP 地址 **192.168.1.101**。

