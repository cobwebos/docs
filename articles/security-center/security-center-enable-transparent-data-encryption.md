---
title: "启用 Azure 安全中心的透明数据加密 |Microsoft 文档"
description: "本文档演示如何实现 Azure 安全中心建议**启用透明数据加密**。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: e4be8a0e-2118-4ee9-a266-69e52d9f7f8e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.translationtype: Human Translation
ms.sourcegitcommit: f532f1aea9b8c4be6707862be4001099aa3c1d32
ms.openlocfilehash: 2a2963affdbff3710ad08f86c6ed4e6304335559
ms.contentlocale: zh-cn
ms.lasthandoff: 02/04/2017

---
# <a name="enable-transparent-data-encryption-in-azure-security-center"></a>启用 Azure 安全中心的透明数据加密
如果尚未启用 TDE，则 Azure 安全中心建议在 SQL 数据库上启用透明数据加密 (TDE)。 TDE 通过加密静态数据库、关联备份和事务日志文件但无需更改应用程序来保护数据，并帮助满足合规性要求。 信息信息参见[借助 Azure SQL 数据库实现透明数据加密](https://msdn.microsoft.com/library/dn948096)。

此建议仅适用于 Azure SQL 服务；不包括虚拟机上运行的 SQL。

> [!NOTE]
> 本文档将使用示例部署介绍该服务。  这并非一份循序渐进的指南。
>
>

## <a name="implement-the-recommendation"></a>实现该建议
1. 在“建议”边栏选项卡中，选择“启用透明数据加密”。
   ![启用透明数据加密][1]
2. “在 SQL 数据库上启用透明数据加密”边栏选项卡。 选择要启用 TDE 的SQL 数据库。
   ![选择要启用 TDE 的 SQL DB][2]
3. 在“透明数据加密”边栏选项卡上，在数据加密下选择“开启”，然后边栏选项卡顶部功能区中选择“保存”。
   ![打开 TDE][3]

   在所选 SQL 数据库上启用 TDE 后，“加密状态”将更改为“已加密”。    

   ![加密状态][4]

## <a name="see-also"></a>另请参阅
本文演示如何实现安全中心建议“启用透明数据加密”。 若要了解有关 SQL TDE 的详细信息，请参阅以下内容：

* [使用 Azure SQL 数据库的透明数据加密](https://msdn.microsoft.com/library/dn948096)
* [透明数据加密 (TDE) 入门](../sql-data-warehouse/sql-data-warehouse-encryption-tde.md)

若要了解有关安全中心的详细信息，请参阅以下文章：

* [在 Azure 安全中心中设置安全策略](security-center-policies.md)了解如何配置 Azure 订阅和资源组的安全策略。
* [在 Azure 安全中心中管理安全建议](security-center-recommendations.md) -- 了解建议如何帮助保护 Azure 资源。
* [Azure 安全中心的安全性运行状况监视](security-center-monitoring.md) -- 了解如何监视 Azure 资源的运行状况。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md) -- 了解如何管理和响应安全警报。
* [通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md) -- 了解如何监视合作伙伴解决方案的运行状态。
* [Azure 安全中心常见问题解答](security-center-faq.md)查找有关使用服务的常见问题。
* [Azure 安全博客](http://blogs.msdn.com/b/azuresecurity/)获取最新的 Azure 安全新闻和信息。

<!--Image references-->
[1]: ./media/security-center-enable-tde-on-sql-databases/enable-tde.png
[2]:./media/security-center-enable-tde-on-sql-databases/transparent-data-encryption-blade.png
[3]: ./media/security-center-enable-tde-on-sql-databases/turn-on-tde.png
[4]: ./media/security-center-enable-tde-on-sql-databases/encrypted.png

