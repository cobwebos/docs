---
title: 为 Azure 中 RHEL 虚拟机上的 SQL Server 配置可用性组侦听器 - Linux 虚拟机 | Microsoft Docs
description: 了解如何在 Azure 中 RHEL 虚拟机上的 SQL Server 中设置可用性组侦听器
ms.service: virtual-machines-linux
ms.subservice: ''
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 03/11/2020
ms.openlocfilehash: 80557eb3776ba17a4922d1fc384b87419ffbd67e
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096909"
---
# <a name="tutorial-configure-availability-group-listener-for-sql-server-on-rhel-virtual-machines-in-azure"></a>教程：为 Azure 中 RHEL 虚拟机上的 SQL Server 配置可用性组侦听器

> [!NOTE]
> 演示的教程为**公共预览版**。 
>
> 本教程结合使用 SQL Server 2017 和 RHEL 7.6，但也可以使用 RHEL 7 或 RHEL 8 中的 SQL Server 2019 来配置 HA。 用于配置可用性组资源的命令在 RHEL 8 中已更改，有关正确命令的详细信息，请查看[创建可用性组资源](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) 和 RHEL 8 资源。

本教程逐步说明如何为 Azure 中 RHEL 虚拟机上的 SQL Server 创建可用性组侦听器。 将了解如何执行以下操作：

> [!div class="checklist"]
> - 在 Azure 门户中创建负载均衡器
> - 为负载均衡器配置后端池
> - 为负载均衡器创建探测
> - 设置负载均衡规则
> - 在群集中创建负载均衡器资源
> - 创建可用性组侦听器
> - 测试与侦听器的连接
> - 测试故障转移

## <a name="prerequisite"></a>先决条件

已完成[**教程：为 Azure 中 RHEL 虚拟机上的 SQL Server 配置可用性组**](sql-server-linux-rhel-ha-stonith-tutorial.md)

## <a name="create-the-load-balancer-in-the-azure-portal"></a>在 Azure 门户中创建负载均衡器

以下说明引导你完成[负载均衡器 - Azure 门户](../../../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md)一文的[在 Azure 门户中创建和配置负载均衡器](../../../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md#create-and-configure-the-load-balancer-in-the-azure-portal)部分中的步骤 1 到步骤 4。

### <a name="create-the-load-balancer"></a>创建负载均衡器

1. 在 Azure 门户中，打开包含 SQL Server 虚拟机的资源组。 

2. 在资源组中，单击“添加”。 

3. 搜索“负载均衡器”，并在搜索结果中选择 **Microsoft** 发布的“负载均衡器”。  

4. 在“负载均衡器”边栏选项卡上，单击“创建”。  

5. 在“创建负载均衡器”对话框中配置负载均衡器，如下所示： 

   | 设置 | 值 |
   | --- | --- |
   | **名称** |表示负载均衡器的文本名称。 例如 **sqlLB**。 |
   | 类型  |**内部** |
   | **虚拟网络** |创建的默认 VNet 应命名为 **VM1VNET**。 |
   | **子网** |选择 SQL Server 实例所在的子网。 默认值应是 **VM1Subnet**。|
   | IP 地址分配  |**静态** |
   | **专用 IP 地址** |使用在群集中创建的 `virtualip` IP 地址。 |
   | **订阅** |使用用于资源组的订阅。 |
   | **资源组** |选择 SQL Server 实例所在的资源组。 |
   | **位置** |选择 SQL Server 实例所在的 Azure 位置。 |

### <a name="configure-the-back-end-pool"></a>配置后端池
Azure 将后端地址池称作*后端池*。 在本例中，后端池是可用性组中三个 SQL Server 实例的地址。 

1. 在资源组中，单击已创建的负载均衡器。 

2. 在“设置”中，单击“后端池”。  

3. 在“后端池”中，单击“添加”创建后端地址池。   

4. 在“添加后端池”中的“名称”下，键入后端池的名称。  

5. 在“关联到”下，选择“虚拟机”。   

6. 选择环境中的每个虚拟机，并将相应的 IP 地址关联到所选的每个虚拟机。

    :::image type="content" source="media/sql-server-linux-rhel-ha-listener-tutorial/add-backend-pool.png" alt-text="添加后端池":::

7. 单击“添加”  。 

### <a name="create-a-probe"></a>创建探测

探测定义 Azure 如何确认哪一个 SQL Server 实例当前拥有可用性组侦听器。 Azure 根据创建探测时定义的端口上的 IP 地址来探测服务。

1. 在负载均衡器的“设置”边栏选项卡上，单击“运行状况探测”。   

2. 在“运行状况探测”边栏选项卡上，单击“添加”。  

3. 在“添加探测”边栏选项卡上配置探测。  使用以下值配置探测：

   | 设置 | 值 |
   | --- | --- |
   | **名称** |表示探测的文本名称。 例如 **SQLAlwaysOnEndPointProbe**。 |
   | 协议  |**TCP** |
   | 端口  |可以使用任何可用的端口。 例如 *59999*。 |
   | 间隔  |*5* |
   | 不正常阈值  |*2* |

4.  单击“确定”。  

5. 登录到所有虚拟机，使用以下命令打开探测端口：

    ```bash
    sudo firewall-cmd --zone=public --add-port=59999/tcp --permanent
    sudo firewall-cmd --reload
    ```

Azure 创建探测，并使用它来测试哪个 SQL Server 实例具有可用性组的侦听器。

### <a name="set-the-load-balancing-rules"></a>设置负载均衡规则

负载均衡规则设置负载均衡器将流量路由到 SQL Server 实例的方式。 对此负载均衡器，需要启用直接服务器返回，因为在三个 SQL Server 实例中，每次只有一个实例拥有可用性组侦听器资源。

1. 在负载均衡器的“设置”边栏选项卡上，单击“负载均衡规则”。   

2. 在“负载均衡规则”边栏选项卡上，单击“添加”。  

3. 使用“添加负载均衡规则”边栏选项卡配置负载均衡规则。  使用以下设置： 

   | 设置 | 值 |
   | --- | --- |
   | **名称** |表示负载均衡规则的文本名称。 例如 **SQLAlwaysOnEndPointListener**。 |
   | 协议  |**TCP** |
   | 端口  |*1433* |
   | 后端端口  |*1433*.将忽略此值，因为此规则使用“浮动 IP (直接服务器返回)”。  |
   | 探测  |使用为此负载均衡器创建的探测的名称。 |
   | **会话暂留** |无  |
   | **空闲超时(分钟)** |*4* |
   | 浮动 IP (直接服务器返回)  |**已启用** |

   :::image type="content" source="media/sql-server-linux-rhel-ha-listener-tutorial/add-load-balancing-rule.png" alt-text="添加负载均衡规则":::

4. 单击“确定”。  
5. Azure 将配置负载均衡规则。 负载均衡器现已配置为将流量路由到托管可用性组侦听器的 SQL Server 实例。 

此时，资源组有一个连接到所有 SQL Server 计算机的负载均衡器。 负载均衡器还包含 SQL Server Always On 可用性组侦听器的 IP 地址，以便任一计算机可以响应针对可用性组的请求。

## <a name="create-the-load-balancer-resource-in-the-cluster"></a>在群集中创建负载均衡器资源

1. 登录到主虚拟机。 我们需要创建用于启用 Azure 负载均衡器探测端口（本示例使用 59999）的资源。 运行以下命令：

    ```bash
    sudo pcs resource create azure_load_balancer azure-lb port=59999
    ```

1. 创建包含 `virtualip` 和 `azure_load_balancer` 资源的组：

    ```bash
    sudo pcs resource group add virtualip_group azure_load_balancer virtualip
    ```

### <a name="add-constraints"></a>添加约束

1. 必须配置并置约束，才能确保 Azure 负载均衡器 IP 地址和 AG 资源在同一节点上运行。 运行以下命令：

    ```bash
    sudo pcs constraint colocation add azure_load_balancer ag_cluster-master INFINITY with-rsc-role=Master
    ```
1. 创建排序约束以确保 AG 资源在 Azure 负载均衡器 IP 地址之前启动并运行。 虽然并置约束意味着排序约束，但这将强制执行它。

    ```bash
    sudo pcs constraint order promote ag_cluster-master then start azure_load_balancer
    ```

1. 若要验证约束，请运行以下命令：

    ```bash
    sudo pcs constraint list --full
    ```

    应该会看到以下输出：

    ```output
    Location Constraints:
    Ordering Constraints:
      promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
      promote ag_cluster-master then start azure_load_balancer (kind:Mandatory) (id:order-ag_cluster-master-azure_load_balancer-mandatory)
    Colocation Constraints:
      virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
      azure_load_balancer with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-azure_load_balancer-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

## <a name="create-the-availability-group-listener"></a>创建可用性组侦听器

1. 在主节点上的 SQLCMD 或 SSMS 中运行以下命令：

    - 请将下面使用的 IP 地址替换为 `virtualip` IP 地址。

    ```sql
    ALTER AVAILABILITY
    GROUP [ag1] ADD LISTENER 'ag1-listener' (
            WITH IP(('10.0.0.7'    ,'255.255.255.0'))
                ,PORT = 1433
            );
    GO
    ```

1. 登录到每个 VM 节点。 使用以下命令打开 hosts 文件，并为每个计算机上的 `ag1-listener` 设置主机名解析。

    ```
    sudo vi /etc/hosts
    ```

    在 **vi** 编辑器中，输入 `i` 以插入文本，并在空白行中添加 `ag1-listener` 的 IP。 然后在该 IP 旁边的空白位置后面添加 `ag1-listener`。

    ```output
    <IP of ag1-listener> ag1-listener
    ```

    若要退出 **vi** 编辑器，请先按 **Esc** 键，然后输入命令 `:wq` 以写入文件并退出。 对每个节点均执行此操作。

## <a name="test-the-listener-and-a-failover"></a>测试侦听器和故障转移

### <a name="test-logging-into-sql-server-using-the-availability-group-listener"></a>使用可用性组侦听器测试登录到 SQL Server

1. 在 SQLCMD 中使用可用性组侦听器名称登录到 SQL Server 的主节点：

    - 使用前面创建的登录名，并将 `<YourPassword>` 替换为正确的密码。 以下示例使用在 SQL Server 中创建的 `sa` 登录名。

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
    ```

1. 检查连接到的服务器的名称。 在 SQLCMD 中运行以下命令：

    ```sql
    SELECT @@SERVERNAME
    ```

    输出应显示当前主节点。 如果以前未测试过故障转移，则主节点应是 `VM1`。

    键入 `exit` 命令退出 SQL 会话。

### <a name="test-a-failover"></a>测试故障转移

1. 运行以下命令，将主要副本手动故障转移到 `<VM2>` 或其他副本。 请将 `<VM2>` 替换为你的服务器名称值。

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

1. 检查约束时，将会看到由于手动故障转移而添加了另一个约束：

    ```bash
    sudo pcs constraint list --full
    ```

    将会看到一个添加了 ID `cli-prefer-ag_cluster-master` 的约束。

1. 使用以下命令删除 ID 为 `cli-prefer-ag_cluster-master` 的约束：

    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

1. 使用命令 `sudo pcs resource` 检查群集资源，应会看到主要实例现已变为 `<VM2>`。

    ```output
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
        Masters: [ <VM2> ]
        Slaves: [ <VM1> <VM3> ]
    Resource Group: virtualip_group
        azure_load_balancer        (ocf::heartbeat:azure-lb):      Started <VM2>
        virtualip  (ocf::heartbeat:IPaddr2):       Started <VM2>
    ```

1. 在 SQLCMD 中使用侦听器名称登录到主要副本：

    - 使用前面创建的登录名，并将 `<YourPassword>` 替换为正确的密码。 以下示例使用在 SQL Server 中创建的 `sa` 登录名。

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
    ```

1. 检查连接到的服务器。 在 SQLCMD 中运行以下命令：

    ```sql
    SELECT @@SERVERNAME
    ```

    应会看到，现已连接至已故障转移到的 VM。

## <a name="next-steps"></a>后续步骤

有关 Azure 中的负载均衡器的详细信息，请参阅：

> [!div class="nextstepaction"]
> [为 Azure SQL Server VM 上的可用性组配置负载均衡器](../../../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md)
