---
title: "StorSimple 虚拟设备 Update 2 | Microsoft Docs"
description: "了解如何在 Microsoft Azure 虚拟网络中创建、部署和管理 StorSimple 虚拟设备。 （适用于 StorSimple Update 2）。"
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: f37752a5-cd0c-479b-bef2-ac2c724bcc37
ms.service: storsimple
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: ccf25aab4d6454e27a57f9a18305ef752421896f
ms.openlocfilehash: 6e4af8f21a3ec9613d9dc36ebceb710c0d8c9906


---
# <a name="deploy-and-manage-a-storsimple-virtual-device-in-azure"></a>在 Azure 中部署和管理 StorSimple 虚拟设备
## <a name="overview"></a>概述
StorSimple 8000 系列虚拟设备是 Microsoft Azure StorSimple 解决方案附带的一项额外功能。 StorSimple 虚拟设备在 Microsoft Azure 虚拟网络中的虚拟机上运行，可用于备份和克隆来自主机的数据。 本教程介绍如何在 Azure 中部署和管理虚拟设备，适用于运行软件版本 Update 2 和更低版本的所有虚拟设备。

#### <a name="virtual-device-model-comparison"></a>虚拟设备模型比较
StorSimple 虚拟设备可以在两种模型中使用：标准 8010（前身为 1100）和高级 8020（在 Update 2 引入）。 下表将两种模型做了比较。

| 设备型号 | 8010<sup>1</sup> | 8020 |
| --- | --- | --- |
| **最大容量** |30 TB |64 TB |
| **Azure VM** |Standard_A3（4 核，7 GB 内存） |Standard_DS3（4 核，14 GB 内存） |
| **版本兼容性** |运行 pre-Update 2 或更高版本的设备版本 |运行 Update 2 或更高版本的设备版本 |
| **上市区域** |所有 Azure 区域 |支持高级存储的 Azure 区域<br></br>有关区域列表，请参阅 [支持 8020 的区域](#supported-regions-for-8020) |
| **存储类型** |为本地磁盘使用 Azure 标准存储<br></br> 了解如何 [创建标准存储帐户](../storage/storage-create-storage-account.md) |为本地磁盘使用 Azure 高级存储<sup>2</sup> <br></br>了解如何[创建高级存储帐户](../storage/storage-premium-storage.md#quick-start-create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk) |
| **工作负荷指导** |在级别从备份中检索文件 |云开发和测试方案、低延迟、高性能工作负荷 <br></br>用于灾难恢复的辅助设备 |

<sup>1</sup>*前身为 1100*。

<sup>2</sup> *8010 和 8020 为云层使用 Azure 标准存储。只有设备中的本地层存在差异*。

#### <a name="supported-regions-for-8020"></a>支持 8020 的区域
下表显示目前支持 8020 的高级存储区域。 随着高级存储在更多区域上市，此列表会不断更新。

| 序列 编号 | 目前支持的区域 |
| --- | --- |
| 1 |美国中部 |
| 2 |美国东部 |
| 3 |美国东部 2 |
| 4 |美国西部 |
| 5 |欧洲北部 |
| 6 |欧洲西部 |
| 7 |亚洲东南部 |
| 8 |日本东部 |
| 9 |日本西部 |
| 10 |澳大利亚东部 |
| 11 |澳大利亚东南部* |
| 12 |东亚* |
| 13 |美国中南部* |

*高级存储最近已在这些地区推出。

本文介绍在 Azure 中部署 StorSimple 虚拟设备的逐步过程。 阅读本文后，将会：

* 了解虚拟设备与物理设备的差异。
* 能够创建和配置虚拟设备。
* 连接到虚拟设备。
* 了解如何使用虚拟设备。

本教程适用于运行 Update 2 和更高版本的所有 StorSimple 虚拟设备。

## <a name="how-the-virtual-device-differs-from-the-physical-device"></a>虚拟设备与物理设备的差异
StorSimple 虚拟设备是软件形式的 StorSimple，在 Microsoft Azure 虚拟机中的单个节点上运行。 虚拟设备为无法使用物理设备的灾难恢复方案提供支持，适合用于从备份进行的项目级检索、本地灾难恢复以及云开发和测试方案。

#### <a name="differences-from-the-physical-device"></a>与物理设备的差异
下表显示 StorSimple 虚拟设备与 StorSimple 物理设备之间的一些主要差异。

|  | 物理设备 | 虚拟设备 |
| --- | --- | --- |
| **位置** |驻留在数据中心。 |在 Azure 中运行。 |
| **网络接口** |有六个网络接口：DATA 0 到 DATA 5。 |只有一个网络接口：DATA 0。 |
| **注册** |在配置过程中注册。 |注册是一个单独的任务。 |
| **服务数据加密密钥** |在物理设备上重新生成密钥，然后使用新密钥更新虚拟设备。 |无法从虚拟设备重新生成。 |

## <a name="prerequisites-for-the-virtual-device"></a>虚拟设备先决条件
以下部分说明 StorSimple 虚拟设备的配置先决条件。 在部署虚拟设备之前，请查看 [有关使用虚拟设备的安全注意事项](storsimple-security.md#storsimple-virtual-device-security)。

#### <a name="azure-requirements"></a>Azure 要求
预配虚拟设备之前，需要在 Azure 环境中做好以下准备：

* 对于虚拟设备，需 [在 Azure 中配置虚拟网络](../virtual-network/virtual-networks-create-vnet-classic-portal.md)。 如果使用高级存储，必须在支持高级存储的 Azure 区域中创建虚拟网络。 有关详细信息，请参阅 [目前支持 8020 的区域](#supported-regions-for-8020)。
* 建议使用 Azure 提供的默认 DNS 服务器，而不要指定自己的 DNS 服务器名称。 如果 DNS 服务器名称无效，或者 DNS 服务器无法正确解析 IP 地址，则创建虚拟设备将会失败。
* 点到站点和站点到站点连接是可选的，而不是必需的。 如果需要，可以针对更高级方案配置这些选项。
* 可以在可使用虚拟设备公开的卷的虚拟网络中创建 [Azure 虚拟机](../virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) （主机服务器）。 这些服务器必须满足以下要求：                             

  * 是装有 iSCSI 发起程序软件的 Windows 或 Linux VM。
  * 在虚拟设备所在的同一个虚拟网络中运行。
  * 能够通过虚拟设备的内部 IP 地址连接到虚拟设备的 iSCSI 目标。
* 确保在同一个虚拟网络中配置 iSCSI 和云流量的支持。

#### <a name="storsimple-requirements"></a>StorSimple 要求
创建虚拟设备之前，请对 Azure StorSimple 服务进行以下更新：

* 针对要用作虚拟设备主机服务器的 VM 添加 [访问控制记录](storsimple-manage-acrs.md) 。
* 使用与虚拟设备位于同一区域中的 [存储帐户](storsimple-manage-storage-accounts.md#add-a-storage-account) 。 使用不同区域中的存储帐户可能导致性能不佳。 可以配合虚拟设备使用标准或高级存储帐户。 如何创建[标准存储帐户](../storage/storage-create-storage-account.md)或[高级存储帐户](../storage/storage-premium-storage.md#quick-start-create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk)的详细信息
* 用于创建虚拟设备的存储帐户应该与用于存储数据的存储帐户不同。 使用相同的存储帐户可能会导致性能不佳。

在开始之前，请确保已准备好以下信息：

* Azure 经典门户帐户和访问凭据。
* 物理设备中服务数据加密密钥的副本。

## <a name="create-and-configure-the-virtual-device"></a>创建和配置虚拟设备
在执行这些过程之前，请确保满足 [虚拟设备先决条件](#prerequisites-for-the-virtual-device)。

创建虚拟网络、配置 StorSimple Manager 服务并向该服务注册实体 StorSimple 设备之后，可以使用以下步骤创建和配置 StorSimple 虚拟设备。

### <a name="step-1-create-a-virtual-device"></a>步骤 1：创建虚拟设备
执行以下步骤创建 StorSimple 虚拟设备。

[!INCLUDE [Create a virtual device](../../includes/storsimple-create-virtual-device-u2.md)]

如果在此步骤中无法创建虚拟设备，可能是因为没有连接到 Internet。 有关详细信息，请转到[排查创建虚拟设备时遇到的 Internet 连接问题](#troubleshoot-internet-connectivity-errors)。

### <a name="step-2-configure-and-register-the-virtual-device"></a>步骤 2：配置和注册虚拟设备
开始此过程之前，请确保已获得服务数据加密密钥的副本。 服务数据加密密钥是在配置第一个 StorSimple 设备时创建的，系统已指示将它保存在安全位置。 如果没有服务数据加密密钥的副本，则必须联系 Microsoft 支持部门获得帮助。

执行以下步骤配置和注册 StorSimple 虚拟设备。

[!INCLUDE [Configure and register a virtual device](../../includes/storsimple-configure-register-virtual-device.md)]

### <a name="step-3-optional-modify-the-device-configuration-settings"></a>步骤 3：（可选）修改设备配置设置
以下部分介绍在使用 CHAP、StorSimple Snapshot Manager 或更改设备管理员密码时，需要在 StorSimple 虚拟设备完成的设备配置设置。

#### <a name="configure-the-chap-initiator"></a>配置 CHAP 发起程序
此参数包含虚拟设备（目标）应该从尝试访问卷的发起端（服务器）获取的凭据。 在此身份验证过程中，发起端提供 CHAP 用户名和 CHAP 密码，在设备上验证自身的身份。 有关详细步骤，请转到 [配置设备的 CHAP](storsimple-configure-chap.md#unidirectional-or-one-way-authentication)。

#### <a name="configure-the-chap-target"></a>配置 CHAP 目标
此参数包含具有 CHAP 功能的发起端请求相互或双向身份验证时，虚拟设备使用的凭据。 在此身份验证过程中，虚拟设备使用反向 CHAP 用户名和反向 CHAP 密码，在发起端验证自身的身份。 请注意，CHAP 目标设置是全局设置。 应用这些设置后，与存储虚拟设备连接的所有卷将使用 CHAP 身份验证。 有关详细步骤，请转到 [配置设备的 CHAP](storsimple-configure-chap.md#bidirectional-or-mutual-authentication)。

#### <a name="configure-the-storsimple-snapshot-manager-password"></a>配置 StorSimple Snapshot Manager 密码
StorSimple Snapshot Manager 软件驻留在 Windows 主机上，可让管理员以本地和云快照的形式管理 StorSimple 设备的备份。

> [!NOTE]
> 对虚拟设备而言，Windows 主机就是 Azure 虚拟机。
>
>

在 StorSimple Snapshot Manager 中配置设备时，系统将提示提供 StorSimple 设备 IP 地址和密码对存储设备进行身份验证。 有关详细步骤，请转到 [配置 StorSimple Snapshot Manager 密码](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password)。

#### <a name="change-the-device-administrator-password"></a>更改设备管理员密码
使用 Windows PowerShell 接口访问虚拟设备时，需要输入设备管理员密码。 为了确保数据的安全，必须更改此密码才能使用虚拟设备。 有关详细步骤，请转到 [配置设备管理员密码](storsimple-change-passwords.md#change-the-device-administrator-password)。

## <a name="connect-remotely-to-the-virtual-device"></a>远程连接到虚拟设备
默认情况下，不支持通过 Windows PowerShell 接口远程访问虚拟设备。 首先需要在虚拟设备上启用远程管理，然后在用于访问虚拟设备的客户端上启用远程管理。

下面详述了包括两个步骤的远程连接过程。

### <a name="step-1-configure-remote-management"></a>步骤 1：配置远程管理
执行以下步骤配置 StorSimple 虚拟设备的远程管理。

[!INCLUDE [Configure remote management via HTTP for virtual device](../../includes/storsimple-configure-remote-management-http-device.md)]

### <a name="step-2-remotely-access-the-virtual-device"></a>步骤 2：远程访问虚拟设备
在 StorSimple 设备设置页上启用远程管理之后，可以使用 Windows PowerShell 远程功能，从同一虚拟网络中的另一个虚拟机连接到虚拟设备；例如，可以从已配置的、用于连接 iSCSI 的主机 VM 进行连接。 在大多数部署中，已打开了一个公共终结点来访问可用于访问虚拟设备的主机 VM。

> [!WARNING]
> **为了增强安全性，强烈建议在连接到终结点时使用 HTTPS，并在完成 PowerShell 远程会话之后删除终结点。**
>
>

应该遵循 [远程连接到 StorSimple 设备](storsimple-remote-connect.md) 中的过程来设置虚拟设备的远程功能。

## <a name="connect-directly-to-the-virtual-device"></a>直接连接到虚拟设备
也可以直接连接到虚拟设备。 如果要从虚拟网络外部或 Microsoft Azure 环境外部的另一台计算机直接连接到虚拟设备，必须根据以下过程中的说明创建其他终结点。

执行以下步骤，在虚拟设备上创建公共终结点。

[!INCLUDE [Create public endpoints on a virtual device](../../includes/storsimple-create-public-endpoints-virtual-device.md)]

建议从同一虚拟网络中的另一个虚拟机进行连接，因为这种做法可将虚拟网络上的公共终结点数目减到最少。 使用此方法时，只需通过远程桌面会话连接到虚拟机，然后就像在局域网中配置任何其他 Windows 客户端一样配置该虚拟网络。 无需附加公共端口号，因为该端口是已知的。

## <a name="work-with-the-storsimple-virtual-device"></a>使用 StorSimple 虚拟设备
创建并配置 StorSimple 虚拟设备后，可以开始使用它。 可以在虚拟设备上使用卷容器、卷和备份策略，如同在物理 StorSimple 设备上所做的一样。唯一差别在于，必须确保从设备列表中选择虚拟设备。 有关虚拟设备的多种管理任务的分步过程，请参阅[使用 StorSimple Manager 服务管理虚拟设备](storsimple-manager-service-administration.md)。

以下部分介绍在使用虚拟设备时会遇到的一些差异。

### <a name="maintain-a-storsimple-virtual-device"></a>维护 StorSimple 虚拟设备
由于是纯软件设备，相比物理设备的维护，虚拟设备的维护可以说非常简单。 可以使用以下选项：

* **软件更新** - 可以查看上次更新软件的日期，以及任何更新状态消息。 若要检查是否有新的更新，可以使用页面底部的“扫描更新”来执行手动扫描。  如果有可用的更新，请单击“安装更新”进行安装。 虚拟设备上只有一个接口，这意味着应用更新时，会造成轻微的服务中断。 虚拟设备将关闭并重新启动（如果需要），以应用任何已发布的更新。 有关分步过程，请转到 [更新设备](storsimple-update-device.md#install-regular-updates-via-the-azure-classic-portal)。
* **支持包** - 可以创建并上载支持包，帮助 Microsoft 支持人员针对虚拟设备问题进行故障排除。 有关分步过程，请转到 [创建和管理支持包](storsimple-create-manage-support-package.md)。

### <a name="storage-accounts-for-a-virtual-device"></a>虚拟设备的存储帐户
创建的存储帐户供 StorSimple Manager 服务、虚拟设备和物理设备使用。 创建存储帐户时，建议在友好名称中使用区域标识符，帮助确保所有系统组件的区域一致。 在虚拟设备中，所有组件必须位于同一区域中，防止出现性能问题。

有关分步过程，请转到 [添加存储帐户](storsimple-manage-storage-accounts.md#add-a-storage-account)。

### <a name="deactivate-a-storsimple-virtual-device"></a>停用 StorSimple 虚拟设备
停用某个虚拟设备会删除预配该设备时创建的 VM 和资源。 停用虚拟设备之后，无法将它还原到以前的状态。 停用虚拟设备之前，请务必停止或删除它所依赖的客户端和主机。

停用虚拟设备会导致以下操作：

* 删除该虚拟设备。
* 删除为该虚拟设备创建的 OS 磁盘和数据磁盘。
* 保留预配期间创建的托管服务和虚拟网络。 如果不使用该服务和网络，应该手动将其删除。
* 保留为该虚拟设备创建的云快照。

有关分步过程，请转到 [停用和删除 StorSimple 设备](storsimple-deactivate-and-delete-device.md)。

虚拟设备在 StorSimple Manager 服务页上显示为已禁用后，可以从“设备”页上的设备列表中删除该虚拟设备。

### <a name="start-stop-and-restart-a-virtual-device"></a>启动、停止和重新启动虚拟设备
不同于 StorSimple 物理设备，StorSimple 虚拟设备上没有打开电源或关闭电源按钮。 但是，有时可能需要停止和重新启动虚拟设备。 例如，某些更新可能需要在 VM 重新启动后才能完成更新过程。 若要启动、停止和重新启动虚拟设备，最简单的方法是使用虚拟机管理控制台。

在管理控制台中，可以看到虚拟设备状态为“正在运行”，因为它在创建之后默认为启动状态。 可以随时启动、停止和重新启动虚拟机。

[!INCLUDE [Stop and restart virtual device](../../includes/storsimple-stop-restart-virtual-device.md)]

### <a name="reset-to-factory-defaults"></a>重置为出厂默认值
如果确定要创建全新的虚拟设备，只需将它停用并删除，然后新建一个。 与重置的物理设备一样，新虚拟设备上不会安装任何更新，因此，请务必在使用新设备之前检查更新。

## <a name="fail-over-to-the-virtual-device"></a>故障转移到虚拟设备
灾难恢复 (DR) 是 StorSimple 虚拟设备旨在解决的重要情形之一。 出现这种情况时，物理 StorSimple 设备甚至整个数据中心可能不可用。 幸运的是，可以在替代位置使用虚拟设备还原操作。 在 DR 期间，源设备中的卷容器将改变所有权，并转移到虚拟设备。 DR 的先决条件是已创建并配置虚拟设备、卷容器内的所有卷都已脱机，并且卷容器具有关联的云快照。

> [!NOTE]
> * 使用虚拟设备作为 DR 的辅助设备时，请记住，8010 有 30 TB 的标准存储，8020 有 64 TB 的高级存储。 较高容量的 8020 虚拟设备可能比较适合 DR 方案。
> * 无法从运行 Update 2 的设备故障转移或复制到运行 pre-Update 1 软件的设备。 但是，可以将运行 Update 2 的设备故障转移到运行 Update 1（1.1 或 1.2）的设备。
>
>

有关分步过程，请转到 [故障转移到虚拟设备](storsimple-device-failover-disaster-recovery.md#fail-over-to-a-storsimple-virtual-device)。

## <a name="shut-down-or-delete-the-virtual-device"></a>关闭或删除虚拟设备
如果以前已设置并使用 StorSimple 虚拟设备，但现在由于计算费的问题而不再想要使用，可以关闭虚拟设备。 关闭虚拟设备不会删除其操作系统或存储中的数据磁盘。 这只可以防止产生订阅费，但仍会产生操作系统和数据磁盘的存储费。

如果删除或关闭虚拟设备，StorSimple Manager 服务的“设备”页会将该设备显示为“脱机”。 如果还要删除虚拟设备创建的备份，可以选择停用或删除设备。 有关详细信息，请参阅 [停用和删除 StorSimple 设备](storsimple-deactivate-and-delete-device.md)。

[!INCLUDE [Shut down a virtual device](../../includes/storsimple-shutdown-virtual-device.md)]

[!INCLUDE [Delete a virtual device](../../includes/storsimple-delete-virtual-device.md)]

## <a name="troubleshoot-internet-connectivity-errors"></a>对 Internet 连接错误进行故障排除
创建虚拟设备时，如果没有 Internet 连接，创建步骤将会失败。 若要了解失败是否由 Internet 连接造成，请在 Azure 经典门户中执行以下步骤：

1. 在 Azure 中创建 Windows server 2012 虚拟机。 此虚拟机应使用与虚拟设备相同的存储帐户、 VNet 和子网。 如果已在 Azure 中具有使用相同存储帐户、VNet 和子网的现有 Windows Server 主机，也可以将其用于排除 Internet 连接故障。
2. 远程登录到上一步中创建的虚拟机。
3. 打开虚拟机内的命令窗口（Win + R，然后键入 `cmd`）。
4. 在提示符处运行以下 cmd。

    `nslookup windows.net`
5. 如果 `nslookup` 失败，则 Internet 连接故障阻止虚拟设备注册到 StorSimple Manager 服务。
6. 对虚拟网络进行必要的更改，以确保虚拟设备能够访问 Azure 站点（如“windows.net”）。

## <a name="next-steps"></a>后续步骤
* 了解如何 [使用 StorSimple Manager 服务管理虚拟设备](storsimple-manager-service-administration.md)。
* 了解如何 [从备份集还原 StorSimple 卷](storsimple-restore-from-backup-set.md)。



<!--HONumber=Jan17_HO1-->


