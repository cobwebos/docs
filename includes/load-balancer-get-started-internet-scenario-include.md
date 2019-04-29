---
author: WenJason
ms.service: load-balancer
ms.topic: include
origin.date: 11/09/2018
ms.date: 12/31/2018
ms.author: v-jay
ms.openlocfilehash: bf3aee30460e052db23cf9090f13e2af3b22628b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60516995"
---
会在此方案中完成以下任务：

* 创建在端口 80 上接收网络流量的负载均衡器，并将负载均衡流量发送到虚拟机“web1”和“web2”
* 在负载均衡器后面创建虚拟机的远程桌面访问/SSH 的 NAT 规则
* 创建运行状况探测

![负载均衡器方案](./media/load-balancer-get-started-internet-scenario-include/scenario-classic.png)