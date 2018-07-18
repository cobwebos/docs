---
title: 使用 STONITH 为 Azure 上的 SAP HANA（大型实例）进行高可用性设置 | Microsoft Docs
description: 使用 STONITH 在 SUSE 中为 Azure 上的 SAP HANA（大型实例）建立高可用性
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 982c6112a19654e268c9c50fec35d65fbc1766c2
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37062014"
---
# <a name="high-availability-set-up-in-suse-using-the-stonith"></a>使用 STONITH 在 SUSE 中进行高可用性设置
本文档将针对如何使用 STONITH 设备在 SUSE 操作系统上设置高可用性，进行详细的分步说明。

**免责声明：** 本指南是通过测试成功运行的 Microsoft HANA 大型实例环境中的设置得出的。*。由于面向 HANA 大型实例的 Microsoft 服务管理团队不支持操作系统，因此，你可能需要联系 SUSE，以进一步了解操作系统层面的疑难解答或说明。* Microsoft 服务管理团队对 STONITH 设备进行设置并提供全力支持，可以对有关 STONITH 设备的问题进行疑难解答。
## <a name="overview"></a>概述
要使用 SUSE 群集设置高可用性，必须满足以下先决条件。
### <a name="pre-requisites"></a>先决条件
- 预配 HANA 大型实例
- 注册操作系统
- HANA 大型实例服务器已连接到 SMT 服务器以获取修补程序/包
- 操作系统已安装最新的修补程序
- NTP（时间服务器）已设置
- 阅读并了解有关 HA 设置的最新版本的 SUSE 文档

### <a name="setup-details"></a>设置详细信息
本指南使用以下设置：
- 操作系统：适用于 SAP 的 SLES 12 SP1
- HANA 大型实例：2xS192（4 个套接字，2 TB）
- HANA 版本：HANA 2.0 SP1
- 服务器名称：sapprdhdb95 (node1) 和 sapprdhdb96 (node2)
- STONITH 设备：基于 iSCSI 的 STONITH 设备
- HANA 大型实例节点之一上的 NTP 设置

在使用 HSR 设置 HANA 大型实例时，可以请求 Microsoft 服务管理团队设置 STONITH。 如果你已经是预配了 HANA 大型实例的现有客户，且需要为现有的边栏选项卡设置 STONITH 设备，则需要以服务申请表 (SRF) 的形式向 Microsoft 服务管理团队提供以下信息。 可以通过技术部客户经理或 HANA 大型实例载入的 Microsoft 联系人来申请 SRF 表。 在预配时，新客户可以申请 STONITH 设备。 在预配申请表中提供有各种输入。

- 服务器名称和服务器 IP 地址（例如，myhanaserver1，10.35.0.1）
- 位置（例如，美国东部）
- 客户名称（例如，Microsoft）
- SID - HANA 系统标识符（例如，H11）

配置 STONITH 设备后，Microsoft 服务管理团队会向你提供 SBD 设备名称，以及可用于配置 STONITH 设置的 iSCSI 存储的 IP 地址。 

要使用 STONITH 设置端到端 HA，必须按照以下步骤进行操作：

1.  标识 SBD 设备
2.  初始化 SBD 设备
3.  配置群集
4.  设置 Softdog 监视器
5.  将节点加入到群集
6.  验证群集
7.  将资源配置为群集
8.  测试故障转移过程

## <a name="1---identify-the-sbd-device"></a>1. 标识 SBD 设备
本部分将介绍如何在 Microsoft 服务管理团队配置 STONITH 后确定适用于设置的 SBD 设备。 本部分仅适用于现有客户。 如果你是新客户，Microsoft 服务管理团队会向你提供 SBD 设备名称，所以，可以跳过此部分。

1.1 将 /etc/iscsi/initiatorname.isci 修改为 
``` 
iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
```

Microsoft 服务管理会提供此字符串。 在这两个节点上修改文件，但每个节点上的节点编号不同。

![initiatorname.png](media/HowToHLI/HASetupWithStonith/initiatorname.png)

1.2 修改 /etc/iscsi/iscsid.conf: Set node.session.timeo.replacement_timeout=5 和 node.startup = automatic。 在这两个节点上修改文件。

1.3 执行发现命令，它会显示四个会话。 在两个节点上都运行该脚本。

```
iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
```

![iSCSIadmDiscovery.png](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

1.4 执行命令以登录到 iSCSI 设备，它会显示四个会话。 在这两个节点上都运行该脚本。

```
iscsiadm -m node -l
```
![iSCSIadmLogin.png](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

1.5 执行重新扫描脚本：rescan-scsi-bus.sh。此脚本显示创建的新磁盘。  在两个节点上都运行该脚本。 应会看到一个大于零的 LUN 编号（例如，1、2 等）

```
rescan-scsi-bus.sh
```
![rescanscsibus.png](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

1.6 若要获取设备名称，请运行命令 fdisk –l。 在两个节点上都运行该脚本。 选择大小为 178 MiB 的设备。

```
  fdisk –l
```

![fdisk-l.png](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="2---initialize-the-sbd-device"></a>2. 初始化 SBD 设备

2.1 初始化两个节点上的 SBD 设备

```
sbd -d <SBD Device Name> create
```
![sbdcreate.png](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2.2 检查已写入到设备的内容。 在两个节点上都执行该操作

```
sbd -d <SBD Device Name> dump
```

## <a name="3---configuring-the-cluster"></a>3. 配置群集
本部分介绍设置 SUSE HA 群集的步骤。
### <a name="31-package-installation"></a>3.1 包安装
3.1.1   请确认已安装 ha_sles 和 SAPHanaSR-doc 模式。 如果未安装，请先安装。 在这两个节点上都安装。
```
zypper in -t pattern ha_sles
zypper in SAPHanaSR SAPHanaSR-doc
```
![zypperpatternha_sles.png](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
![zypperpatternSAPHANASR-doc.png](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)

### <a name="32-setting-up-the-cluster"></a>3.2 设置群集
3.2.1   可以使用 ha-cluster-init 命令或 yast2 向导设置群集。 这种情况使用 yast2 向导。 仅在主节点上执行此步骤。

请按照“yast2 > 高可用性 > 群集”的顺序 ![yast-control-center.png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)
![yast-hawk-install.png](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)

由于已安装 halk2 包，请单击“取消”。

![yast-hawk-continue.png](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)

单击“继续”

预期值 = 部署的节点数（在本例中为 2）![yast-Cluster-Security.png](media/HowToHLI/HASetupWithStonith/yast-Cluster-Security.png) 单击“下一步”
![yast-cluster-configure-csync2.png](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png) 添加节点名称，然后单击“添加建议的文件”

单击“打开 csync2”

单击“生成预共享密钥”，它会显示下面的弹出窗口

![yast-key-file.png](media/HowToHLI/HASetupWithStonith/yast-key-file.png)

单击 **“确定”**

使用 Csync2 中的 IP 地址和预共享密钥执行身份验证。 使用 csync2 -k /etc/csync2/key_hagroup 生成密钥文件。 在创建文件 key_hagroup 后，应将其手动复制到群集的所有成员。 确保将文件从 node1 复制到 node2。

![yast-cluster-conntrackd.png](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)

单击“下一步”
![yast-cluster-service.png](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)

在默认选项中，启动已关闭，将其更改为“打开”，以便 pacemaker 在启动时开始。 可以基于设置需求做出选择。
单击“下一步”，完成群集配置。

## <a name="4---setting-up-the-softdog-watchdog"></a>4. 设置 Softdog 监视器
本部分将介绍监视器 (softdog) 的配置。

4.1 将以下行添加到这两个节点上的 /etc/init.d/boot.local。
```
modprobe softdog
```
![modprobe-softdog.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)

4.2 更新这两个节点上的文件 /etc/sysconfig/sbd，如下所示：
```
SBD_DEVICE="<SBD Device Name>"
```
![sbd-device.png](media/HowToHLI/HASetupWithStonith/sbd-device.png)

4.3 通过运行以下命令在这两个节点上加载内核模块
```
modprobe softdog
```
![modprobe-softdog-command.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4.4 检查并确保该 softdog 在这两个节点上运行，如下所示：
```
lsmod | grep dog
```
![lsmod-grep-dog.png](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)

4.5 在这两个节点上启动 SBD 设备
```
/usr/share/sbd/sbd.sh start
```
![sbd-sh-start.png](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)

4.6 在这两个节点上测试 SBD 守护程序。 在这两个节点上进行配置后，会看到两个条目
```
sbd -d <SBD Device Name> list
```
![sbd-list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.7 向其中一个节点发送测试消息
```
sbd  -d <SBD Device Name> message <node2> <message>
```
![sbd-list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.8 在第二个节点 (node2) 上，可以查看消息状态
```
sbd  -d <SBD Device Name> list
```
![sbd-list-message.png](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)

4.9 若要采用 sbd 配置，请按如下所示更新文件 /etc/sysconfig/sbd。 在这两个节点上都更新文件。
```
SBD_DEVICE=" <SBD Device Name>" 
SBD_WATCHDOG="yes" 
SBD_PACEMAKER="yes" 
SBD_STARTMODE="clean" 
SBD_OPTS=""
```
4.10    在主节点 (node1) 上启动 pacemaker 服务
```
systemctl start pacemaker
```
![start-pacemaker.png](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)

如果 pacemaker 服务失败，请参阅“方案 5：Pacemaker 服务失败”。

## <a name="5---joining-the-cluster"></a>5. 加入群集
本部分将介绍如何将节点加入到群集。

### <a name="51-add-the-node"></a>5.1 添加节点
在 node2 上运行以下命令，以将 node2 加入群集。
```
ha-cluster-join
```
如果在加入群集期间收到错误，请参阅“方案 6：Node2 无法加入群集”。

## <a name="6---validating-the-cluster"></a>6. 验证群集

### <a name="61-start-the-cluster-service"></a>6.1 启动群集服务
查看并选择在这两个节点上首次启动群集。
```
systemctl status pacemaker
systemctl start pacemaker
```
![systemctl-status-pacemaker.png](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
### <a name="62-monitor-the-status"></a>6.2 监视状态
运行命令 crm_mon，以确保这两个节点处于联机状态。 可以在该群集的任意节点上运行该命令
```
crm_mon
```
![crm-mon.png](media/HowToHLI/HASetupWithStonith/crm-mon.png) 还可以登录到 hawk 查看群集状态 https://<node IP>:7630。 默认用户是 hacluster，密码为 linux。 如果需要，可以使用 passwd 命令更改密码。

## <a name="7-configure-cluster-properties-and-resources"></a>7.配置群集属性和资源 
本部分将介绍配置群集资源的步骤。
在本示例中，设置以下资源，其余资源可以通过参考 SUSE HA 指南进行配置（如果需要）。 仅在其中一个节点中执行配置。 在主节点上执行该操作。

- 群集启动
- STONITH 设备
- 虚拟 IP 地址


### <a name="71-cluster-bootstrap-and-more"></a>7.1 群集启动和详细信息
添加群集启动。 创建文件并按以下所示添加文本：
```
sapprdhdb95:~ # vi crm-bs.txt
# enter the following to crm-bs.txt
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
```
将配置添加到群集。
```
crm configure load update crm-bs.txt
```
![crm-configure-crmbs.png](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)

### <a name="72-stonith-device"></a>7.2 STONITH 设备
添加资源 STONITH。 创建文件并按以下所示添加文本。
```
# vi crm-sbd.txt
# enter the following to crm-sbd.txt
primitive stonith-sbd stonith:external/sbd \
params pcmk_delay_max="15" \
op monitor interval="15" timeout="15"
```
将配置添加到群集。
```
crm configure load update crm-sbd.txt
```

### <a name="73-the-virtual-ip-address"></a>7.3 虚拟 IP 地址
添加资源虚拟 IP。 按以下所示创建文件并添加文本。
```
# vi crm-vip.txt
primitive rsc_ip_HA1_HDB10 ocf:heartbeat:IPaddr2 \
operations $id="rsc_ip_HA1_HDB10-operations" \
op monitor interval="10s" timeout="20s" \
params ip="10.35.0.197"
```
将配置添加到群集。
```
crm configure load update crm-vip.txt
```

### <a name="74-validate-the-resources"></a>7.4 验证资源

在运行命令 crm_mon 时，可以在那里看到两个资源。
![crm_mon_command.png](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

此外，可以在 https://<node IP address>:7630/cib/live/state 上看到状态

![hawlk-status-page.png](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)

## <a name="8-testing-the-failover-process"></a>8.测试故障转移过程
若要测试故障转移过程，请停止 node1 上的 pacemaker 服务，并将资源故障转移到 node2。
```
Service pacemaker stop
```
现在，停止 node2 上的 pacemaker 服务，资源已故障转移到 node1

**在故障转移前**
![Before-failover.png](media/HowToHLI/HASetupWithStonith/Before-failover.png)
**在故障转移后**
![after-failover.png](media/HowToHLI/HASetupWithStonith/after-failover.png)
![crm-mon-after-failover.png](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)


## <a name="9-troubleshooting"></a>9.故障排除
本部分将介绍几个在安装过程中可能会遇到的失败情景。 你不一定会遇到这些问题。

### <a name="scenario-1-cluster-node-not-online"></a>情景 1：群集节点未联机
如果任何节点在群集管理器中都未显示为联机，则可以尝试以下方法使其联机。

启动 iSCSI 服务
```
service iscsid start
```

现在，应能够登录到该 iSCSI 节点
```
iscsiadm -m node -l
```
预期输出如下所示
```
sapprdhdb45:~ # iscsiadm -m node -l
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] (multiple)
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] successful.
```
### <a name="scenario-2-yast2-does-not-show-graphical-view"></a>情景 2：yast2 不显示图形视图
在本文档中，使用 yast2 图形屏幕来设置高可用性群集。 如果 yast2 不随所示图形窗口一起打开并引发 Qt 错误，请执行以下步骤。 如果正常打开，则可以跳过这些步骤。

**错误**

![yast2-qt-gui-error.png](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

**预期输出**

![yast-control-center.png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

如果 yast2 不随图形视图一起打开，请按照以下步骤进行操作。

安装所需程序包。 必须以用户“root”身份登录，并设置 SMT 以下载/安装包。

若要安装包，使用 yast > 软件 > 软件管理 > 依赖项 > 选项“安装建议的包...”。 以下屏幕截图显示了预期屏幕。
>[!NOTE]
>需要在这两个节点上都执行这些步骤，以便可以从两个节点访问 yast2 图形视图。

![yast-sofwaremanagement.png](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)

在依赖项下，选择“安装建议的包”![yast-dependencies.png](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

查看所做的更改，并点击“确定”

![yast](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

包安装操作将继续执行 ![yast-performing-installation.png](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

单击“下一步”

![yast-installation-report.png](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

单击“完成”

另外，还需要安装 libqt4 和 libyui qt 包。
```
zypper -n install libqt4
```
![zypper-install-libqt4.png](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
```
zypper -n install libyui-qt
```
![zypper-install-ligyui.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
![zypper-install-ligyui_part2.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png) Yast2 现在应能够打开图形视图，如下所示。
![yast2-control-center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-does-not-high-availability-option"></a>情景 3：yast2 不显示高可用性选项
对于要在 yast2 控制中心上显示的高可用性选项，需要安装其他包。

使用 Yast2> 软件 > 软件管理 > 选择以下模式

- SAP HANA 服务器基
- C/C++ 编译器和工具
- 高可用性
- SAP Application 服务器基

以下屏幕介绍安装模式的步骤。

使用 yast2 > 软件 > 软件管理

![yast2-control-center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

选择模式

![yast-pattern1.png](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)
![yast-pattern2.png](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

单击“接受”

![yast-changed-packages.png](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

单击“继续”

![yast2-performing-installation.png](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

hte 安装完成后，单击“下一步”

![yast2-installation-report.png](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>情景 4：HANA 安装失败，显示 gcc 程序集错误
HANA 安装失败，显示以下错误。

![Hana-installation-error.png](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

若要解决此问题，需要安装库（libgcc_sl 和 libstdc++6），如下所示。

![zypper-install-lib.png](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>情景 5：Pacemaker 服务失败

Pacemaker 服务启动期间发生了以下问题。

```
sapprdhdb95:/ # systemctl start pacemaker
A dependency job for pacemaker.service failed. See 'journalctl -xn' for details.
```
```
sapprdhdb95:/ # journalctl -xn
-- Logs begin at Thu 2017-09-28 09:28:14 EDT, end at Thu 2017-09-28 21:48:27 EDT. --
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration map
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration ser
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster closed pr
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster quorum se
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync profile loading s
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [MAIN  ] Corosync Cluster Engine exiting normally
Sep 28 21:48:27 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager
-- Subject: Unit pacemaker.service has failed
-- Defined-By: systemd
-- Support: http://lists.freedesktop.org/mailman/listinfo/systemd-devel
--
-- Unit pacemaker.service has failed.
--
-- The result is dependency.
```
```
sapprdhdb95:/ # tail -f /var/log/messages
2017-09-28T18:44:29.675814-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.676023-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster closed process group service v1.01
2017-09-28T18:44:29.725885-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.726069-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster quorum service v0.1
2017-09-28T18:44:29.726164-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync profile loading service
2017-09-28T18:44:29.776349-04:00 sapprdhdb95 corosync[57600]:   [MAIN  ] Corosync Cluster Engine exiting normally
2017-09-28T18:44:29.778177-04:00 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager.
2017-09-28T18:44:40.141030-04:00 sapprdhdb95 systemd[1]: [/usr/lib/systemd/system/fstrim.timer:8] Unknown lvalue 'Persistent' in section 'Timer'
2017-09-28T18:45:01.275038-04:00 sapprdhdb95 cron[57995]: pam_unix(crond:session): session opened for user root by (uid=0)
2017-09-28T18:45:01.308066-04:00 sapprdhdb95 CRON[57995]: pam_unix(crond:session): session closed for user root
```

若要修复此问题，请删除文件 /usr/lib/systemd/system/fstrim.timer 中的以下行

```
Persistent=true
```

![Persistent.png](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node-2-unable-to-join-the-cluster"></a>情景 6：Node2 无法加入群集

在使用 ha-cluster-join 命令将 node2 加入到现有群集时，发生以下错误。

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![ha-cluster-join-error.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

若要修复，请在这两个节点上运行以下命令

```
ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
```

![ssh-keygen-node1.PNG](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

![ssh-keygen-node2.PNG](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

执行前面的修复操作后，应能将 node2 添加到群集中

![ha-cluster-join-fix.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="10-general-documentation"></a>10.常规文档
可以在以下文章中找到有关 SUSE HA 设置的详细信息： 

- [SAP HANA SR 性能优化方案](https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf )
- [基于存储的隔离](https://www.suse.com/documentation/sle_ha/book_sleha/data/sec_ha_storage_protect_fencing.html)
- [博客 - 使用适用于 SAP HANA 的 Pacemaker 群集- 第 1 部分](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/)
- [博客 - 使用适用于 SAP HANA 的 Pacemaker 群集- 第 2 部分](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/)