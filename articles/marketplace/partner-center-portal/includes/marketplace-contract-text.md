---
title: include 文件
description: 包含 Microsoft 商业 marketplace 标准合同的文本文件
documentationcenter: partner-center-commercial-marketplace
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
ms.date: 01/22/2020
ms.author: dsindona
ms.custom: include file
ms.openlocfilehash: e7692d8c90d71e76628ecb44ade529eabedfa909
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691524"
---
Microsoft 为商业市场提供标准合同模板。

- **使用 Microsoft 商业应用商店的标准约定？**

为了简化客户的采购过程并降低软件供应商的法律复杂性，Microsoft 为 Microsoft 商用 marketplace 提供了标准合同，有助于促进 marketplace 中的交易。 商业 marketplace 发布者可以选择以标准合同提供其软件，而客户只需来审查并接受一次，而不是手工编写自定义条款和条件。 可在此处找到标准协定： https://go.microsoft.com/fwlink/?linkid=2041178。

您可以选择使用标准合同，而不是通过选择 "为商业应用商店使用标准协定" 复选框来提供自己的自定义条款和条件。

![使用 "标准协定" 复选框](./media/use-standard-contract.png)

> [!NOTE]
> 使用 Microsoft 商业应用商店的标准合同发布产品/服务后，将无法使用自己的自定义条款和条件。 这是一个 "或" 方案。 你可以在标准合同**或**你自己的条款和条件下提供解决方案。 如果要修改标准协定的条款，可以通过标准协定改正来实现。

**标准协定改正**

标准协定改正允许发布者为简单起见选择标准协定术语，并自定义其产品或企业的条款。 如果客户已查看并接受 Microsoft 标准合同，则他们只需要查看对约定的修正。

对于商业 marketplace 发布者，可以使用两种改正：

- 通用修正：这些修正适用于所有客户的标准协定。 通用修正在购买流程中的每个产品/服务的客户上显示。 客户必须接受标准合同条款和修正条款，然后才能使用产品/服务。
- 自定义修正：这些修正是仅通过 Azure 租户 Id 面向特定客户的标准协定的特殊修正。 发布者可以选择要以其为目标的租户。 只有租户中的客户会看到产品/服务购买流程中的自定义修订条款。  客户必须接受标准合同条款和修正条款，然后才能使用产品/服务。

>[!NOTE]
> 这两种类型的改正堆栈彼此之上。 以自定义修正为目标的客户也会在购买过程中获得标准合同的通用修正。

**适用于 Microsoft 商用 Marketplace 标准合同的通用修正条款**：在此框中输入通用修订条款。 可以为每个产品/服务提供单个通用修订。 您可以在此框中输入不限数量的字符。 在发现和购买流程期间，会向 AppSource、Azure Marketplace 和/或 Azure 门户中的客户显示这些条款。

**适用于 Microsoft 商用 Marketplace 标准合同的自定义修订条款**：从选择 "**添加自定义修订条款**" 开始。 最多可为每个产品提供10个自定义修订条款。

- **自**定义修订条款：在自定义修订条款框中输入自定义修订条款。 您可以在此框中输入不限数量的字符。 只有你为这些自定义术语指定的租户 Id 的客户才会在 Azure 门户的产品/服务购买流程中提供自定义修订条款。  
- **租户 id** （必需）：每个自定义修订最多可针对20个租户 id。 如果添加自定义修正，则必须至少提供一个租户 ID。 租户 ID 用于标识 Azure 中的客户。 可以通过导航到 portal.azure.com > Azure Active Directory > 属性来向客户提供此 ID。 目录 ID 值是租户 ID （例如，50c464d3-4930-494c-963c-1e951d15360e）。 你还可以使用其域名 URL 查找你的客户的租户 ID，"[我的 Microsoft Azure 和 Office 365 租户 id 是什么？"](https://www.whatismytenantid.com)。
- **描述**（可选）：根据需要提供适用于租户 ID 的友好描述，以帮助你确定要使用修正的客户。

**条款和条件**

如果要提供自己的自定义条款和条件，则可以选择在 "条款和条件" 字段中输入它们。 最多可在此字段中输入10000个字符的文本。 如果你的条款和条件需要较长的说明，请在此字段中输入一个 URL 链接，你可以在其中找到你的条款和条件。 它将向客户显示为活动链接。

客户必须接受这些条款，然后才能试用你的产品/服务。

选择 "**保存草稿**"，然后继续。
