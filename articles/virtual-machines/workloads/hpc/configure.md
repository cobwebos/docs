---
title: 高性能计算-Azure 虚拟机 |Microsoft Docs
description: 了解如何在 Azure 上的高性能计算。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: bcd121127721a6f76d76f11edf6574165c8e8ddb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66809807"
---
# <a name="optimization-for-linux"></a>针对 Linux 进行优化

本文介绍几种关键技术来优化您的操作系统映像。 详细了解如何[启用 InfiniBand](enable-infiniband.md)和优化的 OS 映像。

## <a name="update-lis"></a>更新 LIS

如果部署使用自定义映像 （例如，如 CentOS/RHEL 7.4 或 7.5 旧版操作系统），更新 VM 上的 LIS。

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

## <a name="reclaim-memory"></a>回收内存

通过自动回收内存以避免远程内存访问，提高效率。

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

若要使此 VM 重新启动后保留：

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
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g'/etc/selinux/config
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

* 详细了解如何[启用 InfiniBand](enable-infiniband.md)和优化 OS 映像。

* 详细了解如何[HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)在 Azure 上。
