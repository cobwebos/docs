---
title: "请求增加 DocumentDB 帐户配额 | Microsoft Docs"
description: "了解如何请求对 DocumentDB 数据库配额（如文档存储空间和每个集合的吞吐量）的调整。"
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 68f7dc8d-534f-4301-a42c-bcd1bb1b77fe
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: d5af6e3d9ba35558e0d7850820f4b9a479a259be
ms.openlocfilehash: 7c73de46500ac565680f2d16da05b4821507cd93


---
# <a name="request-increased-documentdb-account-quotas"></a>请求增加 DocumentDB 帐户配额
[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) 有一组默认配额，可以通过联系 Azure 支持部门加以调整。  本文介绍了如何请求增加配额。

阅读本文之后，你将能够回答以下问题：  

* 可联系 Azure 支持部门来调整哪些 DocumentDB 数据库配额？
* 我如何请求对 DocumentDB 帐户配额进行调整？

## <a name="a-idquotasa-documentdb-account-quotas"></a><a id="Quotas"></a> DocumentDB 帐户配额
下表描述了 DocumentDB 配额。 可联系 Azure 支持部门调整带星号 (*) 的配额：

[!INCLUDE [azure-documentdb-limits](../../includes/azure-documentdb-limits.md)]

## <a name="a-idrequestquotaincreasea-request-a-quota-adjustment"></a><a id="RequestQuotaIncrease"></a> 请求配额调整
以下步骤演示了如何请求配额调整。

1. 在 [Azure 门户](https://portal.azure.com)中，依次单击“更多服务”、“帮助 + 支持”。
   
    ![启动帮助与支持的屏幕截图](media/documentdb-increase-limits/helpsupport.png)
2. 在“帮助 + 支持”边栏选项卡上，单击“新建支持请求”。
   
    ![创建支持票证的屏幕截图](media/documentdb-increase-limits/getsupport.png)
3. 在“新支持请求”边栏选项卡上，单击“基本”。 接下来，将“问题类型”设置为“配额”，将“订阅”设置为托管 DocumentDB 帐户的订阅，将“配额类型”设置为“DocumentDB”，并将“支持计划”设置为“配额支持 - 包括”。 然后单击“下一步”。
   
    ![支持票证请求类型的屏幕截图](media/documentdb-increase-limits/supportrequest1.png)
4. 在“问题”边栏选项卡中，选择严重级别，并在“详细信息”中包含关于配额增加的信息。 单击“资源组名称” 的 Azure 数据工厂。
   
    ![支持票证订阅选取器的屏幕截图](media/documentdb-increase-limits/supportrequest2.png)
5. 最后，在“联系信息”边栏选项卡中填写你的联系信息，然后单击“创建”。

创建支持票证后，你会通过电子邮件收到支持请求编号。  还可以通过单击“帮助 + 支持”边栏选项卡上的“管理支持请求”来查看支持请求。

![支持请求边栏选项卡的屏幕截图](media/documentdb-increase-limits/supportrequest4.png)

## <a name="a-namenextstepsa-next-steps"></a><a name="NextSteps"></a>后续步骤
* 若要了解有关 DocumentDB 的详细信息，请单击[此处](http://azure.com/docdb)。




<!--HONumber=Nov16_HO3-->


