---
title: 升级 Azure Migrate 中的收集器设备 | Microsoft Docs
description: 介绍 Azure Migrate 收集器设备升级。
author: musa-57
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: hamusa
services: azure-migrate
ms.openlocfilehash: f29556c8f2a5aa727ce80632eaccf5e1ed6d7c1a
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415538"
---
# <a name="collector-update-release-history"></a>收集器更新版本历史记录

本文总结了 [Azure Migrate](migrate-overview.md) 中的收集器设备的升级信息。

Azure Migrate 收集器是一种轻量级设备，用于在迁移到 Azure 之前发现本地 vCenter 环境，以便进行评估。 [了解详细信息](concepts-collector.md)。

## <a name="continuous-discovery-upgrade-versions"></a>持续发现：升级版本

尚未提供面向持续发现设备的升级。

## <a name="one-time-discovery-deprecated-now-previous-upgrade-versions"></a>一次性发现（现已弃用）：以前的升级版本

> [!NOTE]
> 一次性发现设备现在已弃用，因为此方法依赖于 vCenter Server 针对性能数据点可用性的统计信息设置并且收集平均性能计数器，这导致用于迁移到 Azure 的 VM 大小不足。

### <a name="version-10916-released-on-10292018"></a>版本 1.0.9.16（于 2018 年 10 月 29 日发布）

包含安装设备时遇到的 PowerCLI 问题的修补程序。

升级[包 1.0.9.16](https://aka.ms/migrate/col/upgrade_9_16) 的哈希值

**算法** | **哈希值**
--- | ---
MD5 | d2c53f683b0ec7aaf5ba3d532a7382e1
SHA1 | e5f922a725d81026fa113b0c27da185911942a01
SHA256 | a159063ff508e86b4b3b7b9a42d724262ec0f2315bdba8418bce95d973f80cfc

### <a name="version-10914"></a>版本 1.0.9.14

升级[包 1.0.9.14](https://aka.ms/migrate/col/upgrade_9_14) 的哈希值

**算法** | **哈希值**
--- | ---
MD5 | c5bf029e9fac682c6b85078a61c5c79c
SHA1 | af66656951105e42680dfcc3ec3abd3f4da8fdec
SHA256 | 58b685b2707f273aa76f2e1d45f97b0543a8c4d017cd27f0bdb220e6984cc90e

### <a name="version-10913"></a>版本 1.0.9.13

升级[包 1.0.9.13](https://aka.ms/migrate/col/upgrade_9_13) 的哈希值

**算法** | **哈希值**
--- | ---
MD5 | 739f588fe7fb95ce2a9b6b4d0bf9917e
SHA1 | 9b3365acad038eb1c62ca2b2de1467cb8eed37f6
SHA256 | 7a49fb8286595f39a29085534f29a623ec2edb12a3d76f90c9654b2f69eef87e


## <a name="run-an-upgrade"></a>运行升级

无需再次下载 OVA，即可将收集器升级到最新版本。

1. 可以在下面的列表中下载最新的升级包。
2. 若要确保下载的修补程序是安全的，请打开管理员命令窗口并运行以下命令生成 ZIP 文件的哈希。 生成的哈希应与针对特定版本提到的哈希相匹配：

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    示例：**C:\>CertUtil -HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256)**
3. 将 zip 文件复制到收集器设备虚拟机。
4. 右键单击 zip 文件 >“全部提取”。
5. 右键单击 Setup.ps1 > “使用 PowerShell 运行”，并按照安装说明进行操作。


## <a name="next-steps"></a>后续步骤

- [详细了解](concepts-collector.md)收集器设备。
- 为 VMware VM [运行评估](tutorial-assessment-vmware.md)。
