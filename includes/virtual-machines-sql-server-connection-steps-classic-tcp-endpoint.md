---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: d4e8d99cd7c67136f359772664eb017c6207e6e4
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172893"
---
### <a name="create-a-tcp-endpoint-for-the-virtual-machine"></a>为虚拟机创建 TCP 终结点
要从 Internet 访问 SQL Server，虚拟机必须具有终结点以侦听传入的 TCP 通信。 此 Azure 配置步骤将传入 TCP 端口通信定向到虚拟机可以访问的 TCP 端口。

> [!NOTE]
> 如果在同一云服务或虚拟网络中连接，则不需要创建一个公开访问的终结点。 在这种情况下，可以继续执行下一步。 有关详细信息，请参阅[连接方案](../articles/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-connect.md#connection-scenarios)。
> 
> 

1. 在 Azure 门户中，选择“虚拟机(经典)”。 
2. 然后选择 SQL Server 虚拟机。
3. 选择“终结点”，并单击终结点边栏选项卡顶部的“添加”按钮。  
   
    ![用于创建终结点的门户步骤](./media/virtual-machines-sql-server-connection-steps/portal-endpoint-creation.png)
4. 在“添加终结点”边栏选项卡上，提供“名称”，例如 SQLEndpoint。  
5. 选择“TCP”作为“协议”   。
6. 为“公用端口”指定端口号，如“57500”   。
7. 为“专用端口”，指定 SQL Server 侦听端口，默认为“1433”   。
8. 单击“确定”  创建终结点。

