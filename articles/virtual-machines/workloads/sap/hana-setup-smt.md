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
ms.openlocfilehash: f387c1afe88f2bba476309b2e2e01942d2b7ae5b
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2018
ms.locfileid: "45982619"
---
# <a name="setting-up-smt-server-for-suse-linux"></a>为 SUSE Linux 安装 SMT 服务器
SAP HANA 大型实例不直接连接 Internet。 因此，无法直接向 OS 提供程序注册此类单元，也无法直接下载并应用修补程序。 如果是 SUSE Linux，一种解决方案是在 Azure VM 中安装 SMT 服务器。 而 Azure VM 需要托管在连接到 HANA 大型实例的 Azure VNet 中。 通过此类 SMT 服务器，HANA 大型实例单元可以注册并下载修补程序。 

SUSE 在[用于 SLES 12 SP2 的订阅管理工具](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf)中提供了更多指南。 

作为完成 HANA 大型实例任务的 SMT 服务器的安装前提条件，将需要：

- 连接到 HANA 大型实例 ER 线路的 Azure VNet。
- 与组织相关联的 SUSE 帐户。 而组织则需要拥有有效的 SUSE 订阅。

## <a name="installation-of-smt-server-on-azure-vm"></a>在 Azure VM 上安装 SMT 服务器

在这一步中，将在 Azure VM 中安装 SMT 服务器。 第一步是登录 [SUSE 客户中心](https://scc.suse.com/)。

登录后，依次转到“组织”-->“组织凭据”。 在此部分中，应能找到安装 SMT 服务器所需的凭据。

第三步是在 Azure VNet 中安装 SUSE Linux VM。 若要部署此 VM，请获取 Azure 的 SLES 12 SP2 库映像（选择 BYOS SUSE 映像）。 在部署过程中，请勿定义 DNS 名称，也不要使用此屏幕截图中的静态 IP 地址

![SMT 服务器的 VM 部署](./media/hana-installation/image3_vm_deployment.png)

部署的 VM 为较小 VM，在地址为 10.34.1.4 的 Azure VNet 中获取内部 IP 地址。 VM 名称为 smtserver。 安装完成后，检查了与 HANA 大型实例单元的连接。 可能需要在 Azure VM 的 etc/hosts 中配置 HANA 大型实例单元解析，具体视名称解析的组织方式而定。 向用于保留修补程序的 VM 额外添加磁盘。 启动盘本身可能太小。 在此示例中，磁盘装载到 /srv/www/htdocs，如下面的屏幕截图所示。 100 GB 磁盘应该就足够了。

![SMT 服务器的 VM 部署](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

登录 HANA 大型实例单元，维护 /etc/hosts，并检查能否到达应通过网络运行 SMT 服务器的 Azure VM。

成功完成此检查后，需要登录应运行 SMT 服务器的 Azure VM。 若要使用 putty 登录 VM，需要在 bash 窗口中按以下顺序运行命令：

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

运行这些命令后，重启 bash 以激活设置。 然后，启动 YAST。

将 VM (smtserver) 连接到 SUSE 站点。

```
smtserver:~ # SUSEConnect -r <registration code> -e s<email address> --url https://scc.suse.com
Registered SLES_SAP 12.2 x86_64
To server: https://scc.suse.com
Using E-Mail: email address
Successfully registered system.
```

在 VM 连接到 SUSE 站点之后，安装 smt 包。 使用以下 putty 命令安装 smt 包。

```
smtserver:~ # zypper in smt
Refreshing service 'SUSE_Linux_Enterprise_Server_for_SAP_Applications_12_SP2_x86_64'.
Loading repository data...
Reading installed packages...
Resolving package dependencies...
```


还可以使用 YAST 工具安装 smt 包。 在 YAST 中，转到“软件维护”并搜索 smt。 选择“smt”后会自动切换到“yast2-smt”，如下所示

![YAST 中的 SMT](./media/hana-installation/image5_smt_in_yast.PNG)


接受 smtserver 上的安装选择。 安装完成后，立即转到 SMT 服务器配置，并输入之前从 SUSE 客户中心获取的组织凭据。 此外，还请输入 Azure VM 主机名，作为 SMT 服务器 URL。 在此示例中，它是 https://smtserver，如下图所示。

![SMT 服务器配置](./media/hana-installation/image6_configuration_of_smtserver1.png)

接下来，需要测试与 SUSE 客户中心的连接是否有效。 如下图所示，在此示例中，连接确实有效。

![测试与 SUSE 客户中心的连接](./media/hana-installation/image7_test_connect.png)

在 SMT 安装程序启动后，需要输入数据库密码。 由于这是新安装，因此需要定义数据库密码，如下图所示。

![定义数据库密码](./media/hana-installation/image8_define_db_passwd.PNG)

下一次交互是在证书创建完成时。 浏览对话框（如下图所示），应该能够继续执行步骤。

![创建 SMT 服务器证书](./media/hana-installation/image9_certificate_creation.PNG)

配置的最后一步“运行同步检查”可能需要几分钟的时间才能完成。 安装和配置 SMT 服务器后，应该会在装入点 /srv/www/htdocs 下找到目录存储库，并能在存储库下找到一些子目录。 

运行以下命令，重启 SMT 服务器及其相关服务。

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

## <a name="download-of-packages-onto-smt-server"></a>将程序包下载到 SMT 服务器上

重启所有服务后，使用 YAST 在“SMT 管理”中选择相应程序包。 程序包的选择依据是 HANA 大型实例服务器的 OS 映像，而不是运行 SMT 服务器的 VM 的 SLES 发行版或版本。 程序包选择屏幕示例如下所示。

![选择程序包](./media/hana-installation/image10_select_packages.PNG)

选择完程序包后，需要开始将选择的程序包首次复制到安装的 SMT 服务器。 在 shell 中使用命令 smt-mirror 触发此次复制，如下所示


![将程序包下载到 SMT 服务器](./media/hana-installation/image11_download_packages.PNG)

如上所示，应将程序包复制到装入点 /srv/www/htdocs 下创建的目录。 此过程可能需要一段时间才能完成。 可能需要一个小时或更长时间，具体视选择的程序包数量而定。
此过程完成之后，需要继续安装 SMT 客户端。 

## <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>在 HANA 大型实例单元上安装 SMT 客户端

在此示例中，客户端为 HANA 大型实例单元。 SMT 服务器安装程序将脚本 clientSetup4SMT.sh 复制到了 Azure VM 中。 将此脚本复制到要连接到 SMT 服务器的 HANA 大型实例单元中。 使用 -h 选项启动此脚本，并将 SMT 服务器名称作为参数传递给它。 在此示例中，名称为 smtserver。

![配置 SMT 客户端](./media/hana-installation/image12_configure_client.PNG)

可能会遇到以下情况：客户端从服务器成功加载证书，但无法进行注册，如下所示。

![客户端注册失败](./media/hana-installation/image13_registration_failed.PNG)

如果无法注册，请阅读这篇 [SUSE 支持文档](https://www.suse.com/de-de/support/kb/doc/?id=7006024)，并执行其中所述步骤。

> [!IMPORTANT] 
> 需要提供 VM 名称（在此示例中为 smtserver）作为服务器名称，无需提供完全限定的域名。 只需提供 VM 名称即可。 

执行完这些步骤后，需要在 HANA 大型实例单元上运行以下命令

```
SUSEConnect –cleanup
```

> [!Note] 
> 在我们的测试中，始终都需要在这一步后等待几分钟。 如果在采取 SUSE 文章中介绍的纠正措施后直接运行 clientSetup4SMT.sh，最后会看到指明证书尚未生效的消息。 通常需要先等待 5-10 分钟，再运行 clientSetup4SMT.sh，这样就可以成功完成客户端配置。

如果遇到需要根据 SUSE 文章中的步骤进行修复的问题，必须在 HANA 大型实例单元上再次重启 clientSetup4SMT.sh。 现在，应该可以成功完成了，如下所示。

![客户端注册成功](./media/hana-installation/image14_finish_client_config.PNG)

在这一步中，将 HANA 大型实例单元的 SMT 客户端配置为，连接在 Azure VM 中安装的 SMT 服务器。 现在可以使用“zypper up”或“zypper in”，将 OS 修补程序安装到 HANA 大型实例或安装其他程序包。 不言而喻，只能获取之前在 SMT 服务器上下载的修补程序。

**后续步骤**
- 请参阅 [HLI 上的 HANA 安装](hana-example-installation.md)。











