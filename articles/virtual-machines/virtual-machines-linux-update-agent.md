---
title: "从 GitHub 更新 Azure Linux 代理 | Microsoft Docs"
description: "了解如何从 Github 将 Azure 中 Linux VM 的 Azure Linux 代理更新到最新版本"
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
ms.date: 12/14/2015
ms.author: mingzhan
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: e5baf0c89325abbef33bbabf1cfe29b878079e44


---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm-to-the-latest-version-from-github"></a>如何从 GitHub 将 VM 上的 Azure Linux 代理更新到最新版本
若要更新 Azure 中 Linux VM 上的 [Azure Linux 代理](https://github.com/Azure/WALinuxAgent)，则必须已具备以下条件：

1. 在 Azure 中具有运行的 Linux VM。
2. 使用 SHH 连接到该 Linux VM。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

<br>

> [!NOTE]
> 如果将从 Windows 计算机执行此任务，则可以使用 PuTTY 通过 SSH 登录到 Linux 计算机。 有关详细信息，请参阅[如何登录到运行 Linux 的虚拟机](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
> 
> 

Azure 支持的 Linux 发行版已将 Azure Linux 代理包放入其存储库中，因此，请先从该发行版存储库中查找并安装最新版本（如果可能）。  

对于 Ubuntu，只需键入：

```bash
sudo apt-get install walinuxagent
```

在 CentOS 中，请键入：

```bash
sudo yum install waagent
```

对于 Oracle Linux，请确保已启用 `Addons` 存储库。 选择编辑文件 `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) 或 `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux)，并在此文件中 **[ol6_addons]** 或 **[ol7_addons]** 下将行 `enabled=0` 更改为 `enabled=1`。

然后，要安装最新版本的 Azure Linux 代理，请键入：

```bash
sudo yum install WALinuxAgent
```

如果找不到外接程序存储库，你只需根据你的 Oracle Linux 版本，将这些行添加到 .repo 文件末尾处：

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

## <a name="install-wget"></a>安装 wget
使用 SSH 登录到你的 VM。

通过在命令行上键入 `#sudo yum install wget` 来安装 wget（有一些发行版在默认情况下未安装它，如 Redhat、CentOS 和 Oracle Linux 6.4 和 6.5 版）。

## <a name="download-the-latest-version"></a>下载最新版本
在网页中打开 [GitHub 中的 Azure Linux 代理版本](https://github.com/Azure/WALinuxAgent/releases)，并找到最新的版本号。 （可以通过键入 `#waagent --version` 查明你的当前版本。）

### <a name="for-version-20x-type"></a>对于版本 2.0.x，请键入：

```bash
wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-[version]/waagent
```

以下行使用版本 2.0.14 作为示例：

```bash
wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.14/waagent
```

### <a name="for-version-21x-or-later-type"></a>对于 2.1.x 或更高版本，请键入：
```bash
wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-[version].zip
unzip WALinuxAgent-[version].zip
cd WALinuxAgent-[version]
```

以下行使用版本 2.1.0 作为示例：

```bash
wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-2.1.0.zip
unzip WALinuxAgent-2.1.0.zip  
cd WALinuxAgent-2.1.0
```

## <a name="install-the-azure-linux-agent"></a>安装 Azure Linux 代理
### <a name="for-version-20x-use"></a>对于版本 2.0.x，请使用：
使 waagent 成为可执行文件：

```bash
chmod +x waagent
```

将新的可执行文件复制到 /usr/sbin/。

对于大多数 Linux，请使用：

```bash
sudo cp waagent /usr/sbin
```

对于 CoreOS，使用：

```bash
sudo cp waagent /usr/share/oem/bin/
```

如果这是新安装的 Azure Linux 代理，请运行：

```bash
sudo /usr/sbin/waagent -install -verbose
```

### <a name="for-version-21x-use"></a>对于版本 2.1.x，请使用：
可能需要先安装程序包 `setuptools` -- 详情请参阅[此处](https://pypi.python.org/pypi/setuptools)。 然后运行：

```bash
sudo python setup.py install
```

## <a name="restart-the-waagent-service"></a>重新启动 waagent 服务
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

## <a name="confirm-the-azure-linux-agent-version"></a>确认 Azure Linux 代理版本
    
```bash
waagent -version
```

对于 CoreOS，上面的命令可能无效。

你将看到 Linux 代理版本已更新为新版本。

有关 Azure Linux 代理的详细信息，请参阅 [Azure Linux 代理自述文件](https://github.com/Azure/WALinuxAgent)。




<!--HONumber=Nov16_HO3-->


