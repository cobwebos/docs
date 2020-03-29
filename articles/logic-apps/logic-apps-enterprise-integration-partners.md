---
title: 为 B2B 集成添加贸易伙伴
description: 在集成帐户中创建交易伙伴，以便与 Azure 逻辑应用一起使用
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: e58cbe85f30ea09adde45d55bb7b80c710c45495
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792440"
---
# <a name="add-trading-partners-to-integration-accounts-for-azure-logic-apps"></a>将贸易伙伴添加到 Azure 逻辑应用的集成帐户

在[Azure 逻辑应用中](../logic-apps/logic-apps-overview.md)，可以使用逻辑应用的[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)创建自动企业对企业 （B2B） 集成工作流。 为了表示您的组织和其他人，您可以创建贸易伙伴并将其添加为集成帐户中的工件。 合作伙伴是参与 B2B 事务并相互交换消息的实体。

在创建这些合作伙伴之前，请确保与合作伙伴讨论和共享有关如何识别和验证对方发送的消息的信息。 就这些详细信息达成一致后，您就可以在集成帐户中创建合作伙伴了。

## <a name="partner-roles-in-integration-accounts"></a>集成帐户中的合作伙伴角色

要定义与合作伙伴交换的消息的详细信息，请创建[协议](../logic-apps/logic-apps-enterprise-integration-agreements.md)并将其添加为集成帐户中的项目。 协议需要集成帐户中至少两个合作伙伴。 您的组织始终是协议中的*主机合作伙伴*。 与您的组织交换消息的组织是*来宾合作伙伴*。 客户合作伙伴可以是另一家公司，甚至可以是您自己组织中的部门。 添加这些合作伙伴后，便可以创建协议。

在协议中，从主机合作伙伴的角度指定处理传入和传出消息的详细信息。 对于传入邮件，"**接收设置"** 指定主机合作伙伴如何接收协议中的来宾合作伙伴的消息。 对于传出邮件，"**发送设置"** 指定主机合作伙伴如何向来宾合作伙伴发送消息。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 用于存储合作伙伴、协议和其他 B2B 工件的[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。 此集成帐户必须与 Azure 订阅关联。

## <a name="create-partner"></a>创建合作伙伴

1. 登录到 Azure[门户](https://portal.azure.com)。

1. 在 Azure 主菜单中，选择“所有服务”****。 在搜索框中，输入"集成"，然后选择 **"集成帐户**"。

   ![选择"集成帐户"](./media/logic-apps-enterprise-integration-partners/find-integration-accounts.png)

1. 在“集成帐户”**** 下，选择要添加合作伙伴的集成帐户。

   ![选择“集成帐户”](./media/logic-apps-enterprise-integration-partners/select-integration-account.png)

1. 选择“合作伙伴”**** 磁贴。

   ![选择“合作伙伴”磁贴](./media/logic-apps-enterprise-integration-partners/choose-partners.png)

1. 在“合作伙伴”**** 下，选择“添加”****。 在 **"添加合作伙伴**"下，提供下表所述合作伙伴的详细信息。

   ![选择"添加"并提供合作伙伴详细信息](./media/logic-apps-enterprise-integration-partners/add-partners.png)

   | properties | 必选 | 描述 |
   |----------|----------|-------------|
   | **名称** | 是 | 合作伙伴姓名 |
   | **限定 符** | 是 | 向组织提供唯一业务标识的身份验证机构，例如**D-U-N-S（Dun & Bradstreet）。** <p>合作伙伴可以选择相互定义的业务标识。 对于这些方案，选择用于 EDIFACT**的相互定义**或**X12 的互定义 （X12）。** <p>对于罗塞塔网，仅选择**DUNS**，这是标准。 |
   | **价值** | 是 | 标识逻辑应用接收的文档的值。 <p>对于罗塞塔网，此值必须是对应于 DUNS 编号的九位数字。 |
   ||||

   > [!NOTE]
   > 对于使用罗塞塔网的合作伙伴，可以通过首先创建这些合作伙伴，然后[编辑它们](#edit-partner)来指定其他信息。

1. 完成后，选择“确定”****。

   您的新合作伙伴现在将显示在**合作伙伴**列表中。 此外，"**合作伙伴"** 磁贴更新当前合作伙伴数。

   ![新合作伙伴](./media/logic-apps-enterprise-integration-partners/new-partner.png)

<a name="edit-partner"></a>

## <a name="edit-partner"></a>编辑合作伙伴

1. 在 [Azure 门户](https://portal.azure.com)中，查找并选择“集成帐户”。
选择“合作伙伴”**** 磁贴。

   ![选择“合作伙伴”磁贴](./media/logic-apps-enterprise-integration-partners/edit.png)

1. 在 **"合作伙伴**"下，选择要编辑的合作伙伴，然后选择 **"编辑**"。 在 **"编辑"** 下，进行更改。

   ![进行更改和保存更改](./media/logic-apps-enterprise-integration-partners/edit-partner.png)

   对于罗塞塔网，在**罗塞塔网合作伙伴属性**下，您可以指定以下附加信息：

   | properties | 必选 | 描述 |
   |----------|----------|-------------|
   | **合作伙伴分类** | 否 | 合作伙伴的组织类型 |
   | **供应链代码** | 否 | 合作伙伴的供应链代码，例如"信息技术"或"电子组件" |
   | **联系人姓名** | 否 | 合作伙伴的联系人姓名 |
   | **电子邮件** | 否 | 合作伙伴的电子邮件地址 |
   | **传真** | 否 | 合作伙伴的传真号码 |
   | **电话** | 否 | 合作伙伴的电话号码 |
   ||||

1. 完成后，选择 **"确定"** 以保存更改。

## <a name="delete-partner"></a>删除合作伙伴

1. 在 [Azure 门户](https://portal.azure.com)中，查找并选择“集成帐户”。 选择“合作伙伴”**** 磁贴。

   ![选择“合作伙伴”磁贴](./media/logic-apps-enterprise-integration-partners/choose-partners-to-delete.png)

1. 在“合作伙伴”**** 下，选择要删除的合作伙伴。 选择 **"删除**"。

   ![删除合作伙伴](./media/logic-apps-enterprise-integration-partners/delete-partner.png)

## <a name="next-steps"></a>后续步骤

* 了解有关协议的更多[内容](../logic-apps/logic-apps-enterprise-integration-agreements.md)