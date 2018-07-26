---
title: 在 Azure 中的 SUSE Linux Enterprise Server 上设置 Pacemaker | Microsoft Docs
description: 在 Azure 中的 SUSE Linux Enterprise Server 上设置 Pacemaker
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/13/2018
ms.author: sedusch
ms.openlocfilehash: 9ce95bcf15d0186c1baea3df407d0fc0c4200f45
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2018
ms.locfileid: "39115470"
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>在 Azure 中的 SUSE Linux Enterprise Server 上设置 Pacemaker

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[virtual-machines-linux-maintenance]:../../linux/maintenance-and-updates.md#memory-preserving-maintenance
[virtual-machines-windows-maintenance]:../../windows/maintenance-and-updates.md#memory-preserving-maintenance

有两个选项可用来在 Azure 中设置 Pacemaker 群集。 可以使用隔离代理，它负责通过 Azure API 重新启动失败的节点；还可以使用 SBD 设备。

SBD 设备需要一台额外的充当 iSCSI 目标服务器并提供 SBD 设备的虚拟机。 不过，也可以与其他 Pacemaker 群集共享此 iSCSI 目标服务器。 使用 SBD 设备的优点是可以更快地进行故障转移，如果使用本地 SBD 设备，则不需要对操作 pacemaker 群集的方式进行任何更改。 当 iSCSI 目标服务器不可用时，SBD 隔离仍然可以使用 Azure 隔离代理作为备用隔离机制。

如果不希望另外投资购买一台虚拟机，也可以使用 Azure 隔离代理。 其缺点在于，如果资源停止失败或者群集节点不再可以彼此通信，则故障转移可能要花费 10 到 15 分钟。

![SLES 上的 Pacemaker 概述](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

>[!IMPORTANT]
> 在规划和部署 Linux Pacemaker 群集节点与 SBD 设备时，若要实现整个群集配置的整体可靠性，必须做到：所涉及的 VM 与托管 SBD 设备的 VM 之间的路由不通过任何其他设备（例如 [NVA](https://azure.microsoft.com/solutions/network-appliances/)）。 否则，NVA 的问题和维护事件可能会对整个群集配置的稳定性和可靠性产生负面影响。 为了避免此类障碍，在规划和部署 Linux Pacemaker 群集节点与 SBD 设备时，请勿定义通过 NVA 和类似设备路由群集节点与 SBD 设备之间的流量的 NVA 路由规则或[用户定义路由规则](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)。 
>


## <a name="sbd-fencing"></a>SBD 隔离

如果希望使用 SBD 设备进行隔离，请执行以下步骤。

### <a name="set-up-an-iscsi-target-server"></a>设置 iSCSI 目标服务器

如果尚没有 iSCSI 目标虚拟机，则首先需要创建一台。 可以与多个 Pacemaker 群集共享 iSCSI 目标服务器。

1. 部署一台新的 SLES 12 SP1 或更高版本的虚拟机并通过 ssh 连接到计算机。 计算机不需要为大型机。 Standard_E2s_v3 或 Standard_D2s_v3 这样的虚拟机大小就足够了。

1. 更新 SLES

   <pre><code>
   sudo zypper update
   </code></pre>

1. 删除包

   请卸载以下包，避免 targetcli 和 SLES 12 SP3 的已知问题。 可以忽略找不到的包的相关错误
   
   <pre><code>
   sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>
   
1. 安装 iSCSI 目标包

   <pre><code>
   sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. 启用 iSCSI 目标服务

   <pre><code>   
   sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>在 iSCSI 目标服务器上创建 iSCSI 设备

将一个新的数据磁盘附加到可以用于此群集的 iSCSI 目标虚拟机。 数据磁盘最小可以为 1 GB，并且必须放置在高级存储帐户或高级托管磁盘中以便从[单一 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines) 受益。

在 **iSCSI 目标 VM** 上运行以下命令来为新群集创建 iSCSI 磁盘。 在下面的示例中，**cl1** 用来标识新群集，**prod-cl1-0** 和 **prod-cl1-1** 是群集节点的主机名。 请将它们替换为你的群集节点的主机名。

<pre><code>
# List all data disks with the following command
sudo ls -al /dev/disk/azure/scsi1/

# total 0
# drwxr-xr-x 2 root root  80 Mar 26 14:42 .
# drwxr-xr-x 3 root root 160 Mar 26 14:42 ..
# lrwxrwxrwx 1 root root  12 Mar 26 14:42 lun0 -> ../../../<b>sdc</b>
# lrwxrwxrwx 1 root root  12 Mar 26 14:42 lun1 -> ../../../sdd

# Then use the disk name to list the disk id
sudo ls -l /dev/disk/by-id/scsi-* | grep sdc

# lrwxrwxrwx 1 root root  9 Mar 26 14:42 /dev/disk/by-id/scsi-14d53465420202020a50923c92babda40974bef49ae8828f0 -> ../../sdc
# lrwxrwxrwx 1 root root  9 Mar 26 14:42 <b>/dev/disk/by-id/scsi-360022480a50923c92babef49ae8828f0 -> ../../sdc</b>

# Use the data disk that you attached for this cluster to create a new backstore
sudo targetcli backstores/block create <b>cl1</b> <b>/dev/disk/by-id/scsi-360022480a50923c92babef49ae8828f0</b>

sudo targetcli iscsi/ create iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/luns/ create /backstores/block/<b>cl1</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/acls/ create iqn.2006-04.<b>prod-cl1-0.local:prod-cl1-0</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/acls/ create iqn.2006-04.<b>prod-cl1-1.local:prod-cl1-1</b>

# save the targetcli changes
sudo targetcli saveconfig
</code></pre>

### <a name="set-up-sbd-device"></a>设置 SBD 设备

从群集连接到在最后一步中创建的 iSCSI 设备。
在要创建的新群集的节点上运行以下命令。
以下各项带有前缀 [A] - 适用于所有节点、[1] - 仅适用于节点 1，或 [2] - 仅适用于节点 2。

1. **[A]** 连接到 iSCSI 设备

   首先，启用 iSCSI 和 SBD 服务。

   <pre><code>
   sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]** 在第一个节点上更改发起程序名称

   <pre><code>
   sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   更改此文件的内容以匹配在 iSCSI 目标服务器上创建 iSCSI 设备时使用的 ACL

   <pre><code>   
   InitiatorName=<b>iqn.2006-04.prod-cl1-0.local:prod-cl1-0</b>
   </code></pre>

1. **[2]** 在第二个节点上更改发起程序名称

   <pre><code>
   sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   更改此文件的内容以匹配在 iSCSI 目标服务器上创建 iSCSI 设备时使用的 ACL

   <pre><code>
   InitiatorName=<b>iqn.2006-04.prod-cl1-1.local:prod-cl1-1</b>
   </code></pre>

1. **[A]** 重新启动 iSCSI 服务

   现在，重新启动 iSCSI 服务以应用更改
   
   <pre><code>
   sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   连接 iSCSI 设备。 在下面的示例中，10.0.0.17 是 iSCSI 目标服务器的 IP 地址，3260 是默认端口。 <b>iqn.2006-04.cl1.local:cl1</b> 是运行第一个命令时列出的目标名称。

   <pre><code>
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>
   
   sudo iscsiadm -m node -T <b>iqn.2006-04.cl1.local:cl1</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   请确保 iSCSI 设备可用并记下设备名称（在下面的示例中为 /dev/sde）

   <pre><code>
   lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  cl1              4.0   /dev/sde</b>
   </code></pre>

   现在，检索 iSCSI 设备的 ID。

   <pre><code>
   ls -l /dev/disk/by-id/scsi-* | grep <b>sde</b>
   
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-1LIO-ORG_cl1:3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   # <b>lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df -> ../../sde</b>
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-SLIO-ORG_cl1_3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   </code></pre>

   此命令列出三个设备 ID。 建议使用以 scsi-3 开头的 ID，在上面的示例中为
   
   **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**

1. **[1]** 创建 SBD 设备

   使用 iSCSI 设备的设备 ID 在第一个群集节点上创建新的 SBD 设备。

   <pre><code>
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 10 -4 20 create
   </code></pre>

1. **[A]** 调整 SBD 配置

   打开 SBD 配置文件

   <pre><code>
   sudo vi /etc/sysconfig/sbd
   </code></pre>

   更改 SBD 设备的属性，启用 pacemaker 集成，并更改 SBD 的启动模式。

   <pre><code>
   [...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   </code></pre>

   创建 softdog 配置文件

   <pre><code>
   echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   现在，加载模块

   <pre><code>
   sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>群集安装

以下各项带有前缀 [A] - 适用于所有节点、[1] - 仅适用于节点 1，或 [2] - 仅适用于节点 2。

1. [A] 更新 SLES

   <pre><code>
   sudo zypper update
   </code></pre>

1. [1] 启用 SSH 访问

   <pre><code>
   sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

2. [2] 启用 SSH 访问

   <pre><code>
   sudo ssh-keygen
   
   # insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key   
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. [1] 启用 SSH 访问

   <pre><code>
   # insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. [A] 安装隔离代理
   
   <pre><code>
   sudo zypper install fence-agents
   </code></pre>

1. [A] 设置主机名称解析   

   可以使用 DNS 服务器，或修改所有节点上的 /etc/hosts。 此示例演示如何使用 /etc/hosts 文件。
   请替换以下命令中的 IP 地址和主机名。 使用 /etc/hosts 的好处是群集会变为独立于也可能会成为单一故障点的 DNS。

   <pre><code>
   sudo vi /etc/hosts
   </code></pre>
   
   将以下行插入 /etc/hosts。 根据环境更改 IP 地址和主机名   
   
   <pre><code>
   # IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. [1] 安装群集
   
   <pre><code>
   sudo ha-cluster-init
   
   # Do you want to continue anyway? [y/N] -> y
   # Network address to bind to (for example: 192.168.1.0) [10.79.227.0] -> Press ENTER
   # Multicast address (for example: 239.x.x.x) [239.174.218.125] -> Press ENTER
   # Multicast port [5405] -> Press ENTER
   # Do you wish to configure an administration IP? [y/N] -> N
   </code></pre>

1. [2] 向群集添加节点
   
   <pre><code> 
   sudo ha-cluster-join
   
   # Do you want to continue anyway? [y/N] -> y
   # IP address or hostname of existing node (for example: 192.168.1.1) [] -> IP address of node 1 for example 10.0.0.14
   # /root/.ssh/id_rsa already exists - overwrite? [y/N] N
   </code></pre>

1. [A] 将 hacluster 密码更改为相同的密码

   <pre><code> 
   sudo passwd hacluster
   </code></pre>

1. [A] 将 corosync 配置为使用其他传输，并添加 nodelist。 否则，群集不会工作。
   
   <pre><code> 
   sudo vi /etc/corosync/corosync.conf   
   </code></pre>

   如果值不存在或不同，请将以下粗体显示的内容添加到文件。 请确保将令牌更改为 30000，以允许内存保留维护。 请参阅本文以了解有关 [Linux][virtual-machines-linux-maintenance] 或 [Windows][virtual-machines-windows-maintenance] 的详细信息。
   
   <pre><code> 
   [...]
     <b>token:          30000
     token_retransmits_before_loss_const: 10
     join:           60
     consensus:      6000
     max_messages:   20</b>
     
     interface { 
        [...] 
     }
     <b>transport:      udpu</b>
   } 
   <b>nodelist {
     node {
      # IP address of <b>prod-cl1-0</b>
      ring0_addr:10.0.0.6
     }
     node {
      # IP address of <b>prod-cl1-1</b>
      ring0_addr:10.0.0.7
     } 
   }</b>
   logging {
     [...]
   }
   quorum {
        # Enable and configure quorum subsystem (default: off)
        # see also corosync.conf.5 and votequorum.5
        provider: corosync_votequorum
        <b>expected_votes: 2</b>
        <b>two_node: 1</b>
   }
   </code></pre>

   然后重启 corosync 服务

   <pre><code>
   sudo service corosync restart
   </code></pre>

1. **[1]** 更改 pacemaker 默认设置

   <pre><code>
   sudo crm configure rsc_defaults resource-stickiness="1"   
   </code></pre>

## <a name="create-stonith-device"></a>创建 STONITH 设备

STONITH 设备使用服务主体对 Microsoft Azure 授权。 请按照以下步骤创建服务主体。

1. 转到 <https://portal.azure.com>
1. 打开“Azure Active Directory”边栏选项卡  
   转到“属性”并记下目录 ID。 这是“租户 ID”。
1. 单击“应用注册”
1. 单击“添加”
1. 输入一个名称，选择应用程序类型“Web 应用/API”，输入登录 URL（例如 http://localhost) ），并单击“创建”
1. 不会使用登录 URL，可为它输入任何有效的 URL
1. 选择新应用，并在“设置”选项卡中单击“密钥”
1. 输入新密钥的说明，选择“永不过期”，并单击“保存”
1. 记下值。 此值用作服务主体的**密码**
1. 记下应用程序 ID。 此 ID 用作服务主体的用户名（以下步骤中的“登录 ID”）

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** 为隔离代理创建自定义角色

默认情况下，服务主体无权访问 Azure 资源。 需要为服务主体授予启动和停止（解除分配）群集所有虚拟机的权限。 如果尚未创建自定义角色，可以使用 [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell#create-a-custom-role) 或 [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli#create-a-custom-role) 来创建它

将以下内容用于输入文件。 你需要调整内容以适应你的订阅，也就是说，将 c276fc76-9cd4-44c9-99a7-4fd71546436e 和 e91d47c4-76f3-4271-a796-21b4ecfe3624 替换为你的订阅的 ID。 如果只有一个订阅，请删除 AssignableScopes 中的第二个条目。

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to deallocate and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/deallocate/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

### <a name="1-assign-the-custom-role-to-the-service-principal"></a>**[1]** 向服务主体分配自定义角色

将在最后一章中创建的自定义角色“Linux 隔离代理角色”分配给服务主体。 不要再使用“所有者”角色！

1. 转到 https://portal.azure.com
1. 打开“所有资源”边栏选项卡
1. 选择第一个群集节点的虚拟机
1. 选择“访问控制(IAM)”
1. 单击“添加”
1. 选择角色“Linux 隔离代理角色”
1. 输入前面创建的应用程序名称
1. 单击“确定”

为第二个群集节点重复上述步骤。

### <a name="1-create-the-stonith-devices"></a>**[1]** 创建 STONITH 设备

编辑虚拟机的权限后，可以在群集中配置 STONITH 设备。

<pre><code>
# replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure property stonith-timeout=900

sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
   params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

</code></pre>

### <a name="1-create-fence-topology-for-sbd-fencing"></a>**[1]** 创建用于 SBD 隔离的隔离拓扑

如果希望使用 SBD 设备，当 iSCSI 目标服务器不可用时，我们仍建议使用 Azure 隔离代理作为备用机制。

<pre><code>
sudo crm configure fencing_topology \
  stonith-sbd rsc_st_azure

</code></pre>
### **[1]** 启用 STONITH 设备

<pre><code>
sudo crm configure property stonith-enabled=true 
</code></pre>


## <a name="next-steps"></a>后续步骤
* [适用于 SAP 的 Azure 虚拟机规划和实施][planning-guide]
* [适用于 SAP 的 Azure 虚拟机部署][deployment-guide]
* [适用于 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]
* 若要了解如何在 Azure VM 上建立 SAP HANA 高可用性以及规划灾难恢复，请参阅 [Azure 虚拟机 (VM) 上的 SAP HANA 高可用性][sap-hana-ha]
