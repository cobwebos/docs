---
title: 创建 Azure HPC 缓存
description: 如何创建 Azure HPC 缓存实例
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: befbe2435a518b82cf5a3ab12e6129aa3ce5c22b
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537962"
---
# <a name="create-an-azure-hpc-cache"></a>创建 Azure HPC 缓存

使用 Azure 门户创建缓存。

![Azure 门户中的缓存概述的屏幕截图，底部带有“创建”按钮](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>定义基本详细信息

![Azure 门户中的“项目详细信息”页的屏幕截图](media/hpc-cache-create-basics.png)

在“项目详细信息”**** 中，选择将托管缓存的订阅和资源组。

在“服务详细信息”**** 中，设置缓存名称和其他属性：

* 位置 - 选择其中一个[支持的区域](hpc-cache-overview.md#region-availability)。
* 虚拟网络 - 可选择现有虚拟网络或创建新的虚拟网络。
* 子网 - 选择或创建至少包含 64 个 IP 地址 (/24) 的子网，它将仅用于此 Azure HPC 缓存实例。

## <a name="set-cache-capacity"></a>设置缓存容量
<!-- referenced from GUI - update aka.ms link if you change this header text -->

在“缓存”**** 页上，必须设置缓存的容量。 此处设置的值确定缓存可以容纳多少数据，以及为客户端请求提供服务的速度。

容量还会影响缓存的成本。

通过设置以下两个值选择容量：

* 缓存的最大数据传输速率（吞吐量）（GB/秒）
* 为缓存数据分配的存储量 (TB)

选择可用吞吐量值和缓存存储大小之一。

请记住，实际的数据传输速率取决于工作负荷、网络速度和存储目标的类型。 你选择的值设置整个缓存系统的最大吞吐量，但其中一些用于开销任务。 例如，如果客户端请求尚未存储在缓存中的文件，或者该文件被标记为过期，则缓存将使用其部分吞吐量从后端存储中提取该文件。

Azure HPC 缓存会管理缓存和预加载哪些文件，以最大限度地提高缓存命中率。 当不经常访问缓存内容时，会不断评估缓存内容，并将文件移动到长期存储。 选择一种缓存存储大小，该大小可以轻松地保留活动的工作文件集，并具有额外的空间来存储元数据和其他开销。

![缓存大小调整页的屏幕截图](media/hpc-cache-create-capacity.png)

## <a name="enable-azure-key-vault-encryption-optional"></a>启用 Azure 密钥保管库加密（可选）

如果您的缓存位于支持客户管理的加密密钥的区域，**则"磁盘加密密钥"** 页将显示在 **"缓存**"和 **"标记"** 选项卡之间。 截至发布时，此选项在美国东部、美国中南部和美国西部 2 中支持。

如果要管理与缓存存储一起使用的加密密钥，请在 **"磁盘加密密钥"** 页上提供 Azure 密钥保管库信息。 密钥保管库必须位于与缓存相同的区域和订阅中。

如果您不需要客户管理的密钥，则可以跳过此部分。 默认情况下，Azure 使用 Microsoft 管理的密钥加密数据。 阅读[Azure 存储加密](../storage/common/storage-service-encryption.md)以了解更多信息。

> [!NOTE]
>
> * 创建缓存后，无法在 Microsoft 管理的密钥和客户管理的密钥之间进行更改。
> * 创建缓存后，必须授权它访问密钥保管库。 单击缓存 **"概述"** 页中的 **"启用加密**"按钮以打开加密。 在创建缓存后的 90 分钟内执行此步骤。
> * 缓存磁盘是在此授权后创建的。 这意味着初始缓存创建时间较短，但在您授权访问后，缓存将无法使用 10 分钟或更长时间。

有关客户管理密钥加密过程的完整说明，请改为[使用 Azure HPC 缓存的客户托管加密密钥](customer-keys.md)。

![加密密钥页的屏幕截图，其中选择了"客户托管"，并显示密钥保管库字段](media/create-encryption.png)

选择**客户管理以**选择客户管理密钥加密。 将显示密钥保管库规范字段。 选择要使用的 Azure 密钥保管库，然后选择要用于此缓存的密钥和版本。 密钥必须为 2048 位 RSA 密钥。 可以从此页面创建新的密钥保管库、密钥或密钥版本。

创建缓存后，必须授权它使用密钥保管库服务。 请从[缓存中读取授权 Azure 密钥保管库加密](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache)，了解详细信息。

## <a name="add-resource-tags-optional"></a>添加资源标记（可选）

在“标记”**** 页上，可以向 Azure HPC 缓存实例添加[资源标记](https://go.microsoft.com/fwlink/?linkid=873112)。

## <a name="finish-creating-the-cache"></a>完成缓存创建

配置新缓存后，单击"审阅 **+ 创建**"选项卡。门户验证您的选择，并允许您查看您的选择。 如果所有内容都正确，请单击“创建”****。

缓存创建花费大约 10 分钟。 可在 Azure 门户的通知面板中跟踪进度。

![门户中缓存创建“正在部署”和“通知”页的屏幕截图](media/hpc-cache-deploy-status.png)

创建完成后，将显示一条通知，其中包含指向新的 Azure HPC 缓存实例的链接，并且缓存将显示在订阅的**资源**列表中。

![Azure 门户中的 Azure HPC 缓存实例的屏幕截图](media/hpc-cache-new-overview.png)

> [!NOTE]
> 如果缓存使用客户管理的加密密钥，则在部署状态更改完成之前，缓存可能会显示在资源列表中。 一旦缓存的状态等待**密钥，** 您可以[授权它](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache)使用密钥保管库。

## <a name="next-steps"></a>后续步骤

缓存显示在 **"资源"** 列表中后，可以移动到下一步。

* [定义存储目标](hpc-cache-add-storage.md)，以便授予缓存对数据源的访问权限。
* 如果使用客户管理的加密密钥，则需要从缓存的概述页[授权 Azure 密钥保管库加密](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache)以完成缓存设置。 必须先执行此步骤，然后才能添加存储。 阅读[使用客户管理的加密密钥](customer-keys.md)了解详细信息。
