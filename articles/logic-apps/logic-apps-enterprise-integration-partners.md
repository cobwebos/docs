---
title: 添加用于 B2B 集成的贸易合作伙伴
description: 在集成帐户中创建用于 Azure 逻辑应用的贸易合作伙伴
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: e58cbe85f30ea09adde45d55bb7b80c710c45495
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792440"
---
# <a name="add-trading-partners-to-integration-accounts-for-azure-logic-apps"></a>向 Azure 逻辑应用的集成帐户添加贸易合作伙伴

在[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)中，可以通过将[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)与逻辑应用配合使用来创建自动化的企业到企业（B2B）集成工作流。 为表示你的组织和其他人，你可以创建贸易合作伙伴并将其作为项目添加到集成帐户。 合作伙伴是参与 B2B 交易和交换消息的实体。

创建这些合作伙伴之前，请务必与合作伙伴讨论并共享相关信息，了解如何识别和验证其他发送的消息。 在您同意这些详细信息后，您就可以在集成帐户中创建合作伙伴了。

## <a name="partner-roles-in-integration-accounts"></a>集成帐户中的合作伙伴角色

若要定义与合作伙伴交换的消息的详细信息，请创建[协议](../logic-apps/logic-apps-enterprise-integration-agreements.md)，并将其作为项目添加到集成帐户。 协议要求你的集成帐户中至少有两个合作伙伴。 组织始终是协议中的*主机伙伴*。 与组织交换消息的组织是*来宾合作伙伴*。 来宾合作伙伴可以是其他公司，甚至是自己的组织中的部门。 添加这些合作伙伴后，便可以创建协议。

在协议中，指定用于处理来自主机伙伴的透视的传入和传出消息的详细信息。 对于传入消息，**接收设置**指定了主机伙伴如何从协议中的来宾合作伙伴接收消息。 对于传出消息，**发送设置**指定主机伙伴向来宾合作伙伴发送消息的方式。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 用于存储合作伙伴、协议和其他 B2B 项目的[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。 此集成帐户必须与你的 Azure 订阅相关联。

## <a name="create-partner"></a>创建合作伙伴

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 主菜单中，选择“所有服务”。 在搜索框中输入 "集成"，并选择 "**集成帐户**"。

   ![选择 "集成帐户"](./media/logic-apps-enterprise-integration-partners/find-integration-accounts.png)

1. 在“集成帐户”下，选择要添加合作伙伴的集成帐户。

   ![选择“集成帐户”](./media/logic-apps-enterprise-integration-partners/select-integration-account.png)

1. 选择“合作伙伴”磁贴。

   ![选择“合作伙伴”磁贴](./media/logic-apps-enterprise-integration-partners/choose-partners.png)

1. 在“合作伙伴”下，选择“添加”。 在 "**添加合作伙伴**" 下，按下表所述提供合作伙伴的详细信息。

   ![选择 "添加" 并提供合作伙伴详细信息](./media/logic-apps-enterprise-integration-partners/add-partners.png)

   | properties | 需要 | 描述 |
   |----------|----------|-------------|
   | 名称 | 是 | 合作伙伴的名称 |
   | **限定符** | 是 | 向组织提供唯一业务标识的身份验证正文，例如， **d-link & Bradstreet）** 。 <p>合作伙伴可以选择使用双方定义的业务标识。 对于这些方案，请选择 "为 EDIFACT**定义的相互定义**" 或 "用于 X12 的**双方定义的（x12）** "。 <p>对于 RosettaNet，仅选择 " **DUNS**"，这是标准。 |
   | **值** | 是 | 一个值，该值标识逻辑应用接收的文档。 <p>对于 RosettaNet，此值必须是对应于 DUNS 数的9位数字。 |
   ||||

   > [!NOTE]
   > 对于使用 RosettaNet 的合作伙伴，你可以通过先创建这些伙伴并随后对其进行[编辑](#edit-partner)来指定其他信息。

1. 完成后，选择“确定”。

   你的新合作伙伴现在会显示在 "**合作伙伴**" 列表中。 此外，**伙伴**磁贴会更新当前的合作伙伴数。

   ![新合作伙伴](./media/logic-apps-enterprise-integration-partners/new-partner.png)

<a name="edit-partner"></a>

## <a name="edit-partner"></a>编辑合作伙伴

1. 在 [Azure 门户](https://portal.azure.com)中，查找并选择“集成帐户”。
选择“合作伙伴”磁贴。

   ![选择“合作伙伴”磁贴](./media/logic-apps-enterprise-integration-partners/edit.png)

1. 在 "**合作伙伴**" 下，选择要编辑的合作伙伴，然后选择 "**编辑**"。 在 "**编辑**" 下进行更改。

   ![进行更改和保存更改](./media/logic-apps-enterprise-integration-partners/edit-partner.png)

   对于 RosettaNet，在 " **Rosettanet 合作伙伴属性**" 下，你可以指定以下附加信息：

   | properties | 需要 | 描述 |
   |----------|----------|-------------|
   | **合作伙伴分类** | No | 合作伙伴的组织类型 |
   | **供应链代码** | No | 合作伙伴的供应链代码，如 "信息技术" 或 "电子组件" |
   | **联系人姓名** | No | 合作伙伴的联系人姓名 |
   | **电子邮件** | No | 合作伙伴的电子邮件地址 |
   | **Fax** | No | 合作伙伴的传真号码 |
   | **电话** | No | 合作伙伴的电话号码 |
   ||||

1. 完成后，选择 **"确定"** 以保存所做的更改。

## <a name="delete-partner"></a>删除合作伙伴

1. 在 [Azure 门户](https://portal.azure.com)中，查找并选择“集成帐户”。 选择“合作伙伴”磁贴。

   ![选择“合作伙伴”磁贴](./media/logic-apps-enterprise-integration-partners/choose-partners-to-delete.png)

1. 在“合作伙伴”下，选择要删除的合作伙伴。 选择“删除”。

   ![删除合作伙伴](./media/logic-apps-enterprise-integration-partners/delete-partner.png)

## <a name="next-steps"></a>后续步骤

* 了解有关[协议](../logic-apps/logic-apps-enterprise-integration-agreements.md)的详细信息