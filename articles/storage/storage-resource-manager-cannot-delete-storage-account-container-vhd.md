---
title: "排查删除 Azure 存储帐户、容器或 VHD 时发生的错误 | Microsoft Docs"
description: "排查删除 Azure 存储帐户、容器或 VHD 时发生的错误"
services: storage
documentationcenter: 
author: genlin
manager: cshepard
editor: na
tags: storage
ms.assetid: 17403aa1-fe8d-45ec-bc33-2c0b61126286
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: c3ea7cfba9fbf1064e2bd58344a7a00dc81eb148
ms.openlocfilehash: 318d7146ea53a806baf813ff7de2fe91f18becc8
ms.contentlocale: zh-cn
ms.lasthandoff: 07/20/2017

---
# <a name="troubleshoot-errors-when-you-delete-azure-storage-accounts-containers-or-vhds"></a>排查删除 Azure 存储帐户、容器或 VHD 时发生的错误

尝试在 [Azure 门户](https://portal.azure.com)中删除 Azure 存储帐户、容器或虚拟硬盘 (VHD) 时，可能会收到错误。 本文提供故障排除指导，帮助解决 Azure Resource Manager 部署中的问题。

如果本文不能帮助解决 Azure 问题，请访问 [MSDN 和堆栈溢出](https://azure.microsoft.com/support/forums/)上的 Azure 论坛。 可以将问题发布到这些论坛上，或者发布到 Twitter 上的 @AzureSupport。 此外，还可以通过在 [Azure 支持](https://azure.microsoft.com/support/options/)站点上选择“获取支持”来发出 Azure 支持请求。

## <a name="symptoms"></a>症状
### <a name="scenario-1"></a>方案 1
尝试删除 Resource Manager 部署中存储帐户的 VHD 时，会收到以下错误消息：

**无法删除 Blob“vhds/BlobName.vhd”。错误：目前 blob 上有租约，但请求中未指定任何租约 ID。**

如果虚拟机 (VM) 在尝试删除的 VHD 上有租约，会出现此问题。

### <a name="scenario-2"></a>方案 2
尝试删除 Resource Manager 部署中存储帐户的容器时，会收到以下错误消息：

**无法删除存储容器“vhds”。错误：目前容器上有租约，但请求中未指定任何租约 ID。**

如果容器具有租用状态为“已锁定”的 VHD，会出现此问题。

### <a name="scenario-3"></a>方案 3
尝试删除 Resource Manager 部署中的存储帐户时，会收到以下错误消息：

**无法删除存储帐户“StorageAccountName”。错误：正在使用存储帐户的项目，因此无法删除该存储帐户。**

如果存储帐户包含处于租用状态的 VHD，会出现此问题。

## <a name="solution"></a>解决方案 
若要解决这些问题，必须识别导致错误的 VHD 和关联的 VM。 然后，从 VM 分离该 VHD（适用于数据磁盘），或删除正在使用该 VHD 的 VM（适用于 OS 磁盘）。 这会从 VHD 中删除租约，从而能够删除该 VHD。 

为此，可使用以下方法之一：

### <a name="method-1---use-azure-storage-explorer"></a>方法 1 - 使用 Azure 存储资源管理器

### <a name="step-1-identify-the-vhd-that-prevent-deletion-of-the-storage-account"></a>步骤 1：识别阻止删除存储帐户的 VHD

1. 删除存储帐户时，会出现如下所示的消息对话框： 

    ![删除存储帐户时的消息](media/storage-resource-manager-cannot-delete-storage-account-container-vhd/delete-storage-error.png) 

2. 检查“磁盘 URL”来识别存储帐户以及阻止删除该存储帐户的 VHD。 在以下示例中，“.blob.core.windows.net”前面的字符串是存储帐户名称，“SCCM2012-2015-08-28.vhd”是 VHD 名称。  

        https://portalvhds73fmhrw5xkp43.blob.core.windows.net/vhds/SCCM2012-2015-08-28.vhd

### <a name="step-2-delete-the-vhd-by-using-azure-storage-explorer"></a>步骤 2：使用 Azure 存储资源管理器删除 VHD

1. 下载并安装最新版本的 [Azure 存储资源管理器](http://storageexplorer.com/)。 此工具是 Microsoft 提供的一个独立应用，可用于在 Windows、macOS 和 Linux 上轻松处理 Azure 存储数据。
2. 打开 Azure 存储资源管理器，在左侧栏中选择 ![帐户图标](media/storage-resource-manager-cannot-delete-storage-account-container-vhd/account.png) ，选择 Azure 环境，登录。

3. 选择所有订阅，或选择包含所要删除的存储帐户的订阅。

    ![添加订阅](media/storage-resource-manager-cannot-delete-storage-account-container-vhd/addsub.png)

4. 转到前面通过磁盘 URL 获取的存储帐户，选择“Blob 容器” > “VHD”，搜索阻止删除该存储帐户的 VHD。
5. 如果找到此 VHD，请检查“VM 名称”来查找正在使用此 VHD 的 VM。

    ![检查 VM](media/storage-resource-manager-cannot-delete-storage-account-container-vhd/check-vm.png)

6. 使用 Azure 门户从 VHD 中删除租约。 有关详细信息，请参阅[从 VHD 中删除租约](#remove-the-lease-from-the-vhd)。 

7. 转到 Azure 存储资源管理器，右键单击 VHD，选择“删除”。

8. 删除存储帐户。

### <a name="method-2---use-azure-portal"></a>方法 2 - 使用 Azure 门户 

#### <a name="step-1-identify-the-vhd-that-prevent-deletion-of-the-storage-account"></a>步骤 1：识别阻止删除存储帐户的 VHD

1. 删除存储帐户时，会出现如下所示的消息对话框： 

    ![删除存储帐户时的消息](media/storage-resource-manager-cannot-delete-storage-account-container-vhd/delete-storage-error.png) 

2. 检查“磁盘 URL”来识别存储帐户以及阻止删除该存储帐户的 VHD。 在以下示例中，“.blob.core.windows.net”前面的字符串是存储帐户名称，“SCCM2012-2015-08-28.vhd”是 VHD 名称。  

        https://portalvhds73fmhrw5xkp43.blob.core.windows.net/vhds/SCCM2012-2015-08-28.vhd

2. 登录到 [Azure 门户](https://portal.azure.com)。
3. 在“中心”菜单上，选择“所有资源”。 转到该存储帐户，选择“Blob” > “VHD”。

    ![门户屏幕截图，突出显示了存储帐户和“VHD”容器](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/opencontainer.png)

4. 找到前面通过磁盘 URL 获取的 VHD。 然后，确定正在使用该 VHD 的 VM。 通常情况下，可通过检查 VHD 的名称确定保存该 VHD 的 VM：

Resource Manager 开发模型中的 VM

   * OS 磁盘通常遵循以下命名约定：VMName-YYYY-MM-DD-HHMMSS.vhd
   * 数据磁盘通常遵循以下命名约定：VMName-YYYY-MM-DD-HHMMSS.vhd

经典开发模型中的 VM

   * OS 磁盘通常遵循以下命名约定：CloudServiceName-VMName-YYYY-MM-DD-HHMMSS.vhd
   * 数据磁盘通常遵循以下命名约定：CloudServiceName-VMName-YYYY-MM-DD-HHMMSS.vhd

#### <a name="step-2-remove-the-lease-from-the-vhd"></a>步骤 2：从 VHD 中删除租约

[从 VHD 中删除租约](#remove-the-lease-from-the-vhd)，删除存储帐户。

## <a name="what-is-a-lease"></a>租约是什么？
租约是可用于控制对 Blob（例如，VHD）的访问的锁。 租用 Blob 时，仅租约的所有者才能访问该 Blob。 租约非常重要，原因如下：

* 如果多个所有者尝试同时写入 Blob 的相同部分，它可防止数据损坏。
* 如果某个对象正在主动使用 Blob（例如，VM），它可防止删除该 Blob。
* 如果某个对象正在主动使用存储帐户（例如，VM），它可防止删除该存储帐户。

### <a name="remove-the-lease-from-the-vhd"></a>从 VHD 中删除租约
如果 VHD 是 OS 磁盘，则必须删除 VM 才能删除租约：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在“中心”菜单上，选择“虚拟机”。
3. 选择在 VHD 上保存租约的 VM。
4. 确保没有任何对象正主动使用该虚拟机，并确保不再需要该虚拟机。
5. 在“VM 详细信息”边栏选项卡顶部，选择“删除”，然后单击“是”确认。
6. 应删除 VM，但可以保留 VHD。 但是，VHD 上不应该再有租约。 可能需要几分钟才能释放租约。 若要确认是否已释放租约，请转到“所有资源” > “存储帐户名称” > “Blob” > “VHD”。 在“Blob 属性”窗格中，“租用状态”值应为“已解锁”。

如果 VHD 是数据磁盘，请从 VM 分离 VHD 以删除租约：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在“中心”菜单上，选择“虚拟机”。
3. 选择在 VHD 上保存租约的 VM。
4. 选择“VM 详细信息”边栏选项卡中的“磁盘”。
5. 选择在 VHD 上保存租约的数据磁盘。 可通过检查 VHD 的 URL 确定附加在磁盘中的 VHD。
6. 确保没有任何对象正在主动使用该数据磁盘。
7. 在“磁盘详细信息”边栏选项卡上，单击“分离”。
8. 磁盘现在应该已从 VM 分离，且 VHD 上不应该再有租约。 可能需要几分钟才能释放租约。 若要确认是否已释放租约，请转到“所有资源” > “存储帐户名称” > “Blob” > “VHD”。 在“Blob 属性”窗格中，“租用状态”值应为“已解锁”。

## <a name="next-steps"></a>后续步骤
* [删除存储帐户](storage-create-storage-account.md#delete-a-storage-account)
* [如何在 Microsoft Azure 中解除 blob 存储的锁定租约 (PowerShell)](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)

