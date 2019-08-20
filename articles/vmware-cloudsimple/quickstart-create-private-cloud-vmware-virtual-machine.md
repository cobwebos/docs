---
title: 快速入门-在私有云上创建 VMware VM
description: 介绍如何在 CloudSimple 私有云上创建和 VMware VM
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4e1642366d41906035e1ba1b7f75d0ad875c739b
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69574614"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>在私有云上创建 VMware 虚拟机

若要在私有云上创建虚拟机, 请首先从 Azure 门户访问 CloudSimple 门户。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="access-the-cloudsimple-portal"></a>访问 CloudSimple 门户

1. 选择“所有服务”。
2. 搜索 " **CloudSimple Services**"。
3. 选择要在其上创建私有云的 CloudSimple 服务。
4. 在 "**概述**" 页上, 单击 **"CloudSimple 门户"** , 打开 CloudSimple 门户的新浏览器选项卡。  如果系统提示, 请用 Azure 登录凭据登录。  

    ![启动 CloudSimple 门户](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>启动 vCenter web-ui

你现在可以启动 vCenter 来设置虚拟机和策略。

若要访问 vCenter, 请从 CloudSimple 门户启动。 在主页上的 "**常见任务**" 下, 单击 "**启动 vSphere 客户端**"。  选择私有云, 然后单击 "在私有云上**启动 VSphere 客户端**"。

   ![启动 vSphere 客户端](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>上传 ISO 或 vSphere 模板

  > [!WARNING]
  > 对于 ISO 上传, 请使用 vSphere HTML5 客户端。  使用 Flash 客户端可能会导致错误。

1. 获取要上传到 vCenter 的 ISO 或 vSphere 模板以创建 VM, 并使其在本地系统上可用。
2. 在 vCenter 中, 单击 "**磁盘**" 图标并选择 " **vsanDatastore**"。 单击 "**文件**", 然后单击 "**新建文件夹**"。
    ![vCenter ISO](media/vciso00.png)

3. 创建名为 "Iso and Templates" 的文件夹。

4. 导航到 "Iso" 和 "模板" 中的 Iso 文件夹, 然后单击 "**上传文件**"。 按照屏幕上的说明上传 ISO。

## <a name="create-a-virtual-machine-in-vcenter"></a>在 vCenter 中创建虚拟机

1. 在 vCenter 中, 单击 "**主机和群集**" 图标。

2. 右键单击 "**工作负荷**", 然后选择 "**新建虚拟机**"。
    ![新 VM](media/vcvm01.png)

3. 选择 "**新建虚拟机**", 然后单击 "**下一步**"。
    ![新 VM](media/vcvm02.png)

4. 为计算机命名, 选择**工作负荷 VM 的**位置, 并单击 "**下一步**"。
    ![新 VM](media/vcvm03.png)

5. 选择**工作负荷**计算资源, 然后单击 "**下一步**"。
    ![新 VM](media/vcvm04.png)

6. 选择**vsanDatastore**并单击 "**下一步**"。
    ![新 VM](media/vcvm05.png)

7. 保留默认的 ESXi 6.5 兼容性选择, 并单击 "**下一步**"。
    ![新 VM](media/vcvm06.png)

8. 选择要创建的 VM 的 ISO 来宾操作系统, 然后单击 "**下一步**"。
    ![新 VM](media/vcvm07.png)

9. 选择 "硬盘和网络选项"。 对于新的 CD/DVD 驱动器, 请选择 "**数据存储 ISO 文件**"。  如果要允许来自公共 IP 地址的流量流向此 VM, 请选择 "网络" 作为 " **vm-1**"。
    ![新 VM](media/vcvm08.png)

10. 此时将打开选择窗口。 选择之前上传到 Iso 和 Templates 文件夹的文件, 然后单击 **"确定"** 。
    ![新 VM](media/vcvm10.png)

11. 查看设置, 然后单击 **"确定"** 以创建 VM。
    ![新 VM](media/vcvm11.png)

VM 现在已添加到工作负荷计算资源, 可供使用。 
![新 VM](media/vcvm12.png)

基本设置现已完成。 你可以开始使用私有云, 就像使用本地 VM 基础结构一样。

以下部分包含有关为私有云工作负荷设置 DNS 和 DHCP 服务器以及修改默认网络配置的可选信息。

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>将用户和标识源添加到 vCenter (可选)

CloudSimple 将使用用户名`cloudowner@cloudsimple.local`分配默认 vCenter 用户帐户。 不需要额外的帐户设置即可开始。  CloudSimple 通常为管理员分配执行正常操作所需的特权。  将本地 active directory 或 Azure AD 设置为私有云上的[其他标识源](set-vcenter-identity.md)。

## <a name="create-a-dns-and-dhcp-server-optional"></a>创建 DNS 和 DHCP 服务器 (可选)

在私有云环境中运行的应用程序和工作负荷需要进行名称解析, 并使用 DHCP 服务进行查找和 IP 地址分配。 需要适当的 DHCP 和 DNS 基础结构才能提供这些服务。 可以在 vCenter 中配置虚拟机, 以在私有云环境中提供这些服务。

先决条件

* 配置了 VLAN 的分布式端口组

* 将设置路由到本地或基于 Internet 的 DNS 服务器

* 用于创建虚拟机的虚拟机模板或 ISO

以下链接提供有关在 Linux 和 Windows 上设置 DHCP 和 DNS 服务器的指导。

#### <a name="linux-based-dns-server-setup"></a>基于 Linux 的 DNS 服务器设置

Linux 提供了用于设置 DNS 服务器的各种包。  下面是有关设置开源绑定 DNS 服务器的说明的链接。

[示例设置](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

#### <a name="windows-based-setup"></a>基于 Windows 的安装

这些 Microsoft 主题介绍如何将 Windows server 设置为 DNS 服务器和 DHCP 服务器。

[Windows Server 作为 DNS 服务器](https://docs.microsoft.com/windows-server/networking/dns/dns-top)

[Windows Server 作为 DHCP 服务器](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>自定义网络配置 (可选)

CloudSimple 门户中的 "网络" 页允许您为 Vm 指定防火墙表和公共 IP 地址的配置。

### <a name="allocate-public-ips"></a>分配公共 Ip

1. 在 CloudSimple 门户中导航到 "**网络 > 公共 IP** "。
2. 单击 "**分配公共 IP**"。
3. 输入一个名称以标识 IP 地址条目。
4. 保留默认位置。
5. 如果需要, 请使用滑块更改空闲超时。
6. 输入要为其分配公共 IP 地址的本地 IP 地址。
7. 如果需要, 请输入关联的 DNS 名称。
8. 单击“完成”。

    ![公共 IP](media/quick-create-pc-public-ip.png)

开始分配公共 IP 地址的任务。 可以在**活动 > 任务**"页上检查任务的状态。 分配完成后, "公共 Ip" 页面上会显示新条目。 

此 IP 地址必须映射到的 VM 需要配置在上面指定的本地地址。 配置 IP 地址的过程特定于 VM 操作系统。 有关正确的过程, 请参阅 VM 操作系统的文档。

#### <a name="example"></a>示例

例如, 下面是 Ubuntu 16.04 的详细信息。

将静态方法添加到文件中的 inet 地址族配置/etc/network/interfaces。 更改 "地址"、"子网" 和 "网关" 值。 在此示例中, 我们使用的是 eth0 接口, 内部 IP 地址 192.168.24.10, 网关地址 192.168.24.1, 子网掩码为255.255.255.0。 对于你的环境, 欢迎电子邮件中提供了可用的子网信息。

```
sudo vi /etc/network/interfaces
```

```
auto eth0
Iface eth0 inet static
iface eth0 inet static
address 192.168.24.10
netmask 255.255.255.0
gateway 192.168.24.1
dns-nameservers 8.8.8.8
dns-domain acme.com
dns-search acme.com
```
手动禁用接口。

```
sudo ifdown eth0
```
再次手动启用接口。

```
sudo ifup eth0
```

默认情况下,**拒绝**来自 Internet 的所有传入流量。 如果要打开任何其他端口, 请创建[防火墙表](firewall.md)。

将内部 IP 地址配置为静态 IP 地址后, 验证是否可以从 VM 内部访问 Internet。

```
ping 8.8.8.8
```
同时, 验证是否可以使用公共 IP 地址通过 Internet 访问 VM。

确保 VM 上的任何 iptable 规则未阻止端口80入站端口。
        
```
netstat -an | grep 80
```

启动在端口80上侦听的 http 服务器。
       
```
python2.7 -m SimpleHTTPServer 80
```

或

```
python3 -m http.server 80
```
在桌面上启动浏览器, 并将其指向公共 IP 地址的端口 80, 以浏览 VM 上的文件。 

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>公共 IP 的默认 CloudSimple 防火墙规则

* VPN 流量:允许使用 VPN 和所有工作负荷网络和管理网络之间的所有流量。
* 私有云内部流量:允许 (from/to) 工作负荷网络与管理网络 (如上所示) 之间的所有东西部流量。
* Internet 流量:
  * 拒绝从 Internet 传入的所有流量到工作负载网络和管理网络。
  * 允许从工作负载网络或管理网络发往 Internet 的所有流量。

你还可以使用防火墙规则功能修改流量的安全方式。 有关详细信息, 请参阅[设置防火墙表和规则](firewall.md)。

## <a name="install-solutions-optional"></a>安装解决方案 (可选)

可以在 CloudSimple 私有云上安装解决方案, 充分利用私有云 vCenter 环境。 可以设置备份、灾难恢复、复制和其他功能来保护虚拟机。 示例包括 VMware Site Recovery Manager (VMware SRM) 和 Veeam 备份 & 复制。

若要安装解决方案, 必须在有限的时间段内请求其他权限。 请参阅[提升权限](escalate-private-cloud-privileges.md)。

## <a name="next-steps"></a>后续步骤

* [在 Azure 上使用 VMware Vm](quickstart-create-vmware-virtual-machine.md)
* [使用 Azure ExpressRoute 连接到本地网络](on-premises-connection.md)
* [在 CloudSimple 网络上设置 VPN 网关](vpn-gateway.md)
