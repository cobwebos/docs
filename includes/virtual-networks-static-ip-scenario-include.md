---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 02/14/2020
ms.author: genli
ms.openlocfilehash: 280943bd965c4799ce294321129d1088be9c0caf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "80060520"
---
## <a name="scenario"></a>方案

为了更好地说明如何为 VM 配置静态 IP 地址，本文档使用这一方案：

![虚拟网络方案：前端和后端子网，前端子网具有静态 IP 地址](./media/virtual-networks-static-ip-scenario-include/static-ip-scenario.png)

在此方案中，在 FrontEnd 子网中创建一个名为 DNS01 的 VM，然后将它设置为使用静态 IP 地址 192.168.1.101  。
