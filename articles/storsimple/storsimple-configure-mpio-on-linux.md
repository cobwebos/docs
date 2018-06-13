---
title: 在 StorSimple Linux 主机上配置 MPIO | Microsoft 文档
description: 在与运行 CentOS 6.6 的 Linux 主机连接的 StorSimple 上配置 MPIO
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: tysonn
ms.assetid: ca289eed-12b7-4e2e-9117-adf7e2034f2f
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 2fbae15c1c6a9ec886f57f9df903612ae10d8e12
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2018
ms.locfileid: "27779085"
---
# <a name="configure-mpio-on-a-storsimple-host-running-centos"></a>在运行 CentOS 的 StorSimple 主机上配置 MPIO
本文说明在 Centos 6.6 主机服务器上配置多路径 IO (MPIO) 所要执行的步骤。 主机服务器已连接到 Microsoft Azure StorSimple 设备，以通过 iSCSI 发起程序获得高可用性。 本文详细描述多路径设备的自动发现，以及仅适用于 StorSimple 卷的特定设置。

此过程适用于 StorSimple 8000 系列设备的所有型号。

> [!NOTE]
> 此过程不可用于 StorSimple 云设备。 有关详细信息，请参阅“如何为云设备配置主机服务器”。


## <a name="about-multipathing"></a>关于多路径
使用多路径功能可在主机服务器与存储设备之间配置多个 I/O 路径。 这些 I/O 路径是可以包含不同电缆、交换机、网络接口和控制器的物理 SAN 连接。 多路径聚合了 I/O 路径，可配置与所有聚合路径关联的新设备。

多路径有双重目的：

* **高可用性**：如果 I/O 路径的任何要素（例如电缆、交换机、网络接口或控制器）发生故障，多路径可提供替代路径。
* **负载均衡**：根据存储设备的配置，多路径可通过检测 I/O 路径上的负载以及动态重新均衡这些负载来改善性能。

### <a name="about-multipathing-components"></a>关于多路径组件
Linux 中的多路径由内核组件和以下用户空间组件构成。

* **内核**：主要组件是 *device-mapper*，可重新路由 I/O，支持路径和路径组的故障转移。

* **用户空间**：这些组件是 *multipath-tools*，可通过向 device-mapper 多路径模块发出工作指令来管理多路径设备。 这些工具包括：
   
   * **Multipath**：列出和配置多路径设备。
   * **Multipathd**：执行多路径和监视路径的后台程序。
   * **Devmap-name**：为 udev for devmaps 提供有意义的设备名称。
   * **Kpartx**：将线性 devmaps 映射到设备分区，使多路径映射可分区。
   * **Multipath.conf**：多路径后台程序的配置文件，用于覆盖内置配置表。

### <a name="about-the-multipathconf-configuration-file"></a>关于 multipath.conf 配置文件
配置文件 `/etc/multipath.conf` 可让用户配置许多多路径功能。 `multipath` 命令和内核后台程序 `multipathd` 使用此文件中的信息。 只有在配置多路径设备期间才可查阅此文件。 请务必在运行 `multipath` 命令之前完成所有更改。 如果以后修改该文件，则必须停止再重新启动 multipathd，才能使更改生效。

multipath.conf 包括五个节：

- **系统级默认值** *(defaults)*：可以覆盖系统级默认值。
- **列入方块列表的设备** *(blacklist)*：可以指定不应受 device-mapper 控制的设备列表。
- **方块列表异常** *(blacklist_exceptions)*：可以识别要被视为多路径设备的特定设备，即使这些设备已列入方块列表。
- **存储控制器特定的设置** *(devices)*：可以指定要应用到设备的配置设置（包含供应商和产品信息）。
- **设备特定的设置** *(multipaths)*：可以使用此节微调单个 LUN 的配置设置。

## <a name="configure-multipathing-on-storsimple-connected-to-linux-host"></a>在连接到 Linux 主机的 StorSimple 上配置多路径
可以配置连接到 Linux 主机的 StorSimple 设备，以实现高可用性和负载均衡。 例如，如果 Linux 主机有两个连接到 SAN 的接口，设备有两个连接到 SAN 的接口，并且这些接口位于同一子网中，则有 4 个路径可用。 但是，如果设备和主机接口上的每个 DATA 接口位于不同的 IP 子网中（且不可路由），则只有 2 个路径可用。 可以配置多路径，以便自动发现所有可用路径、选择这些路径的负载均衡算法、应用仅限 StorSimple 卷的特定配置设置，并启用和验证多路径。

以下过程描述当有两个网络接口的 StorSimple 设备连接到有两个网络接口的主机时，如何配置多路径。

## <a name="prerequisites"></a>系统必备
本部分详细说明 CentOS 服务器和 StorSimple 设备的配置先决条件。

### <a name="on-centos-host"></a>在 CentOS 主机上
1. 确保 CentOS 主机已启用 2 个网络接口。 键入：
   
    `ifconfig`
   
    以下示例显示主机上有两个网络接口（`eth0` 和 `eth1`）时的输出。
   
        [root@centosSS ~]# ifconfig
        eth0  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:41  
          inet addr:10.126.162.65  Bcast:10.126.163.255  Mask:255.255.252.0
          inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3341/64 Scope:Global
          inet6 addr: fe80::215:5dff:fea2:3341/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
         RX packets:36536 errors:0 dropped:0 overruns:0 frame:0
          TX packets:6312 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:13994127 (13.3 MiB)  TX bytes:645654 (630.5 KiB)
   
        eth1  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:42  
          inet addr:10.126.162.66  Bcast:10.126.163.255  Mask:255.255.252.0
          inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3342/64 Scope:Global
          inet6 addr: fe80::215:5dff:fea2:3342/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:25962 errors:0 dropped:0 overruns:0 frame:0
          TX packets:11 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:2597350 (2.4 MiB)  TX bytes:754 (754.0 b)
   
        loLink encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:12 errors:0 dropped:0 overruns:0 frame:0
          TX packets:12 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:720 (720.0 b)  TX bytes:720 (720.0 b)
2. 在 CentOS 服务器上安装 *iSCSI-initiator-utils*。 执行以下步骤安装 *iSCSI-initiator-utils*。
   
   1. 以 `root` 身份登录到 CentOS 主机。
   2. 安装 *iSCSI-initiator-utils*。 键入：
      
       `yum install iscsi-initiator-utils`
   3. 成功安装 *iSCSI-Initiator-utils* 后，启动 iSCSI 服务。 键入：
      
       `service iscsid start`
      
       有时 `iscsid` 无法真正启动，此时可能需要使用 `--force` 选项
   4. 为确保在启动期间启用 iSCSI 发起程序，请使用 `chkconfig` 命令启用该服务。
      
       `chkconfig iscsi on`
   5. 若要验证是否正确设置了该服务，请运行以下命令：
      
       `chkconfig --list | grep iscsi`
      
       下面显示了示例输出。
      
           iscsi   0:off   1:off   2:on3:on4:on5:on6:off
           iscsid  0:off   1:off   2:on3:on4:on5:on6:off
      
       从上面的示例可以看到，启动时，iSCSI 环境会在运行级别 2、3、4 和 5 运行。
3. 安装 *device-mapper-multipath*。 键入：
   
    `yum install device-mapper-multipath`
   
    安装随即开始。 系统提示确认时，请键入 **Y**。

### <a name="on-storsimple-device"></a>在 StorSimple 设备上
StorSimple 设备应该：

* 至少有两个接口已启用 iSCSI。 若要验证 StorSimple 设备上是否有两个接口已启用 iSCSI，请在 StorSimple 设备的 Azure 经典门户中执行以下步骤：
  
  1. 登录 StorSimple 设备的经典门户。
  2. 选择 StorSimple Manager 服务，单击“设备”，然后选择特定的 StorSimple 设备。 单击“配置”并验证网络接口设置。 以下屏幕截图显示两个已启用 iSCSI 的网络接口。 下面 DATA 2 和 DATA 3 两个 10 GbE 接口都已启用 iSCSI。
     
      ![MPIO StorSimple DATA 2 配置](./media/storsimple-configure-mpio-on-linux/IC761347.png)
     
      ![MPIO StorSimple DATA 3 配置](./media/storsimple-configure-mpio-on-linux/IC761348.png)
     
      在“配置”页中
     
     1. 确定这两个网络接口都已启用 iSCSI。 “已启用 iSCSI”字段应设置为“是”。
     2. 确保网络接口的速度相同，两者都应是 1 GbE 或 10 GbE。
     3. 请记下已启用 iSCSI 的接口的 IPv4 地址，并保存供稍后在主机上使用。
* 应该可以从 CentOS 服务器访问 StorSimple 设备上的 iSCSI 接口。
      若要验证是否可以访问，需要在主机服务器上提供已启用 StorSimple iSCSI 的网络接口的 IP 地址。 使用的命令以及 DATA2 (10.126.162.25) 和 DATA3 (10.126.162.26) 的相应输出如下所示：
  
        [root@centosSS ~]# iscsiadm -m discovery -t sendtargets -p 10.126.162.25:3260
        10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
        10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target

### <a name="hardware-configuration"></a>硬件配置
建议在不同的路径上连接两个 iSCSI 网络接口，以实现冗余。 下图显示可实现高可用性以及使 CentOS 服务器与 StorSimple 设备的多路径实现负载均衡的建议硬件配置。

![连接到 Linux 主机的 StorSimple 的 MPIO 硬件配置](./media/storsimple-configure-mpio-on-linux/MPIOHardwareConfigurationStorSimpleToLinuxHost2M.png)

如上图所示：

* StorSimple 设备采用包含两个控制器的主动-被动配置。
* 两个 SAN 交换机已连接到设备控制器。
* 在 StorSimple 设备上启用了两个 iSCSI 发起程序。
* CentOS 主机上已启用两个网络接口。

如果主机和数据接口均可路由，则上述配置会在设备与主机之间生成 4 个不同的路径。

> [!IMPORTANT]
> * 建议不要对多路径混合使用 1 GbE 与 10 GbE 网络接口。 使用两个网络接口时，两者的类型应完全相同。
> * 在 StorSimple 设备上，DATA0、DATA1、DATA4 和 DATA5 为 1 GbE 接口，DATA2 和 DATA3 为 10 GbE 网络接口。|
> 
> 

## <a name="configuration-steps"></a>配置步骤
多路径的配置步骤包括配置可用于自动发现的路径、指定要使用的负载均衡算法、启用多路径以及最终验证配置。 以下部分详细介绍了这些步骤。

### <a name="step-1-configure-multipathing-for-automatic-discovery"></a>步骤 1：为自动发现配置多路径
系统可以自动发现和配置多路径支持的设备。

1. 初始化 `/etc/multipath.conf` 文件。 键入：
   
     `mpathconf --enable`
   
    上述命令将创建 `sample/etc/multipath.conf` 文件。
2. 启动多路径服务。 键入：
   
    `service multipathd start`
   
    将显示以下输出：
   
    `Starting multipathd daemon:`
3. 启用多路径自动发现。 键入：
   
    `mpathconf --find_multipaths y`
   
    此命令将修改 `multipath.conf` 的 defaults 节，如下所示：
   
        defaults {
        find_multipaths yes
        user_friendly_names yes
        path_grouping_policy multibus
        }

### <a name="step-2-configure-multipathing-for-storsimple-volumes"></a>步骤 2：为 StorSimple 卷配置多路径
默认情况下，所有设备都已列入 multipath.conf 文件中的方块列表，因而会被绕过。 必须创建方块列表例外，允许 StorSimple 设备中的卷启动多路径。

1. 编辑 `/etc/mulitpath.conf` 文件。 键入：
   
    `vi /etc/multipath.conf`
2. 在 multipath.conf 文件中找到 blacklist_exceptions 节。 在此节中，需要将 StorSimple 设备列为方块列表例外。 可按如下所示在此文件中取消注释相关行，以修改此文件（仅使用所用设备的特定型号）：
   
        blacklist_exceptions {
            device {
                       vendor  "MSFT"
                       product "STORSIMPLE 8100*"
            }
            device {
                       vendor  "MSFT"
                       product "STORSIMPLE 8600*"
            }
           }

### <a name="step-3-configure-round-robin-multipathing"></a>步骤 3：配置轮循机制多路径
此负载均衡算法以均衡的轮循机制方式，使用主动控制器的所有可用多路径。

1. 编辑 `/etc/multipath.conf` 文件。 键入：
   
    `vi /etc/multipath.conf`
2. 在 `defaults` 节下面，将 `path_grouping_policy` 设置为 `multibus`。 `path_grouping_policy` 指定将默认路径分组策略应用到未指定的多路径。 defaults 节如下所示。
   
        defaults {
                user_friendly_names yes
                path_grouping_policy multibus
        }

> [!NOTE]
> 最常用的 `path_grouping_policy` 值包括：
> 
> * failover = 每个优先级组的 1 个路径
> * multibus = 1 个优先级组中的所有有效路径
> 
> 

### <a name="step-4-enable-multipathing"></a>步骤 4：启用多路径
1. 重新启动 `multipathd` 守护程序。 键入：
   
    `service multipathd restart`
2. 输出如下所示：
   
        [root@centosSS ~]# service multipathd start
        Starting multipathd daemon:  [OK]

### <a name="step-5-verify-multipathing"></a>步骤 5：验证多路径
1. 首先确保与 StorSimple 设备建立 iSCSI 连接，如下所示：
   
   a. 发现 StorSimple 设备。 键入：
      
    ```
    iscsiadm -m discovery -t sendtargets -p  <IP address of network interface on the device>:<iSCSI port on StorSimple device>
    ```
    
    如果 DATA0 的 IP 地址为 10.126.162.25，并且在 StorSimple 设备上打开了端口 3260 以便传送出站 iSCSI 流量，则输出如下所示：
    
    ```
    10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    ```

    复制上述输出中 StorSimple 设备的 IQN，即 `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`。

   b. 使用目标 IQN 连接到该设备。 此处，StorSimple 设备即是 iSCSI 目标。 键入：

    ```
    iscsiadm -m node --login -T <IQN of iSCSI target>
    ```

    以下示例显示的输出中包含目标 IQN `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`。 该输出指示已成功连接到设备上的两个已启用 iSCSI 的网络接口。

    ```
    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
    Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
    Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
    Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
    Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
    Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
    Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
    ```

    如果此处只显示了一个主机接口和两个路径，则需要在主机上为这两个接口启用 iSCSI。 可以遵循 [Linux 文档中的详细说明](https://access.redhat.com/documentation/Red_Hat_Enterprise_Linux/5/html/Online_Storage_Reconfiguration_Guide/iscsioffloadmain.html)。

2. 卷通过 StorSimple 设备向 CentOS 服务器公开。 有关详细信息，请参阅[步骤 6：创建卷](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume)（通过 StorSimple 设备上的 Azure 门户）。

3. 验证可用路径。 键入：

      ```
      multipath –l
      ```

      以下示例显示通过两个可用路径连接到单个主机网络接口的 StorSimple 设备上的两个网络接口的输出。

        ```
        mpathb (36486fd20cc081f8dcd3fccb992d45a68) dm-3 MSFT,STORSIMPLE 8100
        size=100G features='0' hwhandler='0' wp=rw
        `-+- policy='round-robin 0' prio=0 status=active
        |- 7:0:0:1 sdc 8:32 active undef running
        `- 6:0:0:1 sdd 8:48 active undef running
        ```

        The following example shows the output for two network interfaces on a StorSimple device connected to two host network interfaces with four available paths.

        ```
        mpathb (36486fd27a23feba1b096226f11420f6b) dm-2 MSFT,STORSIMPLE 8100
        size=100G features='0' hwhandler='0' wp=rw
        `-+- policy='round-robin 0' prio=0 status=active
        |- 17:0:0:0 sdb 8:16 active undef running
        |- 15:0:0:0 sdd 8:48 active undef running
        |- 14:0:0:0 sdc 8:32 active undef running
        `- 16:0:0:0 sde 8:64 active undef running
        ```

        After the paths are configured, refer to the specific instructions on your host operating system (Centos 6.6) to mount and format this volume.

## <a name="troubleshoot-multipathing"></a>排查多路径问题
如果在配置多路径期间遇到任何问题，请参阅本部分提供的一些有用提示。

问： `multipath.conf` 文件中的更改未生效。

A. 对 `multipath.conf` 文件进行任何更改后，需要重新启动多路径服务。 输入以下命令：

    service multipathd restart

问： 我在 StorSimple 设备上启用了两个网络接口并在主机上启用了两个网络接口。 但列出可用路径时，只看到两个路径。 我原本以为能够看到四个可用路径。

A. 请确保这两个路径位于同一子网且可路由。 如果网络接口位于不同的 vLAN 且不可路由，则只会显示两个路径。 验证方法之一是确定是否可从 StorSimple 设备上的网络接口访问这两个主机接口。 需要[联系 Microsoft 支持](storsimple-8000-contact-microsoft-support.md)，因为这种验证只能通过支持会话完成。

问： 列出可用路径时，未看到任何输出。

A. 通常，看不到任何多路径的路径即表示多路径后台程序有问题，其中很有可能是 `multipath.conf` 文件有问题。

此外，最好是检查在连接到目标后是否确实能够看到一些磁盘，因为多路径列表不包含响应内容也可能意味着没有任何磁盘。

* 使用以下命令重新扫描 SCSI 总线：
  
    `$ rescan-scsi-bus.sh `（sg3_utils 包的一部分）
* 键入以下命令：
  
    `$ dmesg | grep sd*`
     
     或
  
    `$ fdisk –l`
  
    这些命令将返回最近添加的磁盘的详细信息。
* 若要确定磁盘是否为 StorSimple 磁盘，请使用以下命令：
  
    `cat /sys/block/<DISK>/device/model`
  
    此命令将返回一个字符串，确定它是否为 StorSimple 磁盘。

有一个不太可能的原因是 iscsid pid 已过时。 使用以下命令从 iSCSI 会话注销：

    iscsiadm -m node --logout -p <Target_IP>

针对 iSCSI 目标（即 StorSimple 设备）上所有已连接的网络接口重复此命令。 从所有 iSCSI 会话注销后，使用 iSCSI 目标 IQN 重新建立 iSCSI 会话。 输入以下命令：

    iscsiadm -m node --login -T <TARGET_IQN>


问： 我不确定我的设备是否已列入允许列表。

A. 若要验证设备是否已列入允许列表，请使用以下故障排除交互式命令：

    multipathd –k
    multipathd> show devices
    available block devices:
    ram0 devnode blacklisted, unmonitored
    ram1 devnode blacklisted, unmonitored
    ram2 devnode blacklisted, unmonitored
    ram3 devnode blacklisted, unmonitored
    ram4 devnode blacklisted, unmonitored
    ram5 devnode blacklisted, unmonitored
    ram6 devnode blacklisted, unmonitored
    ram7 devnode blacklisted, unmonitored
    ram8 devnode blacklisted, unmonitored
    ram9 devnode blacklisted, unmonitored
    ram10 devnode blacklisted, unmonitored
    ram11 devnode blacklisted, unmonitored
    ram12 devnode blacklisted, unmonitored
    ram13 devnode blacklisted, unmonitored
    ram14 devnode blacklisted, unmonitored
    ram15 devnode blacklisted, unmonitored
    loop0 devnode blacklisted, unmonitored
    loop1 devnode blacklisted, unmonitored
    loop2 devnode blacklisted, unmonitored
    loop3 devnode blacklisted, unmonitored
    loop4 devnode blacklisted, unmonitored
    loop5 devnode blacklisted, unmonitored
    loop6 devnode blacklisted, unmonitored
    loop7 devnode blacklisted, unmonitored
    sr0 devnode blacklisted, unmonitored
    sda devnode whitelisted, monitored
    dm-0 devnode blacklisted, unmonitored
    dm-1 devnode blacklisted, unmonitored
    dm-2 devnode blacklisted, unmonitored
    sdb devnode whitelisted, monitored
    sdc devnode whitelisted, monitored
    dm-3 devnode blacklisted, unmonitored


有关详细信息，请转到 [use troubleshooting interactive command for multipathing](http://www.centos.org/docs/5/html/5.1/DM_Multipath/multipath_config_confirm.html)（对多路径使用故障排除交互式命令）。

## <a name="list-of-useful-commands"></a>有用命令列表
| Type | 命令 | 说明 |
| --- | --- | --- |
| **iSCSI** |`service iscsid start` |启动 iSCSI 服务 |
| &nbsp; |`service iscsid stop` |停止 iSCSI 服务 |
| &nbsp; |`service iscsid restart` |重新启动 iSCSI 服务 |
| &nbsp; |`iscsiadm -m discovery -t sendtargets -p <TARGET_IP>` |发现指定地址上的可用目标 |
| &nbsp; |`iscsiadm -m node --login -T <TARGET_IQN>` |登录到 iSCSI 目标 |
| &nbsp; |`iscsiadm -m node --logout -p <Target_IP>` |从 iSCSI 目标注销 |
| &nbsp; |`cat /etc/iscsi/initiatorname.iscsi` |列显 iSCSI 发起程序名称 |
| &nbsp; |`iscsiadm –m session –s <sessionid> -P 3` |检查 iSCSI 会话的状态以及在主机上发现的卷 |
| &nbsp; |`iscsi –m session` |显示在主机与 StorSimple 设备之间建立的所有 iSCSI 会话 |
|  | | |
| **多路径** |`service multipathd start` |启动多路径后台程序 |
| &nbsp; |`service multipathd stop` |停止多路径后台程序 |
| &nbsp; |`service multipathd restart` |重新启动多路径后台程序 |
| &nbsp; |`chkconfig multipathd on` </br> 或 </br> `mpathconf –with_chkconfig y` |使多路径后台程序在引导时启动 |
| &nbsp; |`multipathd –k` |启动交互式控制台进行故障排除 |
| &nbsp; |`multipath –l` |列出多路径连接和设备 |
| &nbsp; |`mpathconf --enable` |在 `/etc/mulitpath.conf` 中创建示例 mulitpath.conf 文件 |
|  | | |

## <a name="next-steps"></a>后续步骤
在 Linux 主机上配置 MPIO 时，可能还需要参考以下 CentoS 6.6 文档：

* [Setting up MPIO on CentOS](http://www.centos.org/docs/5/html/5.1/DM_Multipath/setup_procedure.html)（在 CentOS 上设置 MPIO）
* [Linux Training Guide](http://linux-training.be/files/books/LinuxAdm.pdf)（Linux 培训指南）

