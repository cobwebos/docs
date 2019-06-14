---
title: 升级 Azure Migrate 中的收集器设备 | Microsoft Docs
description: 介绍 Azure Migrate 收集器设备升级。
author: musa-57
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 03/29/2019
ms.author: hamusa
services: azure-migrate
ms.openlocfilehash: 7cd44318716200d665ece9ffecc45225bdfb85eb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60685913"
---
# <a name="collector-appliance-updates"></a>收集器设备更新

本文总结了 [Azure Migrate](migrate-overview.md) 中的收集器设备的升级信息。

Azure Migrate 收集器是一种轻量级设备，用于在迁移到 Azure 之前发现本地 vCenter 环境，以便进行评估。 [了解详细信息](concepts-collector.md)。

## <a name="how-to-upgrade-the-appliance"></a>如何升级设备

无需再次下载 OVA，即可将收集器升级到最新版本。

1. 关闭所有浏览器窗口和任何打开的文件/文件夹在设备中。
2. 从下面所述，在本文中的更新的列表中下载最新的升级包。
3. 若要确保下载的程序包是安全的打开管理员命令窗口并运行以下命令生成 ZIP 文件的哈希值。 生成的哈希应与针对特定版本提到的哈希相匹配：

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    示例：**C:\>CertUtil -HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256)**
4. 将 zip 文件复制到收集器设备虚拟机。
5. 右键单击 zip 文件 >“全部提取”  。
6. 右键单击 Setup.ps1   > “使用 PowerShell 运行”  ，并按照安装说明进行操作。

## <a name="collector-update-release-history"></a>收集器更新版本历史记录

### <a name="continuous-discovery-upgrade-versions"></a>持续发现：升级版本

#### <a name="version-101014-released-on-03292019"></a>版本 1.0.10.14 （03/29/2019年上发布）

包含几个 UI 增强功能。

哈希值，用于升级[打包 1.0.10.14](https://aka.ms/migrate/col/upgrade_10_14)

**算法** | **哈希值**
--- | ---
MD5 | 846b1eb29ef2806bcf388d10519d78e6
SHA1 | 6243239fa49c6b3f5305f77e9fd4426a392d33a0
SHA256 | fb058205c945a83cc4a31842b9377428ff79b08247f3fb8bb4ff30c125aa47ad

#### <a name="version-101012-released-on-03132019"></a>版本 1.0.10.12 （03/13/2019年上发布）

包含问题的修补程序中选择 Azure 云设备中。

哈希值，用于升级[打包 1.0.10.12](https://aka.ms/migrate/col/upgrade_10_12)

**算法** | **哈希值**
--- | ---
MD5 | 27704154082344c058238000dff9ae44
SHA1 | 41e9e2fb71a8dac14d64f91f0fd780e0d606785e
SHA256 | c6e7504fcda46908b636bfe25b8c73f067e3465b748f77e50027e66f2727c2a9

### <a name="one-time-discovery-deprecated-now-previous-upgrade-versions"></a>一次性发现（现已弃用）：以前的升级版本

> [!NOTE]
> 一次性发现设备现在已弃用，因为此方法依赖于 vCenter Server 针对性能数据点可用性的统计信息设置并且收集平均性能计数器，这导致用于迁移到 Azure 的 VM 大小不足。

#### <a name="version-10916-released-on-10292018"></a>版本 1.0.9.16（于 2018 年 10 月 29 日发布）

包含安装设备时遇到的 PowerCLI 问题的修补程序。

升级[包 1.0.9.16](https://aka.ms/migrate/col/upgrade_9_16) 的哈希值

**算法** | **哈希值**
--- | ---
MD5 | d2c53f683b0ec7aaf5ba3d532a7382e1
SHA1 | e5f922a725d81026fa113b0c27da185911942a01
SHA256 | a159063ff508e86b4b3b7b9a42d724262ec0f2315bdba8418bce95d973f80cfc

#### <a name="version-10914"></a>版本 1.0.9.14

升级[包 1.0.9.14](https://aka.ms/migrate/col/upgrade_9_14) 的哈希值

**算法** | **哈希值**
--- | ---
MD5 | c5bf029e9fac682c6b85078a61c5c79c
SHA1 | af66656951105e42680dfcc3ec3abd3f4da8fdec
SHA256 | 58b685b2707f273aa76f2e1d45f97b0543a8c4d017cd27f0bdb220e6984cc90e

#### <a name="version-10913"></a>版本 1.0.9.13

升级[包 1.0.9.13](https://aka.ms/migrate/col/upgrade_9_13) 的哈希值

**算法** | **哈希值**
--- | ---
MD5 | 739f588fe7fb95ce2a9b6b4d0bf9917e
SHA1 | 9b3365acad038eb1c62ca2b2de1467cb8eed37f6
SHA256 | 7a49fb8286595f39a29085534f29a623ec2edb12a3d76f90c9654b2f69eef87e


## <a name="next-steps"></a>后续步骤

- [详细了解](concepts-collector.md)收集器设备。
- 为 VMware VM [运行评估](tutorial-assessment-vmware.md)。
