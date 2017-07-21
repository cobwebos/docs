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
ms.date: 06/30/2017
ms.author: steveesp
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 1340048d5d518caff3397f671d0c75caaab4b5ac
ms.contentlocale: zh-cn
ms.lasthandoff: 07/01/2017


---

# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>优化 Azure 虚拟机网络吞吐量

Azure 虚拟机 (VM) 的默认网络设置可以进一步针对网络吞吐量进行优化。 本文介绍如何针对 Microsoft Azure Windows 和 Linux VM（包括 Ubuntu、CentOS 和 Red Hat 等主要分发）优化网络吞吐量。

## <a name="windows-vm"></a>Windows VM

与不使用 RSS 的 VM 相比，使用接收方缩放 (RSS) 的 VM 可达到更高的最大吞吐量。 默认情况下，RSS 在 Windows VM 中已禁用。 完成以下步骤以确定是否启用了 RSS 并在已禁用它时启用它。

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

默认情况下，RSS 在 Azure Linux VM 中始终已启用。 自 2017 年 1 月以后发布的 Linux 内核包含新的网络优化选项，可使 Linux VM 实现更高的网络吞吐量。

### <a name="ubuntu"></a>Ubuntu

若要获得优化功能，首先需更新到 2017 年 6 月以后的最新支持版本，该版本是：
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

### <a name="centos"></a>CentOS

若要获得优化功能，首先需更新到 2017 年 5 月以后的最新支持版本，该版本是：
```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.3",
"Version": "latest"
```
更新完成后，安装最新 Linux Integration Services (LIS)。
吞吐量优化功能在从 4.2 开始的 LIS 中。 输入以下命令安装 LIS：

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

若要获得优化功能，首先需更新到 2017 年 1 月以后的最新支持版本，该版本是：
```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7.3"
"Version": "7.3.2017062722"
```
更新完成后，安装最新 Linux Integration Services (LIS)。
吞吐量优化功能在从 4.2 开始的 LIS 中。 输入以下命令下载并安装 LIS：

```bash
mkdir lis4.2.1
cd lis4.2.1
wget https://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.1-1.tar.gz
tar xvzf lis-rpms-4.2.1-1.tar.gz
cd LISISO
install.sh #or upgrade.sh if prior LIS was previously installed
```

查看[下载页](https://www.microsoft.com/download/details.aspx?id=55106)，详细了解适用于 Hyper-V 的 Linux Integration Services 版本 4.2。

## <a name="next-steps"></a>后续步骤
* 现在，虚拟机进行了优化，请参阅[带宽/吞吐量测试 Azure VM](virtual-network-bandwidth-testing.md)，查阅方案的结果。
* 通过 [Azure 虚拟网络常见问题解答 (FAQ)](virtual-networks-faq.md) 了解详细信息

