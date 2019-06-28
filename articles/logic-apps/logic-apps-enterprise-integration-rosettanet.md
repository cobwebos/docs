---
title: 用于 B2B 企业集成-Azure 逻辑应用的 RosettaNet 消息
description: 交换 Azure 逻辑应用使用 Enterprise Integration Pack 中的 RosettaNet 消息
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 88e02f3fbbca8007fdf479bb973f50c42a878d6e
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333225"
---
# <a name="exchange-rosettanet-messages-for-b2b-enterprise-integration-in-azure-logic-apps"></a>交换 Azure 逻辑应用中的 B2B 企业集成的 RosettaNet 消息 

[RosettaNet](https://resources.gs1us.org)是已建立了共享业务信息的标准过程非盈利组织联盟。 这些标准通常用于供应链流程，并且在半导体、 电子器件、 和物流行业中普遍。 RosettaNet 联盟创建并维护合作伙伴接口流程 (Pip) 提供通用的业务流程定义为所有 RosettaNet 消息交换。 RosettaNet 基于 XML，并定义消息指南，以业务流程的界面和实现框架，可用于公司之间的通信。

在中[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)，RosettaNet 连接器可帮助您创建支持 RosettaNet 标准的集成解决方案。 连接器基于在 RosettaNet 实现框架 (RNIF) 版本 2.0.01。 RNIF 是一种开放式网络应用程序框架，使业务合作伙伴能够协同运行 RosettaNet Pip。 此框架定义的消息结构、 确认、 多用途 Internet 邮件扩展 (MIME) 编码、 和的数字签名需求。

具体而言，该连接器提供这些功能：

* 编码或接收 RosettaNet 消息。
* 解码或发送 RosettaNet 消息。
* 等待响应和生成的失败通知。

对于这些功能，连接器就支持由 RNIF 2.0.01 定义的所有 Pip。 与伙伴的通信可以是同步或异步。

## <a name="rosettanet-concepts"></a>RosettaNet 概念

下面是一些概念和术语。 是唯一的 RosettaNet 规范生成基于 RosettaNet 的集成时，很重要：

* **PIP**

  RosettaNet 组织创建并维护合作伙伴接口流程 (Pip) 提供通用的业务流程定义为所有 RosettaNet 消息交换。 每个 PIP 规范提供的文档类型定义 (DTD) 文件和消息指南文档。 DTD 文件定义的服务内容消息结构。 消息指南文档，这是用户可读的 HTML 文件，指定了元素级的约束。 同时，这些文件提供的业务流程的完整定义。

   按高级业务功能，或群集和子函数或段，Pip 进行分类。 例如，"3A4"采购订单 PIP、"3"时，订单管理函数，"3A"报价和订单录入子功能。 有关详细信息，请参阅[RosettaNet 站点](https://resources.gs1us.org)。

* **Action**

  一部分的 PIP，操作消息是业务合作伙伴之间交换的消息。

* **Signal**

   一部分的 PIP，信号消息是操作消息的响应中发送的确认。

* **单个操作和双操作**

  对于单操作 PIP，则唯一的响应是确认信号消息。 对于双操作 PIP，发起方接收响应消息和答复除了单操作消息流的确认。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* [集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)用于存储你的协议和其他 B2B 项目。 此集成帐户必须与你的 Azure 订阅相关联。

* 在至少两台[合作伙伴](../logic-apps/logic-apps-enterprise-integration-partners.md)，是在集成帐户中定义，并配置下的"DUNS"限定符**业务标识**

* PIP 流程配置，需要发送或接收 RosettaNet 消息集成帐户中。 过程配置存储的所有 PIP 配置特性。 然后，与合作伙伴创建协议时，可以引用此配置。 若要在集成帐户中创建 PIP 流程配置，请参阅[添加 PIP 流程配置](#add-pip)。

* 可选[证书](../logic-apps/logic-apps-enterprise-integration-certificates.md)加密、 解密或签名将上传到集成帐户的消息。 证书是必需的仅当您使用签名或加密。

<a name="add-pip"></a>

## <a name="add-pip-process-configuration"></a>添加 PIP 流程配置

若要将 PIP 流程配置添加到集成帐户，请执行以下步骤：

1. 在中[Azure 门户](https://portal.azure.com)、 查找和打开集成帐户。

1. 上**概述**窗格中，选择**RosettaNet PIP**磁贴。

   ![选择 RosettaNet 磁贴](media/logic-apps-enterprise-integration-rosettanet/select-rosettanet-tile.png)

1. 下**RosettaNet PIP**，选择**添加**。 提供你 PIP 的详细信息。

   ![添加 RosettaNet PIP 详细信息](media/logic-apps-enterprise-integration-rosettanet/add-rosettanet-pip.png)

   | 属性 | 需要 | 描述 |
   |----------|----------|-------------|
   | **Name** | 是 | PIP 名称 |
   | **PIP 代码** | 是 | PIP 三位代码。 有关详细信息，请参阅[RosettaNet Pip](https://docs.microsoft.com/biztalk/adapters-and-accelerators/accelerator-rosettanet/rosettanet-pips)。 |
   | **PIP 版本** | 是 | PIP 的版本号，可根据所选的 PIP 代码 |
   ||||

   有关这些 PIP 属性的详细信息，请访问[RosettaNet 网站](https://resources.gs1us.org/RosettaNet-Standards/Standards-Library/PIP-Directory#1043208-pipsreg)。

1. 完成后，选择**确定**，这将创建的 PIP 配置。

1. 若要查看或编辑过程配置，请选择 PIP，并选择**编辑为 JSON**。

   所有进程的配置设置都来自于 PIP 规范。 逻辑应用将填充的大多数设置是最常用的这些属性值的默认值。

   ![编辑 RosettaNet PIP 配置](media/logic-apps-enterprise-integration-rosettanet/edit-rosettanet-pip.png)

1. 确认设置相应的 PIP 规范中的值相对应，并满足业务需求。 如有必要，更新 JSON 中的值并保存这些更改。

## <a name="create-rosettanet-agreement"></a>创建 RosettaNet 协议

1. 在 [Azure 门户](https://portal.azure.com)中，找到并打开你的集成帐户（如果尚未打开）。

1. 上**概述**窗格中，选择**协议**磁贴。

   ![选择协议磁贴](media/logic-apps-enterprise-integration-rosettanet/select-agreement-tile.png)

1. 在“协议”  下，选择“添加”  。 提供协议详细信息。

   ![添加协议详细信息](media/logic-apps-enterprise-integration-rosettanet/add-agreement-details.png)

   | 属性 | 需要 | 描述 |
   |----------|----------|-------------|
   | **Name** | 是 | 协议名称 |
   | **协议类型** | 是 | 选择**RosettaNet**。 |
   | **主机合作伙伴** | 是 | 协议需要有主机和来宾合作伙伴。 宿主合作伙伴代表配置协议的组织。 |
   | **主机标识** | 是 | 管理方的标识符 |
   | **来宾合作伙伴** | 是 | 协议需要有主机和来宾合作伙伴。 托管方代表与管理方进行交易的组织。 |
   | **来宾标识** | 是 | 托管方的标识符 |
   | **接收设置** | 多种多样 | 这些属性适用于接收到的主机合作伙伴的所有消息 |
   | **发送设置** | 多种多样 | 这些属性适用于发送方的所有消息 |  
   | **RosettaNet PIP 的引用** | 是 | 协议 PIP 引用。 所有 RosettaNet 消息都需要 PIP 配置。 |
   ||||

1. 若要设置你用于从来宾合作伙伴接收传入消息的协议，选择**接收设置**。

   ![接收设置](media/logic-apps-enterprise-integration-rosettanet/add-agreement-receive-details.png)

   1. 若要启用签名或加密的传入消息下,**消息**，选择**应对消息进行签名**或**应对消息进行加密**分别。

      | 属性 | 需要 | 描述 |
      |----------|----------|-------------|
      | **应对消息进行签名** | 否 | 与所选证书的传入消息进行签名。 |
      | **证书** | 是的如果启用签名 | 要用于签名的证书 |
      | **启用消息加密** | 否 | 对与所选证书的传入消息进行加密。 |
      | **证书** | 是的如果启用加密 | 要用于加密的证书 |
      ||||

   1. 在每次选择下选择各自[证书](./logic-apps-enterprise-integration-certificates.md)，以前添加到集成帐户，用于进行签名或加密。

1. 若要设置你的协议将消息发送到来宾合作伙伴，请选择**发送设置**。

   ![发送设置](media/logic-apps-enterprise-integration-rosettanet/add-agreement-send-details.png)

   1. 若要启用签名或加密的传出消息下,**消息**，选择**启用消息签名**或**启用消息加密**分别。 在每个所选内容，选择相应的算法和[证书](./logic-apps-enterprise-integration-certificates.md)，以前添加到集成帐户，用于进行签名或加密。

      | 属性 | 需要 | 描述 |
      |----------|----------|-------------|
      | **启用消息签名** | 否 | 使用所选的签名算法和证书的传出消息进行签名。 |
      | **签名算法** | 是的如果启用签名 | 若要使用的签名算法基于所选的证书 |
      | **证书** | 是的如果启用签名 | 要用于签名的证书 |
      | **启用消息加密** | 否 | 加密传出使用所选的加密算法和证书。 |
      | **加密算法** | 是的如果启用加密 | 加密算法，若要使用，根据所选的证书 |
      | **证书** | 是的如果启用加密 | 要用于加密的证书 |
      ||||

   1. 下**终结点**，指定要用于发送操作消息和确认所需的 Url。

      | 属性 | 需要 | 描述 |
      |----------|----------|-------------|
      | **操作 URL** |  是 | 要用于发送操作消息的 URL。 URL 是必填的字段的同步和异步消息。 |
      | **确认 URL** | 是 | 要用于发送确认消息的 URL。 URL 是必填的字段用于异步消息。 |
      ||||

1. 若要将你与 RosettaNet PIP 引用适用于合作伙伴的协议设置，请选择**RosettaNet PIP 引用**。 下**PIP 名称**，选择你以前创建的 PIP 的名称。

   ![PIP 的引用](media/logic-apps-enterprise-integration-rosettanet/add-agreement-pip-details.png)

   所选内容填充其余的属性，基于您在集成帐户中设置的 PIP。 如果有必要，您可以更改**PIP 角色**。

   ![所选的 PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-selected-pip.png)

完成这些步骤后，你准备好发送或接收 RosettaNet 消息。

## <a name="rosettanet-templates"></a>RosettaNet 模板

若要加快开发速度和建议集成模式，可以进行解码和编码 RosettaNet 消息使用逻辑应用模板。 在创建逻辑应用时，可以从逻辑应用设计器中的模板库中进行选择。 您还可以在这些模板[适用于 Azure 逻辑应用 GitHub 存储库](https://github.com/Azure/logicapps)。

![RosettaNet 模板](media/logic-apps-enterprise-integration-rosettanet/decode-encode-rosettanet-templates.png)

## <a name="receive-or-decode-rosettanet-messages"></a>接收或 RosettaNet 消息解码

1. [创建空白逻辑应用](quickstart-create-first-logic-app-workflow.md)。

1. [将集成帐户链接](logic-apps-enterprise-integration-create-integration-account.md#link-account)到逻辑应用。

1. 添加要解码的 RosettaNet 消息的操作之前，必须添加用于启动逻辑应用，如请求触发器的触发器。

1. 添加后触发器，请选择**新步骤**。

   ![添加请求触发器](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. 在搜索框中，输入"rosettanet"，然后选择以下操作：**RosettaNet 解码**

   ![找到并选择"RosettaNet 解码"操作](media/logic-apps-enterprise-integration-rosettanet/select-decode-rosettanet-action.png)

1. 提供操作的属性的信息：

   ![提供操作详细信息](media/logic-apps-enterprise-integration-rosettanet/decode-action-details.png)

   | 属性 | 需要 | 描述 |
   |----------|----------|-------------|
   | **消息** | 是 | 要解码的 RosettaNet 消息  |
   | **标头** | 是 | 提供的值是 RNIF 版本，版本和响应类型，其中指示合作伙伴之间的通信类型，可以为同步或异步 HTTP 标头 |
   | **角色** | 是 | PIP 中的托管合作伙伴的角色 |
   ||||

   RosettaNet 解码操作中，从输出中的，以及其他属性，包括**出站信号**，您可以选择是否要进行编码并返回给合作伙伴，或对该输出进行任何其他操作。

## <a name="send-or-encode-rosettanet-messages"></a>发送或 RosettaNet 消息编码

1. [创建空白逻辑应用](quickstart-create-first-logic-app-workflow.md)。

1. [将集成帐户链接](logic-apps-enterprise-integration-create-integration-account.md#link-account)到逻辑应用。

1. 可添加对 RosettaNet 消息进行编码的操作之前，必须添加用于启动逻辑应用，如请求触发器的触发器。

1. 添加后触发器，请选择**新步骤**。

   ![添加请求触发器](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. 在搜索框中，输入"rosettanet"，然后选择以下操作：**RosettaNet 编码**

   ![找到并选择"RosettaNet 编码"操作](media/logic-apps-enterprise-integration-rosettanet/select-encode-rosettanet-action.png)

1. 提供操作的属性的信息：

   ![提供操作详细信息](media/logic-apps-enterprise-integration-rosettanet/encode-action-details.png)

   | 属性 | 需要 | 描述 |
   |----------|----------|-------------|
   | **消息** | 是 | 要编码的 RosettaNet 消息  |
   | **主机合作伙伴** | 是 | 主机合作伙伴名称 |
   | **来宾合作伙伴** | 是 | 来宾合作伙伴名称 |
   | **PIP 代码** | 是 | PIP 代码 |
   | **PIP 版本** | 是 | PIP 版本 |  
   | **PIP 实例标识** | 是 | 此 PIP 消息的唯一标识符 |  
   | **消息类型** | 是 | 要编码的消息的类型 |  
   | **角色** | 是 | 主机合作伙伴的角色 |
   ||||

   编码的消息现在已准备好向合作伙伴发送。

1. 若要发送的编码的消息，此示例使用**HTTP**操作，这是重命名为"HTTP-编码的发送给合作伙伴的消息"。

   ![用于发送 RosettaNet 消息的 HTTP 操作](media/logic-apps-enterprise-integration-rosettanet/send-rosettanet-message-to-partner.png)

   每个 RosettaNet 标准业务事务被视为完成仅当定义的 PIP 的所有步骤都都完成时。

1. 主机将编码的消息发送给合作伙伴后，主机将等待信号和确认。 若要完成此任务，将添加**RosettaNet 等待响应**操作。

   ![添加"RosettaNet 等待响应"操作](media/logic-apps-enterprise-integration-rosettanet/rosettanet-wait-for-response-action.png)

   用于等待和重试次数的持续时间基于在集成帐户中的 PIP 配置。 如果未收到响应，此操作将生成失败通知。 若要处理重试次数，始终放**编码**并**等待响应**中的操作**直到**循环。

   ![Until 循环与 RosettaNet 操作](media/logic-apps-enterprise-integration-rosettanet/rosettanet-loop.png)

## <a name="next-steps"></a>后续步骤

* 了解如何使用 [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) 执行验证、转换和其他消息操作。
* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
