---
title: 启用和创建大型文件共享-Azure 文件
description: 本文介绍如何启用和创建大型文件共享。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 00db5905c008644bc21704179363849756fa7dcc
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518416"
---
# <a name="how-to-enable-and-create-large-file-shares"></a>如何启用和创建大型文件共享

最初，标准文件共享最多只能扩展到5个 TiB，而对于大型文件共享，它们最多可以扩展到 100 TiB。 默认情况下，高级文件共享最多可扩展到 100 TiB。 

若要使用标准文件共享来扩展到 100 TiB，必须启用存储帐户才能使用大型文件共享。 可以启用现有帐户，也可以创建新帐户以使用大型文件共享。

## <a name="prerequisites"></a>必备组件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。

## <a name="restrictions"></a>限制

启用了大文件共享的帐户支持 LRS 或 ZRS。 目前，启用了大型文件共享的帐户不支持 GZRS、GRS 或 GRS。 在一个帐户上启用大型文件共享是一种不可逆的过程，一旦启用，你的帐户将无法转换为 GZRS、GRS 或 GRS。

## <a name="create-a-new-storage-account"></a>新建存储帐户

登录到 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 门户中，选择“所有服务”。 在资源列表中，键入“存储帐户”。 开始键入时，会根据输入筛选该列表。 选择“存储帐户”。
1. 在显示的“存储帐户”窗口中，选择“添加”。
1. 选择要在其中创建存储帐户的订阅。
1. 在“资源组”字段下，选择“新建”。 输入新资源组的名称，如下图中所示。

    ![显示如何在门户中创建资源组的屏幕截图](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. 然后，输入存储帐户的名称。 所选名称在 Azure 中必须唯一。 该名称还必须为 3 到 24 个字符，并且只能包含数字和小写字母。
1. 选择存储帐户的位置，确保它是[LFS 支持的区域之一](storage-files-planning.md#regional-availability)。
1. 将复制设置为**本地冗余存储**或**区域冗余存储**。
1. 将这些字段设置为其默认值：

   |字段  |Value  |
   |---------|---------|
   |部署模型     |资源管理器         |
   |性能     |标准版         |
   |帐户类型     |StorageV2（常规用途 v2）         |
   |访问层     |热         |

1. 选择 "**高级**" 并选择 "为**大文件共享** **启用**"。
1. 选择“查看+创建”可查看存储帐户设置并创建帐户。

    ![large-file-shares-advanced-enable .png](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. 选择**创建**。

## <a name="enable-on-existing-account"></a>对现有帐户启用

你还可以在现有帐户上启用大型文件共享。 如果执行此操作，则该帐户将无法再转换为 GZRS、GRS 或 GRS。 此帐户不可逆。

1. 打开[Azure 门户](https://portal.azure.com)，导航到要在其上启用大型文件共享的存储帐户。
1. 打开存储帐户并选择 "**配置**"。
1. 在**大文件共享**上选择 "**启用**"，然后选择 "保存"。
1. 选择 "**概述**"，然后选择 "**刷新**"。

![enable-large-file-shares-on-existing .png](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

你现在已在存储帐户上启用了大型文件共享。

如果收到以下错误： "目前还不能使用该帐户的大型文件共享"。 您可以等待一段时间，因为您的区域可能在完成推出过程中，或者，如果您有紧急需求，请联系支持人员。

## <a name="create-a-large-file-share"></a>创建大文件共享

创建较大的文件共享与创建标准文件共享几乎完全相同。 主要区别在于，最多可将配额设置为 100 TiB。

1. 从存储帐户中，选择 "**文件共享**"。
1. 选择“+ 文件共享”。
1. 输入文件共享的名称和（可选）所需的配额大小，最多 100 TiB，然后选择 "**创建**"。 

![large-file-shares-create-share .png](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

## <a name="expand-existing-file-shares"></a>展开现有文件共享

在存储帐户上启用大型文件共享后，可以将现有共享扩展到较高的配额。

1. 从存储帐户中，选择 "**文件共享**"。
1. 右键单击文件共享，然后选择 "**配额**"。
1. 输入所需的新大小，然后选择 **"确定"** 。

![update-large-file-share-quota .png](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

## <a name="next-steps"></a>后续步骤

* [连接并装载文件共享 - Windows](storage-how-to-use-files-windows.md)
* [连接并装载文件共享 - Linux](../storage-how-to-use-files-linux.md)
* [连接并装载文件共享 - macOS](storage-how-to-use-files-mac.md)