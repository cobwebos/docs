---
title: 用于 B2B 集成的 RosettaNet 消息-Azure 逻辑应用
description: 在 Azure 逻辑应用中通过 Enterprise Integration Pack 交换 RosettaNet 消息
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 570c7907f320b881e2db0bd45cdce311490f4f45
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680334"
---
# <a name="exchange-rosettanet-messages-for-b2b-enterprise-integration-in-azure-logic-apps"></a>Azure 逻辑应用中用于 B2B 企业集成的 Exchange RosettaNet 消息

[RosettaNet](https://resources.gs1us.org)是为共享业务信息而建立了标准流程的非盈利联盟。 这些标准通常用于供应链流程，并在半导体、电子和物流行业中广泛使用。 RosettaNet 联盟创建并维护合作伙伴接口流程（Pip），后者为所有 RosettaNet 消息交换提供通用的业务流程定义。 RosettaNet 基于 XML，并定义了消息准则、业务流程的接口，以及用于公司间通信的实现框架。

在[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)中，RosettaNet 连接器可帮助你创建支持 RosettaNet 标准的集成解决方案。 连接器基于 RosettaNet 实现框架（RNIF）版本2.0.01。 RNIF 是一种开放的网络应用程序框架，可让业务合作伙伴协作运行 RosettaNet Pip。 此框架定义消息结构、确认需要、多用途 Internet 邮件扩展（MIME）编码和数字签名。

具体而言，连接器提供以下功能：

* 编码或接收 RosettaNet 消息。
* 解码或发送 RosettaNet 消息。
* 等待响应并生成失败通知。

对于这些功能，连接器支持由 RNIF 2.0.01 定义的所有 Pip。 与合作伙伴的通信可以是同步的，也可以是异步的。

## <a name="rosettanet-concepts"></a>RosettaNet 概念

下面是有关 RosettaNet 规范的一些概念和术语，在生成基于 RosettaNet 的集成时非常重要：

* **PIP**

  RosettaNet 组织创建并维护合作伙伴接口流程（Pip），后者为所有 RosettaNet 消息交换提供通用的业务流程定义。 每个 PIP 规范都提供文档类型定义（DTD）文件和消息准则文档。 DTD 文件定义了服务内容消息结构。 消息指南文档是用户可读的 HTML 文件，它指定了元素级约束。 这些文件共同提供了业务流程的完整定义。

   Pip 按高级业务函数、群集、subfunction 或段进行分类。 例如，"3A4" 是采购订单的 PIP，而 "3" 是订单管理功能，"3A" 是引号 & Order Entry subfunction。 有关详细信息，请参阅[RosettaNet 站点](https://resources.gs1us.org)。

* **Action**

  PIP 操作消息是在合作伙伴之间交换的业务消息。

* **指明**

   PIP 信号消息的一部分是为了响应操作消息而发送的确认消息。

* **单个操作和双重操作**

  对于单操作 PIP，唯一的响应是确认信号消息。 对于双重操作 PIP，发起方将收到一条响应消息，并使用确认除了单操作消息流之外，还会回复。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 用于存储协议和其他 B2B 项目的[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。 此集成帐户必须与你的 Azure 订阅相关联。

* 在集成帐户中定义了至少两个[合作伙伴](../logic-apps/logic-apps-enterprise-integration-partners.md)，并且在**业务标识**下配置了 "DUNS" 限定符

* 在集成帐户中发送或接收 RosettaNet 消息所需的 PIP 流程配置。 过程配置存储所有 PIP 配置特征。 然后，你可以在创建与合作伙伴的协议时引用此配置。 若要在集成帐户中创建 PIP 进程配置，请参阅[添加 pip 流程配置](#add-pip)。

* 用于对上传到集成帐户的消息进行加密、解密或签名的可选[证书](../logic-apps/logic-apps-enterprise-integration-certificates.md)。 仅当使用签名或加密时，才需要使用证书。

<a name="add-pip"></a>

## <a name="add-pip-process-configuration"></a>添加 PIP 过程配置

若要向集成帐户添加 PIP 过程配置，请执行以下步骤：

1. 在[Azure 门户](https://portal.azure.com)中，找到并打开你的集成帐户。

1. 在 "**概述**" 窗格上，选择 " **RosettaNet PIP** " 磁贴。

   ![选择 RosettaNet 磁贴](media/logic-apps-enterprise-integration-rosettanet/select-rosettanet-tile.png)

1. 在**ROSETTANET PIP**下，选择 "**添加**"。 提供 PIP 详细信息。

   ![添加 RosettaNet PIP 详细信息](media/logic-apps-enterprise-integration-rosettanet/add-rosettanet-pip.png)

   | properties | 需要 | 描述 |
   |----------|----------|-------------|
   | 名称 | 是 | PIP 名称 |
   | **PIP 代码** | 是 | PIP 三位代码。 有关详细信息，请参阅[RosettaNet pip](https://docs.microsoft.com/biztalk/adapters-and-accelerators/accelerator-rosettanet/rosettanet-pips)。 |
   | **PIP 版本** | 是 | PIP 版本号，可根据所选 PIP 代码提供 |
   ||||

   有关这些 PIP 属性的详细信息，请访问[RosettaNet 网站](https://resources.gs1us.org/RosettaNet-Standards/Standards-Library/PIP-Directory#1043208-pipsreg)。

1. 完成后，选择 **"确定"** ，这将创建 PIP 配置。

1. 若要查看或编辑过程配置，请选择 PIP，然后选择 "**编辑为 JSON**"。

   所有进程配置设置都来自 PIP 规范。 逻辑应用使用默认值填充大多数设置，这些默认值是这些属性最常使用的值。

   ![编辑 RosettaNet PIP 配置](media/logic-apps-enterprise-integration-rosettanet/edit-rosettanet-pip.png)

1. 确认这些设置对应于适当 PIP 规范中的值并满足你的业务需求。 如有必要，请更新 JSON 中的值并保存这些更改。

## <a name="create-rosettanet-agreement"></a>创建 RosettaNet 协议

1. 在 [Azure 门户](https://portal.azure.com)中，找到并打开你的集成帐户（如果尚未打开）。

1. 在 "**概述**" 窗格上，选择 "**协议**" 磁贴。

   ![选择协议磁贴](media/logic-apps-enterprise-integration-rosettanet/select-agreement-tile.png)

1. 在“协议”下，选择“添加”。 提供协议详细信息。

   ![添加协议详细信息](media/logic-apps-enterprise-integration-rosettanet/add-agreement-details.png)

   | properties | 需要 | 描述 |
   |----------|----------|-------------|
   | 名称 | 是 | 协议名称 |
   | **协议类型** | 是 | 选择 " **RosettaNet**"。 |
   | **主机伙伴** | 是 | 协议需要有主机和来宾合作伙伴。 宿主合作伙伴代表配置协议的组织。 |
   | **主机标识** | 是 | 管理方的标识符 |
   | **来宾合作伙伴** | 是 | 协议需要有主机和来宾合作伙伴。 托管方代表与管理方进行交易的组织。 |
   | **来宾标识** | 是 | 托管方的标识符 |
   | **接收设置** | 多种多样 | 这些属性适用于主机伙伴接收的所有消息 |
   | **发送设置** | 多种多样 | 这些属性适用于主机伙伴发送的所有消息 |  
   | **RosettaNet PIP 引用** | 是 | 协议的 PIP 引用。 所有 RosettaNet 消息都需要 PIP 配置。 |
   ||||

1. 若要设置用于从来宾合作伙伴接收传入消息的协议，请选择 "**接收设置**"。

   ![接收设置](media/logic-apps-enterprise-integration-rosettanet/add-agreement-receive-details.png)

   1. 若要为传入消息启用签名或加密，请在 "**消息**" 下，选择 "**应对消息进行签名**" 或**应分别加密消息**。

      | properties | 需要 | 描述 |
      |----------|----------|-------------|
      | **应对消息进行签名** | No | 用所选证书对传入消息进行签名。 |
      | **证书** | 是（如果启用了签名） | 要用于签名的证书 |
      | **启用消息加密** | No | 用所选证书对传入消息进行加密。 |
      | **证书** | 是，如果已启用加密 | 要用于加密的证书 |
      ||||

   1. 在每个选择下，选择以前添加到集成帐户的相应[证书](./logic-apps-enterprise-integration-certificates.md)，以用于签名或加密。

1. 若要设置向来宾合作伙伴发送消息的协议，请选择 "**发送设置**"。

   ![发送设置](media/logic-apps-enterprise-integration-rosettanet/add-agreement-send-details.png)

   1. 若要为传出消息启用签名或加密，请在 "**消息**" 下选择 "**启用消息签名**" 或 "**启用消息加密**"。 在每个选择下，选择你以前添加到集成帐户的相应算法和[证书](./logic-apps-enterprise-integration-certificates.md)，以用于签名或加密。

      | properties | 需要 | 描述 |
      |----------|----------|-------------|
      | **启用消息签名** | No | 用所选的签名算法和证书对传出消息进行签名。 |
      | **签名算法** | 是（如果启用了签名） | 基于所选证书使用的签名算法 |
      | **证书** | 是（如果启用了签名） | 要用于签名的证书 |
      | **启用消息加密** | No | 用所选加密算法和证书加密传出。 |
      | **加密算法** | 是，如果已启用加密 | 基于所选证书使用的加密算法 |
      | **证书** | 是，如果已启用加密 | 要用于加密的证书 |
      ||||

   1. 在 "**终结点**" 下，指定用于发送操作消息和确认的必需 url。

      | properties | 需要 | 描述 |
      |----------|----------|-------------|
      | **操作 URL** |  是 | 用于发送操作消息的 URL。 URL 是同步和异步消息的必填字段。 |
      | **确认 URL** | 是 | 用于发送确认消息的 URL。 URL 是异步消息的必填字段。 |
      ||||

1. 若要设置适用于合作伙伴的 RosettaNet PIP 引用的协议，请选择 " **ROSETTANET pip 引用**"。 在 " **Pip 名称**" 下，选择先前创建的 PIP 的名称。

   ![PIP 引用](media/logic-apps-enterprise-integration-rosettanet/add-agreement-pip-details.png)

   你的选择会填充其余属性，这些属性基于你在集成帐户中设置的 PIP。 如果需要，可以更改**PIP 角色**。

   ![所选 PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-selected-pip.png)

完成这些步骤后，就可以发送或接收 RosettaNet 消息了。

## <a name="rosettanet-templates"></a>RosettaNet 模板

若要加速开发和推荐集成模式，可以使用逻辑应用模板解码和编码 RosettaNet 消息。 创建逻辑应用时，可以从逻辑应用设计器的模板库中进行选择。 你还可以在[适用于 Azure 逻辑应用的 GitHub 存储库](https://github.com/Azure/logicapps)中找到这些模板。

![RosettaNet 模板](media/logic-apps-enterprise-integration-rosettanet/decode-encode-rosettanet-templates.png)

## <a name="receive-or-decode-rosettanet-messages"></a>接收或解码 RosettaNet 消息

1. [创建空白逻辑应用](quickstart-create-first-logic-app-workflow.md)。

1. 将[集成帐户链接](logic-apps-enterprise-integration-create-integration-account.md#link-account)到逻辑应用。

1. 在添加用于解码 RosettaNet 消息的操作之前，必须添加用于启动逻辑应用的触发器，如请求触发器。

1. 添加触发器后，选择 "**新建步骤**"。

   ![添加请求触发器](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. 在搜索框中，输入 "rosettanet"，并选择此操作： **Rosettanet 解码**

   ![查找并选择 "RosettaNet 解码" 操作](media/logic-apps-enterprise-integration-rosettanet/select-decode-rosettanet-action.png)

1. 提供操作属性的信息：

   ![提供操作详细信息](media/logic-apps-enterprise-integration-rosettanet/decode-action-details.png)

   | properties | 需要 | 描述 |
   |----------|----------|-------------|
   | **消息** | 是 | 要解码的 RosettaNet 消息  |
   | **标头** | 是 | 为版本提供值的 HTTP 标头，即 RNIF 版本和响应类型，指示伙伴之间的通信类型，并且可以是同步或异步 |
   | **角色** | 是 | 主机伙伴在 PIP 中的角色 |
   ||||

   从 RosettaNet 解码操作中，输出与其他属性一起包括**出站信号**，您可以选择对其进行编码并返回到合作伙伴，或对该输出执行任何其他操作。

## <a name="send-or-encode-rosettanet-messages"></a>发送或编码 RosettaNet 消息

1. [创建空白逻辑应用](quickstart-create-first-logic-app-workflow.md)。

1. 将[集成帐户链接](logic-apps-enterprise-integration-create-integration-account.md#link-account)到逻辑应用。

1. 在添加操作以编码 RosettaNet 消息之前，必须添加用于启动逻辑应用的触发器，如请求触发器。

1. 添加触发器后，选择 "**新建步骤**"。

   ![添加请求触发器](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. 在搜索框中，输入 "rosettanet"，并选择此操作： **Rosettanet 编码**

   ![查找并选择 "RosettaNet 编码" 操作](media/logic-apps-enterprise-integration-rosettanet/select-encode-rosettanet-action.png)

1. 提供操作属性的信息：

   ![提供操作详细信息](media/logic-apps-enterprise-integration-rosettanet/encode-action-details.png)

   | properties | 需要 | 描述 |
   |----------|----------|-------------|
   | **消息** | 是 | 要编码的 RosettaNet 消息  |
   | **主机伙伴** | 是 | 主机伙伴名称 |
   | **来宾合作伙伴** | 是 | 来宾合作伙伴名称 |
   | **PIP 代码** | 是 | PIP 代码 |
   | **PIP 版本** | 是 | PIP 版本 |  
   | **PIP 实例标识** | 是 | 此 PIP 消息的唯一标识符 |  
   | **消息类型** | 是 | 要编码的消息的类型 |  
   | **角色** | 是 | 主机伙伴的角色 |
   ||||

   编码的消息现在已准备好发送到合作伙伴。

1. 若要发送编码的消息，此示例使用**http**操作，该操作已重命名为 "Http-向伙伴发送编码的消息"。

   ![用于发送 RosettaNet 消息的 HTTP 操作](media/logic-apps-enterprise-integration-rosettanet/send-rosettanet-message-to-partner.png)

   按照 RosettaNet 标准，仅当 PIP 定义的所有步骤都完成时，业务事务才被视为已完成。

1. 主机将编码的消息发送给合作伙伴后，主机会等待信号和确认。 若要完成此任务，请添加**RosettaNet 等待响应**操作。

   ![添加 "RosettaNet 等待响应" 操作](media/logic-apps-enterprise-integration-rosettanet/rosettanet-wait-for-response-action.png)

   用于等待的持续时间和重试次数取决于集成帐户中的 PIP 配置。 如果未收到响应，此操作将生成失败通知。 若要处理重试，请始终将**编码**置于**Until**循环中并**等待响应**操作。

   ![Until 循环 with RosettaNet 操作](media/logic-apps-enterprise-integration-rosettanet/rosettanet-loop.png)

## <a name="next-steps"></a>后续步骤

* 了解如何使用 [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) 执行验证、转换和其他消息操作。
* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
