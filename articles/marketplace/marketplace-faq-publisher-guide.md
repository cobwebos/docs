---
title: 微软商业市场常见问题解答
description: 有关 Azure 应用商店和微软应用程序源的常见问题的解答
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/4/2019
ms.author: dsindona
ms.openlocfilehash: ae5c691375d52bc7da7a877f5b2e0c2ca52cc87e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288455"
---
# <a name="microsoft-commercial-marketplace-faqs"></a>微软商业市场常见问题解答

回答有关 Azure 应用商店和 Microsoft AppSource 的常见问题的解答。

## <a name="faq-for-customers"></a>面向客户的常见问题解答

### <a name="what-you-need-to-know-about-azure-marketplace-and-microsoft-appsource"></a>您需要了解的 Azure 应用商店和微软应用程序源

**什么是 Azure 市场？**

在 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace)中可以访问 Microsoft 及其合作伙伴提供的解决方案和服务，以及获取其相关信息。 客户可以发现、尝试或购买基于 Azure 或为 Azure 构建的云软件解决方案。 我们的目录包含 8,000 多款商品，提供虚拟机 (VM)、API、Azure 应用、解决方案模板等 Azure 构建基块，以及托管应用程序、SaaS 应用、容器和咨询服务。

**谁是 Azure 市场客户？**

Azure 应用商店专为对商业 IT 软件和服务感兴趣的 IT 专业人员和云开发人员而设计。

**Azure 市场目前提供哪些类型的产品？**

Azure 市场提供 Microsoft 和合作伙伴为扩展 Azure 产品和服务而构建的技术解决方案和服务。 解决方案目录跨越多个类别，包括：

* 基础操作系统
* 数据库
* security
* identity
* 网络
* 区块链
* 开发人员工具

### <a name="azure-marketplace-for-customers"></a>面向客户的 Azure 市场

**如何开始使用 Azure 市场？**

访问 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps)可以找到各种经过认证和优化、可在 Azure 上运行的企业应用程序与解决方案。 [**创建资源**](https://portal.azure.com/#create/hub)时，还可以通过[Azure 门户]访问 Azure 应用商店。

**Azure 市场的关键优势是什么？**

通过 Azure 市场，客户可以发现针对 Azure 构建的或者在 Azure 上构建的技术应用程序。 它将 Microsoft Azure 的解决方案和服务市场整合到一个统一的平台中，只需单击几下鼠标即可发现、试用、购买或部署解决方案。

**如何从 Azure 市场购买产品？**

可通过以下方式购买 Azure 市场套餐：

* [基于 Web 的网店](https://azuremarketplace.microsoft.com/marketplace/apps)
* [Azure 门户][Azure 门户]
* [Azure 命令行接口 （CLI）](/cli/azure/?view=azure-cli-latest)

>[!Note]
>预付额度和其他形式的货币承诺不能用于支付软件许可证费用，但可用于支付相关 Azure 使用费。 [Azure 货币承诺](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/)中列出了例外项目。

**我可以选择部署 Azure 应用商店购买哪个 Azure 区域吗？**

客户可以选择部署到他们启用的任何 Azure 数据中心区域。 我们建议选择距离服务最近的数据中心位置，以优化性能和控制预算。

**如果我不小心删除了 Azure 应用商店购买，是否可以*撤消*该操作？**

不能，删除操作是永久性的。 如果意外删除了订阅，可以重新购买。 任何未使用的功能或预付费服务将会丢失。

**如果我尝试删除我的另一个应用程序正使用的 Azure 市场购买内容，是否会收到警告？**

否。删除购买内容时，Azure 不会发出警告，即使该购买内容正在使用中，或者应用程序依赖于它。

**如果我的 Azure 市场购买内容有任何对其他资产的依赖项（如 Azure 网站），我必须管理这些依赖项吗？**

不会自动管理 Azure 市场套餐的相关依赖项。 请在使用之前仔细查看 Azure 市场购买内容的说明，以确定是否有你应在部署之前注意的任何依赖项。

**可从 Azure 云解决方案提供商处购买 Azure 市场解决方案吗？**

如果发布者已将其产品配置为通过云解决方案提供商 （CSP） 渠道提供，则云解决方案提供商合作伙伴可以选择转售解决方案。

**支持哪些国家/地区购买通过 Azure 应用商店销售/调配的应用程序和服务？**

Azure 应用商店可供[参与策略](/legal/marketplace/participation-policy)中列出的国家/地区的 Azure 客户使用。

**Azure 应用商店支持哪些货币？**

交易方式如下：澳元、BRL、加元、瑞士法郎、丹麦克朗、欧元、英镑、INR、日元、韩元、挪威克朗、新西兰元、卢布、瑞典克朗、新台币、美元、人民币。

### <a name="deploying-a-solution-from-azure-marketplace"></a>从 Azure 市场部署解决方案

**我已经将 Azure 应用商店虚拟机 （VM） 部署到订阅，现在我想将订阅从一个 Azure 帐户迁移到另一个 Azure 帐户。当前是否支持此功能？**

若要迁移 Azure 订阅（包括 Azure 市场 VM 和服务），请先删除或取消任何旧版 Azure 市场 VM 和服务，然后再关联到新的 Azure 帐户。 迁移完成后，将使用新注册帐户的付款方式对由此产生的使用费进行计费。

**我想将 Azure 应用商店虚拟机 （VM） 订阅迁移到我的企业协议。当前是否支持此功能？**

若要将 Azure 市场虚拟机 (VM) 订阅迁移到企业协议，请在迁移之前停止或取消任何旧版订阅。 完成 Azure 帐户和相关订阅的迁移后，可以重新购买 Azure 应用商店 VM 或服务。  将根据企业协议按季度计收产生的使用费。

### <a name="pricing-and-payment"></a>定价与付款

**Azure 市场订阅如何收费？**

定价因产品类型和发布者规格而异。 通过 Azure 订阅单独收取软件许可证费用和 Azure 使用成本。

非捆绑：**

+ *自带许可证 （BYOL） 模型*：直接从发布商或经销商处获取软件许可证时，无需支付额外的软件相关费用或费用。

捆绑：**

Azure 订阅包含在发布者的独立软件供应商 （ISV） 解决方案定价中。

收费：**

+ *免费：* 免费 SKU。 不会因客户使用套餐而向其收取软件许可证费用。

+ *免费软件试用：* 优惠期限为有限。 在试用期内，发布商的软件许可费不收取任何费用。 在试用期过后，将自动根据发布者提出的标准费率向使用套餐的客户收费。

+ *基于使用情况：* 房价根据产品使用范围收费或计费。 对于虚拟机映像，将按小时收费。 对于开发者服务和 API，将按套餐定义的度量单位收费。

+ *固定费用：* SaaS 订阅可以按月或按年计费的固定费用定价。 这还可以包括根据消耗量（例如带宽、电子邮件或票证）收费的其他计费维度。 

+ *每个用户：* SaaS 订阅可以按用户按月或按年计费定价。 

特定于优惠的定价详细信息可在 Azure 门户上或 Azure [https://azure.microsoft.com/pricing/](https://azure.microsoft.com/pricing/) [门户]中的解决方案详细信息页面上找到。

> [!Note]
> 除每月费用外，除非另有说明，Azure 使用费适用于所有定价模型。

**如何为 BYOL 市场解决方案提供软件许可证密钥，Azure 市场发挥哪种作用？**

BYOL 解决方案许可证凭据的获取和实施由发布者负责。 对于虚拟机产品/服务，许可证密钥的获取通常在应用程序启动后在发布者的应用程序中进行。 使用通过 Azure 应用程序解决方案模板部署的虚拟机产品/服务时，可以将资源管理器模板配置为提示用户输入一系列输入，包括许可证凭据。

下面是按套餐类型列出的最常见选项：

*虚拟机优惠：*

+ *选项 1：* 许可证密钥的获取通常在应用程序启动后在发布者应用程序中进行。

+ *选项 2：* 在所选订阅中部署 VM 产品/服务后，最终用户（通过产品/服务提供的命令行/Web 界面）输入许可证密钥。 许可证可以是密钥和/或文件，具体由发布者确定。

Azure 应用（解决方案模板和托管应用）：**

+ *选项 1：* 可以配置资源管理器模板以提示输入一系列输入，包括许可证凭据。 可以在最终用户订阅中部署套餐之前，提供许可证文件（文件上传）或密钥（在文本框中输入）。

+ *选项 2：* 您可以通过产品/产品/服务提供的命令行/Web界面输入许可证密钥。  在选定的订阅中部署 Azure 应用套餐后执行此操作。 许可证可以是密钥和/或文件，具体由发布者确定。

**支持哪些类型的试验？**

发布商可以为付费 SaaS 优惠添加一个免费月，为 VM 映像添加一个或三个月的免费消费月。 免费试用优惠是带有启动试用请求的操作列表。 这些引导客户访问发布者定义的网站以设置试用体验。 试用版也可以添加到第一个月的免费付费产品中。 

**是否需要存档付款方式（例如信用卡），才能部署免费层套餐或自带许可 (BYOL) 套餐？**

不是。 部署免费层或 BYOL 套餐无需付款方式。 但是，部署免费试用套餐则需要付款方式。 附带“立即获取”或“免费软件试用”按钮的商品将部署到选定的 Azure 订阅中********。  这些列表使用所选帐户的注册付款方式计费。 Azure 使用费将与软件许可证费用分开计费。

**如果企业协议 (EA) 间接客户对于在 Azure 市场上销售的套餐的定价有问题，那么他们该与谁联系？**

对于所有 Azure 市场定价问题，企业协议 (EA) 间接客户必须与其授权解决方案提供商 (LSP) 联系。

**我能控制员工对 Azure 市场的访问以及购买特权吗？**

是的，注册时，注册管理员可对企业协议 (EA) 客户关闭所有帐户的购买特权，并将其重新打开足够长的时间以便进行购买。

**商业市场购买支持哪些付款方式？**

客户可以使用信用卡从 Azure 应用商店和 Microsoft AppSource 购买产品。 如果您有现有的 Azure 订阅，则从 Azure 应用商店购买将使用在帐户上配置的付款方式，并在同一发票上显示为单独的行项目。 某些产品/服务会消耗 Azure 货币承诺，但大多数商业市场购买不会减少企业协议承诺，尽管 Azure 基础结构消耗将。

**我能对 Azure 市场套餐应用我帐户上的 Azure 订阅额度或货币承诺资金吗？**

特定 Azure 市场套餐可使用 Azure 订阅额度或货币承诺资金。 有关参与此计划的产品的完整列表，请参阅 [Azure 货币承诺](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/)。 这些套餐不包括 BYOL 或 BYOS 选项。 所有其他 Azure 应用商店产品/服务不能使用 Azure 订阅积分或货币承诺：例如免费一个月试用积分、每月 MSDN 积分、Azure 促销积分、货币承诺余额以及从中提供的任何其他免费积分蔚蓝。

**批量许可折扣是否适用于 Azure 市场购买内容？**

不是。 拥有 Azure 市场中解决方案的发布者可以定价。  标准 Microsoft 批量许可折扣不适用于 Azure 市场购买内容。

**可在哪里查看 Azure 市场订阅详细信息和计费信息？**

MOSP -[微软在线订阅计划](https://azure.microsoft.com/support/legal/subscription-agreement/?country=us&language=en)（Web直接）客户可以在[Azure 门户]的成本管理和计费部分的"发票"选项卡中查看应用商店购买详细信息。

企业协议 （EA） 客户可以在[Azure 门户]的成本管理 + 计费部分的"发票"选项卡中查看应用商店采购详细信息。

云解决方案提供商 （CSP） 合作伙伴可以在"订单历史记录"选项卡中选择公司后，在"订单历史记录"选项卡中查看合作伙伴中心中每个客户的应用商店购买详细信息。

**如何取消 Azure VM 的 Azure 市场附加产品？**

由于此附加产品与 Azure 虚拟机 (VM) 关联，因此必须通过删除 VM 来停止 VM，才能取消 Azure 市场购买。 这会停止 Azure 市场购买内容的所有订阅使用和收费。

**将按何种频率对 Azure 市场购买内容计费？**

对于不可从货币承诺扣费的所有 Azure 市场套餐，将以欠款形式按月计费。 年度 SaaS 订阅对全年服务收取一次费用。

MOSP - [Microsoft 在线订阅计划](https://azure.microsoft.com/support/legal/subscription-agreement/)（Web 直接）客户每月根据其 Azure 订阅配置文件存档的同一信用卡收费。 年度 SaaS 订阅对全年服务收取一次费用。

**如何将 Azure 市场购买内容从 MOSP 订阅迁移到我的直接企业协议 (EA) 订阅？**

虽然大多数 Microsoft 订阅可轻松转换为企业协议，但这些订阅内的 Azure 市场购买内容不能。

如需将从 Azure 市场购买的其他服务迁移到 EA 订阅，请先从现有 MOSP 订阅中取消应用程序，然后在 EA 订阅中重新购买这些应用程序。 执行此操作之后，可以提交额度请求，要求退还在市场服务订阅之间可能重叠的月份的款项 - [创建票证](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)即可。

**Azure 应用商店中虚拟机产品的成本结构*中的价格*、*软件价格*和*总价*之间的差别是什么？**

*价格*是指 Azure 虚拟机运行软件的成本。 *软件价格*是指在 Azure 虚拟机上运行的应用商店发布者软件的成本。 *总价*是指在 Azure 虚拟机上运行的 Azure 虚拟机和应用商店发布者软件的总成本。

**如何了解我已使用了多少 Azure 市场购买内容？**

在[Azure 门户]中可以找到估计的使用信息。 此类使用情况估计信息可能不包括近期活动，并且可能以从过去的消费派生的投影为基础。 在公开预览期间，此功能并非对所有购买可用，而是因产品类型而异。

### <a name="customer-support"></a>客户支持

**有关 Azure 市场的常见支持问题，我应联系谁？**

有关使用情况或故障排除的常见应用程序支持，请直接联系应用程序发布者。

如果对购买的 Azure 市场的计费和订阅抱有疑问，请与 [Azure 支持部门](https://support.microsoft.com/getsupport?oaspworkflow=start_1.0.0.0&wf=0&wfname=productselection&prid=16230&forceorigin=esmc&ccsid=636694515623707953)联系。

**对于在 Azure 市场中购买的解决方案的技术支持，我应联系谁？**

请联系发布者/提供商获取所有技术产品支持。 可在 Azure 市场中的解决方案详细信息页上找到发布者联系方式和/或指向支持网站的链接。

**有关从市场购买的第三方解决方案的计费支持/问题，我应联系谁？**

请通过 [Azure 支持](https://support.microsoft.com/getsupport?oaspworkflow=start_1.0.0.0&wf=0&wfname=productselection&prid=16230&forceorigin=esmc&ccsid=636694515623707953)联系 Microsoft 支持部门。

**如果我对 Azure 市场上出售的合作伙伴解决方案的价格或条款有疑问，应联系谁？**

请联系发布者/提供商获取所有技术产品支持。 可在 Azure 市场中的每个解决方案详细信息页上找到发布者联系方式和/或指向支持网站的链接。

**如果不满意，能否退货？**

无法退回从[Azure 应用商店](https://azuremarketplace.microsoft.com/)进行的采购，但可以取消/删除。 基于消费的优惠按使用情况计费，因此当停止时，费用也停止。 订阅将被取消，并且不会超过当前计费周期。 如果在购买后不久取消订阅（每月 24 小时，每年 14 天），则提供全额退款。

有关其市场服务或购买内容的任何技术问题，客户必须直接联系发布者。 可在 Azure 市场中的解决方案详细信息页上找到发布者联系方式和/或指向支持网站的链接。

**增加中期许可证时如何处理许可证添加？**

添加到现有订阅的许可证在订阅持续时间的剩余时间内按比例评级。

**中期时如何处理许可证删除？**

取消的许可证将受此常见问题解答中的退款政策的约束。  取消的所有许可证将立即从您的帐户中删除，并且将不再可供使用。

**支持基于消费的优惠退款吗？**

任何基于消费的费用（无论是每小时 VM 还是自定义仪表）均无法通过取消而退款。 消费一旦发生，费用将由 Microsoft 的商务平台处理，任何要求退款的服务质量争议都将在发布商和客户之间的 Microsoft 系统之外直接处理。 支持统一费率加计量计费的优惠遵循统一费率费用的标准退款政策。

**是否可以在中期更改计划？**

不，不可能在每月和每年之间转换。

**客户能否从同一优惠购买两个计划？**

是的，客户可以同时拥有同一优惠中的两个计划。

**退款和计划变更政策因网店而异吗？**

否，业务策略在整个 Azure 应用商店和 Microsoft AppSource 中是一致的。 如果云解决方案提供计划中的合作伙伴转售，合作伙伴可能会为其客户实施不同的策略。


## <a name="faq-for-publishers"></a>面向发布者的常见问题解答

### <a name="what-you-need-to-know-about-azure-marketplace-and-microsoft-appsource"></a>您需要了解的 Azure 应用商店和微软应用程序源

**什么是 Azure 市场？**

[Azure 市场](https://azuremarketplace.microsoft.com/marketplace)是一个在线应用程序和服务市场。 客户（主要是 IT 专业人员和开发人员）可以发现和购买基于 Azure 或为 Azure 构建的云软件解决方案。 市场目录包含 8,000 多款商品，例如虚拟机 (VM)、API、解决方案模板、SaaS 应用程序和咨询服务等 Azure 构建基块。

Azure 市场是与 Microsoft 开展联合市场推广活动的起点。  我们专注于帮助合作伙伴争取更多客户。 客户可以发布新的产品，还可以使用 Azure 市场开展促销和需求生成营销活动、与 Microsoft 一起执行联合销售/营销活动。

**谁是 Azure 市场客户？**

Azure 市场主要面向对商用 IT 软件和服务感兴趣的 IT 专业人员与云开发人员。

### <a name="azure-marketplace-for-publishers"></a>发布商的 Azure 应用商店

**为什么我应该在 Azure 市场上发布我的应用程序，它对我有什么好处？**

Azure 市场为 Microsoft 合作伙伴提供向 Azure 客户推广和销售产品及服务的市场。 发布商可立即访问 140 个全球市场、我们的 300，000 多个合作伙伴以及 Azure 的企业客户网络。  该市场包括 90% 以上的财富 500 强公司和许多世界领先的开发人员。 Azure 应用商店中的新合作伙伴会自动获得一套[无成本的上市权益](gtm-your-marketplace-benefits.md#list-trial-and-consulting-benefits)，以帮助提高在 Azure 市场中对其产品/服务的认识。

**Azure 市场和 AppSource 之间的区别是什么？**

Microsoft 合作伙伴可以根据其目标受众的位置来选择发布位置。

Microsoft 提供了两个不同的云市场网店 - Azure 应用商店和 AppSource。 这些店面允许客户查找、试用和购买云应用程序和服务。 每个店面满足独特的客户需求，并使 Microsoft 合作伙伴能够根据目标受众提供相应的解决方案或服务。

对于 IT 专业人员、开发人员或技术用户，可以选择 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps)。

对于业务线决策者和企业主，可以选择 [AppSource](https://appsource.microsoft.com/)。

有关 Azure 市场和 AppSource 的更多详细信息和优势，请查看[发布者指南](marketplace-publishers-guide.md)。

**是否必须是 Microsoft 合作伙伴网络 (MPN) 的成员才能在 Azure 市场中发布任何应用程序和服务？**

是的，必须成为 MPN 成员才能在 Azure 市场中发布内容。 请访问 [Microsoft 合作伙伴网络](https://partner.microsoft.com/membership)开始注册。

**在 Azure 市场中发布解决方案的条件是什么？**

若要在 Azure 市场中发布内容，合作伙伴必须演示其应用程序可在 Azure 上运行或扩展。 出版商必须为客户提供[服务级别协议](https://azure.microsoft.com/support/legal/sla/)、[隐私政策](https://privacy.microsoft.com/privacystatement)以及电话和在线支持。 各种工作负荷具有不同的要求。 有关更多指导，请查看 [Azure 市场参与策略](./marketplace-participation-policy.md)和[发布者指南](marketplace-publishers-guide.md)。

**在 Azure 市场中发布是否收费？**

通过 Azure 市场上传商品、试用版或 BYOL（自带许可证）时，发布者无需支付发布费用。

**通过 Azure 市场购买是否有任何交易费？**

通过 Azure 市场购买解决方案许可证时，软件许可证的收入将在发布者与 Microsoft 之间分摊。  这种分摊是根据[市场发布者协议](https://cloudpartner.azure.com/Content/Unversioned/PublisherAgreement2.pdf)中的条款和条件进行的。 此外，对于使用 BYOL（自带许可证）的发布者解决方案，发布者无需支付交易费。

**什么是标准合同？**

Microsoft 提供了发布者可以选择使用的标准合同条款，以便客户具有简化的采购和法律审查流程。

**在何处可以找到将应用程序与 Azure Active Directory (AAD) 集成的指导原则？**

Microsoft 通过 AAD 对所有市场用户进行身份验证。  可以直接预配到试用环境中，而无需其他登录步骤。  例如，经过身份验证的用户单击市场中的试用商品后，即可重定向到试用环境。

有关详细信息以及如何在 AAD 中开始启用试用，请访问[发布者指南中的 Azure Active Directory 部分](enable-appsource-marketplace-using-azure-ad.md)。

**如何开始使用开发人员中心注册？**

若要开始，发布者应确认某个[开发人员中心帐户](deprecated/register-dev-center.md)尚未注册（目的是防止重复）。 确认后，下一步是使用与开发人员帐户关联的 Microsoft 帐户[登录](https://account.microsoft.com/account/)，以完成注册。

如果您还没有 Microsoft 帐户，则可以[创建一个帐户](https://signup.live.com/)（例如： contoso_marketplace@live.com。

**为何需要开发人员中心帐户？**

Microsoft 需要使用开发人员中心帐户代表发布者向客户计收“事务处理”商品类型的费用。 Microsoft 通过开发人员中心帐户注册来验证法律、税务和银行信息。 有关详细信息，请参阅[在开发人员中心注册](./partner-center-portal/create-account.md)。

**什么是潜在顾客？为什么说他们对在市场上作为发布者的我很重要？**

潜在顾客是从市场部署产品的客户。 无论产品是出现在 [Azure 市场](https://azuremarketplace.microsoft.com)中还是出现在 [AppSource](https://appsource.microsoft.com) 中，都能够收到对你产品感兴趣的潜在顾客。  可以根据套餐设置潜在顾客目标。 有关详细信息，请参阅[成为云市场发布者](./partner-center-portal/create-account.md)。

**在何处可获得设置潜在顾客目标的帮助？**

在[合作伙伴中心获取客户线索](./cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads.md)文档中了解更多信息，或通过选择产品/服务类型和潜在[https://aka.ms/marketplacepublishersupport](https://aka.ms/marketplacepublishersupport)顾客管理提交支持票证。

**是否必须在配置潜在顾客目标后，才能在市场上发布套餐？**

是的，如果要发布“与我联系”、“SaaS 应用”或“咨询服务”套餐，则需要配置潜在顾客目标************。

**如何确认潜在顾客配置是否正确？**

完成报价并设置潜在顾客目的地后，可以在[合作伙伴中心](https://cloudpartner.azure.com/)正确发布列表。 商品上线之前，可以验证潜在顾客配置设置是否正常工作。  将测试潜在顾客发送到套餐中配置的潜在顾客目标。

**发布者可在哪些国家/地区销售 Azure 市场？**

位于以下国家/地区的发布商目前可以在 Azure 应用商店中销售：阿富汗、 阿尔巴尼亚、阿尔及利亚、安哥拉、安提瓜和巴布达、阿根廷、亚美尼亚、澳大利亚、奥地利、阿塞拜疆、巴林、孟加拉国、白俄罗斯、比利时、贝宁、玻利维亚、波斯尼亚和黑塞哥维那、博茨瓦纳、巴西、保加利亚、布基纳法索、布隆迪、柬埔寨、喀麦隆、加拿大、中非共和国、乍得、智利、哥伦比亚、科摩罗、刚果、刚果（金）、哥斯达黎加、科特迪瓦、克罗地亚、塞浦路斯、捷克共和国、丹麦、多米尼加、多米尼加共和国、 厄瓜多尔、埃及、萨尔瓦多、厄立特里亚、爱沙尼亚、埃塞俄比亚、斐济群岛、芬兰、法国、格鲁吉亚、德国、加纳、希腊、危地马拉、几内亚、海地、洪都拉斯、香港特别行政区、匈牙利、冰岛、印度、印度尼西亚、伊拉克、爱尔兰、以色列、意大利、牙买加、日本、约旦、哈萨克斯坦、肯尼亚、韩国（韩国）、科威特、老挝、拉脱维亚、黎巴嫩、利比里亚、列支敦士登、立陶宛、卢森堡、马达加斯加、马拉维、马来西亚、马里、马耳他、毛里求斯、墨西哥摩洛哥、莫桑比克、尼泊尔、 荷兰、新西兰、尼加拉瓜、尼日尔、尼日利亚、挪威、阿曼、巴基斯坦、巴拿马、巴拉圭、秘鲁、菲律宾、波兰、葡萄牙、卡塔尔、罗马尼亚、俄罗斯、卢旺达、沙特阿拉伯、塞内加尔、塞尔维亚、塞拉利昂、新加坡、斯洛伐克、斯洛文尼亚、索马里、南非、西班牙、斯里兰卡、瑞典、瑞士、塔吉克斯坦、坦桑尼亚、泰国、东帝汶、多哥、汤加、特立尼达和多巴哥、突尼斯、土耳其、土库曼斯坦、乌干达、乌克兰、阿拉伯联合酋长国国家、乌拉圭、乌兹别克斯坦、委内瑞拉、越南、赞比亚和津巴布韦。

**如何从 Azure 市场中删除商品？**

*虚拟机& Azure 应用：*

1. 登录到[合作伙伴中心](https://cloudpartner.azure.com/)。
1. 从"**所有优惠**"选项卡中选择优惠。
1. 在屏幕左侧窗格中，选择“SKU”**** 选项卡。
1. 选择要删除的 SKU，然后单击该 SKU 的**删除**按钮。
1. 将产品/服务[重新发布](./cloud-partner-portal/manage-offers/cpp-publish-offer.md)到 Azure 市场。

有关详细信息，请参阅[删除套餐](./cloud-partner-portal/manage-offers/cpp-delete-offer.md)。

*Web 应用（SaaS 应用、加载项）&咨询服务：*

1. 在合作伙伴中心中，选择问号图标，然后单击"**支持**"。
1. 转到  <https://go.microsoft.com/fwlink/?linkid=844975> 。
1. 在支持页上，选择套餐类型。
1. 选择 **"删除**已发布产品/服务"。
1. 创建事件票证。
1. 提交。

*O365 应用：*

1.  <https://sellerdashboard.microsoft.com>使用开发帐户登录。
1.  撤回外接程序。

    > [!NOTE]
    > 应用在 90 天后从现有列表中消失。

**为什么我的更改没有反映在优惠中？**

只有在重新发布产品/服务后，合作伙伴中心内所做的更改才会在系统和商店方面进行更新。 请确保您已提交发布要约后进行任何修改。

### <a name="benefits-and-go-to-market-gtm-resources"></a>优势和市场推广 (GTM) 资源

**发布者在 Azure 市场发布产品可享受哪些市场推广权益？**

Azure 市场是与 Microsoft 开展联合市场推广活动的起点，也是通向“联合销售就绪”合作的门户。 Azure 应用商店中的所有新上市项目都自动提供一套[免费上市优惠](https://assetsprod.microsoft.com/mpn/marketplace-gtm-benefits.pdf)，以帮助提高 Microsoft 客户对优惠的意识。 发布套餐后，Microsoft GTM 团队将与你取得联系，并开始交付权益。

有关 GTM 权益以及如何在市场中拓展业务的详细信息，请访问 [Microsoft GTM 服务](https://partner.microsoft.com/reach-customers/gtm)。

**在 Microsoft Web 属性中，Azure 市场解决方案在何处得到推广？**

Azure 应用商店解决方案可在 Microsoft [Azure 门户]和[Azure 应用商店网站](https://azuremarketplace.microsoft.com/marketplace/)中提供。 使用 Azure 的云开发人员和 IT 专业人员每次登录时，都会接触到合作伙伴解决方案。 此外，[Azure 市场主页](https://azuremarketplace.microsoft.com/marketplace)和 [Azure 解决方案页面](https://azure.microsoft.com/solutions/)上还会展示并轮换合作伙伴解决方案的子集。

### <a name="billing-and-payments"></a>计费与付款

**如何接收 Azure 市场销售的付款？**

Microsoft 的所有付款均将通过 PayPal 或电子资金转帐 (EFT) 按月处理。 我们力争在客户使用服务之日起的两个月内付款，但具体时间取决于客户的付款方式。 45 天的托管期适用于信用卡客户。

**对于使用基于使用情况计费购买的基于虚拟机的解决方案，当客户增加或减少基础虚拟机数量时，是否会遵循我的软件许可证的定价？**

是的，立即按新价格进行计费。  如果客户更改虚拟机大小，并且定价表中根据虚拟机大小指定了不同的价格，则会发生定价变化。

**Azure 市场是否支持按节点计费？**

Azure 市场目前不支持对虚拟机按节点计费。 发布者仍可根据 Microsoft VM 费率确定每个节点的计费费率。  计算方法是将 VM 数目乘以使用小时数和每小时费率。

**有关计费或套餐管理的问题，我应联系谁？**

联系 [Microsoft 支持部门](https://support.microsoft.com/getsupport?oaspworkflow=start_1.0.0.0&wf=0&wfName=productselection&prid=15635)记录一个票证。

### <a name="publisher-support"></a>发布者支持

**有关 Azure 市场的常见支持问题，我应联系谁？**

有关可用性或故障排除的一般应用程序支持，请联系[合作伙伴中心支持](https://support.microsoft.com/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=16230&ccsid=636565784998876007)。

如果对购买的 Azure 市场的计费和订阅抱有疑问，请与 [Azure 支持部门](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)联系。

**有关发布或报价管理的问题，我应联系谁？**

有关常见问题的最新资源和文档，请访问 [Azure 市场发布者指南](marketplace-publishers-guide.md)。 此外，您还可以[在合作伙伴中心使用 Microsoft 支持](https://support.microsoft.com/getsupport?oaspworkflow=start_1.0.0.0&wf=0&wfname=productselection&prid=16230&forceorigin=esmc&ccsid=636694515623707953)登录票证。

### <a name="azure-marketplace-for-publishers"></a>发布商的 Azure 应用商店

**如何定义我的地理位置可用性，以便在不同国家/地区进行销售？**

1.  在合作伙伴中心中，导航到要向其添加新国家/地区 SKU。  在**SKU 详细信息**中，导航到**国家/地区可用性**，然后单击 **"选择区域**"。

    ![选择区域](media/marketplace-publishers-guide/FAQ-choose-geo.png)

1.  将弹出一个列表，所有可用的国家/地区将销售到。  单击要使此 SKU 可用的每个国家/地区旁边的复选框。 单击“确定”。

    ![选择国家/地区列表](media/marketplace-publishers-guide/FAQ-select-countries.png)

1.  最后，要将更改应用于实时优惠，请单击"**发布**"。  

    > [!Note]
    > 更改生效需要 24 小时。

<!---    ![Publish offer](media/marketplace-publishers-guide/FAQ-publish-offer.png) -->

**发布者如何更改现有产品/服务的地理可用性？**

发布者可以编辑现有产品/服务，选择新的国家/地区，并使用电子表格下载/上传功能设置定价。

**客户可以在哪些国家/地区购买 Azure 应用商店产品？**

Azure 应用商店支持由客户的帐单地址定义的 141 个从购买地。 有关国家/地区列表，请参阅[参与政策](/legal/marketplace/participation-policy)。

**Azure 应用商店支持哪些货币？**

交易方式如下：澳元、BRL、加元、瑞士法郎、丹麦克朗、欧元、英镑、INR、日元、韩元、挪威克朗、新西兰元、卢布、瑞典克朗、新台币和美元。

### <a name="pricing-and-payment"></a>定价与付款

**免费层与免费软件试用之间有何区别？**

“免费层”订阅套餐永久免费。  “免费软件试用”（立即试用）套餐为收费订阅，只是在有限的时间段内免费。

**验证端到端采购和预配流的流程是什么？**

在发布过程中，您将获得对优惠预览的访问权限。 访问权限仅限于您在"预览"选项卡中指定的用户，这是其他人看不到的实时优惠。 您可以购买此项并测试流程;但是，您将根据产品/服务配置向您收取全额费用。

为了以极低的价格完成购买，Microsoft 建议以您可以接受的价格为自己发布一个私人计划，作为测试成本。 零支持，但不会反映完整的体验，如付费订阅。

**微软是否会在标准政策之外提供退款？**

是的，根据您的请求通过支持票证，Microsoft 将处理给您的客户的积分，如果您认为合适。

## <a name="next-steps"></a>后续步骤

访问[Azure 应用商店和 AppSource 发布者指南](/azure/marketplace/marketplace-publishers-guide)页面。


[Azure 门户]: https://portal.azure.com
