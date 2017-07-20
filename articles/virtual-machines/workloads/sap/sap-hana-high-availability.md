---
title: "Azure 虚拟机 (VM) 上的 SAP HANA 高可用性 | Microsoft Docs"
description: "在 Azure 虚拟机 (VM) 上建立 SAP HANA 的高可用性。"
services: virtual-machines-linux
documentationcenter: 
author: MSSedusch
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/25/2017
ms.author: sedusch
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 7fa853983119ef4e570b768ca177d169c6e17153
ms.contentlocale: zh-cn
ms.lasthandoff: 05/31/2017


---
# <a name="high-availability-of-sap-hana-on-azure-virtual-machines-vms"></a>Azure 虚拟机 (VM) 上的 SAP HANA 高可用性

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d
[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged%2Fazuredeploy.json

在本地，可以使用 HANA 系统复制或共享存储来建立 SAP HANA 的高可用性。
目前，仅支持在 Azure 上设置 HANA 系统复制。 SAP HANA 复制由一个主节点和至少一个从属节点组成。 对主节点上数据所做的更改将以同步或异步方式复制到从属节点。

本文介绍如何部署虚拟机、配置虚拟机、安装群集框架，以及安装和配置 SAP HANA 系统复制。
在示例配置和安装命令等内容中，使用了实例编号 03 和 HANA 系统 ID HDB。

请先阅读以下 SAP 说明和文档

* SAP 说明 [2205917]：SUSE Linux Enterprise Server for SAP Applications 的建议 OS 设置
* SAP 说明 [1944799]：SUSE Linux Enterprise Server for SAP Applications 的 SAP HANA 指导原则
* [SAP HANA SR 性能优化方案][suse-hana-ha-guide]本指南包含用于在本地设置 SAP HANA 系统复制的全部所需信息。 请使用本指南作为基准。
  
## <a name="deploying-linux"></a>部署 Linux

SUSE Linux Enterprise Server for SAP Applications 中已随附 SAP HANA 的资源代理。
Azure 应用商店中包含 SUSE Linux Enterprise Server for SAP Applications 12 的映像，你可以使用该映像中的 BYOS（自带订阅）部署新的虚拟机。

### <a name="manual-deployment"></a>手动部署

1. 创建资源组。
1. 创建虚拟网络
1. 创建两个存储帐户
1. 创建可用性集  
   设置最大更新域
1. 创建负载均衡器（内部）  
   为上述步骤选择 VNET
1. 创建虚拟机 1  
   https://portal.azure.com/#create/suse-byos.sles-for-sap-byos12-sp1  
   SLES For SAP Applications 12 SP1 (BYOS)  
   选择存储帐户 1  
   选择可用性集  
1. 创建虚拟机 2  
   https://portal.azure.com/#create/suse-byos.sles-for-sap-byos12-sp1  
   SLES For SAP Applications 12 SP1 (BYOS)  
   选择存储帐户 2   
   选择可用性集  
1. 添加数据磁盘
1. 配置负载均衡器
    1. 创建前端 IP 池
        1. 打开负载均衡器，选择前端 IP 池，然后单击“添加”
        1. 输入新前端 IP 池的名称（例如 hana-frontend）
       1. 单击“确定”
        1. 创建新前端 IP 池后，请记下其 IP 地址
    1. 创建后端池
        1. 打开负载均衡器，单击后端池，然后单击“添加”
        1. 输入新后端池的名称（例如 hana-backend）
        1. 单击“添加虚拟机”
        1. 选择前面创建的可用性集
        1. 选择 SAP HANA 群集的虚拟机
        1. 单击“确定”
    1. 创建运行状况探测器
       1. 打开负载均衡器，选择运行状况探测，然后单击“添加”
        1. 输入新运行状况探测的名称（例如 hana-hp）
        1. 选择 TCP 作为协议，选择端口 625**03**，将“间隔”保留为 5，将“不正常阈值”保留为 2
        1. 单击“确定”
    1. 创建负载均衡规则
        1. 打开负载均衡器，选择负载均衡规则，然后单击“添加”
        1. 输入新负载均衡器规则的名称（例如 hana-lb-3**03**15）
        1. 选择前面创建的前端 IP 地址、后端池和运行状况探测（例如 hana-frontend）
        1. 将协议保留为“TCP”，输入端口 3**03**15
        1. 将空闲超时增大到 30 分钟
       1. **确保启用浮动 IP**
        1. 单击“确定”
        1. 针对端口 3**03**17 重复上述步骤

### <a name="deploy-with-template"></a>使用模板进行部署
可以使用 github 上的某个快速启动模板部署全部所需的资源。 该模板将部署虚拟机、负载均衡器、可用性集，等等。请遵照以下步骤部署模板：

1. 在 Azure 门户中打开[数据库模板][template-multisid-db]或[聚合模板][template-converged]。数据库模板只会创建数据库的负载均衡规则，而聚合模板还会创建 ASCS/SCS 和 ERS（仅限 Linux）实例的负载均衡规则。 如果你打算安装基于 SAP NetWeaver 的系统，同时想要在同一台计算机上安装 ASCS/SCS 实例，请使用[聚合模板][template-converged]。
1. 输入以下参数
    1. SAP 系统 ID  
       输入要安装的 SAP 系统的 SAP 系统 ID。 该 ID 将用作所部署的资源的前缀。
    1. 堆栈类型（仅当使用聚合模板时才适用）  
       选择 SAP NetWeaver 堆栈类型
    1. OS 类型  
       选择一个 Linux 发行版。 对于本示例，请选择“SLES 12 BYOS”
    1. 数据库类型  
       选择 HANA
    1. SAP 系统大小  
       新系统将提供的 SAPS 数量。 如果不确定系统需要多少 SAPS，请咨询你的 SAP 技术合作伙伴或系统集成商
    1. 系统可用性  
       选择 HA
    1. 管理员用户名和管理员密码  
       创建可用于登录计算机的新用户。
    1. 新子网或现有子网  
       确定是要创建新的虚拟网络和子网，还是使用现有子网。 如果已有连接到本地网络的虚拟网络，请选择现有虚拟网络。
    1. 子网 ID  
    虚拟机应当连接到的子网的 ID。 选择用于将虚拟机连接到本地网络的 VPN 或 Express Route 虚拟网络的子网。 ID 通常类似于 /subscriptions/`<subscription id`>/resourceGroups/`<resource group name`>/providers/Microsoft.Network/virtualNetworks/`<virtual network name`>/subnets/`<subnet name`>

## <a name="setting-up-linux-ha"></a>设置 Linux HA

以下各项带有前缀 [A] - 适用于所有节点、[1] - 仅适用于节点 1，或 [2] - 仅适用于节点 2。

1. [A] 仅限 SLES for SAP BYOS - 注册 SLES 以便能够使用存储库
1. [A] 仅限 SLES for SAP BYOS - 添加公共云模块
1. [A] 更新 SLES
    ```bash
    sudo zypper update

    ```

1. [1] 启用 SSH 访问
    ```bash
    sudo ssh-keygen -tdsa
    
    # Enter file in which to save the key (/root/.ssh/id_dsa): -> ENTER
    # Enter passphrase (empty for no passphrase): -> ENTER
    # Enter same passphrase again: -> ENTER
    
    # copy the public key
    sudo cat /root/.ssh/id_dsa.pub
    ```

2. [2] 启用 SSH 访问
    ```bash
    sudo ssh-keygen -tdsa

    # insert the public key you copied in the last step into the authorized keys file on the second server
    sudo vi /root/.ssh/authorized_keys
    
    # Enter file in which to save the key (/root/.ssh/id_dsa): -> ENTER
    # Enter passphrase (empty for no passphrase): -> ENTER
    # Enter same passphrase again: -> ENTER
    
    # copy the public key    
    sudo cat /root/.ssh/id_dsa.pub
    ```

1. [1] 启用 SSH 访问
    ```bash
    # insert the public key you copied in the last step into the authorized keys file on the first server
    sudo vi /root/.ssh/authorized_keys
    
    ```

1. [A] 安装 HA 扩展
    ```bash
    sudo zypper install sle-ha-release fence-agents
    
    ```

1. [A] 设置磁盘布局
    1. LVM  
    我们通常建议对存储数据和日志文件的卷使用 LVM。 以下示例假设虚拟机上附加了四个应该用于创建两个卷的数据磁盘。
        * 为想要使用的所有磁盘创建物理卷。
    <pre><code>
    sudo pvcreate /dev/sdc
    sudo pvcreate /dev/sdd
    sudo pvcreate /dev/sde
    sudo pvcreate /dev/sdf
    </code></pre>
        * 为数据文件创建卷组，一个卷组用于日志文件，另一个卷组用于 SAP HANA 的共享目录
    <pre><code>
    sudo vgcreate vg_hana_data /dev/sdc /dev/sdd
    sudo vgcreate vg_hana_log /dev/sde
    sudo vgcreate vg_hana_shared /dev/sdf
    </code></pre>
        * 创建逻辑卷
    <pre><code>
    sudo lvcreate -l 100%FREE -n hana_data vg_hana_data
    sudo lvcreate -l 100%FREE -n hana_log vg_hana_log
    sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared
    sudo mkfs.xfs /dev/vg_hana_data/hana_data
    sudo mkfs.xfs /dev/vg_hana_log/hana_log
    sudo mkfs.xfs /dev/vg_hana_shared/hana_shared
    </code></pre>
        * 创建装载目录，并复制所有逻辑卷的 UUID
    <pre><code>
    sudo mkdir -p /hana/data
    sudo mkdir -p /hana/log
    sudo mkdir -p /hana/shared
    # write down the id of /dev/vg_hana_data/hana_data, /dev/vg_hana_log/hana_log and /dev/vg_hana_shared/hana_shared
    sudo blkid
    </code></pre>
        * 创建三个逻辑卷的 fstab 条目
    <pre><code>
    sudo vi /etc/fstab
    </code></pre>
    将此行插入到 /etc/fstab
    <pre><code>
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_data/hana_data&gt;</b> /hana/data xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_log/hana_log&gt;</b> /hana/log xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_shared/hana_shared&gt;</b> /hana/shared xfs  defaults,nofail  0  2
    </code></pre>
        * 装载新卷
    <pre><code>
    sudo mount -a
    </code></pre>
    1. 无格式磁盘  
       对于小型或演示系统，可将 HANA 数据和日志文件放在一个磁盘上。 以下命令在 /dev/sdc 上创建一个分区，并使用 XFS 格式化该分区。
    ```bash
    sudo fdisk /dev/sdc
    sudo mkfs.xfs /dev/sdc1
    
    # <a name="write-down-the-id-of-devsdc1"></a>记下 /dev/sdc1 的 ID
    sudo /sbin/blkid  sudo vi /etc/fstab
    ```

    Insert this line to /etc/fstab
    <pre><code>
    /dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
    </code></pre>

    Create the target directory and mount the disk.

    ```bash
    sudo mkdir /hana
    sudo mount -a
    ```

1. [A] 为所有主机设置主机名称解析  
    可以使用 DNS 服务器，或修改所有节点上的 /etc/hosts。 此示例演示如何使用 /etc/hosts 文件。
   请替换以下命令中的 IP 地址和主机名
    ```bash
    sudo vi /etc/hosts
    ```
    将以下行插入 /etc/hosts。 根据环境更改 IP 地址和主机名    
    
    <pre><code>
    <b>&lt;IP address of host 1&gt; &lt;hostname of host 1&gt;</b>
    <b>&lt;IP address of host 2&gt; &lt;hostname of host 2&gt;</b>
    </code></pre>

1. [1] 安装群集
    ```bash
    sudo ha-cluster-init
    
    # Do you want to continue anyway? [y/N] -> y
    # Network address to bind to (e.g.: 192.168.1.0) [10.79.227.0] -> ENTER
    # Multicast address (e.g.: 239.x.x.x) [239.174.218.125] -> ENTER
    # Multicast port [5405] -> ENTER
    # Do you wish to use SBD? [y/N] -> N
    # Do you wish to configure an administration IP? [y/N] -> N
    ```
        
1. [2] 向群集添加节点
    ```bash
    sudo ha-cluster-join
        
    # WARNING: NTP is not configured to start at system boot.
    # WARNING: No watchdog device found. If SBD is used, the cluster will be unable to start without a watchdog.
    # Do you want to continue anyway? [y/N] -> y
    # IP address or hostname of existing node (e.g.: 192.168.1.1) [] -> IP address of node 1 e.g. 10.0.0.5
    # /root/.ssh/id_dsa already exists - overwrite? [y/N] N
    ```

1. [A] 将 hacluster 密码更改为相同的密码
    ```bash
    sudo passwd hacluster
    
    ```

1. [A] 将 corosync 配置为使用其他传输，并添加 nodelist。 否则，群集不会工作。
    ```bash
    sudo vi /etc/corosync/corosync.conf    
    
    ```

    将以下粗体内容添加到文件。
    
    <pre><code> 
    [...]
      interface { 
          [...] 
      }
      <b>transport:      udpu</b>
    } 
    <b>nodelist {
      node {
        ring0_addr:     < ip address of note 1 >
      }
      node {
        ring0_addr:     < ip address of note 2 > 
      } 
    }</b>
    logging {
      [...]
    </code></pre>

    然后重新启动 corosync 服务

    ```bash
    sudo service corosync restart
    
    ```

1. [A] 安装 HANA HA 包  
    ```bash
    sudo zypper install SAPHanaSR
    
    ```

## <a name="installing-sap-hana"></a>安装 SAP HANA

遵循 [SAP HANA SR 性能优化方案指南][suse-hana-ha-guide]的第 4 章安装 SAP HANA 系统复制。

1. [A] 从 HANA DVD 运行 hdblcm
    * 选择安装 -> 1
    * 选择要安装的其他组件 -> 1
    * 输入安装路径 [/hana/shared]：-> 按 ENTER
    * 输入本地主机名 [..]：-> 按 ENTER
    * 是否要将其他主机添加到系统? (y/n) [n]：-> 按 ENTER
    * 输入 SAP HANA 系统 ID：<SID of HANA e.g. HDB>
    * 输入实例编号 [00]：   
  HANA 实例编号。 如果使用了 Azure 模板或者遵循了上述示例，请使用 03
    * 选择数据库模式/输入索引 [1]：-> 按 ENTER
    * 选择系统用途/输入索引 [4]：  
  选择系统用途
    * 输入数据卷的位置 [/hana/data/HDB]：-> 按 ENTER
    * 输入日志卷的位置 [/hana/log/HDB]：-> 按 ENTER
    * 是否限制最大内存分配? [n]：-> 按 ENTER
    * 输入主机 '...' 的证书主机名 [...]：-> 按 ENTER
    * 输入 SAP 主机代理用户 (sapadm) 密码：
    * 确认 SAP 主机代理用户 (sapadm) 密码：
    * 输入系统管理员 (hdbadm) 密码：
    * 确认系统管理员 (hdbadm) 密码：
    * 输入系统管理员主目录 [/usr/sap/HDB/home]：-> 按 ENTER
    * 输入系统管理员登录 Shell [/ bin/sh]：-> 按 ENTER
    * 输入系统管理员用户 ID [1001]：-> 按 ENTER
    * 输入用户组 (sapsys) 的 ID [79]：-> 按 ENTER
    * 输入数据库用户 (SYSTEM) 密码：
    * 确认数据库用户 (SYSTEM) 密码：
    * 重新引导计算机后是否重新启动系统? [n]：-> 按 ENTER
    * 是否继续? (y/n)：  
  验证摘要并输入 y 继续
1. [A] 升级 SAP 主机代理  
  从 [SAP 软件中心][sap-swcenter]下载最新的 SAP 主机代理存档，然后运行以下命令升级代理。 替换存档的路径，使其指向已下载的文件。
    ```bash
    sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
    ```

1. [1] 创建 HANA 复制（以 root 身份）  
    运行以下命令。 请确保将粗体字符串（HANA 系统 ID HDB 和实例编号 03）替换为 SAP HANA 安装的值。
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"' 
    hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync' 
    hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME' 
    </code></pre>

1. [A] 创建密钥存储条目（以 root 身份）
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
    </code></pre>
1. [1] 备份数据库（以 root 身份）
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbsql -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')" 
    </code></pre>
1. [1] 切换到 sapsid 用户（例如 hdbadm）并创建主站点。
    <pre><code>
    su - <b>hdb</b>adm
    hdbnsutil -sr_enable –-name=<b>SITE1</b>
    </code></pre>
1. [2] 切换到 sapsid 用户（例如 hdbadm）并创建辅助站点。
    <pre><code>
    su - <b>hdb</b>adm
    sapcontrol -nr <b>03</b> -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=<b>saphanavm1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
    </code></pre>

## <a name="configure-cluster-framework"></a>配置群集框架

更改默认设置

<pre>
sudo vi crm-defaults.txt
# enter the following to crm-saphana.txt
<code>
property $id="cib-bootstrap-options" \
  no-quorum-policy="ignore" \
  stonith-enabled="true" \
  stonith-action="reboot" \
  stonith-timeout="150s"
rsc_defaults $id="rsc-options" \
  resource-stickiness="1000" \
  migration-threshold="5000"
op_defaults $id="op-options" \
  timeout="600"
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>现在，将文件加载到群集
sudo crm configure load update crm-defaults.txt
</pre>

### <a name="create-stonith-device"></a>创建 STONITH 设备

STONITH 设备使用服务主体对 Microsoft Azure 授权。 请遵循以下步骤创建服务主体。

1. 转到 <https://portal.azure.com>
1. 打开“Azure Active Directory”边栏选项卡  
   转到“属性”并记下目录 ID。 这是**租户 ID**。
1. 单击“应用注册”
1. 单击“添加”
1. 输入名称，选择应用程序类型“Web 应用/API”，输入登录 URL（例如 http://localhost），然后单击“创建”
1. 不会使用登录 URL，可为它输入任何有效的 URL
1. 选择新应用，然后在“设置”选项卡中单击“密钥”
1. 输入新密钥的说明，选择“永不过期”，然后单击“保存”
1. 记下值。 此值用作服务主体的**密码**
1. 记下应用程序 ID。 此 ID 用作服务主体的用户名（以下步骤中的**登录 ID**）

默认情况下，服务主体无权访问 Azure 资源。 需要为服务主体授予启动和停止（解除分配）群集所有虚拟机的权限。

1. 转到 https://portal.azure.com
1. 打开“所有资源”边栏选项卡
1. 选择虚拟机
1. 选择“访问控制(IAM)”
1. 单击“添加”
1. 选择“所有者”角色
1. 输入前面创建的应用程序名称
1. 单击“确定”

编辑虚拟机的权限后，可以在群集中配置 STONITH 设备。

<pre>
sudo vi crm-fencing.txt
# enter the following to crm-fencing.txt
# replace the bold string with your subscription id, resource group, tenant id, service principal id and password
<code>
primitive rsc_st_azure_1 stonith:fence_azure_arm \
    params subscriptionId="<b>subscription id</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant id</b>" login="<b>login id</b>" passwd="<b>password</b>"

primitive rsc_st_azure_2 stonith:fence_azure_arm \
    params subscriptionId="<b>subscription id</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant id</b>" login="<b>login id</b>" passwd="<b>password</b>"

colocation col_st_azure -2000: rsc_st_azure_1:Started rsc_st_azure_2:Started
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>现在，将文件加载到群集
sudo crm configure load update crm-fencing.txt
</pre>

### <a name="create-sap-hana-resources"></a>创建 SAP HANA 资源

<pre>
sudo vi crm-saphanatop.txt
# enter the following to crm-saphana.txt
# replace the bold string with your instance number and HANA system id
<code>
primitive rsc_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
    operations $id="rsc_sap2_<b>HDB</b>_HDB<b>03</b>-operations" \
    op monitor interval="10" timeout="600" \
    op start interval="0" timeout="600" \
    op stop interval="0" timeout="300" \
    params SID="<b>HDB</b>" InstanceNumber="<b>03</b>"

clone cln_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> \
    meta is-managed="true" clone-node-max="1" target-role="Started" interleave="true"
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>现在，将文件加载到群集
sudo crm configure load update crm-saphanatop.txt
</pre>

<pre>
sudo vi crm-saphana.txt
# enter the following to crm-saphana.txt
# replace the bold string with your instance number, HANA system id and the frontend IP address of the Azure load balancer. 
<code>
primitive rsc_SAPHana_<b>HDB</b>_HDB<b>03</b> ocf:suse:SAPHana \
    operations $id="rsc_sap_<b>HDB</b>_HDB<b>03</b>-operations" \
    op start interval="0" timeout="3600" \
    op stop interval="0" timeout="3600" \
    op promote interval="0" timeout="3600" \
    op monitor interval="60" role="Master" timeout="700" \
    op monitor interval="61" role="Slave" timeout="700" \
    params SID="<b>HDB</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
    DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"

ms msl_SAPHana_<b>HDB</b>_HDB<b>03</b> rsc_SAPHana_<b>HDB</b>_HDB<b>03</b> \
    meta is-managed="true" notify="true" clone-max="2" clone-node-max="1" \
    target-role="Started" interleave="true"

primitive rsc_ip_<b>HDB</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \ 
    meta target-role="Started" is-managed="true" \ 
    operations $id="rsc_ip_<b>HDB</b>_HDB<b>03</b>-operations" \ 
    op monitor interval="10s" timeout="20s" \ 
    params ip="<b>10.0.0.21</b>" 
primitive rsc_nc_<b>HDB</b>_HDB<b>03</b> anything \ 
    params binfile="/usr/bin/nc" cmdline_options="-l -k 625<b>03</b>" \ 
    op monitor timeout=20s interval=10 depth=0 
group g_ip_<b>HDB</b>_HDB<b>03</b> rsc_ip_<b>HDB</b>_HDB<b>03</b> rsc_nc_<b>HDB</b>_HDB<b>03</b>
 
colocation col_saphana_ip_<b>HDB</b>_HDB<b>03</b> 2000: g_ip_<b>HDB</b>_HDB<b>03</b>:Started \ 
    msl_SAPHana_<b>HDB</b>_HDB<b>03</b>:Master  
order ord_SAPHana_<b>HDB</b>_HDB<b>03</b> 2000: cln_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> \ 
    msl_SAPHana_<b>HDB</b>_HDB<b>03</b>
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>现在，将文件加载到群集
sudo crm configure load update crm-saphana.txt
</pre>

### <a name="test-cluster-setup"></a>测试群集设置
下一章介绍如何测试设置。 每项测试假设你是 root 用户，SAP HANA 主控节点在虚拟机 saphanavm1 上运行。

#### <a name="fencing-test"></a>隔离测试

可以通过禁用节点 saphanavm1 上的网络接口来测试隔离代理的设置。

<pre><code>
sudo ifdown eth0
</code></pre>

现在，虚拟机应会根据群集配置重新启动或停止。
如果将 stonith-action 设置为 off，虚拟机将会停止，资源将迁移到正在运行的虚拟机。

如果设置 AUTOMATED_REGISTER ="false"，则再次启动虚拟机后，SAP HANA 资源将无法作为辅助节点启动。 在这种情况下，需要执行以下命令，将 HANA 实例配置为辅助节点：

<pre><code>
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

#### <a name="testing-a-manual-failover"></a>测试手动故障转移

可以通过停止节点 saphanavm1 上的 pacemaker 服务来测试手动故障转移。
<pre><code>
service pacemaker stop
</code></pre>

故障转移后，可以再次启动该服务。 如果设置 AUTOMATED_REGISTER ="false"，saphanavm1 上的 SAP HANA 资源将无法作为辅助节点启动。 在这种情况下，需要执行以下命令，将 HANA 实例配置为辅助节点：

<pre><code>
service pacemaker start
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 


# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

#### <a name="testing-a-migration"></a>测试迁移

可通过执行以下命令来迁移 SAP HANA 主控节点
<pre><code>
crm resource migrate msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm2</b>
crm resource migrate g_ip_<b>HDB</b>_HDB<b>03</b> <b>saphanavm2</b>
</code></pre>

这应会将 SAP HANA 主控节点以及包含虚拟 IP 地址的组迁移到 saphanavm2。
如果设置 AUTOMATED_REGISTER ="false"，saphanavm1 上的 SAP HANA 资源将无法作为辅助节点启动。 在这种情况下，需要执行以下命令，将 HANA 实例配置为辅助节点：

<pre><code>
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 
</code></pre>

迁移过程将创建位置约束，需要再次删除这些约束。

<pre><code>
crm configure edited

# delete location contraints that are named like the following contraint. You should have two contraints, one for the SAP HANA resource and one for the IP address group.
location cli-prefer-g_ip_<b>HDB</b>_HDB<b>03</b> g_ip_<b>HDB</b>_HDB<b>03</b> role=Started inf: <b>saphanavm2</b>
</code></pre>

此外，还需要清理辅助节点资源的状态

<pre><code>
# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

## <a name="next-steps"></a>后续步骤
* [适用于 SAP 的 Azure 虚拟机规划和实施][planning-guide]
* [适用于 SAP 的 Azure 虚拟机部署][deployment-guide]
* [适用于 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]
* 若要了解如何建立高可用性以及针对 Azure 上的 SAP HANA（大型实例）规划灾难恢复，请参阅 [Azure 上的 SAP HANA（大型实例）的高可用性和灾难恢复](hana-overview-high-availability-disaster-recovery.md)。 

