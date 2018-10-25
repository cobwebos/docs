---
title: 更新 Azure 市场的现有产品 | Microsoft Docs
description: 介绍如何使用云合作伙伴门户更新现有的 Azure 市场套餐。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: bfe2b0c70c8b912f6489ed461f5bcf6f233ed60d
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805544"
---
<a name="update-an-existing-offer-for-azure-marketplace"></a>更新 Azure 市场的现有产品
==============================================

套餐上线后，可对其应用多种类型的更新。   可以借助[云合作伙伴门户](https://cloudpartner.azure.com/)中的多种功能来编辑套餐，以确保更改正确。这些功能包括：

-  将新虚拟机 (VM) 映像版本添加到现有 SKU
-  更改 SKU 的可用区域
-  添加新的 SKU
-  更新套餐或 SKU 的市场元数据 
-  更新即用即付套餐的定价
-  比较功能
-  功能历史记录

修改套餐或 SKU 后，必须先将其重新发布，然后更改才会生效。
本文将提供 VM 产品更新方方面面的分步指导。


<a name="unpermitted-changes-to-vm-offersku"></a>VM 产品/SKU 的未经许可更改
-----------------------------------

套餐在 Azure 市场中上线后，无法修改 VM 套餐或 SKU 的某些属性。

-  产品 ID 和产品发布者 ID。
-  现有 SKU 的 SKU ID。
-  现有 SKU 的数据磁盘计数。
-  现有 SKU 的计费/许可证模型更改。


<a name="updating-the-vm-image-version-for-a-sku"></a>更新 SKU 的 VM 映像版本
---------------------------------------

产品的 SKU 的 VM 映像可能已更新其他功能、安全修补程序等。在这些情况下，可能需要更新 SKU 所引用的 VM 映像。 使用以下步骤更新 VM 映像。 

1.  登录到[云合作伙伴门户](https://cloudpartner.azure.com/)。
2.  在“所有套餐”下，找到要更新的套餐。
3.  在“SKU”窗体下，单击要更新的 VM 映像的 SKU。
4.  在“磁盘版本”下，单击“+新建磁盘版本”以添加新的 VM 映像。
5.  提供新的 VM 映像**磁盘版本**。 此磁盘版本需遵循[语义版本](http://semver.org/)格式。 版本格式应为 X.Y.Z，X、Y 和 Z 是整数。 确保提供的新版本高于先前版本，否则重新发布时，新版本号不会显示在 Azure 门户或 Azure 市场中。
6.  对于“OS VHD URL”，请输入为操作系统 VHD 创建的共享访问签名 (SAS) URI。 请注意，不同 SKU 版本间无法更改数据磁盘计数。 如果先前版本配置了数据磁盘，则此新版本也必须配置数据磁盘。
7.  单击“发布”开始发布工作流，在 Azure 应用市场和 Azure 门户中推出新的 VM 版本。


<a name="changing-regions-a-sku-is-available-in"></a>更改 SKU 的可用区域
--------------------------------------

以后可能需要在更多区域提供产品/SKU。
或者，可能需要在特定区域停止支持产品/SKU。
若要实施这些更改，请执行以下步骤。

1.  登录到[云合作伙伴门户](https://cloudpartner.azure.com/)。
2.  在“所有套餐”下，找到要更新的套餐。
3.  在“SKU”窗体下，单击要更新其可用区域的 SKU。
4.  单击“国家/地区可用性”字段下的“选择国家/地区”按钮。
5.  在区域可用性弹出菜单中，添加/删除要为此 SKU 选择的区域。
6.  单击“发布”开始发布工作流，以更新 SKU 的可用区域。

如果要在新区域中提供 SKU，可通过“导出定价数据”功能对此特定区域指定定价。 如果加回以前可用的区域，则无法更新其定价，因为不允许更改定价。


<a name="adding-a-new-sku"></a>添加新的 SKU
----------------

若要使新 SKU 可用于现有套餐，请执行以下步骤。

1.  登录到[云合作伙伴门户](https://cloudpartner.azure.com/)。
2.  在“所有套餐”下，找到要更新的套餐。
3.  在“SKU”窗体下，单击“添加新 SKU”并在弹出菜单中提供一个 SKU ID。
4.  遵循[将虚拟机发布到 Azure 市场](./cloud-partner-portal-publish-virtual-machine.md)中详述的剩余步骤。
5.  单击“发布”开始发布工作流，推出新的 SKU。


<a name="updating-offer-marketplace-metadata"></a>更新产品市场元数据。
------------------------------------

在一些情况下，需要更新与产品关联的市场元数据，例如更新公司徽标等。使用以下步骤更新套餐的元数据。

1.  登录到[云合作伙伴门户](https://cloudpartner.azure.com/)。
2.  在“所有套餐”下，找到要更新的套餐。
3.  转到“市场”窗体，并遵照此处的说明进行更改。
4.  单击“发布”开始发布工作流，使更改生效。


<a name="updating-pricing-on-published-offers"></a>更新已发布套餐的定价
------------------------------------

发布即用即付套餐后，无法提高现有 SKU 的价格，但可以在同一套餐下创建新的 SKU，删除旧 SKU，然后重新发布套餐。 我们还允许降低已发布套餐的价格。 降低套餐价格：

1.  单击要降低其定价的 SKU。
2.  如果在 1x1 GUI 中设置了定价，可以直接在该 UI 中更改价格。 如果通过导入/导出电子表格设置了定价，则只能通过导入/导出功能降低价格。
3.  单击“ **保存**”。
4.  重新发布套餐并上线。

套餐完全在网站中上线后，新客户会看到定价，所有新客户将按已降低的新价格付费。

在降价生效期间，计费周期开始时，将以回溯的方式向现有客户反映降价情况。
如果在降价时所处的周期内已对他们计费，则在下一个计费周期，他们会收到一笔退款，以冲抵降价。


<a name="simplified-currency-pricing"></a>简化的货币定价
---------------------------

从 2018 年 9 月开始，将会显示一个名为“简化的货币定价”的新部分。 Microsoft 正在针对全球客户实现更多的可预测定价和托收方案，以简化 Azure 市场业务。 这种简化将会通过减少客户发票所采用的货币数量来实现。

新部分将以这些新货币定价。 将所有客户迁移到这些新的结算货币后，原始定价部分将会停用，只保留“简化的货币定价”部分。

必须在 2018 年 11 月 1 之前，为要改变结算货币的区域设置新价格。 无法为结算货币不会改变的区域提高价格。 下面是将会改变货币的区域：

| 国家/地区                  | ISO2 代码 | 简化的计费货币 |
|--------------------------|-----------|-----------------------------|
| **阿尔及利亚**              | DZ        | USD                         |
| **阿根廷**            | AR        | USD                         |
| **巴林**              | BH        | USD                         |
| **白俄罗斯**              | BY        | USD                         |
| **巴西**               | BR        | BRL                         |
| **保加利亚**             | BG        | EUR                         |
| **智利**                | CL        | USD                         |
| **哥伦比亚**             | CO        | USD                         |
| **哥斯达黎加**           | CR        | USD                         |
| **克罗地亚**              | HR        | EUR                         |
| **捷克共和国**       | CZ        | EUR                         |
| **埃及**                | EG        | USD                         |
| **危地马拉**            | GT        | USD                         |
| **香港**            | HK        | USD                         |
| **匈牙利**              | HU        | EUR                         |
| **冰岛**              | IS        | EUR                         |
| **印度尼西亚**            | ID        | USD                         |
| **以色列**               | IL        | USD                         |
| **约旦**               | JO        | USD                         |
| **哈萨克斯坦**           | KZ        | USD                         |
| **肯尼亚**                | KE        | USD                         |
| **科威特**               | KW        | USD                         |
| **列支敦士登**        | LI        | EUR                         |
| **马其顿 (FYRO)**     | MK        | USD                         |
| **马来西亚**             | MY        | USD                         |
| **墨西哥**               | MX        | USD                         |
| **黑山**           | ME        | USD                         |
| **摩洛哥**              | MA        | USD                         |
| **尼日利亚**              | NG        | USD                         |
| **阿曼**                 | OM        | USD                         |
| **巴基斯坦**             | PK        | USD                         |
| **巴拉圭**             | PY        | USD                         |
| **秘鲁**                 | PE        | USD                         |
| **菲律宾**          | PH        | USD                         |
| **波兰\***             | PL        | EUR                         |
| **卡塔尔**                | QA        | USD                         |
| **罗马尼亚**              | RO        | EUR                         |
| **沙特阿拉伯**         | SA        | USD                         |
| **塞尔维亚**               | RS        | USD                         |
| **新加坡**            | SG        | USD                         |
| **南非**         | ZA        | USD                         |
| **泰国**             | TH        | USD                         |
| **特立尼达和多巴哥**  | TT        | USD                         |
| **突尼斯**              | TN        | USD                         |
| **土耳其**               | TR        | USD                         |
| **乌克兰**              | UA        | USD                         |
| **阿拉伯联合酋长国** | AE        | USD                         |
| **乌拉圭**              | UY        | USD                         |
|  |  |  |

> [!NOTE]
> 如果使用 API 发布套餐，可在套餐中看到一个名为 **JSON** 的新部分。 根据套餐的类型，此部分带有 `virtualMachinePricingV2` 或 `monthlyPricingV2` 批注。

如果对此项更改有任何疑问，请联系 Azure 市场支持人员。


<a name="compare-feature"></a>“比较”功能
---------------

对已发布的套餐进行更改时，可以使用“比较”功能来审核所做的更改。 利用“比较”：

1.  在编辑过程中的任意时间点，可以单击套餐旁边的“比较”按钮。

2.  并列查看营销资产和元数据的版本。


<a name="history-of-publishing-actions"></a>发布操作的历史记录
-----------------------------

若要查看任何历史发布活动，请单击云合作伙伴门户左侧导航栏中的“历史记录”选项卡。 在此处可以查看 Azure 市场套餐生存期内执行的带时间戳的操作。
