---
title: "用于资源策略的 Azure 门户 | Microsoft Docs"
description: "介绍如何使用 Azure 门户创建和管理 Resource Manager 策略。 可在订阅或资源组中应用策略。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: tomfitz
ms.openlocfilehash: 868b2cc1559053057d17b34c03e2e31347f399bf
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="use-azure-portal-to-assign-and-manage-resource-policies"></a>使用 Azure 门户分配和管理资源策略
通过 Azure 门户，可以将资源策略分配给资源组和订阅。 借助用户界面，可以轻松选择想要分配的策略，并为该策略指定参数值以自定义策略设置。 

若要通过门户分配策略，策略定义必须已存在于订阅中。 订阅具有若干已可分配给资源组或订阅的内置策略定义。 使用门户分配策略时，可看到这些内置策略和任何已定义的自定义策略。 有关策略的介绍和定义自定义策略的方法，请参阅[资源策略概述](resource-manager-policy.md)。

策略由所有子资源继承。 因此，如果将策略应用到资源组，则会将其应用到该资源组中的所有资源。 在本文中，术语“作用域”指被分配了策略的资源组或订阅。 

在创建和更新资源（PUT 和 PATCH 操作）时评估策略。

## <a name="assign-a-policy"></a>分配策略

1. 要将策略分配给资源组或订阅，请选择此资源组或订阅。 在设置中，选择“策略”。

   ![选择策略](./media/resource-manager-policy-portal/select-policies.png)

2. 若要为此作用域创建策略分配，选择“添加分配”。

   ![添加分配](./media/resource-manager-policy-portal/add-assignment.png)

3. 选择要分配的策略。 即使未向订阅添加任何策略定义，也可看到用于分配的内置策略。 这些内置策略涵盖多种常见方案。

   ![选择定义](./media/resource-manager-policy-portal/select-definition.png)

4. 选择策略后，将看到策略说明和策略参数。 例如，下图显示**允许的位置**参数，用于限制可用位置的策略需使用此参数。

   ![显示参数](./media/resource-manager-policy-portal/show-parameters.png)

5. 通过用户界面，选择要为策略参数指定的值（如可用于部署的位置）。

   ![选择参数值](./media/resource-manager-policy-portal/select-parameters.png)

6. 为其他参数提供值。 开始分配策略时，会根据所选边栏选项卡自动分配作用域。 完成后选择“确定”。

   ![定义参数](./media/resource-manager-policy-portal/define-parameters.png)

  已将策略分配给指定的作用域。

## <a name="view-policy-assignments"></a>查看策略分配

分配策略后，该作用域的策略列表中会列出该策略。 “详细信息”选项卡显示策略分配摘要。

![显示详细信息](./media/resource-manager-policy-portal/show-details.png)

“分配规则”选项卡显示策略定义的 JSON。

![显示分配规则](./media/resource-manager-policy-portal/show-assignment-rule.png)

## <a name="change-an-existing-policy-assignment"></a>更改现有策略分配

若要更改策略，选择“编辑分配”或“删除”

![编辑或删除分配](./media/resource-manager-policy-portal/edit-delete-policy.png)

## <a name="assign-custom-policies"></a>分配自定义策略

如果订阅中有已定义的自定义策略，这些策略可通过门户进行分配。 这些策略以**[自定义]**开头

![自定义策略](./media/resource-manager-policy-portal/show-custom-policy.png)

## <a name="next-steps"></a>后续步骤
* 若要了解用于定义策略的 JSON 语法，请参阅[资源策略概述](resource-manager-policy.md)。
* 有关企业可如何使用 Resource Manager 有效管理订阅的指南，请参阅 [Azure 企业基架 - 出于合规目的监管订阅](resource-manager-subscription-governance.md)。
* 该策略架构在 [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json) 中发布。 

