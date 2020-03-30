---
title: 快速入门 - 在私有云上创建 Azure VMware VM - 云简单创建 Azure VMware 解决方案
description: 描述如何在云简单私有云上创建 Azure VMware VM
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4ac818cfd267b781366c0e32c9f93cc885dff99c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77566142"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>在私有云上创建 VMware 虚拟机

要在私有云上创建虚拟机，请首先从 Azure 门户访问云简单门户。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 中的[https://portal.azure.com](https://portal.azure.com)Azure 门户。

## <a name="access-the-cloudsimple-portal"></a>访问 CloudSimple 门户

1. 选择**所有服务**。
2. 搜索**云简单服务**。
3. 选择要在其中创建私有云的云简单服务。
4. 在 **"概述"** 页上，单击"**转到云简单"门户**可打开 CloudSimple 门户的新浏览器选项卡。  如果出现提示，请使用 Azure 登录凭据登录。  

    ![启动云简单门户](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>启动 vCenter Web-ui

您现在可以启动 vCenter 来设置虚拟机和策略。

要访问 vCenter，从云简单门户开始。 在主页上，在 **"常见任务**"下，单击 **"启动 vSphere 客户端**"。  选择私有云，然后单击在私有云上**启动 vSphere 客户端**。

   ![启动 vSphere 客户端](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>上传 ISO 或 vSphere 模板

  > [!WARNING]
  > 对于 ISO 上传，请使用 vSphere HTML5 客户端。  使用 Flash 客户端可能会导致错误。

1. 获取要上载到 vCenter 以创建 VM 并在本地系统上可用的 ISO 或 vSphere 模板。
2. 在 vCenter 中，单击 **"磁盘"** 图标并选择 **"vsan Datastore**"。 单击 **"文件**"，然后单击 **"新建文件夹**"。
    ![vCenter ISO](media/vciso00.png)

3. 创建名为"IsO 和模板"的文件夹。

4. 导航到 ISO 和模板中的 ISO 文件夹，然后单击 **"上传文件**"。 按照屏幕上的说明上传 ISO。

## <a name="create-a-virtual-machine-in-vcenter"></a>在 vCenter 中创建虚拟机

1. 在 vCenter 中，单击 **"主机和群集"** 图标。

2. 右键单击 **"工作负载**"并选择 **"新虚拟机**"。
    ![新建 VM](media/vcvm01.png)

3. 选择 **"创建新虚拟机**"，然后单击"**下一步**"。
    ![新建 VM](media/vcvm02.png)

4. 命名计算机，选择工作负载**VM**的位置，然后单击 **"下一步**"。
    ![新建 VM](media/vcvm03.png)

5. 选择 **"工作负载**计算资源"，然后单击 **"下一步**"。
    ![新建 VM](media/vcvm04.png)

6. 选择**vsan Datastore，** 然后单击 **"下一步**"。
    ![新建 VM](media/vcvm05.png)

7. 保留默认 ESXi 6.5 兼容性选择，然后单击 **"下一步**"。
    ![新建 VM](media/vcvm06.png)

8. 为要创建的 VM 选择 ISO 的来宾操作系统，然后单击"**下一步**"。
    ![新建 VM](media/vcvm07.png)

9. 选择硬盘和网络选项。 对于新的 CD/DVD 驱动器，请选择**数据存储 ISO 文件**。  如果要允许流量从公共 IP 地址到此 VM，请选择网络为**vm-1**。
    ![新建 VM](media/vcvm08.png)

10. 将打开一个选择窗口。 选择您以前上载到 ISO 和模板文件夹的文件，然后单击"**确定**"。
    ![新建 VM](media/vcvm10.png)

11. 查看设置并单击 **"确定"** 以创建 VM。
    ![新建 VM](media/vcvm11.png)

VM 现已添加到工作负载计算资源中，并可供使用。 
![新建 VM](media/vcvm12.png)

基本设置现已完成。 您可以开始使用私有云，类似于使用本地 VM 基础结构的方式。

以下各节包含有关为私有云工作负载设置 DNS 和 DHCP 服务器以及修改默认网络配置的可选信息。

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>将用户和标识源添加到 vCenter（可选）

CloudSimple 分配了一个默认 vCenter`cloudowner@cloudsimple.local`用户帐户，该用户帐户具有用户名 。 无需设置其他帐户即可开始。  CloudSimple 通常会为管理员分配执行正常操作所需的权限。  将本地活动目录或 Azure AD 设置为私有云上[的其他标识源](set-vcenter-identity.md)。

## <a name="create-a-dns-and-dhcp-server-optional"></a>创建 DNS 和 DHCP 服务器（可选）

在私有云环境中运行的应用程序和工作负载需要名称解析和 DHCP 服务才能进行查找和 IP 地址分配。 需要适当的 DHCP 和 DNS 基础结构来提供这些服务。 您可以在 vCenter 中配置虚拟机，以在私有云环境中提供这些服务。

先决条件

* 配置了 VLAN 的分布式端口组

* 将设置路由到本地或基于 Internet 的 DNS 服务器

* 虚拟机模板或 ISO 以创建虚拟机

以下链接提供有关在 Linux 和 Windows 上设置 DHCP 和 DNS 服务器的指导。

#### <a name="linux-based-dns-server-setup"></a>基于 Linux 的 DNS 服务器设置

Linux 提供了用于设置 DNS 服务器的各种软件包。  下面是设置开源 BIND DNS 服务器的说明的链接。

[示例设置](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

#### <a name="windows-based-setup"></a>基于 Windows 的设置

这些 Microsoft 主题描述了如何将 Windows 服务器设置为 DNS 服务器和 DHCP 服务器。

[视窗服务器作为 DNS 服务器](https://docs.microsoft.com/windows-server/networking/dns/dns-top)

[视窗服务器为 DHCP 服务器](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>自定义网络配置（可选）

CloudSimple 门户中的"网络"页面允许您为 VM 指定防火墙表和公共 IP 地址的配置。

### <a name="allocate-public-ips"></a>分配公共 IP

1. 在云简单门户中导航到**网络>公共 IP。**
2. 单击 **"分配公共 IP**"。
3. 输入名称以标识 IP 地址条目。
4. 保留默认位置。
5. 如果需要，使用滑块更改空闲超时。
6. 输入要为其分配公共 IP 地址的本地 IP 地址。
7. 如果需要，输入关联的 DNS 名称。
8. 单击 **“完成”**。

    ![公共 IP](media/quick-create-pc-public-ip.png)

分配公共 IP 地址的任务开始。 您可以在"**活动>任务**"页上检查任务的状态。 分配完成后，新条目将显示在公共 IP 页上。

必须映射此 IP 地址的 VM 需要使用上面指定的本地地址进行配置。 配置 IP 地址的过程特定于 VM 操作系统。 有关正确的过程，请参阅 VM 操作系统的文档。

#### <a name="example"></a>示例

例如，以下是 Ubuntu 16.04 的详细信息。

将静态方法添加到文件 /etc/网络/接口中的 inet 地址系列配置。 更改地址、网码和网关值。 在此示例中，我们使用 eth0 接口、内部 IP 地址 192.168.24.10、网关地址 192.168.24.1 和 netmask 255.255.0。 对于您的环境，欢迎电子邮件中提供了可用的子网信息。

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

默认情况下，所有来自 Internet 的传入流量都**被拒绝**。 如果要打开任何其他端口，请创建[防火墙表](firewall.md)。

将内部 IP 地址配置为静态 IP 地址后，请验证是否可以从 VM 内到达 Internet。

```
ping 8.8.8.8
```
还要验证您是否可以使用公共 IP 地址从 Internet 到达 VM。

确保 VM 上的任何 ip 可规则不会阻止端口 80 入站。
        
```
netstat -an | grep 80
```

启动侦听端口 80 的 http 服务器。
       
```
python2.7 -m SimpleHTTPServer 80
```

或

```
python3 -m http.server 80
```
在桌面上启动浏览器，并将其指向端口 80，用于公共 IP 地址以浏览 VM 上的文件。

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>公共 IP 的默认云简单防火墙规则

* VPN 流量：允许 VPN 与所有工作负载网络和管理网络之间的所有流量。
* 私有云内部流量：允许工作负载网络和管理网络之间的所有东西部流量（从/到）（如上所示）。
* 互联网流量：
  * 从 Internet 传入的所有流量都被拒绝到工作负载网络和管理网络。
  * 允许从工作负载网络或管理网络向 Internet 输出所有流量。

您还可以使用防火墙规则功能修改流量的安全方式。 有关详细信息，请参阅[设置防火墙表和规则](firewall.md)。

## <a name="install-solutions-optional"></a>安装解决方案（可选）

您可以在云简单私有云上安装解决方案，以充分利用私有云 vCenter 环境。 您可以设置备份、灾难恢复、复制和其他功能来保护虚拟机。 示例包括 VMware 站点恢复管理器 （VMware SRM） 和 Veeam 备份&复制。

要安装解决方案，您必须在有限的时间内请求其他权限。 请参阅[升级权限](escalate-private-cloud-privileges.md)。

## <a name="next-steps"></a>后续步骤

* [使用 Azure 上的 VMware VM](quickstart-create-vmware-virtual-machine.md)
* [使用 Azure 快速路由连接到本地网络](on-premises-connection.md)
* [在云简单网络上设置 VPN 网关](vpn-gateway.md)
