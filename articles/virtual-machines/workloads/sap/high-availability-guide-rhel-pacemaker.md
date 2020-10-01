---
title: 在 Azure 中的 RHEL 上设置 Pacemaker | Microsoft Docs
description: 在 Azure 中的 Red Hat Enterprise Linux 上设置 Pacemaker
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/29/2020
ms.author: radeltch
ms.openlocfilehash: 4c444cb84f215ba4f42c14eb64f1d2f441e4280d
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91598297"
---
# <a name="setting-up-pacemaker-on-red-hat-enterprise-linux-in-azure"></a>在 Azure 中的 Red Hat Enterprise Linux 上设置 Pacemaker

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351

[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot


请先阅读以下 SAP 说明和文档：

* SAP 说明 [1928533]，其中包含：
  * SAP 软件部署支持的 Azure VM 大小的列表。
  * Azure VM 大小的重要容量信息。
  * 支持的 SAP 软件、操作系统 (OS) 和数据库组合。
  * Microsoft Azure 上 Windows 和 Linux 所需的 SAP 内核版本。
* SAP 说明 [2015553] 列出了在 Azure 中 SAP 支持的 SAP 软件部署的先决条件。
* SAP 说明 [2002167] 包含适用于 Red Hat Enterprise Linux 的建议 OS 设置
* SAP 说明 [2009879] 包含适用于 Red Hat Enterprise Linux 的 SAP HANA 准则
* SAP 说明 [2178632] 包含为 Azure 中的 SAP 报告的所有监控指标的详细信息。
* SAP 说明 [2191498] 包含 Azure 中的 Linux 所需的 SAP 主机代理版本。
* SAP 说明 [2243692] 包含 Azure 中的 Linux 上的 SAP 许可的相关信息。
* SAP 说明 [1999351] 包含适用于 SAP 的 Azure 增强型监视扩展的其他故障排除信息。
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) 包含适用于 Linux 的所有必需 SAP 说明。
* [针对 Linux 上的 SAP 的 Azure 虚拟机规划和实施][planning-guide]
* [适用于 Linux 上的 SAP 的 Azure 虚拟机部署（本文）][deployment-guide]
* [适用于 Linux 上的 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]
* [SAP HANA system replication in pacemaker cluster](https://access.redhat.com/articles/3004101)（Pacemaker 群集中的 SAP HANA 系统复制）
* 通用 RHEL 文档
  * [High Availability Add-On Overview](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)（高可用性附加产品概述）
  * [High Availability Add-On Administration](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)（高可用性附加产品管理）
  * [High Availability Add-On 参考](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [RHEL 高可用性群集的支持策略 - sbd 和 fence_sbd](https://access.redhat.com/articles/2800691)
* Azure 特定的 RHEL 文档：
  * [Support Policies for RHEL High Availability Clusters - Microsoft Azure Virtual Machines as Cluster Members](https://access.redhat.com/articles/3131341)（RHEL 高可用性群集的支持策略 - Microsoft Azure 虚拟机作为群集成员）
  * [Installing and Configuring a Red Hat Enterprise Linux 7.4 (and later) High-Availability Cluster on Microsoft Azure](https://access.redhat.com/articles/3252491)（在 Microsoft Azure 上安装和配置 Red Hat Enterprise Linux 7.4 [及更高版本] 高可用性群集）
  * [采用 RHEL 8-高可用性和群集的注意事项](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/considerations_in_adopting_rhel_8/high-availability-and-clusters_considerations-in-adopting-rhel-8)
  * [在 RHEL 7.6 的 Pacemaker 中将 SAP S/4HANA ASCS/ERS 配置为 Standalone Enqueue Server 2 (ENSA2)](https://access.redhat.com/articles/3974941)

## <a name="cluster-installation"></a>群集安装

![RHEL 上的 Pacemaker 概述](./media/high-availability-guide-rhel-pacemaker/pacemaker-rhel.png)

> [!NOTE]
> Red Hat 不支持软件仿真的监视程序。 Red Hat 不支持云平台上的 SBD。 有关详细信息，请参阅 [RHEL 高可用性群集的支持策略 - sbd 和 fence_sbd](https://access.redhat.com/articles/2800691)。
> Azure 上 Pacemaker Red Hat Enterprise Linux 群集唯一支持的隔离机制是 Azure 隔离代理。  

以下各项带有前缀 [A] - 适用于所有节点、[1] - 仅适用于节点 1，或 [2] - 仅适用于节点 2  。

1. **[A]** 注册。 如果使用已启用 RHEL 8.x HA 的映像，则不需要执行此步骤。  

   注册虚拟机，将其附加到包含适用于 RHEL 7 的存储库的池。

   <pre><code>sudo subscription-manager register
   # List the available pools
   sudo subscription-manager list --available --matches '*SAP*'
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

   通过将池附加到 Azure Marketplace PAYG RHEL 映像，你将能够有效地按 RHEL 使用情况进行双重计费：一次用于 PAYG 映像，一次用于附加的池中的 RHEL 权限。 为了缓解这种情况，Azure 现在提供了 BYOS RHEL 映像。 有关详细信息，请参阅[此处](../redhat/byos.md)。

1. **[A]** 启用 RHEL for SAP 存储库。 如果使用已启用 RHEL 8.x HA 的映像，则不需要执行此步骤。  

   为了安装所需的包，启用以下存储库。

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-sap-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-eus-rpms
   </code></pre>

1. **[A]** 安装 RHEL HA 附加产品

   <pre><code>sudo yum install -y pcs pacemaker fence-agents-azure-arm nmap-ncat
   </code></pre>

   > [!IMPORTANT]
   > 如果资源停止故障或者群集节点无法互相通信，我们建议客户使用以下 Azure 隔离代理版本（或更高版本）以获取更快的故障转移时间：  
   > RHEL 7.7 或更高版本使用最新的可用围栏-代理包版本  
   > RHEL 7.6：fence-agents-4.2.1-11.el7_6.8  
   > RHEL 7.5：fence-agents-4.0.11-86.el7_5.8  
   > RHEL 7.4：fence-agents-4.0.11-66.el7_4.12  
   > 有关详细信息，请参阅 [Azure VM running as a RHEL High Availability cluster member take a very long time to be fenced, or fencing fails / times-out before the VM shuts down](https://access.redhat.com/solutions/3408711)（隔离作为 RHEL 高可用性群集成员运行的 Azure VM 需要很长时间，或者在 VM 关闭之前隔离失败/超时）。

   检查 Azure 隔离代理的版本。 如有必要，请将其更新为上述版本或更高版本。

   <pre><code># Check the version of the Azure Fence Agent
    sudo yum info fence-agents-azure-arm
   </code></pre>

   > [!IMPORTANT]
   > 如果需要使用自定义角色更新 Azure 隔离代理，请确保更新自定义角色来将关闭操作包含在内。 有关详细信息，请参阅[为隔离代理创建自定义角色](#1-create-a-custom-role-for-the-fence-agent)。  

1. [A] 设置主机名称解析

   可以使用 DNS 服务器，或修改所有节点上的 /etc/hosts。 此示例演示如何使用 /etc/hosts 文件。
   请替换以下命令中的 IP 地址和主机名。  

   >[!IMPORTANT]
   > 如果在群集配置中使用主机名，则必须具有可靠的主机名解析。 如果名称不可用并且可能导致群集故障转移延迟，则群集通信将失败。
   > 使用 /etc/hosts 的好处是群集可以独立于 DNS（也可能会成为单一故障点）。  

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   将以下行插入 /etc/hosts。 根据环境更改 IP 地址和主机名

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. [A] 将 hacluster 密码更改为相同的密码

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** 为 Pacemaker 添加防火墙规则

   向群集节点之间的所有群集通信添加以下防火墙规则。

   <pre><code>sudo firewall-cmd --add-service=high-availability --permanent
   sudo firewall-cmd --add-service=high-availability
   </code></pre>

1. **[A]** 启用基本群集服务

   运行以下命令以启用并启动 Pacemaker 服务。

   <pre><code>sudo systemctl start pcsd.service
   sudo systemctl enable pcsd.service
   </code></pre>

1. **[1]** 创建 Pacemaker 群集

   运行以下命令以验证节点并创建群集。 将令牌设置为 30000，以允许内存保留维护。 有关详细信息，请参阅这篇[适用于 Linux][virtual-machines-linux-maintenance] 的文章。  
   
   如果在 **RHEL 7、windows**上构建群集，请使用以下命令：  
   <pre><code>sudo pcs cluster auth <b>prod-cl1-0</b> <b>prod-cl1-1</b> -u hacluster
   sudo pcs cluster setup --name <b>nw1-azr</b> <b>prod-cl1-0</b> <b>prod-cl1-1</b> --token 30000
   sudo pcs cluster start --all
   </code></pre>

   如果在 **RHEL**2.x 上构建群集，请使用以下命令：  
   <pre><code>sudo pcs host auth <b>prod-cl1-0</b> <b>prod-cl1-1</b> -u hacluster
   sudo pcs cluster setup <b>nw1-azr</b> <b>prod-cl1-0</b> <b>prod-cl1-1</b> totem token=30000
   sudo pcs cluster start --all
   </code></pre>

   通过执行以下命令来验证群集状态：  
   <pre><code> # Run the following command until the status of both nodes is online
   sudo pcs status
   # Cluster name: nw1-azr
   # WARNING: no stonith devices and stonith-enabled is not false
   # Stack: corosync
   # Current DC: <b>prod-cl1-1</b> (version 1.1.18-11.el7_5.3-2b07d5c5a9) - partition with quorum
   # Last updated: Fri Aug 17 09:18:24 2018
   # Last change: Fri Aug 17 09:17:46 2018 by hacluster via crmd on <b>prod-cl1-1</b>
   #
   # 2 nodes configured
   # 0 resources configured
   #
   # Online: [ <b>prod-cl1-0</b> <b>prod-cl1-1</b> ]
   #
   # No resources
   #
   # Daemon Status:
   #   corosync: active/disabled
   #   pacemaker: active/disabled
   #   pcsd: active/enabled
   </code></pre>

1. **[A]** 设置预期的投票。 
   
   <pre><code># Check the quorum votes 
    pcs quorum status
    # If the quorum votes are not set to 2, execute the next command
    sudo pcs quorum expected-votes 2
   </code></pre>

   >[!TIP]
   > 如果构建多节点群集，该群集具有两个以上的节点，请不要将投票设置为2。    

1. **[1]** 允许并发隔离操作

   <pre><code>sudo pcs property set concurrent-fencing=true
   </code></pre>

## <a name="create-stonith-device"></a>创建 STONITH 设备

STONITH 设备使用服务主体对 Microsoft Azure 授权。 请按照以下步骤创建服务主体。

1. 转到 <https://portal.azure.com>
1. 打开“Azure Active Directory”边栏选项卡  
   请参阅 "属性" 并记下目录 ID。 这是“租户 ID”。
1. 单击“应用注册”
1. 单击“新建注册”
1. 输入名称，选择“仅限此组织目录中的帐户” 
2. 选择“Web”作为应用程序类型，输入登录 URL（例如 http:\//localhost），然后单击“添加”  
   不会使用登录 URL，可为它输入任何有效的 URL
1. 选择“证书和机密”，然后单击“新建客户端机密”
1. 输入新密钥的说明，选择“永不过期”，并单击“添加”
1. 将节点设置为值。 此值用作服务主体的**密码**
1. 选择“概述”。 记下应用程序 ID。 此 ID 用作服务主体的用户名（以下步骤中的“登录 ID”）

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** 为隔离代理创建自定义角色

默认情况下，服务主体无权访问 Azure 资源。 需要为服务主体授予启动和停止（关闭）群集所有虚拟机的权限。 如果尚未创建自定义角色，可以使用 [PowerShell](../../../role-based-access-control/role-assignments-powershell.md) 或 [Azure CLI](../../../role-based-access-control/role-assignments-cli.md) 来创建它

将以下内容用于输入文件。 你需要调整内容以适应你的订阅，也就是说，将 c276fc76-9cd4-44c9-99a7-4fd71546436e 和 e91d47c4-76f3-4271-a796-21b4ecfe3624 替换为你的订阅的 ID。 如果只有一个订阅，请删除 AssignableScopes 中的第二个条目。

```json
{
    "properties": {
        "roleName": "Linux Fence Agent Role",
        "description": "Allows to power-off and start virtual machines",
        "assignableScopes": [
            "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
            "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
        ],
        "permissions": [
            {
                "actions": [
                    "Microsoft.Compute/*/read",
                    "Microsoft.Compute/virtualMachines/powerOff/action",
                    "Microsoft.Compute/virtualMachines/start/action"
                ],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ]
    }
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** 向服务主体分配自定义角色

将在最后一章中创建的自定义角色“Linux 隔离代理角色”分配给服务主体。 不要再使用“所有者”角色！

1. 转到 https://portal.azure.com
1. 打开“所有资源”边栏选项卡
1. 选择第一个群集节点的虚拟机
1. 选择“访问控制(IAM)”
1. 单击“添加角色分配”
1. 选择角色“Linux 隔离代理角色”
1. 输入前面创建的应用程序名称
1. 点击“保存”

为第二个群集节点重复上述步骤。

### <a name="1-create-the-stonith-devices"></a>**[1]** 创建 STONITH 设备

编辑虚拟机的权限后，可以在群集中配置 STONITH 设备。

<pre><code>
sudo pcs property set stonith-timeout=900
</code></pre>

> [!NOTE]
> 如果 RHEL 主机名和 Azure 节点名不相同，则仅命令仅需要“pcmk_host_map”选项。 请参阅命令中的粗体部分。

对于 RHEL **7、windows**，使用以下命令来配置隔离设备：    
<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm login="<b>login ID</b>" passwd="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:10.0.0.6;prod-cl1-1:10.0.0.7"</b> \
power_timeout=240 pcmk_reboot_timeout=900 pcmk_monitor_timeout=120 pcmk_monitor_retries=4 pcmk_action_limit=3 \
op monitor interval=3600
</code></pre>

对于 RHEL **2.x**，请使用以下命令来配置隔离设备：  
<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm username="<b>login ID</b>" password="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:10.0.0.6;prod-cl1-1:10.0.0.7"</b> \
power_timeout=240 pcmk_reboot_timeout=900 pcmk_monitor_timeout=120 pcmk_monitor_retries=4 pcmk_action_limit=3 \
op monitor interval=3600
</code></pre>

> [!IMPORTANT]
> 对监视和防护操作进行反序列化。 因此，如果存在运行时间较长的监视操作和同时发生的防护事件，则群集故障转移不会延迟，因为已在运行监视操作。  

### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]** 启用 STONITH 设备

<pre><code>sudo pcs property set stonith-enabled=true
</code></pre>

> [!TIP]
>Azure 隔离代理要求与[使用标准 ILB 的 VM 的公共终结点连接](./high-availability-guide-standard-load-balancer-outbound-connections.md)中所述的公共终结点建立出站连接并提供可能的解决方案。  

## <a name="next-steps"></a>后续步骤

* [适用于 SAP 的 Azure 虚拟机规划和实施][planning-guide]
* [适用于 SAP 的 Azure 虚拟机部署][deployment-guide]
* [适用于 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]
* 若要了解如何在 Azure VM 上建立 SAP HANA 高可用性以及规划灾难恢复，请参阅 [Azure 虚拟机 (VM) 上的 SAP HANA 高可用性][sap-hana-ha]
