---
title: Azure 导入/导出服务常见问题解答 | Microsoft Docs
description: 阅读有关 Azure 导入导出服务的常见问题解答。
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/06/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 0c8af4cafb2c7ab4d39a591f5972803161c2aa0f
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83120379"
---
# <a name="azure-importexport-service-frequently-asked-questions"></a>Azure 导入/导出服务：常见问题解答

下面是你在使用 Azure 导入/导出服务将数据传输到 Azure 存储时可能会提出的问题及其解答。 问题和解答分为以下几个类别：

- 关于导入/导出服务
- 为导入/导出准备磁盘
- 导入/导出作业
- 寄送磁盘
- 杂项

## <a name="about-importexport-service"></a>关于导入/导出服务

### <a name="can-i-copy-azure-file-storage-using-the-azure-importexport-service"></a>是否可以使用 Azure 导入/导出服务复制 Azure 文件存储？

是的。 Azure 导入/导出服务支持导入到 Azure 文件存储。 目前它不支持导出 Azure 文件。

### <a name="is-the-azure-importexport-service-available-for-csp-subscriptions"></a>Azure 导入/导出服务是否适用于 CSP 订阅？

是的。 Azure 导入/导出服务支持云解决方案提供商 (CSP) 订阅。

### <a name="can-i-use-the-azure-importexport-service-to-copy-pst-mailboxes-and-sharepoint-data-to-o365"></a>是否可以使用 Azure 导入/导出服务将 PST 邮箱和 SharePoint 数据复制到 O365？

是的。 有关详细信息，请转到[将 PST 文件或 SharePoint 数据导入到 Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx)。

### <a name="can-i-use-the-azure-importexport-service-to-copy-my-backups-offline-to-the-azure-backup-service"></a>是否可以使用 Azure 导入/导出服务将我的备份脱机复制到 Azure 备份服务？

是的。 有关详细信息，请转到 [Azure 备份中的脱机备份工作流](../../backup/backup-azure-backup-import-export.md)。

### <a name="can-i-purchase-drives-for-importexport-jobs-from-microsoft"></a>是否可以从 Microsoft 购买用于导入/导出作业的驱动器？

不是。 对于导入和导出作业，你需要使用自己的驱动器。

## <a name="preparing-disks-for-importexport"></a>为导入/导出准备磁盘

### <a name="can-i-skip-the-drive-preparation-step-for-an-import-job-can-i-prepare-a-drive-without-copying"></a>是否可以跳过导入作业的驱动器准备步骤？ 是否可以在不复制的情况下准备磁盘？

不是。 必须使用 Azure WAImportExport 工具准备用来导入数据的任何驱动器。 还要使用该工具将数据复制到驱动器。

### <a name="do-i-need-to-perform-any-disk-preparation-when-creating-an-export-job"></a>在创建导出作业时我是否需要执行任何磁盘准备操作？

不是。 但建议执行一些预先检查。 若要检查所需的磁盘数，请使用 WAImportExport 工具的 PreviewExport 命令。 有关详细信息，请参阅[预览导出作业的驱动器使用情况](https://msdn.microsoft.com/library/azure/dn722414.aspx)。 此命令可以根据要使用的驱动器的大小，帮助你预览所选 Blob 的驱动器使用情况。 此外，请检查是否可以向要寄送的用于导出作业的硬盘驱动器进行读取和写入。

## <a name="importexport-jobs"></a>导入/导出作业

### <a name="can-i-cancel-my-job"></a>是否可以取消我的作业？

是的。 可以取消状态为“正在创建”或“正在寄送”的作业。   在这些阶段之外，无法取消作业，它将一直执行到最终阶段。

### <a name="how-long-can-i-view-the-status-of-completed-jobs-in-the-azure-portal"></a>在 Azure 门户中可以查看多长时间的已完成作业的状态？

可以查看最长 90 天的已完成作业的状态。 已完成作业在 90 天后会被删除。

### <a name="if-i-want-to-import-or-export-more-than-10-drives-what-should-i-do"></a>如果我想要导入或导出 10 个以上驱动器，我应该怎样做？

一个导入或导出作业在单个作业中只能引用 10 个驱动器。 若要寄送 10 个以上驱动器，应当创建多个作业。 与同一作业关联的驱动器必须放在同一个包裹中一起寄送。
有关数据容量跨越多个磁盘导入作业的详细信息和指南，请联系 Microsoft 支持部门。

### <a name="the-uploaded-blob-shows-status-as-lease-expired-what-should-i-do"></a>上传的 blob 显示状态为“租用已过期”。   应采取何种操作？

你可以忽略“租用已过期”字段。 导入/导出在上传期间对 blob 进行租用，以确保没有其他进程可以并行更新 blob。 “租用已过期”意味着导入/导出不再上传到它，并且 blob 可以供你使用。

## <a name="shipping-disks"></a>寄送磁盘

### <a name="what-is-the-maximum-number-of-hdd-for-in-one-shipment"></a>一次装运的最大 HDD数量是多少？

对于一次装运的 HDD 数量没有限制。 如果磁盘属于多个作业，我们建议：

- 为磁盘标上对应的作业名称。
- 使用后缀为 -1、-2 等的跟踪号码更新作业。

### <a name="should-i-include-anything-other-than-the-hdd-in-my-package"></a>是否应该在包裹中放置除了 HDD 之外的其他东西？

请仅在寄送包裹中寄送硬盘驱动器。 不要包括电源线或 USB 电缆之类的物品。

### <a name="do-i-have-to-ship-my-drives-using-fedex-or-dhl"></a>是否必须通过 FedEx 或 DHL 寄送我的驱动器？

可以通过任何知名的快递商（例如 FedEx、DHL、UPS 或美国邮政总局）将驱动器寄送到 Azure 数据中心。 但是，为了能够从数据中心将驱动器回寄给你，你必须提供：

- 在美国和欧洲的 FedEx 帐户编号，或者
- 在亚洲和澳大利亚区域的 DHL 帐户编号。

> [!NOTE]
> 印度中的数据中心需要使用信头上的声明字母（传递 challan）来返回驱动器。 若要安排所需的条目传递，还必须将所选的承运人预订并共享数据中心。

### <a name="are-there-any-restrictions-with-shipping-and-returning-my-drive-internationally"></a>对于发货情况是否有任何限制并在国际上返还我的驱动器？

请注意，发运的物理介质可能需要穿越国界。 应当负责确保物理介质和数据是遵照适用的法律导入和/或导出的。 在发运物理介质之前，请咨询顾问以验证介质和数据是否可以合法地发运到所确定的数据中心。 这会有助于确保它可以及时到达 Microsoft。

上传完成后，将驱动器返回到国际地址的过程可能需要的时间比本地发货所需的典型2-3 天要长。 在 Azure 门户中列出为打包的阶段期间，Data Box 团队确保提供了正确的文档，以确保发货符合各种国际进口和出口要求。

### <a name="are-there-any-special-requirements-for-delivering-my-disks-to-a-datacenter"></a>将磁盘传送到数据中心是否有任何特殊要求？

要求取决于特定的 Azure 数据中心限制。

- 有一些站点（如澳大利亚、德国和英国南部）需要出于安全原因在包裹上写入 Microsoft datacenter Inbound ID 号。 将驱动器或磁盘寄送到数据中心之前，请联系 Azure DataBox Operations （ adbops@microsoft.com ）获取此号码。 如果没有此编号，将拒绝包。
- 印度数据中心需要驱动程序的个人详细信息，例如政府 ID 卡或证明不需要。 （例如，PAN、AADHAR、DL）、name、contact 和 car 盘子编号，用于获取入口经历。 若要避免交付延迟，请向您的运营商通知这些要求。

### <a name="when-creating-a-job-the-shipping-address-is-a-location-that-is-different-from-my-storage-account-location-what-should-i-do"></a>创建作业时，寄送地址是一个不同于存储帐户位置的位置。   应采取何种操作？

某些存储帐户位置映射到备用寄送位置。 此前可用的寄送位置也可临时映射到备用位置。 在寄送驱动器之前，请始终查看在创建作业过程中提供的寄送地址。

### <a name="when-shipping-my-drive-the-carrier-asks-for-the-data-center-contact-address-and-phone-number-what-should-i-provide"></a>寄送我的驱动器时，承运商要求我提供数据中心的联系地址和电话号码。 我该如何提供？

电话号码和数据中心地址作为作业创建的一部分提供。

## <a name="miscellaneous"></a>杂项

### <a name="what-happens-if-i-accidentally-send-an-hdd-that-does-not-conform-to-the-supported-requirements"></a>如果我无意中发送了不符合支持的要求的 HDD，会发生什么情况？

Azure 数据中心会将不符合支持要求的驱动器返还给你。 如果包裹中只有某些驱动器满足支持要求，将处理这些驱动器，并且不符合支持的要求的驱动器将返还给你。

### <a name="does-the-service-format-the-drives-before-returning-them"></a>该服务是否会在返还驱动器之前将其格式化？

不是。 所有驱动器都使用 BitLocker 加密。

### <a name="how-can-i-access-data-that-is-imported-by-this-service"></a>如何才能访问此服务导入的数据？

使用 Azure 门户或[存储资源管理器](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)访问 Azure 存储帐户下的数据。  

### <a name="after-the-import-is-complete-what-does-my-data-look-like-in-the-storage-account-is-my-directory-hierarchy-preserved"></a>导入完成后，我的数据在存储帐户中看起来是什么样的？ 是否会保留我的目录层次结构？

为导入作业准备硬盘驱动器时，目标由数据集 CSV 文件中的 DstBlobPathOrPrefix 字段指定。 该目标是存储帐户中的目标容器，可以将硬盘驱动器中的数据复制到其中。 在该目标容器中，将为硬盘驱动器中的文件夹创建虚拟目录，为文件创建 Blob。

### <a name="if-a-drive-has-files-that-already-exist-in-my-storage-account-does-the-service-overwrite-existing-blobs-or-files"></a>如果驱动器的文件已存在于我的存储帐户中，该服务是否会覆盖现有 Blob 或文件？

视情况而定。 准备驱动器时，可以使用数据集 CSV 文件中名为 /Disposition:<rename|no-overwrite|overwrite> 的字段指定是否应覆盖或忽略目标文件。 默认情况下，该服务会将新文件重命名，而不是覆盖现有 Blob 或文件。

### <a name="is-the-waimportexport-tool-compatible-with-32-bit-operating-systems"></a>WAImportExport 工具是否与 32 位操作系统兼容？

不是。 WAImportExport 工具仅兼容 64 位 Windows 操作系统。 有关受支持的 OS 的完整列表，请转到[受支持的操作系统](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements)。

### <a name="what-is-the-maximum-block-blob-and-page-blob-size-supported-by-azure-importexport"></a>Azure 导入/导出支持的最大块 Blob 和页 Blob 大小是多少？

- 最大块 Blob 大小大约为 4.768TB 或 5,000,000 MB。
- 最大页 Blob 大小为 8 TB。

### <a name="does-azure-importexport-support-aes-256-encryption"></a>Azure 导入/导出是否支持 AES-256 加密？

是的。 Azure 导入/导出服务使用 AES-256 BitLocker 加密。

## <a name="next-steps"></a>后续步骤

* [什么是 Azure 导入/导出？](storage-import-export-service.md)
