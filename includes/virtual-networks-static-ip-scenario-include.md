---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 02/14/2020
ms.author: genli
ms.openlocfilehash: 280943bd965c4799ce294321129d1088be9c0caf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060520"
---
## <a name="scenario"></a>方案

为了更好地说明如何为 VM 配置静态 IP 地址，本文档使用以下方案：

![虚拟网络方案：前端和后端子网，具有前端子网的静态 IP 地址](./media/virtual-networks-static-ip-scenario-include/static-ip-scenario.png)

在这种情况下，您将在*FrontEnd*子网中创建名为*DNS01*的 VM，然后将其设置为使用*192.168.1.101*的静态 IP 地址。
