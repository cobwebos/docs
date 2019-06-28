---
title: 添加贸易合作伙伴的 B2B 集成-Azure 逻辑应用
description: 创建贸易合作伙伴集成帐户中的使用与 Azure 逻辑应用
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 681f16132c1de2ec5f3b27f80633d32879b0746c
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330116"
---
# <a name="add-trading-partners-to-integration-accounts-for-azure-logic-apps"></a>为 Azure 逻辑应用将贸易合作伙伴添加到集成帐户

在中[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)，可以使用创建自动化的企业到企业 (B2B) 集成工作流[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)使用逻辑应用。 若要表示您的组织和其他人，创建并将贸易合作伙伴作为项目添加到集成帐户。 合作伙伴是参与 B2B 事务并与相互交换消息的实体。

在创建之前这些合作伙伴，请确保讨论并与您有关如何识别和验证其他发送的消息的合作伙伴共享信息。 这些详细信息达成一致后，即可创建集成帐户中的合作伙伴。

## <a name="partner-roles-in-integration-accounts"></a>在集成帐户中的伙伴角色

若要定义有关与你的合作伙伴交换的消息的详细信息，请创建并添加[协议](../logic-apps/logic-apps-enterprise-integration-agreements.md)作为到集成帐户项目。 协议需要至少两个合作伙伴在集成帐户中。 你的组织是始终*主机合作伙伴*协议中。 与你的组织交换消息的组织*来宾合作伙伴*。 来宾合作伙伴可以是另一家公司或甚至是在您自己的组织中的部门。 添加这些合作伙伴后，便可以创建协议。

在协议中，指定用于处理从托管合作伙伴的角度来看的传入和传出消息的详细信息。 对于传入消息，**接收设置**指定主机合作伙伴如何接收从来宾合作伙伴协议中的消息。 对于传出消息，**发送设置**指定主机合作伙伴如何将消息发送到来宾合作伙伴。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* [集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)用于存储你的合作伙伴、 协议和其他 B2B 项目。 此集成帐户必须与你的 Azure 订阅相关联。

## <a name="create-partner"></a>创建合作伙伴

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 主菜单中，选择“所有服务”  。 在搜索框中，输入"集成"，然后选择**集成帐户**。

   ![选择"集成帐户"](./media/logic-apps-enterprise-integration-partners/find-integration-accounts.png)

1. 在“集成帐户”  下，选择要添加合作伙伴的集成帐户。

   ![选择“集成帐户”](./media/logic-apps-enterprise-integration-partners/select-integration-account.png)

1. 选择“合作伙伴”  磁贴。

   ![选择“合作伙伴”磁贴](./media/logic-apps-enterprise-integration-partners/choose-partners.png)

1. 在“合作伙伴”  下，选择“添加”  。 下**添加合作伙伴**，提供合作伙伴的详细信息，如下面的表中所述。

   ![选择"添加"，并提供合作伙伴的详细信息](./media/logic-apps-enterprise-integration-partners/add-partners.png)

   | 属性 | 需要 | 描述 |
   |----------|----------|-------------|
   | **Name** | 是 | 合作伙伴的名称 |
   | **Qualifier** | 是 | 例如，提供到组织中，唯一业务标识的身份验证正文**D-U-N-S (Dun & Bradstreet)** 。 <p>合作伙伴可以选择相互定义的业务标识。 对于这些情况下，选择**双方**对于 EDIFACT 或**双方 (X12)** 适用于 X12。 <p>对于 RosettaNet，选择仅**邓氏**，这是标准。 |
   | **ReplTest1** | 是 | 一个值，标识逻辑应用接收的文档。 <p>RosettaNet，此值必须对应于 duns 的 9 位数字。 |
   ||||

   > [!NOTE]
   > 对于使用 RosettaNet 的合作伙伴，您可以通过首先创建这些合作伙伴指定的其他信息，然后[之后编辑它们](#edit-partner)。

1. 完成后，选择“确定”  。

   你的新合作伙伴现在出现在**合作伙伴**列表。 此外，**合作伙伴**磁贴更新合作伙伴的当前数目。

   ![新的合作伙伴](./media/logic-apps-enterprise-integration-partners/new-partner.png)

<a name="edit-partner"></a>

## <a name="edit-partner"></a>编辑合作伙伴

1. 在 [Azure 门户](https://portal.azure.com)中，查找并选择“集成帐户”。
选择“合作伙伴”  磁贴。

   ![选择“合作伙伴”磁贴](./media/logic-apps-enterprise-integration-partners/edit.png)

1. 下**合作伙伴**，选择你想要编辑和选择的合作伙伴**编辑**。 下**编辑**，使所做的更改。

   ![进行更改和保存更改](./media/logic-apps-enterprise-integration-partners/edit-partner.png)

   有关 RosettaNet 下**RosettaNet 合作伙伴属性**，可以指定此附加信息：

   | 属性 | 需要 | 描述 |
   |----------|----------|-------------|
   | **合作伙伴分类** | 否 | 合作伙伴的组织类型 |
   | **供应链代码** | 否 | 合作伙伴的供应链代码，例如，"信息技术"或"电子组件" |
   | **联系人姓名** | 否 | 合作伙伴的联系人姓名 |
   | **电子邮件** | 否 | 合作伙伴的电子邮件地址 |
   | **Fax** | 否 | 合作伙伴的传真号码 |
   | **电话** | 否 | 合作伙伴的电话号码 |
   ||||

1. 完成后，选择**确定**以保存所做的更改。

## <a name="delete-partner"></a>删除合作伙伴

1. 在 [Azure 门户](https://portal.azure.com)中，查找并选择“集成帐户”。 选择“合作伙伴”  磁贴。

   ![选择“合作伙伴”磁贴](./media/logic-apps-enterprise-integration-partners/choose-partners-to-delete.png)

1. 在“合作伙伴”  下，选择要删除的合作伙伴。 选择“删除”  。

   ![删除合作伙伴](./media/logic-apps-enterprise-integration-partners/delete-partner.png)

## <a name="next-steps"></a>后续步骤

* 详细了解[协议](../logic-apps/logic-apps-enterprise-integration-agreements.md)