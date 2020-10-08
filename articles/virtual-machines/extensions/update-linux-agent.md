---
title: 从 GitHub 更新 Azure Linux 代理
description: 了解如何为 Azure 中的 Linux VM 更新 Azure Linux 代理
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: f1f19300-987d-4f29-9393-9aba866f049c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 08/02/2017
ms.author: mimckitt
ms.openlocfilehash: 882ed23fe9f7e759bef7464d512685163a26b288
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91816181"
---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm"></a>如何更新 VM 上的 Azure Linux 代理

若要更新 Azure 中 Linux VM 上的 [Azure Linux 代理](https://github.com/Azure/WALinuxAgent) ，则必须已具备以下条件：

- 在 Azure 中具有运行的 Linux VM。
- 使用 SHH 连接到该 Linux VM。

应始终先对 Linux 发行版存储库中的程序包进行检查。 虽然可用的程序包很有可能不是最新版本，但启用自动更新可确保 Linux 代理始终获得最新的更新。 如果从程序包管理器进行安装遇到问题，应向发行版供应商寻求支持。

> [!NOTE]
> 有关详细信息，请参阅 [Azure 认可的 Linux 分发版](../linux/endorsed-distros.md)

验证 [Azure 中的虚拟机代理的最低版本支持](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)，然后再继续。


## <a name="ubuntu"></a>Ubuntu

检查当前程序包的版本

```bash
apt list --installed | grep walinuxagent
```

更新程序包缓存

```bash
sudo apt-get -qq update
```

安装最新版本的程序包

```bash
sudo apt-get install walinuxagent
```

确保启用了自动更新。 首先，检查是否已启用自动更新：

```bash
cat /etc/waagent.conf
```

查找“AutoUpdate.Enabled”。 如果看到以下输出，则表示已启用：

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

若要允许运行：

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

为14.04 重新启动 waagengt 服务

```bash
initctl restart walinuxagent
```

为 16.04/17.04 重启 waagent 服务

```bash
systemctl restart walinuxagent.service
```

## <a name="red-hat--centos"></a>Red Hat / CentOS

### <a name="rhelcentos-6"></a>RHEL/CentOS 6

检查当前程序包的版本

```bash
sudo yum list WALinuxAgent
```

检查可用的更新

```bash
sudo yum check-update WALinuxAgent
```

安装最新版本的程序包

```bash
sudo yum install WALinuxAgent
```

确保已启用自动更新 

首先，检查是否已启用自动更新：

```bash
cat /etc/waagent.conf
```

查找“AutoUpdate.Enabled”。 如果看到以下输出，则表示已启用：

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

若要允许运行：

```bash
sudo sed -i 's/\# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

重新启动 waagent 服务

```
sudo service waagent restart
```

## <a name="rhelcentos-7"></a>RHEL/CentOS 7

检查当前程序包的版本

```bash
sudo yum list WALinuxAgent
```

检查可用的更新

```bash
sudo yum check-update WALinuxAgent
```

安装最新版本的程序包

```bash
sudo yum install WALinuxAgent  
```

确保启用了自动更新。 首先，检查是否已启用自动更新：

```bash
cat /etc/waagent.conf
```

查找“AutoUpdate.Enabled”。 如果看到以下输出，则表示已启用：

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

若要允许运行：

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

重新启动 waagent 服务

```bash
sudo systemctl restart waagent.service
```

## <a name="suse-sles"></a>SUSE SLES

### <a name="suse-sles-11-sp4"></a>SUSE SLES 11 SP4

检查当前程序包的版本

```bash
zypper info python-azure-agent
```

检查可用更新。 上面的输出将显示程序包是否为最新版。

安装最新版本的程序包

```bash
sudo zypper install python-azure-agent
```

确保已启用自动更新 

首先，检查是否已启用自动更新：

```bash
cat /etc/waagent.conf
```

查找“AutoUpdate.Enabled”。 如果看到以下输出，则表示已启用：

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

若要允许运行：

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

重新启动 waagent 服务

```bash
sudo /etc/init.d/waagent restart
```

### <a name="suse-sles-12-sp2"></a>SUSE SLES 12 SP2

检查当前程序包的版本

```bash
zypper info python-azure-agent
```

检查可用的更新

上面的输出将显示程序包是否为最新版。

安装最新版本的程序包

```bash
sudo zypper install python-azure-agent
```

确保已启用自动更新 

首先，检查是否已启用自动更新：

```bash
cat /etc/waagent.conf
```

查找“AutoUpdate.Enabled”。 如果看到以下输出，则表示已启用：

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

若要允许运行：

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

重新启动 waagent 服务

```bash
sudo systemctl restart waagent.service
```

## <a name="debian"></a>Debian

### <a name="debian-7-jesse-debian-7-stretch"></a>Debian 7 "Jesse"/Debian 7 "Stretch"

检查当前程序包的版本

```bash
dpkg -l | grep waagent
```

更新程序包缓存

```bash
sudo apt-get -qq update
```

安装最新版本的程序包

```bash
sudo apt-get install waagent
```

启用代理自动更新此版本的 Debian 没有版本 >= 2.0.16，因此不能对其进行自动更新。 上述命令的输出将显示程序包是否为最新版。

### <a name="debian-8-jessie--debian-9-stretch"></a>Debian 8 “Jessie” / Debian 9 “Stretch”

检查当前程序包的版本

```bash
apt list --installed | grep waagent
```

更新程序包缓存

```bash
sudo apt-get -qq update
```

安装最新版本的程序包

```bash
sudo apt-get install waagent
```

请确保先启用自动更新，并查看其是否已启用：

```bash
cat /etc/waagent.conf
```

查找“AutoUpdate.Enabled”。 如果看到以下输出，则表示已启用：

```bash
AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

若要允许运行：

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
Restart the waagent service
sudo systemctl restart walinuxagent.service
```

## <a name="oracle-linux-6-and-oracle-linux-7"></a>Oracle Linux 6 和 Oracle Linux 7

对于 Oracle Linux，请确保已启用 `Addons` 存储库。 选择编辑文件 `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) 或 `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux)，并在此文件中 **[ol6_addons]** 或 **[ol7_addons]** 下将行 `enabled=0` 更改为 `enabled=1`。

然后，若要安装最新版本的 Azure Linux 代理，请键入：

```bash
sudo yum install WALinuxAgent
```

如果找不到外接程序存储库，只需根据 Oracle Linux 版本，将这些行添加到 .repo 文件末尾处：

对于 Oracle Linux 6 虚拟机：

```sh
[ol6_addons]
name=Add-Ons for Oracle Linux $releasever ($basearch)
baseurl=https://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
gpgkey=https://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
gpgcheck=1
enabled=1
```

对于 Oracle Linux 7 虚拟机：

```sh
[ol7_addons]
name=Oracle Linux $releasever Add ons ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=1
```

然后键入：

```bash
sudo yum update WALinuxAgent
```

通常只需要这样做，但如果因某种原因而需要直接从 https://github.com 安装它，请使用以下步骤。


## <a name="update-the-linux-agent-when-no-agent-package-exists-for-distribution"></a>分发不存在代理程序包时，请更新 Linux 代理

通过在命令行上键入 `sudo yum install wget` 来安装 wget（某些发行版在默认情况下未安装它，如 Red Hat、CentOS 和 Oracle Linux 6.4 和 6.5 版）。

### <a name="1-download-the-latest-version"></a>1.下载最新版本
在网页中打开 [GitHub 中的 Azure Linux 代理版本](https://github.com/Azure/WALinuxAgent/releases)，并找到最新的版本号。 （可以通过键入 `waagent --version` 查明当前版本。）

对于 2.2.x 或更高版本，请键入：
```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.x.zip
unzip v2.2.x.zip
cd WALinuxAgent-2.2.x
```

以下行使用版本 2.2.0 作为示例：

```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.14.zip
unzip v2.2.14.zip  
cd WALinuxAgent-2.2.14
```

### <a name="2-install-the-azure-linux-agent"></a>2.安装 Azure Linux 代理

对于版本 2.2. x，请使用：可能需要先安装包， `setuptools` 请参阅 [此处](https://pypi.python.org/pypi/setuptools)。 然后运行：

```bash
sudo python setup.py install
```

确保启用了自动更新。 首先，检查是否已启用自动更新：

```bash
cat /etc/waagent.conf
```

查找“AutoUpdate.Enabled”。 如果看到以下输出，则表示已启用：

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

若要允许运行：

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="3-restart-the-waagent-service"></a>3.重新启动 waagent 服务
对于大多数 linux 发行版：

```bash
sudo service waagent restart
```

对于 Ubuntu，使用：

```bash
sudo service walinuxagent restart
```

对于 CoreOS，使用：

```bash
sudo systemctl restart waagent
```

### <a name="4-confirm-the-azure-linux-agent-version"></a>4.确认 Azure Linux 代理版本
    
```bash
waagent -version
```

对于 CoreOS，上面的命令可能无效。

会看到 Linux 代理版本已更新为新版本。

有关 Azure Linux 代理的详细信息，请参阅 [Azure Linux 代理自述文件](https://github.com/Azure/WALinuxAgent)。
