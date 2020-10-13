---
title: include 文件
description: 包含 Microsoft 商业 marketplace 标准合同的文本文件
documentationcenter: partner-center-commercial-marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
ms.date: 01/22/2020
ms.custom: include file
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: e1119cf5c40cdcb1fd598b85410ea9149a7cc146
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91400952"
---
Microsoft 为商业市场提供标准合同模板。

- **使用 Microsoft 商业市场标准协定？**

为了简化客户的采购流程并为软件供应商降低法律复杂性，Microsoft 提供 Microsoft 商业市场标准协定，以帮助促进市场中的交易。 商业市场发布者可以选择根据标准协定提供软件，而不制定自定义条款和条件，客户只需要审查并接受一次即可。 可在此处找到标准协定： https://go.microsoft.com/fwlink/?linkid=2041178 。

你可以通过选中“使用商业市场标准协定”复选框来选择使用标准协定，而不提供自己的自定义条款和条件。

![使用标准协定复选框](./media/use-standard-contract.png)

> [!NOTE]
> 使用 Microsoft 商业市场标准协定发布产品/服务后，你将无法使用自己的自定义条款和条件。 这是一个“二选其一”方案。 你要么根据标准协定提供解决方案，要么根据自己的条款和条件提供解决方案。 如果想修改标准协定的条款，可以通过标准协定修订进行。

**标准协定修订**

标准协定修订让发布者能够出于简单起见选择标准协定条款，同时为其产品或业务自定义条款。 如果客户已查看并接受了 Microsoft 标准协定，则只需要查看协定的修订部分。

有两种修正可供商业市场发行商使用：

- 通用修正：这些修正内容适用于所有客户的标准协定。 在购买流程中，将向产品/服务的所有客户显示通用修正。 客户在使用产品/服务之前，必须接受标准协定的条款和修正内容。
- 自定义修正：这些修正是对标准协定的特殊修正内容，仅通过 Azure 租户 ID 面向特定客户提供。 发行商可以选择要面向的租户。 只有租户中的客户才能在产品/服务的购买流程中看到自定义修正条款。  客户在使用产品/服务之前，必须接受标准协定的条款和修正。

>[!NOTE]
> 这两种修正相互叠加。 在购买过程中，可看到自定义修正内容的客户也会看到标准协定的通用修正内容。

**适用于 Microsoft 商用 Marketplace 标准合同的通用修正条款**：在此框中输入通用修订条款。 可以为每个产品/服务提供一个通用修订。 你可以在此框中输入无限数量的字符。 在发现和购买流程中，这些条款会在 AppSource、Azure 市场和/或 Azure 门户中向客户显示。

**适用于 Microsoft 商用 Marketplace 标准合同的自定义修订条款**：从选择 " **添加自定义修订条款**" 开始。 每个产品/服务最多可以提供 10 个自定义修订条款。

- **自**定义修订条款：在自定义修订条款框中输入自定义修订条款。 你可以在此框中输入无限数量的字符。 在 Azure 门户的产品/服务购买流程中，只会向为这些自定义条款指定的租户 ID 中的客户显示自定义修订条款。  
- **租户 id** (必需) ：每个自定义修订最多可针对20个租户 id。 如果添加自定义修订，则必须至少提供一个租户 ID。 租户 ID 标识 Azure 中的客户。 你可以要求客户提供此 ID，他们可通过导航到 portal.azure.com >“Azure Active Directory”>“属性”来找到它。 目录 ID 值即为租户 ID（例如，50c464d3-4930-494c-963c-1e951d15360e）。 你还可以使用其域名 URL 查找你的客户的租户 ID [，"我的 Microsoft Azure 和 Microsoft 365 租户 id 是什么？"](https://www.whatismytenantid.com)。
- **描述** (可选) ：根据需要提供适用于租户 ID 的友好描述，以帮助您识别修正的客户。

**条款和条件**

如果要提供自己的自定义条款和条件，可以选择在条款和条件字段中输入它们。 最多可在此字段中输入 10,000 个字符的文本。 如果条款和条件需要更长的说明，请在此字段中输入指向可找到你的条款和条件的位置的单个 URL 链接。 它将作为活动链接向客户显示。

客户必须接受这些条款才能试用产品/服务。

选择“保存草稿”，然后继续。
