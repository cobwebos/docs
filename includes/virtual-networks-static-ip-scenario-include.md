---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 02/14/2020
ms.author: genli
ms.openlocfilehash: 280943bd965c4799ce294321129d1088be9c0caf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80060520"
---
## <a name="scenario"></a>场景

为了更好地说明如何为 VM 配置静态 IP 地址，本文档使用以下方案：

![虚拟网络方案：前端子网和后端子网，具有前端子网的静态 IP 地址](./media/virtual-networks-static-ip-scenario-include/static-ip-scenario.png)

在此方案中，将在*前端子网*中创建名为*DNS01*的 VM，并将其设置为使用静态 IP 地址*192.168.1.101*。
