---
title: Microsoft Azure Data Box 磁盘常见问题解答 | Microsoft Docs
description: 包含有关 Azure Data Box 磁盘（用于将大量数据传输到 Azure 的云解决方案）的常见问答和解答
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/17/2018
ms.author: alkohli
ms.openlocfilehash: 5288e9900c75eae7601b84f7366edf9ac739d5da
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125797"
---
# <a name="what-is-azure-data-box-disk-preview"></a>什么是 Azure Data Box 磁盘？ （预览版）

使用 Microsoft Azure Data Box 磁盘云解决方案可以通过快速、经济、可靠的方式将 TB 量级的数据发送到 Azure。 本常见问题解答文章包含在 Azure 门户中使用 Data Box 磁盘时可能遇到的问题及其解答。 

问题和解答分为以下几个类别：

- 关于该服务
- 配置和连接 
- 跟踪状态
- 迁移数据 
- 验证和上传数据 

> [!IMPORTANT]
> Data Box 磁盘以预览版提供。 在部署此解决方案之前，请查看 [Azure 预览版服务的条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="about-the-service"></a>关于该服务

### <a name="q-what-is-azure-data-box-service"></a>问： 什么是 Azure Data Box 服务？ 
A.  Azure Data Box 服务专为脱机数据引入而设计。 此服务可以管理针对数据传输定制的、具有不同存储容量的所有产品组合。 

### <a name="q-what-are-azure-data-box-disks"></a>问： 什么是 Azure Data Box 磁盘？
A. 使用 Azure Data Box 磁盘能够快速、经济、安全地将 TB 量级的数据传入和传出 Azure。 Microsoft 会向你寄送 1 到 5 个磁盘，每个磁盘的最大存储容量为 35 TB。 可以通过 Azure 门户中的 Data Box 服务轻松配置、连接和解锁这些磁盘。  

磁盘已使用 Microsoft BitLocker 驱动器加密进行加密，可在 Azure 门户中管理加密密钥。 然后，从客户的服务器复制数据。 在数据中心，Microsoft 会使用高速专用网络上传链路将数据从驱动器迁移到云中，然后将其上传到 Azure。

### <a name="q-when-should-i-use-data-box-disks"></a>问： 何时应使用 Data Box 磁盘？
A. 如果需要将 35 TB（或更少）的数据传输到 Azure，则使用 Data Box 磁盘会很有利。

### <a name="q-what-is-the-price-of-data-box-disks"></a>问： Data Box 磁盘的价格是多少？
A. 在预览期，Data Box 磁盘免费提供使用。 寄送也是免费的，但是，Azure 存储会产生收费。

### <a name="q-how-do-i-get-data-box-disks"></a>问： 如何获取 Data Box 磁盘？ 
A.  若要获取 Azure Data Box 磁盘，请先注册 [Data Box 磁盘预览版](http://aka.ms/AzureDataBox)。 接下来，登录到 Azure 门户并创建磁盘的 Data Box 订单。 提供联系信息和通知详细信息。 在你提交订单后，我们会在 10 天内将磁盘寄送给你，具体时间取决于磁盘的供货情况。   

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-disks-in-one-instance"></a>问： 在一个实例中使用 Data Box 磁盘最多可以传输多少数据？
A. 如果使用 5 个磁盘，并且磁盘具有 8 TB 容量（7 TB 可用容量），则最大可用容量为 35 TB。 因此，可在一个实例中传输 35 TB 数据。  若要传输更多数据，需订购更多的磁盘。

### <a name="q-how-can-i-check-if-data-box-disks-are-available-in-my-region"></a>问： 如何检查 Data Box 磁盘是否在我的区域中可用？ 
A.  在预览阶段，Data Box 磁盘在美国和欧盟的所有国家/地区可用。  

### <a name="q-which-regions-can-i-store-data-in-with-data-box-disks"></a>问： 使用 Data Box 磁盘可在哪些区域存储数据？
A. 美国、西欧和北欧的所有区域支持 Data Box 磁盘预览版。 仅支持 Azure 公有云区域。 不支持 Azure 政府版或其他主权云。

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues--with-data-box-disks"></a>问： 如果 Data Box 磁盘出现任何问题，我应该与谁联系？
A. 如果遇到 Data Box 磁盘相关的任何问题，请联系 [Data Box 磁盘支持](mailto:expresspodsupport@microsoft.com)。

## <a name="configure-and-connect"></a>配置和连接
 
### <a name="q-can-i-specify-the-number-of-data-box-disks-in-the-order"></a>问： 是否可在订单中指定 Data Box 磁盘的数目？
A.  不是。 根据数据大小和磁盘的供货情况，你最多会收到 5 个 8 TB 磁盘。  

### <a name="q-how-do-i-unlock-the-data-box-disks"></a>问： 如何解锁 Data Box 磁盘？ 
A.  在 Azure 门户中，转到自己的 Data Box 磁盘订单，并导航到“设备详细信息”。 复制支持密钥。 从 Azure 门户下载并提取 Data Box 磁盘解锁工具，然后在要将其上的数据复制到磁盘的计算机中运行 *DataBoxDiskUnlock.exe*。 提供支持密钥以解锁磁盘。 同一个支持密钥可解锁所有磁盘。

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box-disks"></a>问： 是否可以使用 Linux 主机建立连接并将数据复制到 Data Box 磁盘？
A.  不是。 仅支持 Windows 计算机。 有关详细信息，请转到主机的[受支持操作系统](data-box-disk-system-requirements.md)列表。

### <a name="q-my-disks-are-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>问： 我的磁盘已发货，但现在我想取消此订单。 为何取消按钮不可用？
A.  订购磁盘后，只能在发货之前取消订单。 磁盘一旦发货，就再也不能取消订单。 在预览阶段，可以免费退回磁盘，但在此解决方案推出正式版后，此政策可能会有变化。 

### <a name="q-can-i-connect-multiple-data-box-disks-at-the-same-to-the-host-computer-to-transfer-data"></a>问： 是否可以同时将多个 Data Box 磁盘连接到主机来传输数据？
A. 是的。 可将多个 Data Box 磁盘连接到同一主机来传输数据，多个复制作业可以并行运行。

## <a name="track-status"></a>跟踪状态

### <a name="q-how-do-i-track-the-disks-from-when-i-placed-the-order-to-shipping-the-disks-back"></a>问： 从下单到退回磁盘的整个过程中，如何跟踪磁盘的订单状态？ 
A.  可以在 Azure 门户中跟踪 Data Box 磁盘的订单状态。 创建订单时，系统还会提示你提供通知电子邮件。 如果已提供，则在订单状态发生任何变化时，你都会收到电子邮件通知。 详细了解如何[配置通知电子邮件](data-box-portal-ui-admin.md#edit-notification-details)。

### <a name="q-how-do-i-return-the-disks"></a>问： 如何退回磁盘？ 
A.  Microsoft 会在发货包装中连同 Data Box 磁盘一起提供一个发货标签。 在快递公司寄件时，请将该标签贴在包装箱上，并密封包装。 如果该标签已损坏或丢失，请转到“概述”>“下载发货标签”，并下载新的退货标签。

## <a name="migrate-data"></a>迁移数据

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box-disks"></a>问： 在 Data Box 磁盘中最多可以使用多少数据？  
A.  Data Box 磁盘解决方案最多提供 5 个磁盘，最大可用容量为 35 TB。 磁盘本身容量为 8 TB（7 TB 可用容量）。 

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box-disks"></a>问： Data Box 磁盘支持的最大块 Blob 和页 Blob 大小是什么？ 
A.  最大大小受制于 Azure 存储限制。 最大块 Blob 大致为 4.768 TiB，最大页 Blob 大小为 8 TiB。 有关详细信息，请转到 [Azure 存储可伸缩性和性能目标](../storage/common/storage-scalability-targets.md)。 

### <a name="q-what-is-the-data-transfer-speed-for-data-box-disks"></a>问： Data Box 磁盘的数据传输速度是多少？
A. 对通过 USB 3.0 连接的磁盘进行测试时，磁盘性能最高为 430 MB/秒。 实际数字根据所用的文件大小而异。 传输小型文件时，性能可能会下降。

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>问： 如何知道我的数据在传输过程中是否安全？ 
A.  Data Box 磁盘已使用 BitLocker AES-128 位加密进行加密，支持密钥只在 Azure 门户中提供。 使用帐户凭据登录到 Azure 门户即可获取支持密钥。 运行 Data Box 磁盘解锁工具时，请提供此支持密钥。

### <a name="q-how-do-i-copy-the-data-to-the-data-box-disks"></a>问： 如何将数据复制到 Data Box 磁盘？ 
A.  使用 Robocopy、Diskboss 等 SMB 复制工具，甚至是 Windows 文件资源管理器的拖放式操作，即可将数据复制到磁盘。 

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>问： 在加快数据复制方面你们是否有诀窍？
A.  若要加快复制过程：

- 使用多个数据复制流。 例如，在 Robocopy 中使用多线程选项。 有关所用的确切命令的详细信息，请转到[教程：将数据复制到 Azure Data Box 磁盘并验证](data-box-disk-deploy-copy-data.md#copy-data-to-disks)。
- 使用多个会话。
- 不通过网络共享进行复制（否则可能受到网络速度的限制）可确保使数据驻留在磁盘所连接到计算机本地。
- 在整个复制过程中，确保使用 USB 3.0 或更高版本。 下载并使用 [USBView 工具](https://docs.microsoft.com/windows-hardware/drivers/debugger/usbview)，以识别已连接到计算机的 USB 控制器和 USB 设备。
- 为用于复制数据的计算机建立性能基准。 下载并使用 [Bluestop FIO 工具](https://bluestop.org/fio/)来建立服务器硬件的性能基准。

### <a name="q-how-to-speed-up-the-data-if-the-source-data-has-small-files-kbs-or-few-mbs"></a>问： 如果源数据包含小型文件（几个 KB 或 MB），如何加速数据复制？
A.  若要加快复制过程：

- 在高速存储上创建本地 VHDx，或者在 HDD/SSD（速度较慢）上创建空的 VHD。
- 将其装载到 VM。
- 将文件复制到 VM 的磁盘。


### <a name="q-can-i-use-multiple-storage-accounts-with-data-box-disks"></a>问： 是否可对 Data Box 磁盘使用多个存储帐户？
A.  不是。 Data Box 磁盘目前仅支持一个存储帐户（常规或经典帐户）。 支持热 Blob 和冷 Blob。 在预览期，仅支持美国、西欧和北欧的 Azure 公有云中的存储帐户。

## <a name="verify-and-upload"></a>验证和上传

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-disks-back"></a>问： 寄回磁盘后，多久可以访问我在 Azure 中的数据？ 
A.  一旦“数据复制”的订单状态显示为已完成，应该立即就能访问数据。

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>问： 上传后，我的数据位于 Azure 中的哪个位置？
A.  复制磁盘中 *BlockBlob* 和 *PageBlob* 文件夹下的数据时，系统会针对 *BlockBlob* 和 *PageBlob* 文件夹下的每个子文件夹，在 Azure 存储帐户中创建一个容器。 如果直接复制了 *BlockBlob* 和 *PageBlob* 文件夹下的文件，则这些文件将位于 Azure 存储帐户下的默认容器 *$root* 中。 

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>问： 我刚刚发现，我并未遵循容器的 Azure 命名要求。 我的数据是否无法上传到 Azure？
A. 如果容器名称包含大写字母，这些字母会自动转换为小写。 如果名称存在其他方面的不合规情况（包含特殊字符、采用其他语言，等等），则上传将会失败。

### <a name="q-how-do-i-verify-the-data-i-copied-onto-multiple-data-box-disks"></a>问： 如何验证已复制到多个 Data Box 磁盘的数据？
A.  数据复制完成后，可以运行 *AzureImportExport* 文件夹中提供的 `AzureExpressDiskService.cmd` 来生成校验和，以用于验证。 如果有多个磁盘，则需要针对每个磁盘打开命令窗口，并运行此命令。 请注意，根据具体的数据大小，此操作可能需要花费很长时间（大约数小时数）。

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-disks"></a>问： 退回磁盘后，我的数据会发生什么情况？
A.  完成将数据复制到 Azure 的过程后，会根据 NIST SP 800-88 修订版 1 中的指导原则，安全擦除磁盘中的数据。  

### <a name="q-how-is-my-data-protected-during-transit"></a>问： 在传输期间我的数据会受到怎样的保护？ 
A.  Data Box 磁盘已使用 AES-128 Microsoft BitLocker 加密进行加密。 这种加密要求使用单个支持密钥来解锁所有磁盘和访问数据。

### <a name="q-do-i-need-to-rerun-checksum-validation-if-i-add-more-data-to-the-data-box-disks"></a>问： 如果将更多数据添加到 Data Box 磁盘，是否需要重新运行校验和验证？
A. 是的。 将更多数据添加到 Data Box 磁盘后，如果你确定要验证数据（我们不建议这样做！），则需要重新运行验证。

### <a name="q-i-used-all-my-disks-to-transfer-data-and-need-to-order-more-disks-is-there-a-way-to-quickly-place-the-order"></a>问： 我已将所有的磁盘用于传输数据，现在需要订购更多的磁盘。 是否有某种方法可让我快速下单？
A. 可以克隆以前的订单。 克隆操作会创建与以前相同的订单，并允许编辑订单详细信息（仅限这些信息），而无需键入地址、联系人和通知详细信息。 



## <a name="next-steps"></a>后续步骤

- 请查看 [Data Box 系统要求](data-box-disk-system-requirements.md)。
- 了解 [Data Box 限制](data-box-disk-limits.md)。
- 在 Azure 门户中快速部署 [Azure Data Box 磁盘](data-box-disk-quickstart-portal.md)。
