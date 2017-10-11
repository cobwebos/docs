---
title: "从 GitHub 更新 Azure Linux 代理 | Microsoft Docs"
description: "了解如何通过 GitHub 将 Azure 中 Linux VM 的 Azure Linux 代理更新为最新版本"
services: virtual-machines-linux
documentationcenter: 
author: SuperScottz
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: f1f19300-987d-4f29-9393-9aba866f049c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: mingzhan
ms.openlocfilehash: c79e37976a58ae5384b5856e0f7f258a773ef0fd
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2017
---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm"></a>如何更新 VM 上的 Azure Linux 代理

若要更新 Azure 中 Linux VM 上的 [Azure Linux 代理](https://github.com/Azure/WALinuxAgent)，则必须已具备以下条件：

- 在 Azure 中具有运行的 Linux VM。
- 使用 SHH 连接到该 Linux VM。

应始终先对 Linux 发行版存储库中的程序包进行检查。 虽然可用的程序包很有可能不是最新版本，但启用自动更新可确保 Linux 代理始终获得最新的更新。 如果程序包管理器中的安装存在问题，可以向发行版供应商寻求支持。

## <a name="updating-the-azure-linux-agent"></a>更新 Azure Linux 代理

## <a name="ubuntu"></a>Ubuntu

#### <a name="check-your-current-package-version"></a>检查当前程序包的版本

```bash
apt list --installed | grep walinuxagent
```

#### <a name="update-package-cache"></a>更新程序包缓存

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>安装最新版本的程序包

```bash
sudo apt-get install walinuxagent
```

#### <a name="ensure-auto-update-is-enabled"></a>确保已启用自动更新

首先，检查是否已启用自动更新：

```bash
cat /etc/waagent.conf
```

找到“AutoUpdate.Enabled”。 如果看到以下输出，则表示已启用：

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

若要启用运行：

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>重新启动 waagent 服务

#### <a name="restart-agent-for-1404"></a>重新启动 14.04 的代理

```bash
initctl restart walinuxagent
```

#### <a name="restart-agent-for-1604--1704"></a>重新启动 16.04 / 17.04 的代理

```bash
systemctl restart walinuxagent.service
```

## <a name="debian"></a>Debian

### <a name="debian-7-wheezy"></a>Debian 7 “Wheezy”

#### <a name="check-your-current-package-version"></a>检查当前程序包的版本

```bash
dpkg -l | grep waagent
```

#### <a name="update-package-cache"></a>更新程序包缓存

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>安装最新版本的程序包

```bash
sudo apt-get install waagent
```

#### <a name="enable-agent-auto-update"></a>启用代理自动更新
由于此版本的 Debian 没有 > = 2.0.16 的版本，因此 AutoUpdate 对该版本不适用。 上述命令的输出将显示程序包是否为最新版。

### <a name="debian-8-jessie--debian-9-stretch"></a>Debian 8 “Jessie” / Debian 9 “Stretch”

#### <a name="check-your-current-package-version"></a>检查当前程序包的版本

```bash
apt list --installed | grep walinuxagent
```

#### <a name="update-package-cache"></a>更新程序包缓存

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>安装最新版本的程序包

```bash
sudo apt-get install waagent
```
#### <a name="ensure-auto-update-is-enabled"></a>确保已启用自动更新 

首先，检查是否已启用自动更新：

```bash
cat /etc/waagent.conf
```

找到“AutoUpdate.Enabled”。 如果看到以下输出，则表示已启用：

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

若要启用运行：

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>重新启动 waagent 服务

```
sudo systemctl restart walinuxagent.service
```

## <a name="redhat--centos"></a>Redhat / CentOS

### <a name="rhelcentos-6"></a>RHEL/CentOS 6

#### <a name="check-your-current-package-version"></a>检查当前程序包的版本

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>检查可用的更新

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>安装最新版本的程序包

```bash
sudo yum install WALinuxAgent
```

#### <a name="ensure-auto-update-is-enabled"></a>确保已启用自动更新 

首先，检查是否已启用自动更新：

```bash
cat /etc/waagent.conf
```

找到“AutoUpdate.Enabled”。 如果看到以下输出，则表示已启用：

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

若要启用运行：

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>重新启动 waagent 服务

```
sudo service waagent restart
```

### <a name="rhelcentos-7"></a>RHEL/CentOS 7

#### <a name="check-your-current-package-version"></a>检查当前程序包的版本

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>检查可用的更新

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>安装最新版本的程序包

```bash
sudo yum install WALinuxAgent  
```

#### <a name="ensure-auto-update-is-enabled"></a>确保已启用自动更新 

首先，检查是否已启用自动更新：

```bash
cat /etc/waagent.conf
```

找到“AutoUpdate.Enabled”。 如果看到以下输出，则表示已启用：

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

若要启用运行：

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>重新启动 waagent 服务

```bash
sudo systemctl restart waagent.service
```

## <a name="suse-sles"></a>SUSE SLES

### <a name="suse-sles-11-sp4"></a>SUSE SLES 11 SP4

#### <a name="check-your-current-package-version"></a>检查当前程序包的版本

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>检查可用的更新

上面的输出将显示程序包是否为最新版。

#### <a name="install-the-latest-package-version"></a>安装最新版本的程序包

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>确保已启用自动更新 

首先，检查是否已启用自动更新：

```bash
cat /etc/waagent.conf
```

找到“AutoUpdate.Enabled”。 如果看到以下输出，则表示已启用：

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

若要启用运行：

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>重新启动 waagent 服务

```bash
sudo /etc/init.d/waagent restart
```

### <a name="suse-sles-12-sp2"></a>SUSE SLES 12 SP2

#### <a name="check-your-current-package-version"></a>检查当前程序包的版本

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>检查可用的更新

上面的输出将显示程序包是否为最新版。

#### <a name="install-the-latest-package-version"></a>安装最新版本的程序包

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>确保已启用自动更新 

首先，检查是否已启用自动更新：

```bash
cat /etc/waagent.conf
```

找到“AutoUpdate.Enabled”。 如果看到以下输出，则表示已启用：

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

若要启用运行：

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>重新启动 waagent 服务

```bash
sudo systemctl restart waagent.service
```

## <a name="oracle-6-and-7"></a>Oracle 6 和 7

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
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
gpgkey=http://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
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
enabled=0
```

然后键入：

```bash
sudo yum update WALinuxAgent
```

通常只需要这样做，但如果因某种原因而需要直接从 https://github.com 安装它，请使用以下步骤。


## <a name="update-the-linux-agent-when-no-agent-package-exists-for-distribution"></a>没有可用于分发的代理程序包时，请更新 Linux 代理

通过在命令行上键入 `sudo yum install wget` 来安装 wget（某些发行版在默认情况下未安装它，如 Redhat、CentOS 和 Oracle Linux 6.4 和 6.5 版）。

### <a name="1-download-the-latest-version"></a>1.下载最新版本
在网页中打开 [GitHub 中的 Azure Linux 代理版本](https://github.com/Azure/WALinuxAgent/releases)，并找到最新的版本号。 （可以通过键入 `waagent --version` 查明当前版本。）

#### <a name="for-version-22x-or-later-type"></a>对于 2.2.x 或更高版本，请键入：
```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.x.zip
unzip v2.2.x.zip.zip
cd WALinuxAgent-2.2.x
```

以下行使用版本 2.2.0 作为示例：

```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.14.zip
unzip v2.2.14.zip  
cd WALinuxAgent-2.2.14
```

### <a name="2-install-the-azure-linux-agent"></a>2.安装 Azure Linux 代理

#### <a name="for-version-22x-use"></a>对于版本 2.2.x，请使用：
可能需要先安装程序包 `setuptools` -- 详情请参阅[此处](https://pypi.python.org/pypi/setuptools)。 然后运行：

```bash
sudo python setup.py install
```

#### <a name="ensure-auto-update-is-enabled"></a>确保已启用自动更新

首先，检查是否已启用自动更新：

```bash
cat /etc/waagent.conf
```

找到“AutoUpdate.Enabled”。 如果看到以下输出，则表示已启用：

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

若要启用运行：

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
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