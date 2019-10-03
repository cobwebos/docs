---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: e128f3c67a41322d9c25a8d6941e937729760bf4
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172896"
---
在此步骤中，创建一个防火墙规则以打开负载均衡终结点的探测端口（同样采用之前指定的 59999）和另一规则来打开可用性组侦听器端口。 由于在包含可用性组副本的 VM 上创建了负载均衡的终结点，需要打开相应 VM 上的探测端口和侦听器端口。

1. 在托管副本的虚拟机上，启动“具有高级安全性的 Windows 防火墙”。 

2. 右键单击“入站规则”，并单击“新建规则”   。

3. 在“规则类型”页中，选择“端口”，并单击“下一步”。   

4. 在“协议和端口”页面中，选择“TCP”，并选择“特定本地端口”框中的类型“59999”，再单击“下一步”。     

5. 在“操作”  页上，保持选中“允许连接”  ，并单击“下一步”  。

6. 在“配置文件”  页上，接受默认设置，并单击“下一步”  。

7. 在“名称”页的“名称”文本框中指定规则名称，例如 **Always On Listener Probe Port**，并单击“完成”。   

8. 为可用性组侦听器端口重复上述步骤（按之前在脚本的 $EndpointPort 参数中指定的那样），并指定合适的规则名称，例如 **Always On Listener Port**。

