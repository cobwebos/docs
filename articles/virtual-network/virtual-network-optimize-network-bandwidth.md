---
title: "优化 VM 网络吞吐量 | Microsoft 文档"
description: "了解如何优化 Azure 虚拟机网络吞吐量。"
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: steveesp
ms.openlocfilehash: d77440fe62bbd0e720e5ae60b15574dacc4180c0
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2017
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>优化 Azure 虚拟机网络吞吐量

Azure 虚拟机 (VM) 的默认网络设置可以进一步针对网络吞吐量进行优化。 本文介绍如何针对 Microsoft Azure Windows 和 Linux VM（包括 Ubuntu、CentOS 和 Red Hat 等主要分发）优化网络吞吐量。

## <a name="windows-vm"></a>Windows VM

如果[加速网络](virtual-network-create-vm-accelerated-networking.md)支持 Windows VM，则启用该功能会是吞吐量的最佳配置。 对于所有其他 Windows VM，与不使用 RSS 的 VM 相比，使用接收方缩放 (RSS) 可达到更高的最大吞吐量。 默认情况下，RSS 在 Windows VM 中已禁用。 完成以下步骤以确定是否启用了 RSS 并在已禁用它时启用它。

1. 输入 `Get-NetAdapterRss` PowerShell 命令来查看是否为网络适配器启用了 RSS。 在以下从 `Get-NetAdapterRss` 返回的示例输出中，RSS 未启用。

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled              : False
    ```
2. 输入以下命令启用 RSS：

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    前一个命令没有输出。 该命令更改了 NIC 设置，导致暂时连接丢失大约一分钟。 在连接丢失期间显示“重新连接”对话框。 通常在第三次尝试后，连接会还原。
3. 再次输入 `Get-NetAdapterRss` 命令，确认 RSS 在 VM 中已启用。 如果成功，将返回以下示例输出：

    ```powershell
    Name                    :Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled              : True
    ```

## <a name="linux-vm"></a>Linux VM

默认情况下，RSS 在 Azure Linux VM 中始终已启用。 自 2017 年 12 月以后发布的 Linux 内核均包含新的网络优化选项，可使 Linux VM 实现更高的网络吞吐量。

### <a name="ubuntu-for-new-deployments"></a>用于新部署的 Ubuntu

若要获得优化功能，请首先安装 16.04-LTS 的最新支持版本，例如：
```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04-LTS",
"Version": "latest"
```
更新完成后，输入以下命令获取最新内核：

```bash
apt-get -f install
apt-get --fix-missing install
apt-get clean
apt-get -y update
apt-get -y upgrade
```

可选命令：

`apt-get -y dist-upgrade`
#### <a name="ubuntu-azure-kernel-upgrade-for-existing-vms"></a>现有 VM 的 Ubuntu Azure 内核升级

重要的吞吐量性能可通过升级到 Azure Linux 内核来实现。 若要验证是否具有此内核，请检查你的内核版本。

```bash
#Azure kernel name ends with "-azure"
uname -r

#sample output on Azure kernel:
#4.11.0-1014-azure
```

然后作为 root 运行这些命令。
```bash
#run as root or preface with sudo
apt-get update
apt-get upgrade -y
apt-get dist-upgrade -y
apt-get install "linux-azure"
reboot
```

### <a name="centos"></a>CentOS

若要获得最新的优化功能，请首先更新到最新支持版本，例如：
```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.4",
"Version": "latest"
```
更新完成后，安装最新 Linux Integration Services (LIS)。
从 4.2.2-2 开始，吞吐量优化位于 LIS 中，尽管更高版本包含更多改进。 输入以下命令以安装最新 LIS：

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

若要获得优化功能，请首先更新到最新支持版本，例如：
```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7-RAW"
"Version": "latest"
```
更新完成后，安装最新 Linux Integration Services (LIS)。
吞吐量优化功能在从 4.2 开始的 LIS 中。 输入以下命令下载并安装 LIS：

```bash
mkdir lis4.2.3-1
cd lis4.2.3-1
wget https://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.3-1.tar.gz
tar xvzf lis-rpms-4.2.3-1.tar.gz
cd LISISO
install.sh #or upgrade.sh if prior LIS was previously installed
```

查看[下载页](https://www.microsoft.com/download/details.aspx?id=55106)，详细了解适用于 Hyper-V 的 Linux Integration Services 版本 4.2。

## <a name="next-steps"></a>后续步骤
* 现在，虚拟机进行了优化，请参阅[带宽/吞吐量测试 Azure VM](virtual-network-bandwidth-testing.md)，查阅方案的结果。
* 通过 [Azure 虚拟网络常见问题解答 (FAQ)](virtual-networks-faq.md) 了解详细信息
