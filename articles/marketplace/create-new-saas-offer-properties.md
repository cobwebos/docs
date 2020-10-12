---
title: 如何在 Microsoft 合作伙伴中心配置 SaaS 产品/服务属性
description: 了解如何在合作伙伴中心配置软件即服务 (SaaS) Microsoft 商用 marketplace 产品/服务的属性。
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 4dd592cb1b0037e2e1054bb24d987d4456f15e2c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89380680"
---
# <a name="how-to-configure-your-saas-offer-properties"></a>如何配置 SaaS 产品/服务属性

本文介绍如何在 Microsoft 商业应用商店中配置 "软件即服务" (SaaS) 产品/服务的属性。

在 " **属性** " 选项卡上，你将定义适用于你的产品/服务、你的应用版本和法律合同的类别和行业。 请确保在此页上提供有关产品/服务的完整且准确的详细信息，以使其正确显示并提供给正确的客户群体。

## <a name="select-a-category-for-your-offer"></a>为产品/服务选择一个类别

在 " **类别**" 下，选择至少一个和两个类别，将产品/服务分组到适当的 marketplace 搜索区域。 根据所选的类别，我们将确定列出产品/服务的在线商店： Azure Marketplace、Microsoft AppSource 或两者。

## <a name="select-industries-optional"></a>选择行业 (可选) 

在 " **行业**" 下，最多可选择两个行业，最多可选择两个行业 (两个) 的行业。 当客户在在线商店中筛选其在行业和子行业的搜索时，这些行业用于显示您的产品/服务。

> [!NOTE]
> 如果产品/服务不特定于行业，请将此部分留空。

1. 在 " **行业**" 下，选择 " **+ 行业** " 链接。
1. 从 **行业** 列表中选择一个行业。
1. 从 **子行业** 列表中选择至少一个和最多两个纵向。 使用 Ctrl 键可选择多个子行业。
1. 若要添加另一个行业和垂直，请选择 " **+ 行业** "，然后重复步骤1到3。

## <a name="specify-an-app-version-optional"></a>指定应用版本 (可选) 

 在 " **应用版本** " 框中，输入版本号。 应用版本在 AppSource marketplace 中用于标识产品的版本号。

## <a name="provide-terms-and-conditions"></a>提供条款和条件

在 **法律**上，提供产品/服务的条款和条件。 可以使用两个选项：

- [使用带有可选修正的标准协定](#use-the-standard-contract)
- [使用自己的条款和条件](#use-your-own-terms-and-conditions)

若要了解有关标准协定和可选修正的信息，请参阅 [Microsoft 商用 marketplace 的标准约定](standard-contract.md)。 您可以下载 [标准合同](https://go.microsoft.com/fwlink/?linkid=2041178) PDF (确保弹出窗口阻止程序) 。

### <a name="use-the-standard-contract"></a>使用标准协定

为了简化客户的采购过程并降低软件供应商的法律复杂性，Microsoft 提供了一个标准合同，你可以将其用于商业应用商店中的产品/服务。 当你在标准合同下提供软件时，客户只需阅读并接受一次，你就不必创建自定义条款和条件。

1. 选中 " **使用 Microsoft 商业市场的标准合同** " 复选框。

   ![演示了如何使用 Microsoft 的 "商用 marketplace 标准合同" 复选框。](partner-center-portal/media/use-standard-contract.png)
1. 在 **确认** 对话框中，选择 " **接受**"。 可能需要向上滚动才能看到它。
1. 选择“保存草稿”，然后继续操作。

   > [!NOTE]
   > 使用商业应用商店的标准合同发布产品/服务后，不能使用自己的自定义条款和条件。 在标准合同下提供你的解决方案，其中包含可选修正项或你自己的条款和条件。

### <a name="add-amendments-to-the-standard-contract-optional"></a>将修正案添加到标准协定 (可选) 

可使用两种类型的修正案： *通用* 和 *自定义*。

#### <a name="add-universal-amendment-terms"></a>添加通用修订条款

在 " **适用于 Microsoft 的标准协定的 Microsoft 商业市场** " 框中，输入通用修订条款。 你可以在此框中输入无限数量的字符。 在发现和购买流程中，这些条款会在 AppSource、Azure 市场和/或 Azure 门户中向客户显示。

#### <a name="add-one-or-more-custom-amendments"></a>添加一个或多个自定义改正

1. 在 **"自定义修正条款"** 下，选择 " **添加自定义修订条款 (最大 10) ** " 链接。
1. 在 " **自定义修订条款** " 框中，输入修订条款。
1. 在 " **租户 id** " 框中，输入租户 id。 只有与你为这些自定义术语指定的租户 Id 相关联的客户才能在产品/服务的 Azure 门户中看到它们。
   > [!TIP]
   > 租户 ID 用于标识 Azure 中的客户。 你可以向你的客户提供此 ID，并可通过转到 [**https://portal.azure.com**](https://portal.azure.com)  >  **Azure Active Directory**  >  **属性**找到它。 目录 ID 值是租户 ID (例如 `50c464d3-4930-494c-963c-1e951d15360e`) 。 你还可以使用[我的 Microsoft Azure 和 Office 365 租户 ID 是什么？](https://www.whatismytenantid.com/)中客户的域名 URL 查找其组织的租户 ID。
1. 在 " **说明** " 框中，根据需要输入租户 ID 的友好说明。 此说明可帮助您识别修正的目标客户。
1. 若要添加另一个租户 ID，请选择 " **添加客户的租户 id** " 链接，并重复步骤3和4。 最多可以添加20个租户 Id。
1. 若要添加其他修正术语，请重复步骤1到5。 每个产品/服务最多可以提供 10 个自定义修订条款。 
2. 选择“保存草稿”，然后继续操作。

### <a name="use-your-own-terms-and-conditions"></a>使用自己的条款和条件

你可以选择提供自己的条款和条件，而不是标准协定。 客户必须接受这些条款，才能试用你的产品/服务。

1. 在 " **法律**" 下，请确保清除 " **使用 Microsoft 商业市场的标准合同** " 复选框。
1. 在 " **条款和条件** " 框中，输入最多10000个字符的文本。

   > [!NOTE]
   > 如果需要较长的说明，请输入指向你的条款和条件所在位置的单个 web 地址。 它将作为活动链接向客户显示。

1. 选择 " **保存草稿** "，然后继续下一步选项卡 " **产品/服务**"。

## <a name="next-steps"></a>后续步骤

- [如何配置 SaaS 产品/服务的详细信息](create-new-saas-offer-listing.md)
