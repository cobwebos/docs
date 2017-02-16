---
title: "在 Azure 安全中心为存储帐户启用加密 | Microsoft 文档"
description: "本文档演示如何实现 Azure 安全中心建议**为 Azure 存储帐户启用加密**。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 7b5df25f46a2824acb361401ab90c960b8e5978f
ms.openlocfilehash: 5b580183002ae5c42cc08343cea7f659c635d590


---
# <a name="enable-encryption-for-azure-storage-account-in-azure-security-center"></a>在 Azure 安全中心为 Azure 存储帐户启用加密
Azure 安全中心可能会建议你为静态数据启用 Azure 存储服务加密。

存储服务加密 (SSE) 的工作原理是，在将数据写入 Azure 存储时对数据进行加密，以及在检索前对数据进行解密。  SSE 当前仅适用于 Azure Blob 服务，并可用于块 blob、页 blob 和追加 blob。  若要了解详细信息，请参阅[静态数据的存储服务加密](../storage/storage-service-encryption.md)。


> [!Note]
> 启用加密后，仅加密新数据。 存储帐户中的所有现有 blob 保持未加密状态。 若要加密现有 blob，请参阅[存储服务加密常见问题解答](../storage/storage-service-encryption.md#frequently-asked-questions-about-storage-service-encryption-for-data-at-rest)。
>
>

只有 Resource Manager 存储帐户支持存储服务加密。 目前不支持经典存储帐户。 若要了解经典部署模型和 Resource Manager 部署模型，请参阅 [Azure 部署模型](../azure-classic-rm.md)。

> [!NOTE]
> 本文档将使用示例部署介绍该服务。  本文档不是一份分步指南。
>
>

## <a name="implement-the-recommendation"></a>实现该建议
1. 在“建议”边栏选项卡中，选择“为 Azure 存储帐户启用加密”。
   ![为存储帐户启用加密][1]
2. 此时将打开“启用存储加密”边栏选项卡。 此边栏选项卡列出已禁用存储加密的 Azure 存储帐户。 在此示例中，选择“storageacct1”。
   ![启用存储加密][2]
3. 此时将打开 **storageacct1** 的“加密”边栏选项卡。 选择“启用”。
   ![“加密”边栏选项卡][3]
4. 选择“保存”。

现在已为 **storageacct1** 启用存储加密。


## <a name="see-also"></a>另请参阅
本文档演示了如何实现安全中心建议“为 Azure 存储帐户启用加密”。 若要了解有关 Azure 存储服务加密的详细信息，请参阅以下文章：

* [静态数据的 Azure 存储空间服务加密](../storage/storage-service-encryption.md)

若要了解有关安全中心的详细信息，请参阅以下文章：

* [在 Azure 安全中心设置安全策略](security-center-policies.md) - 了解如何配置 Azure 订阅和资源组的安全策略。
* [Azure 安全中心的安全运行状况监视](security-center-monitoring.md) - 了解如何监视 Azure 资源的运行状况。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md) - 了解如何管理和响应安全警报。
* [在 Azure 安全中心管理安全建议](security-center-recommendations.md) - 了解建议如何帮助保护 Azure 资源。
* [Azure 安全中心常见问题](security-center-faq.md) - 查找有关如何使用服务的常见问题。
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/) - 查找关于 Azure 安全性及合规性的博客文章。

<!--Image references-->
[1]: ./media/security-center-enable-encryption-for-storage-account/enable-encryption-for-storage-account.png
[2]: ./media/security-center-enable-encryption-for-storage-account/enable-storage-encryption.png
[3]: ./media/security-center-enable-encryption-for-storage-account/encryption-blade.png



<!--HONumber=Jan17_HO1-->


