---
title: "ClearDB MySql 数据库与 Azure 应用服务搭配使用时的常见问题解答 | Microsoft Docs"
description: "解答将 ClearDB MySQL 数据库与 Azure 应用服务搭配使用时的常见问题。"
documentationcenter: php
services: 
author: sunbuild
manager: yochayk
editor: 
tags: mysql
ms.assetid: c2ed5e78-6d7d-4d0c-b7ee-a52ae41ceab8
ms.service: multiple
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2016
ms.author: sumuth
ms.openlocfilehash: b0fad1d151799f88c72cfe1359978d69cc1421c2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="faq-for-cleardb-mysql-databases-with-azure-app-service"></a>ClearDB MySql 数据库搭配 Azure 应用服务的 FAQ
此 FAQ 解答了为 Azure Web 应用使用和购买 ClearDB MySQL 数据库的常见问题。

## <a name="what-options-do-i-have-for-mysql-on-azure"></a>Azure 上的 MySQL 有哪些选项？
有几种选项：

* [ClearDB 共享 MySQL 数据库](/marketplace/partners/cleardb/databases/)
* [ClearDB MySQL Premium 群集](/marketplace/partners/cleardb-clusters/cluster/)
* [Azure VM 上运行的 MySQL 群集](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)
* [Azure VM 上运行的单个 MySQL 实例](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

ClearDB 是一种 MySQL 托管服务，可管理 MySQL 基础结构。 在 Azure 虚拟机上运行自己的 MySQL 群集或数据库时，必须设置 MySQL 服务器，并使用补丁使其保持更新。

## <a name="do-i-need-a-credit-card-for-the-web-app--mysql-template-in-the-azure-marketplace"></a>要获取 Azure 应用商店中的 Web 应用和 MySQL 模板需要使用信用卡吗？
这取决于所使用订阅的类型。 下面是一些常用订阅类型：

* [即用即付](/offers/ms-azr-0003p/)：需要信用卡，购买付费的 MySQL 数据库时会向信用卡收费。
* [免费试用版](https://azure.microsoft.com/pricing/free-trial/)：包括可用于 Microsoft Azure 服务的信用额度，但不允许购买第三方资源。 若要购买第三方服务或付费的 MySQL 数据库，需使用支持信用卡的订阅。 对于 Web 应用，可以创建免费的 ClearDB MySQL 数据库。
* [MSDN 订阅](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/)和 **MSDN 即用即付开发/测试**：类似于免费试用版，MSDN 订阅需要用户使用信用卡从 ClearDB 购买付费的 MySQL 解决方案。
* [企业协议 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)：每季以单独的合并发票按照 EA 向客户从 Azure 应用商店（第三方）购买的所有商品收费。 针对任何应用商店购买，以货币承诺付款以外的方式收费。 请注意，Azure 应用商店目前无法供在阿塞拜疆、克罗地亚、挪威和波多黎各注册的客户使用。 
* [DreamSpark](https://www.dreamspark.com/Product/Product.aspx?productid=99)：只能为 Web 应用创建免费的 ClearDB 数据库。 可以创建的免费 ClearDB MySQL 数据库数目没有任何限制。 请注意，免费数据库不适用于生产 Web 应用，因为此服务仅供试用。

## <a name="why-was-i-charged-350-for-a-web-app--mysql-from-the-azure-marketplace"></a>为什么我要为 Azure 应用商店中的 Web 应用和 MySQL 付 3.50 美元的费用？
默认数据库选项是 Titan，需 3.50 美元。 创建数据库时我们不会显示成本，可能会错误地购买不想要的数据库。 我们正在设法改善此体验，但在那之前，需要先检查为 Web 应用和数据库选择的定价层，然后再单击“创建”并开始部署资源。

## <a name="i-am-running-mysql-on-my-own-azure-virtual-machine-can-i-connect-my-azure-web-app-to-my-database"></a>我在自己的 Azure 虚拟机上运行 MySQL。 我是否可以将 Azure Web 应用连接到数据库？
是的。 只要 Azure VM 已向 Web 应用授予远程访问权限，Web 应用就能连接到数据库。 有关详细信息，请参阅[在虚拟机上安装 MySQL](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。

## <a name="in-which-countries-are-cleardb-premium-mysql-clusters-supported"></a>支持 ClearDB Premium MySQL 群集的国家/地区有哪些？
除印度、澳大利亚、巴西南部和中国之外的所有 Azure 区域都可以使用 [ClearDB Premium MySQL 群集](/marketplace/partners/cleardb-clusters/cluster/)。

## <a name="can-i-create-a-new-cluster-prior-to-creating-a-database-with-cleardb-premium-cluster-solution"></a>是否可以在创建数据库之前使用 ClearDB Premium 群集解决方案创建新群集？
否，不支持创建空的 ClearDB 群集。 通过 Azure 门户，可以在群集中创建数据库，同时可能会创建新的群集。

## <a name="will-i-be-warned-if-i-try-to-delete-a-cleardb-mysql-database-that-is-in-use-by-one-of-my-applications"></a>如果我尝试删除正由我的某个应用程序使用的 ClearDB MySQL 数据库，会收到警告吗？
不会，如果删除应用程序所依赖的应用商店购买商品，Azure 不会发出警告。

## <a name="which-regions-can-i-create-cleardb-databases-in"></a>可以在哪些区域创建 ClearDB 数据库？
Azure 应用商店无法供在阿塞拜疆、克罗地亚、挪威或波多黎各注册的客户使用。 这些区域不提供 ClearDB。

## <a name="what-pricing-tier-should-i-choose-for-a-production-web-app-and-database"></a>针对生产 Web 应用和数据库，应该选择哪个定价层？
对 Web 应用使用“基本”或更高的定价层。 对于 ClearDB，建议使用 Saturn 或 Jupiter 计划。 请查看 [Web 应用](https://azure.microsoft.com/pricing/details/app-service/)和 [ClearDB MySQL 数据库](/marketplace/partners/cleardb/databases/)每个定价层的功能和限制，以选择符合需要的定价层。

## <a name="how-do-i-upgrade-my-cleardb-database-from-one-plan-to-another"></a>如何将 ClearDB 数据库从一个计划升级到另一个计划？
在 [Azure 门户](https://portal.azure.com)中，可以扩大 ClearDB 共享的托管数据库。 请阅读本篇[文章](https://blogs.msdn.microsoft.com/appserviceteam/2016/10/06/upgrade-your-cleardb-mysql-database-in-azure-portal/)，了解详细信息。 当前不支持在 Azure 门户中升级 ClearDB 高级群集。

## <a name="i-cant-see-my-cleardb-database-in-azure-portal"></a>在 Azure 门户中看不到我的 ClearDB 数据库？
如果使用 Azure Resource Manager 或[新 Azure 门户](https://portal.azure.com)创建 ClearDB 数据库，将无法在[旧 Azure 门户](https://manage.windowsazure.com)中看到此数据库。 要解决此问题，请将数据库手动链接到 Web 应用。 同样，如果在[旧门户](https://manage.windowsazure.com)中创建 ClearDB 数据库，则无法在[新 Azure 门户](https://portal.azure.com)中看到此数据库。 对于后一种情况，没有任何解决方法。

## <a name="who-do-i-contact-for-support-when-my-database-is-down"></a>数据库关闭时应联系谁寻求支持？
如有任何数据库相关问题，请联系 [ClearDB 支持人员](https://www.cleardb.com/developers/help/support)。 准备好向其提供 Azure 订阅信息。

## <a name="can-i-create-additional-users-for-my-cleardb-mysql-database-cluster-solution"></a>我是否可以为自己的 ClearDB MySQL 数据库群集解决方案创建其他用户？
不能。 无法创建其他用户，但可以在自己的 ClearDB 数据库群集上创建其他数据库。  

## <a name="can-basicpro-series-databases-be-upgraded-in-place-similar-to-planetary-plans-today-on-cleardb-portal"></a>是否可以像 ClearDB 门户上目前的 Planetary 计划一样，就地升级 Basic/Pro 系列数据库？
是，可以就地升级 Basic 系列数据库（Basic 60 到 Basic 500）。 可以就地升级 Pro 系列（Pro 125 到 Pro 1000），但 Pro 60 除外。 我们目前不支持升级 Pro 60 数据库。 

## <a name="when-i-migrate-my-resources-from-one-subscription-to-another-does-my-cleardb-mysql-database-get-migrated-as-well"></a>当我将资源从一个订阅迁移到另一个订阅时，我的 ClearDB MySQL 数据库也会跟着迁移吗？
在订阅间迁移资源时，存在某些[限制](app-service/app-service-move-resources.md)。 ClearDB MySQL 数据库是第三方服务，因而在 Azure 订阅迁移期间不会被迁移。 如果在迁移 Azure 资源之前，没有管理 MySQL 数据库的迁移，ClearDB MySQL 数据库可能会被禁用。 首先，手动迁移数据库，再对 Web 应用程序执行 Azure 订阅迁移。 

## <a name="i-hit-the-spending-limit-on-my-subscription-i-removed-the-limit-and-my-app-service-is-online-however-the-database-is-not-accessible-how-do-i-re-enable-the-cleardb-database"></a>我单击了订阅中的支出限制。 我删除了该限制，而且我的应用服务处于联机状态，但数据库却无法访问。 我该如何重新启用 ClearDB 数据库？
请联系 [ClearDB 支持部门](https://www.cleardb.com/developers/help/support)以重新启用该数据库。 向他们提供 Azure 订阅信息和数据库名称。

## <a name="can-i-transfer-a-cleardb-database-from-a-credit-card-subscription-to-an-ea-subscription"></a>可以将 ClearDB 数据库从信用卡订阅转换为 EA 订阅吗？
现有 ClearDB 数据库使用与现有订阅关联的信用卡。 要使用 EA 订阅，需要将数据迁移到新数据库：

* 使用 EA 订阅购买新的 ClearDB 数据库。
* 将数据迁移至新数据库。
* 更新应用程序以使用新数据库。
* 删除旧的 ClearDB 数据库。

使用 MySQL (ClearDB) 创建新 Web 应用或创建 MySQL 数据库 (ClearDB) 时，所选订阅决定服务的支付方式。 对于 EA 订阅，不会阻止用户在 Azure 门户采购 ClearDB 等第三方服务。 EA 订阅不按货币承诺付款计费，会在每季扣取相关费用和逾期欠款。 EA 客户必须设置付款方式，例如信用卡，才能支付任何第三方应用商店服务。

## <a name="where-can-i-see-the-charges-for-cleardb-resources-in-an-ea-subscription"></a>在何处可查看 EA 订阅中 ClearDB 资源的计费情况？
直接 EA 客户可以在企业门户上看到 Azure 应用商店费用。 请注意，所有应用商店购买和使用均会在每季季末就货币承诺付款以外收费。 EA 客户需要直接付费给第三方服务提供商，或者通过启用其 EA 帐户的付款方式（例如信用卡）完成付费。

间接 EA 客户可以在企业门户的“管理订阅”页上找到其 Azure 应用商店订阅，但定价处于隐藏状态。 客户应该联系其 LSP 以了解应用商店费用的相关信息。

EA Azure 注册管理员可以管理 Azure 应用商店第三方服务的访问权限。 在企业门户中，管理员可在“帐户”部分下的“管理帐户和订阅”中禁用或重新启用对应用商店内所购第三方服务的访问权限。

## <a name="who-do-i-contact-for-questions-about-my-bill-for-cleardb-services-in-my-ea-subscription"></a>有关 EA 订阅中的 ClearDB 服务帐单问题，应联系谁？
有关根据 EA 注册计费的问题，请联系[企业客户支持人员](http://aka.ms/AzureEntSupport)。 EA 门户支持团队会回答问题或帮你解决问题。

## <a name="more-information"></a>详细信息
[Azure 应用商店常见问题解答](/marketplace/faq/)

