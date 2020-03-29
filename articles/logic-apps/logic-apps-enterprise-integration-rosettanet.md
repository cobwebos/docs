---
title: 用于 B2B 集成的罗塞塔网消息
description: 将 Azure 逻辑应用中的罗塞塔网消息与企业集成包交换
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 2cc2ac08b9624c1d1d9bee9ce91a7c91189d7f2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792417"
---
# <a name="exchange-rosettanet-messages-for-b2b-enterprise-integration-in-azure-logic-apps"></a>在 Azure 逻辑应用中为 B2B 企业集成交换罗塞塔网消息

[罗塞塔网](https://resources.gs1us.org)是一个非营利性财团，已经建立了共享业务信息的标准流程。 这些标准通常用于供应链流程，在半导体、电子和物流行业广泛分布。 罗塞塔网联盟创建和维护合作伙伴接口流程 （PPs），为所有罗塞塔网消息交换提供通用业务流程定义。 RosettaNet 基于 XML，定义了消息准则、业务流程接口以及公司之间通信的实现框架。

在[Azure 逻辑应用中](../logic-apps/logic-apps-overview.md)，罗塞塔网连接器可帮助您创建支持罗塞塔网标准的集成解决方案。 该连接器基于罗塞塔网实施框架 （RNIF） 版本 2.0.01。 RNIF 是一个开放的网络应用程序框架，使业务合作伙伴能够协作运行罗塞塔网 PIP。 此框架定义邮件结构、确认需求、多用途 Internet 邮件扩展 （MIME） 编码和数字签名。

具体而言，连接器提供以下功能：

* 编码或接收罗塞塔网消息。
* 解码或发送罗塞塔网消息。
* 等待故障通知的响应和生成。

对于这些功能，连接器支持由 RNIF 2.0.01 定义的所有 PIP。 与合作伙伴的通信可以是同步的，也可以是异步的。

## <a name="rosettanet-concepts"></a>罗塞塔网概念

以下是罗塞塔网规范独有的一些概念和术语，在构建基于罗塞塔网的集成时非常重要：

* **匹 普**

  罗塞塔网组织创建和维护合作伙伴接口流程 （PIP），为所有罗塞塔网消息交换提供通用业务流程定义。 每个 PIP 规范都提供了文档类型定义 (DTD) 文件和消息指南文档。 DTD 文件定义了服务内容消息的结构。 消息指南文档作为可读的 HTML 文件，指定了元素级的约束， 这些文件共同提供了业务流程的完整定义。

   PIP 按高级业务功能、群集、子功能或细分进行分类。 例如，"3A4"是采购订单的 PIP，而"3"是订单管理功能，"3A"是报价&订单输入子功能。 有关详细信息，请参阅[罗塞塔网网站](https://resources.gs1us.org)。

* **操作**

  作为 PIP 的一部分，操作消息是合作伙伴之间交换的业务消息。

* **信号**

   作为 PIP 的一部分，信号消息是响应操作消息而发送的确认。

* **单一操作和双重操作**

  对于单操作 PIP，唯一的响应是确认信号消息。 对于双操作 PIP，发源器除了单操作消息流外，还接收响应消息和回复，并带有确认。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 用于存储协议和其他 B2B 工件的[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。 此集成帐户必须与 Azure 订阅关联。

* 在集成帐户中定义并配置在 **"业务标识**"下的"DUNS"限定符的至少两个[合作伙伴](../logic-apps/logic-apps-enterprise-integration-partners.md)

* 在集成帐户中发送或接收罗塞塔网消息所需的 PIP 进程配置。 流程配置存储所有 PIP 配置特征。 然后，您可以在与合作伙伴创建协议时引用此配置。 要在集成帐户中创建 PIP 流程配置，请参阅[添加 PIP 流程配置](#add-pip)。

* 用于加密、解密或签名上载到集成帐户的邮件的可选[证书](../logic-apps/logic-apps-enterprise-integration-certificates.md)。 仅当您使用签名或加密时，才需要证书。

<a name="add-pip"></a>

## <a name="add-pip-process-configuration"></a>添加 PIP 流程配置

要将 PIP 流程配置添加到集成帐户，请按照以下步骤操作：

1. 在 [Azure 门户](https://portal.azure.com)中，找到并打开你的集成帐户。

1. 在 **"概述"** 窗格中，选择**罗塞塔网 PIP**磁贴。

   ![选择罗塞塔网磁贴](media/logic-apps-enterprise-integration-rosettanet/select-rosettanet-tile.png)

1. 在**罗塞塔网 PIP**下，选择 **"添加**"。 提供您的 PIP 详细信息。

   ![添加罗塞塔网 PIP 详细信息](media/logic-apps-enterprise-integration-rosettanet/add-rosettanet-pip.png)

   | properties | 必选 | 描述 |
   |----------|----------|-------------|
   | **名称** | 是 | 您的 PIP 名称 |
   | **PIP 代码** | 是 | PIP 三位数代码。 有关详细信息，请参阅[罗塞塔网 PIP](https://docs.microsoft.com/biztalk/adapters-and-accelerators/accelerator-rosettanet/rosettanet-pips)。 |
   | **PIP 版本** | 是 | PIP 版本号，根据您选择的 PIP 代码提供 |
   ||||

   有关这些 PIP 属性的详细信息，请访问[罗塞塔网网站](https://resources.gs1us.org/RosettaNet-Standards/Standards-Library/PIP-Directory#1043208-pipsreg)。

1. 完成后，选择 **"确定**"，这将创建 PIP 配置。

1. 要查看或编辑流程配置，请选择 PIP，然后选择 **"编辑为 JSON**"。

   所有流程配置设置均来自 PIP 的规范。 逻辑应用使用这些属性最常用的值的默认值填充大多数设置。

   ![编辑罗塞塔网 PIP 配置](media/logic-apps-enterprise-integration-rosettanet/edit-rosettanet-pip.png)

1. 确认这些设置与相应的 PIP 规范中的值相对应，并满足您的业务需求。 如有必要，请更新 JSON 中的值并保存这些更改。

## <a name="create-rosettanet-agreement"></a>创建罗塞塔网协议

1. 在 [Azure 门户](https://portal.azure.com)中，找到并打开你的集成帐户（如果尚未打开）。

1. 在 **"概述"** 窗格中，选择"**协议**"磁贴。

   ![选择协议磁贴](media/logic-apps-enterprise-integration-rosettanet/select-agreement-tile.png)

1. 在“协议”**** 下，选择“添加”****。 提供您的协议详细信息。

   ![添加协议详细信息](media/logic-apps-enterprise-integration-rosettanet/add-agreement-details.png)

   | properties | 必选 | 描述 |
   |----------|----------|-------------|
   | **名称** | 是 | 协议名称 |
   | **协议类型** | 是 | 选择**罗塞塔网**。 |
   | **管理方** | 是 | 协议需要有主机和来宾合作伙伴。 宿主合作伙伴代表配置协议的组织。 |
   | **管理方标识** | 是 | 管理方的标识符 |
   | **托管方** | 是 | 协议需要有主机和来宾合作伙伴。 托管方代表与管理方进行交易的组织。 |
   | **托管方标识** | 是 | 托管方的标识符 |
   | **接收设置** | 不定 | 这些属性适用于主机合作伙伴收到的所有消息 |
   | **发送设置** | 不定 | 这些属性适用于主机合作伙伴发送的所有邮件 |  
   | **罗塞塔网 PIP 参考** | 是 | 协议的 PIP 引用。 所有罗塞塔网消息都需要 PIP 配置。 |
   ||||

1. 要设置接收来自来宾合作伙伴传入消息的协议，请选择"**接收设置**"。

   ![接收设置](media/logic-apps-enterprise-integration-rosettanet/add-agreement-receive-details.png)

   1. 要启用传入消息的签名或加密，请在 **"消息"** 下，选择 **"消息"或**分别**加密消息**。

      | properties | 必选 | 描述 |
      |----------|----------|-------------|
      | **对消息进行签名** | 否 | 使用所选证书对传入邮件进行签名。 |
      | **Certificate** | 是，如果已启用签名 | 用于签名的证书 |
      | **启用消息加密** | 否 | 使用所选证书加密传入消息。 |
      | **Certificate** | 是，如果启用了加密 | 用于加密的证书 |
      ||||

   1. 在每次选择下，选择以前添加到集成帐户的相应[证书](./logic-apps-enterprise-integration-certificates.md)，用于签名或加密。

1. 要设置向来宾合作伙伴发送消息的协议，请选择"**发送设置**"。

   ![发送设置](media/logic-apps-enterprise-integration-rosettanet/add-agreement-send-details.png)

   1. 要启用传出消息的签名或加密，请在 **"消息"** 下分别选择 **"启用消息签名**"或 **"启用邮件加密**"。 在每次选择下，选择以前添加到集成帐户的相应算法和[证书](./logic-apps-enterprise-integration-certificates.md)，以用于签名或加密。

      | properties | 必选 | 描述 |
      |----------|----------|-------------|
      | **启用消息签名** | 否 | 使用选定的签名算法和证书对传出消息进行签名。 |
      | **签名算法** | 是，如果已启用签名 | 基于所选证书要使用的签名算法 |
      | **Certificate** | 是，如果已启用签名 | 用于签名的证书 |
      | **启用消息加密** | 否 | 使用选定的加密算法和证书加密传出。 |
      | **加密算法** | 是，如果启用了加密 | 基于所选证书要使用的加密算法 |
      | **Certificate** | 是，如果启用了加密 | 用于加密的证书 |
      ||||

   1. 在**端点**下，指定用于发送操作消息和确认所需的 URL。

      | properties | 必选 | 描述 |
      |----------|----------|-------------|
      | **操作 URL** |  是 | 用于发送操作消息的 URL。 URL 是同步消息和异步消息的必填字段。 |
      | **确认 URL** | 是 | 用于发送确认消息的 URL。 URL 是异步消息的必填字段。 |
      ||||

1. 要为合作伙伴设置与罗塞塔网 PIP 参考的约定，请选择**罗塞塔网 PIP 参考。** 在**PIP 名称**下，选择以前创建的 PIP 的名称。

   ![PIP 参考](media/logic-apps-enterprise-integration-rosettanet/add-agreement-pip-details.png)

   您的选择填充剩余的属性，这些属性基于您在集成帐户中设置的 PIP。 如有必要，可以更改 PIP**角色**。

   ![选定的 PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-selected-pip.png)

完成这些步骤后，即可发送或接收罗塞塔网消息。

## <a name="rosettanet-templates"></a>罗塞塔网模板

为了加快开发和推荐集成模式，可以使用逻辑应用模板解码和编码罗塞塔网消息。 创建逻辑应用时，可以从逻辑应用设计器中的模板库中选择。 您还可以在 Azure 逻辑应用的[GitHub 存储库](https://github.com/Azure/logicapps)中找到这些模板。

![罗塞塔网模板](media/logic-apps-enterprise-integration-rosettanet/decode-encode-rosettanet-templates.png)

## <a name="receive-or-decode-rosettanet-messages"></a>接收或解码罗塞塔网消息

1. [创建空白逻辑应用](quickstart-create-first-logic-app-workflow.md)。

1. [将集成帐户链接到](logic-apps-enterprise-integration-create-integration-account.md#link-account)逻辑应用。

1. 在添加解码 RosettaNet 消息的操作之前，必须添加启动逻辑应用的触发器，如请求触发器。

1. 添加触发器后，选择 **"新建步骤**"。

   ![添加请求触发器](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. 在搜索框中，输入"罗塞塔内特"，然后选择此操作：**罗塞塔网解码**

   ![查找并选择"罗塞塔网解码"操作](media/logic-apps-enterprise-integration-rosettanet/select-decode-rosettanet-action.png)

1. 提供操作属性的信息：

   ![提供操作详细信息](media/logic-apps-enterprise-integration-rosettanet/decode-action-details.png)

   | properties | 必选 | 描述 |
   |----------|----------|-------------|
   | **消息** | 是 | 要解码的罗塞塔网消息  |
   | **头** | 是 | 提供版本（RNIF 版本）和响应类型的 HTTP 标头，指示合作伙伴之间的通信类型，并且可以是同步或异步的 |
   | **作用** | 是 | 主机合作伙伴在 PIP 中的角色 |
   ||||

   从罗塞塔网解码操作中，输出和其他属性包括**出站信号**，您可以选择对该信号进行编码并返回到合作伙伴，或对该输出执行任何其他操作。

## <a name="send-or-encode-rosettanet-messages"></a>发送或编码罗塞塔网消息

1. [创建空白逻辑应用](quickstart-create-first-logic-app-workflow.md)。

1. [将集成帐户链接到](logic-apps-enterprise-integration-create-integration-account.md#link-account)逻辑应用。

1. 在添加对罗塞塔网消息进行编码的操作之前，必须添加用于启动逻辑应用的触发器，如请求触发器。

1. 添加触发器后，选择 **"新建步骤**"。

   ![添加请求触发器](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. 在搜索框中，输入"罗塞塔内特"，然后选择此操作：**罗塞塔网编码**

   ![查找并选择"罗塞塔网编码"操作](media/logic-apps-enterprise-integration-rosettanet/select-encode-rosettanet-action.png)

1. 提供操作属性的信息：

   ![提供操作详细信息](media/logic-apps-enterprise-integration-rosettanet/encode-action-details.png)

   | properties | 必选 | 描述 |
   |----------|----------|-------------|
   | **消息** | 是 | 要编码的罗塞塔网消息  |
   | **主机合作伙伴** | 是 | 主机合作伙伴名称 |
   | **宾客合作伙伴** | 是 | 来宾合作伙伴名称 |
   | **PIP 代码** | 是 | PIP 代码 |
   | **PIP 版本** | 是 | PIP 版本 |  
   | **PIP 实例标识** | 是 | 此 PIP 消息的唯一标识符 |  
   | **消息类型** | 是 | 要编码的消息的类型 |  
   | **作用** | 是 | 主机合作伙伴的角色 |
   ||||

   编码的消息现在可以发送给合作伙伴了。

1. 要发送编码消息，此示例使用**HTTP**操作，该操作重命名为"HTTP - 向合作伙伴发送编码消息"。

   ![用于发送罗塞塔网消息的 HTTP 操作](media/logic-apps-enterprise-integration-rosettanet/send-rosettanet-message-to-partner.png)

   根据罗塞塔网标准，只有当 PIP 定义的所有步骤都完成时，业务交易才被视为已完成。

1. 主机向合作伙伴发送编码消息后，主机将等待信号和确认。 要完成此任务，请添加**罗塞塔Net等待响应**操作。

   ![添加"罗塞塔网等待响应"操作](media/logic-apps-enterprise-integration-rosettanet/rosettanet-wait-for-response-action.png)

   用于等待的持续时间和重试次数基于集成帐户中的 PIP 配置。 如果未收到响应，此操作将生成失败通知。 要处理重试，请始终将 **"编码"** 和"**等待响应**操作"放在 **"直到**"循环中。

   ![直到循环与罗塞塔网操作](media/logic-apps-enterprise-integration-rosettanet/rosettanet-loop.png)

## <a name="next-steps"></a>后续步骤

* 了解如何使用 [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) 执行验证、转换和其他消息操作。
* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
