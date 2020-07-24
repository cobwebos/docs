---
title: 高性能计算-Azure 虚拟机 |Microsoft Docs
description: 了解 Azure 上的高性能计算。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: 723419b97dc024a700d860dd3fe61ff48073a587
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87019992"
---
# <a name="optimization-for-linux"></a>针对 Linux 进行优化

本文介绍了几种优化 OS 映像的关键技术。 详细了解如何[启用](enable-infiniband.md)"不允许" 和 "优化 OS 映像"。

## <a name="update-lis"></a>更新 .LIS

如果使用自定义映像（例如，CentOS/RHEL 7.4 或7.5 等较早的 OS）进行部署，请在 VM 上更新 .LIS。

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

## <a name="reclaim-memory"></a>回收内存

通过自动回收内存来提高效率，以避免远程内存访问。

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

要使其在 VM 重新启动后保持不变：

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

## <a name="disable-firewall-and-selinux"></a>禁用防火墙和 SELinux

禁用防火墙和 SELinux。

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

## <a name="disable-cpupower"></a>禁用 cpupower

禁用 cpupower。

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

## <a name="next-steps"></a>后续步骤

* 详细了解如何[启用无法实现](enable-infiniband.md)和优化操作系统映像。

* 了解有关 Azure 上的[HPC](/azure/architecture/topics/high-performance-computing/)的详细信息。
