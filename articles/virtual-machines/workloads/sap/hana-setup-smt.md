---
title: 如何为 Azure 上的 SAP HANA（大型实例）安装 SMT 服务器 | Microsoft Docs
description: 如何为 Azure 上的 SAP HANA（大型实例）安装 SMT 服务器。
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 690f41e941f2d1db8fc92d225a54d07570299222
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58313129"
---
# <a name="set-up-smt-server-for-suse-linux"></a>为 SUSE Linux 安装 SMT 服务器
SAP HANA 的大型实例不直接连接到 Internet。 无法直接向操作系统提供程序注册此类单元，也无法直接下载并应用更新。 SUSE Linux 的解决方案是在 Azure 虚拟机中安装 SMT 服务器。 在 Azure 虚拟网络中托管虚拟机，该虚拟网络连接到 HANA 大型实例。 通过此类 SMT 服务器，HANA 大型实例单元可以注册并下载更新。 

有关 SUSE 的更多文档，请参阅 [Subscription Management Tool for SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf)（适用于 SLES 12 SP2 的订阅管理工具）。 

安装完成 HANA 大型实例的任务的 SMT 服务器，必须具备：

- 连接到 HANA 大型实例 ExpressRoute 线路的 Azure 虚拟网络。
- 与组织相关联的 SUSE 帐户。 组织应具有一个有效的 SUSE 订阅。

## <a name="install-smt-server-on-an-azure-virtual-machine"></a>在 Azure 虚拟机上安装 SMT 服务器

第一步，登录 [SUSE 客户中心](https://scc.suse.com/)。

转到“组织” > “组织凭据”。 在此部分中，应能找到安装 SMT 服务器所需的凭据。

然后，在 Azure 虚拟网络中安装 SUSE Linux VM。 若要部署此虚拟机，请获取 Azure 的 SLES 12 SP2 库映像（选择 BYOS SUSE 映像）。 在部署过程中，请勿定义 DNS 名称，也不要使用静态 IP 地址。

![SMT 服务器的虚拟机部署的屏幕截图](./media/hana-installation/image3_vm_deployment.png)

部署的虚拟机较小，获得了 Azure 虚拟网络中的内部 IP 地址 10.34.1.4。 虚拟机的名称为“smtserver”。 安装完成后，检查了与 HANA 大型实例单元的连接。 可能需要在 Azure 虚拟机的 etc/hosts 中配置 HANA 大型实例单元解析，具体视名称解析的组织方式而定。 

将磁盘添加到虚拟机。 使用此磁盘来保存更新，启动盘本身可能太小。 磁盘装载到 /srv/www/htdocs，如下面的屏幕截图所示。 100 GB 磁盘应该就足够了。

![SMT 服务器的虚拟机部署的屏幕截图](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

登录一个或多个 HANA 大型实例单元，维护 /etc/hosts，并检查能否到达应通过网络运行 SMT 服务器的 Azure 虚拟机。

完成此检查后，登录到应运行 SMT 服务器的 Azure 虚拟机。 若要使用 putty 登录虚拟机，在 bash 窗口中运行以下命令序列：

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

重启 bash 以激活设置。 然后，启动 YAST。

将 VM (smtserver) 连接到 SUSE 站点。

```
smtserver:~ # SUSEConnect -r <registration code> -e s<email address> --url https://scc.suse.com
Registered SLES_SAP 12.2 x86_64
To server: https://scc.suse.com
Using E-Mail: email address
Successfully registered system.
```

在虚拟机连接到 SUSE 站点之后，安装 smt 包。 使用以下 putty 命令安装 smt 包。

```
smtserver:~ # zypper in smt
Refreshing service 'SUSE_Linux_Enterprise_Server_for_SAP_Applications_12_SP2_x86_64'.
Loading repository data...
Reading installed packages...
Resolving package dependencies...
```


还可以使用 YAST 工具安装 smt 包。 在 YAST 中，转到“软件维护”并搜索 smt。 选择“smt”后会自动切换到“yast2-smt”。

![YAST 中 SMT 的屏幕截图](./media/hana-installation/image5_smt_in_yast.PNG)


接受 smtserver 上的安装选择。 安装完成后，转到 SMT 服务器配置。 输入之前从 SUSE 客户中心检索到的组织凭据。 此外，还请输入 Azure 虚拟机主机名，作为 SMT 服务器 URL。 在本演示中，它的 https:\//smtserver。

![SMT 服务器配置的屏幕截图](./media/hana-installation/image6_configuration_of_smtserver1.png)

现在，测试与 SUSE 客户中心的连接是否有效。 如以下屏幕截图所示，在此示例中，连接确实有效。

![测试与 SUSE 客户中心的连接的屏幕截图](./media/hana-installation/image7_test_connect.png)

在 SMT 安装程序启动后，提供数据库密码。 因为是新安装，所以应定义该密码，如以下屏幕截图所示。

![定义数据库密码的屏幕截图](./media/hana-installation/image8_define_db_passwd.PNG)

下一步是创建证书。

![为 SMT 服务器创建证书的屏幕截图](./media/hana-installation/image9_certificate_creation.PNG)

在配置结束时，可能需要几分钟才能运行同步检查。 安装和配置 SMT 服务器后，在装入点 /srv/www/htdocs 下应当能找到目录存储库。 存储库下还有一些子目录。 

运行以下命令，重启 SMT 服务器及其相关服务。

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

## <a name="download-packages-onto-smt-server"></a>将程序包下载到 SMT 服务器上

重启所有服务后，使用 YAST 在“SMT 管理”中选择相应程序包。 程序包的选择取决于 HANA 大型实例服务器的操作系统映像。 而不依赖于 SLES 版本或运行 SMT 服务器的虚拟机版本。 下面的屏幕截图展示了此选择屏幕的示例。

![选择程序包的屏幕截图](./media/hana-installation/image10_select_packages.PNG)

接下来，开始将所选程序包的初始副本复制到设置的 SMT 服务器上。 在 shell 中使用命令 smt-mirror 触发此次复制。

![将程序包下载到 SMT 服务器的屏幕截图](./media/hana-installation/image11_download_packages.PNG)

应将程序包复制到装入点 /srv/www/htdocs 下创建的目录。 此过程可能需要一个小时或以上，具体取决于选择的程序包数量。 完成后，继续安装 SMT 客户端。 

## <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>在 HANA 大型实例单元上安装 SMT 客户端

在此示例中，客户端为 HANA 大型实例单元。 SMT 服务器安装程序将脚本 clientSetup4SMT.sh 复制到了 Azure 虚拟机中。 将此脚本复制到要连接到 SMT 服务器的 HANA 大型实例单元中。 使用 -h 选项启动此脚本，并将 SMT 服务器名称作为参数。 在本示例中，名称为“smtserver”。

![配置 SMT 客户端的屏幕截图](./media/hana-installation/image12_configure_client.PNG)

可能会遇到以下情况：客户端从服务器加载证书成功，但注册失败，如以下屏幕截图所示。

![客户端注册失败的屏幕截图](./media/hana-installation/image13_registration_failed.PNG)

如果无法注册，请参阅 [SUSE 支持文档](https://www.suse.com/de-de/support/kb/doc/?id=7006024)，并运行其中所述步骤。

> [!IMPORTANT] 
> 对于服务器名称，请提供虚拟机的名称（在本例中为“smtserver”），无需提供完全限定的域名。 

完成这些步骤后，在 HANA 大型实例单元上运行以下命令：

```
SUSEConnect –cleanup
```

> [!Note] 
> 该步骤后等待几分钟。 如果立即运行 clientSetup4SMT.sh，可能会出错。

如果遇到需要根据 SUSE 文章的步骤进行修复的问题，请在 HANA 大型实例单元上重启 clientSetup4SMT.sh。 现在，应该可以成功完成了。

![成功注册客户端的屏幕截图](./media/hana-installation/image14_finish_client_config.PNG)

将 HANA 大型实例单元的 SMT 客户端配置为连接到 Azure 虚拟机中安装的 SMT 服务器。 现在可以使用“zypper up”或“zypper in”，将操作系统更新安装到 HANA 大型实例或安装其他程序包。 只能在 SMT 服务器上获得之前下载的更新。

## <a name="next-steps"></a>后续步骤
- [HLI 上的 HANA 安装 ](hana-example-installation.md)。











