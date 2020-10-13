---
title: 为 B2B 集成添加贸易合作伙伴
description: 在要与 Azure 逻辑应用配合使用的集成帐户中创建贸易合作伙伴
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 8e3805fae5bf6cc5ad8cf759d3ba75220c6ddbd8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91565065"
---
# <a name="add-trading-partners-to-integration-accounts-for-azure-logic-apps"></a>向 Azure 逻辑应用的集成帐户添加贸易合作伙伴

在 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)中，可以通过将[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)与逻辑应用配合使用来创建自动化的企业到企业 (B2B) 集成工作流。 可以创建代表你的组织和他人的组织的贸易合作伙伴并将其作为项目添加到集成帐户。 合作伙伴是参与 B2B 交易并相互交换消息的实体。

创建这些合作伙伴之前，请务必与合作伙伴讨论并共享相关信息，明确如何确定和验证另一方发送的消息。 在认可这些详细信息后，就可以在集成帐户中创建合作伙伴了。

## <a name="partner-roles-in-integration-accounts"></a>集成帐户中的合作伙伴角色

若要明确与合作伙伴交换的消息的详情，请创建[协议](../logic-apps/logic-apps-enterprise-integration-agreements.md)并将其作为项目添加到集成帐户。 协议要求集成帐户中至少有两个合作伙伴。 在协议中，你的组织始终是主机合作伙伴  。 与你的组织交换消息的组织是来宾合作伙伴  。 来宾合作伙伴可以是其他公司，甚至是你自己的组织中的部门。 添加这些合作伙伴后，便可以创建协议。

在协议中，你从主机合作伙伴的角度详细指定如何处理传入和传出消息。 对于传入消息，可以通过“接收设置”指定主机合作伙伴如何从协议中的来宾合作伙伴处接收消息。  对于传出消息，可以通过“发送设置”  指定主机合作伙伴如何向来宾合作伙伴发送消息。

## <a name="prerequisites"></a>必备条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 用于存储合作伙伴、协议和其他 B2B 项目的[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。 此集成帐户必须与 Azure 订阅相关联。

## <a name="create-partner"></a>创建合作伙伴

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 主菜单中，选择“所有服务”  。 在搜索框中输入“集成”，然后选择“集成帐户”  。

   ![选择“集成帐户”](./media/logic-apps-enterprise-integration-partners/find-integration-accounts.png)

1. 在“集成帐户”  下，选择要添加合作伙伴的集成帐户。

   ![选择“集成帐户”](./media/logic-apps-enterprise-integration-partners/select-integration-account.png)

1. 选择“合作伙伴”  磁贴。

   ![显示 "合作伙伴" 磁贴的屏幕截图。](./media/logic-apps-enterprise-integration-partners/choose-partners.png)

1. 在“合作伙伴”  下，选择“添加”  。 在“添加合作伙伴”  下提供合作伙伴的详细信息，如下表所述。

   ![选择“添加”，提供合作伙伴详细信息](./media/logic-apps-enterprise-integration-partners/add-partners.png)

   | properties | 必选 | 说明 |
   |----------|----------|-------------|
   | **名称** | 是 | 合作伙伴的名称 |
   | **Qualifier** | 是 | 身份验证正文，用于向组织提供唯一业务标识，例如 **D-U-N-S (Dun & Bradstreet)** 。 <p>合作伙伴可以选择使用双方约定的业务标识。 对于这些方案，请为 EDIFACT 选择“双方约定”，或为 X12 选择“双方约定(X12)”。   <p>对于 RosettaNet，请仅选择“DUNS”  ，这是标准。 |
   | **值** | 是 | 一个值，用于标识逻辑应用接收的文档。 <p>对于 RosettaNet，此值必须是对应于 DUNS 号的 9 位数字。 |
   ||||

   > [!NOTE]
   > 对于使用 RosettaNet 的合作伙伴，可以先创建这些合作伙伴，[然后编辑它们](#edit-partner)，通过这种方式指定其他信息。

1. 完成后，选择“确定”  。

   现在会在“合作伙伴”列表中显示新合作伙伴。  此外，“合作伙伴”  磁贴会更新当前的合作伙伴数。

   ![新合作伙伴](./media/logic-apps-enterprise-integration-partners/new-partner.png)

<a name="edit-partner"></a>

## <a name="edit-partner"></a>编辑合作伙伴

1. 在 [Azure 门户](https://portal.azure.com)中，查找并选择“集成帐户”。
选择“合作伙伴”  磁贴。

   ![选择“合作伙伴”磁贴](./media/logic-apps-enterprise-integration-partners/edit.png)

1. 在“合作伙伴”  下选择要编辑的合作伙伴，然后选择“编辑”。  在“编辑”下进行更改。 

   ![进行更改和保存更改](./media/logic-apps-enterprise-integration-partners/edit-partner.png)

   对于 RosettaNet，可以在“RosettaNet 合作伙伴属性”下指定以下附加信息： 

   | properties | 必选 | 说明 |
   |----------|----------|-------------|
   | **合作伙伴分类** | 否 | 合作伙伴的组织类型 |
   | **供应链代码** | 否 | 合作伙伴的供应链代码，例如“信息技术”或“电子组件” |
   | **联系人姓名** | 否 | 合作伙伴的联系人姓名 |
   | **电子邮件** | 否 | 合作伙伴的电子邮件地址 |
   | **Fax** | 否 | 合作伙伴的传真号码 |
   | **电话** | 否 | 合作伙伴的电话号码 |
   ||||

1. 完成后选择“确定”，保存所做的更改。 

## <a name="delete-partner"></a>删除合作伙伴

1. 在 [Azure 门户](https://portal.azure.com)中，查找并选择“集成帐户”。 选择“合作伙伴”  磁贴。

   ![显示要删除合作伙伴时选择的 "合作伙伴" 磁贴的屏幕截图。](./media/logic-apps-enterprise-integration-partners/choose-partners-to-delete.png)

1. 在“合作伙伴”  下，选择要删除的合作伙伴。 选择“删除”  。

   ![删除合作伙伴](./media/logic-apps-enterprise-integration-partners/delete-partner.png)

## <a name="next-steps"></a>后续步骤

* 详细了解[协议](../logic-apps/logic-apps-enterprise-integration-agreements.md)