---
title: 快速入门-创建私有云上的 VMware VM
description: 介绍如何创建和 VMware VM 上 CloudSimple 私有云
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/03/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 33354ce09ad6ba1a9a7c08a8cd3b945f3788011a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595689"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>在您的私有云上创建 VMware 虚拟机

若要创建您的私有云的虚拟机，首先从 Azure 门户访问 CloudSimple 门户。

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="access-the-cloudsimple-portal"></a>访问 CloudSimple 门户

1. 选择“所有服务”  。
2. 搜索**CloudSimple 服务**。
3. 选择你想要创建您的私有云的 CloudSimple 服务。
4. 从**概述**页上，单击**转到 CloudSimple 门户**打开新 CloudSimple 门户的浏览器选项卡。  如果系统提示，请使用你的 Azure 登录在凭据登录。  

    ![启动 CloudSimple 门户](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>启动 vCenter web ui

现在可以启动 vCenter 设置的虚拟机和策略。

若要访问 vCenter，请从 CloudSimple 门户启动。 在主页页面下**常见任务**，单击**启动 vSphere 客户端**。  选择私有云，然后单击**启动 vSphere 客户端**私有云。

   ![启动 vSphere 客户端](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>上传的 ISO 或 vSphere 模板

> [!WARNING]
> ISO 上传使用 vSphere HTML5 客户端。  使用 Flash 客户端可能会导致错误。

1. 获取要上传到 vCenter 来创建虚拟机并使其可在本地系统的 ISO 或 vSphere 模板。

2. 在 vCenter 中，单击**磁盘**图标，然后选择**vsanDatastore**。 单击**文件**，然后单击**新文件夹**。

    ![vCenter ISO](media/vcenter-create-folder.png)

3. 创建一个文件夹来存储 ISO 文件。

4. 导航到新创建的文件夹，然后单击**上传文件**。 请按照屏幕说明上传 ISO。

## <a name="create-a-virtual-machine-in-vcenter"></a>在 vCenter 中创建虚拟机

1. 在 vCenter 中，单击**主机和群集**图标。

2. 右键单击**工作负荷**，然后选择**新的虚拟机**。
    
    ![创建虚拟机](media/create-vcenter-virtual-machine-01.png)

3. 选择**创建新的虚拟机**然后单击**下一步**。

    ![新建虚拟机向导](media/create-vcenter-virtual-machine-02.png)

4. 名称的计算机，选择**工作负荷 VM**文件夹，然后单击**下一步**。

    ![选择名称和文件夹](media/create-vcenter-virtual-machine-03.png)

5. 选择**工作负荷**计算资源，然后单击**下一步**。

    ![选择计算资源](media/create-vcenter-virtual-machine-04.png)

6. 选择**vsanDatastore**然后单击**下一步**。

    ![选择“存储”](media/create-vcenter-virtual-machine-05.png)

7. 保留默认 ESXi 6.5 兼容性选项，然后单击**下一步**。

    ![选择兼容性](media/create-vcenter-virtual-machine-06.png)

8. 选择虚拟机的 ISO 的来宾 OS，然后单击**下一步**。

    ![自定义来宾操作系统](media/create-vcenter-virtual-machine-07.png)

9. 选择硬盘和网络选项。 对于新的 CD/DVD 驱动器，请选择**数据存储 ISO 文件**。  如果你想要允许流量从公共 IP 地址与此虚拟机，选择与网络**vm-1**。

    ![选择硬件自定义项](media/create-vcenter-virtual-machine-08.png)

10. 选择窗口将打开。 选择您以前上传到 Iso 和模板文件夹的文件，然后单击**确定**。

    ![选择 ISO](media/create-vcenter-virtual-machine-10.png)

11. 查看设置，然后单击**确定**创建虚拟机。

    ![查看选项](media/create-vcenter-virtual-machine-11.png)

虚拟机现已添加到工作负荷的计算资源，并随时可供使用。 

![在 vCenter 中的新虚拟机](media/create-vcenter-virtual-machine-12.png)

基本安装程序现已完成。 你可以开始使用您的私有云类似于使用您的本地虚拟机基础结构。

以下各节包含有关设置 DNS 和 DHCP 服务器用于私有云工作负荷和修改默认网络配置的可选信息。

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>将用户和标识源添加到 vCenter （可选）

CloudSimple 分配默认 vCenter 用户帐户使用用户名 **cloudowner@cloudsimple.local** 。 若要开始，需要没有其他帐户设置。  CloudSimple 通常将管理员分配执行正常操作所需的权限。  设置你的本地 active directory 或 Azure AD[附加的标识源](https://docs.azure.cloudsimple.com/set-vcenter-identity/)上您的私有云。

## <a name="create-a-dns-and-dhcp-server-optional"></a>创建 DNS 和 DHCP 服务器 （可选）

应用程序和私有云环境中运行的工作负荷需要名称解析和 DHCP 服务，用于查找和 IP 地址分配。 正确的 DHCP 和 DNS 基础结构需要提供这些服务。 可以在 vCenter 提供私有云环境中的这些服务中配置虚拟机。

### <a name="prerequisites"></a>先决条件

* Vlan 配置为分布式的端口组

* 路由到在本地或基于 Internet 的 DNS 服务器设置

* 虚拟机模板或创建虚拟机的 ISO

以下链接提供有关在 Linux 和 Windows 上的 DHCP 和 DNS 服务器设置的指导。

### <a name="linux-based-dns-server-setup"></a>基于 Linux 的 DNS 服务器安装程序

Linux 提供各种包设置 DNS 服务器。  下面是设置的开源 BIND DNS 服务器的说明的链接。

[设置示例](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

### <a name="windows-based-setup"></a>基于 Windows 的安装程序

以下文章介绍如何将 Windows 服务器作为 DNS 服务器和 DHCP 服务器设置。
<br>
[Windows Server 作为 DNS 服务器](https://docs.microsoft.com/windows-server/networking/dns/dns-top)

[DHCP 服务器的 Windows 服务器](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>自定义网络配置 （可选）

CloudSimple 门户中的网络页面允许您指定防火墙的表的配置和虚拟机的公共 IP 地址。

### <a name="allocate-public-ips"></a>分配的公共 Ip

1. 导航到**网络 > 的公共 IP** CloudSimple 门户中。
2. 单击**分配公共 IP**。
3. 输入一个名称以标识 IP 地址条目。
4. 选择您的私有云的位置。
5. 使用滑块可根据需要更改空闲超时。
6. 输入你想要分配的公共 IP 地址的本地 IP 地址。
7. 如果需要，请输入关联的 DNS 名称。
8. 单击“完成”  。

    ![公共 IP](media/quick-create-pc-public-ip.png)

分配的公共 IP 地址的任务开始。 可以检查任务状态**活动 > 任务**页。 分配完成后，新条目显示在公共 Ip 页。

此 IP 地址必须映射到虚拟机需要使用上面指定的本地地址配置。 若要配置的 IP 地址的过程是特定于虚拟机操作系统。 为虚拟机操作系统的正确过程，请查阅文档。

#### <a name="example"></a>示例

例如，以下是 Ubuntu 16.04 的详细信息。

将静态方法添加到文件中的 inet 地址系列配置```/etc/network/interfaces```。 更改地址、 子网掩码和网关值。 对于此示例中，我们使用 eth0 接口、 192.168.24.10 的内部 IP 地址、 网关地址 192.168.24.1 和子网掩码 255.255.255.0。 

编辑 ```interfaces``` 文件。

```
sudo vi /etc/network/interfaces
```

更新中的以下节```interfaces```文件。

```
auto eth0
iface eth0 inet static
address 192.168.24.10
netmask 255.255.255.0
gateway 192.168.24.1
dns-nameservers 8.8.8.8
dns-domain acme.com
dns-search acme.com
```

禁用的接口。

```
sudo ifdown eth0
```

再次启用该接口。

```
sudo ifup eth0
```

默认情况下，来自 Internet 的所有传入流量是**拒绝**。 如果你想要打开其他端口，创建[防火墙表](https://docs.azure.cloudsimple.com/firewall/)。

在配置后内部 IP 地址作为静态 IP 地址，验证您可以通过 Internet 从虚拟机中。

```
ping 8.8.8.8
```

验证你可以连接到虚拟机从 Internet 使用的公共 IP 地址。

确保任何虚拟机上的防火墙 (iptable) 规则没有阻止端口 80 入站。

```
netstat -an | grep 80
```

开始在端口 80 侦听的 http 服务器。
       
```
python2.7 -m SimpleHTTPServer 80
```

或

```
python3 -m http.server 80
```

在您的桌面上启动浏览器并指向端口 80 用于浏览你的虚拟机上的文件的公共 IP 地址。 

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>默认 CloudSimple 的公共 IP 的防火墙规则

* VPN 流量：允许 （自/至） VPN 和所有的工作负荷网络和管理网络之间的所有流量。
* 私有云内部通信：允许所有之间 （自/至） 工作负荷网络和管理网络 （如上所示） 的东-西流量。
* Internet 流量：
    * 工作负荷网络和管理网络拒绝来自 Internet 的所有传入流量。
    * 允许从工作负荷网络或管理网络到 Internet 的所有传出流量。

此外可以修改你的流量安全的的方式使用防火墙规则功能。 有关详细信息，请参阅[设置防火墙表和规则](https://docs.azure.cloudsimple.com/firewall/)。

## <a name="install-solutions-optional"></a>安装解决方案 （可选）
若要充分利用您的私有云 vCenter 环境在 CloudSimple 私有云上，可以安装解决方案。 您可以设置备份、 灾难恢复、 复制和其他功能来保护你的虚拟机。 例如，VMware 站点恢复管理器 (VMware SRM) 和 Veeam 备份和复制。

若要安装解决方案，必须的有限期内请求其他权限。 请参阅[提升特权](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges)。

## <a name="next-steps"></a>后续步骤

* [使用 Azure 上的 VMware 虚拟机](quickstart-create-vmware-virtual-machine.md)
* [连接到使用 Azure ExpressRoute 的本地网络](https://docs.azure.cloudsimple.com/on-premises-connection)
* [设置 CloudSimple 网络上的 VPN 网关](https://docs.azure.cloudsimple.com/vpn-gateway)
