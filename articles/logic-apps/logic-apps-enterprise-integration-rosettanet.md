---
title: 用于 B2B 集成的 RosettaNet 消息
description: 使用 Enterprise Integration Pack 在 Azure 逻辑应用中交换 RosettaNet 消息
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: f02cbdc7ca8822c5fcc91b106856d7f8f547536b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91565099"
---
# <a name="exchange-rosettanet-messages-for-b2b-enterprise-integration-in-azure-logic-apps"></a>在 Azure 逻辑应用中交换 RosettaNet 消息以实现 B2B 企业集成

[RosettaNet](https://resources.gs1us.org) 是个非盈利性联盟，它为分享业务信息建立了标准过程。 这些标准通常用于供应链过程，并在半导体、电子和物流行业中广泛运用。 RosettaNet 联盟建立并维护合作伙伴接口过程 (PIP)，为所有 RosettaNet 消息交换提供通用业务过程定义。 RosettaNet 基于 XML，它定义了消息准则、业务过程的接口，以及公司间的通信实施框架。

在 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)中，RosettaNet 连接器可帮助你创建支持 RosettaNet 标准的集成解决方案。 该连接器基于 RosettaNet 实施框架 (RNIF) 版本 2.0.01。 RNIF 是开放的网络应用程序框架，可让业务合作伙伴以协作方式运行 RosettaNet PIP。 此框架定义了消息结构、确认需求、多用途 Internet 邮件扩展 (MIME) 编码和数字签名。

具体而言，该连接器提供以下功能：

* 编码或接收 RosettaNet 消息。
* 解码或发送 RosettaNet 消息。
* 等待响应和“失败通知”的生成。

对于这些功能，该连接器支持 RNIF 2.0.01 定义的所有 PIP。 与合作伙伴的通信可以是同步的，也可以是异步的。

## <a name="rosettanet-concepts"></a>RosettaNet 概念

下面是 RosettaNet 规范中特有的一些概念和术语，在构建基于 RosettaNet 的集成时，了解这些概念和术语非常重要：

* **PIP**

  RosettaNet 组织建立并维护合作伙伴接口过程 (PIP)，为所有 RosettaNet 消息交换提供通用业务过程定义。 每个 PIP 规范提供文档类型定义 (DTD) 文件和消息准则文档。 DTD 文件定义服务内容消息结构。 消息准则文档是用户可读的 HTML 文件，指定元素级约束。 这些文件共同提供了业务过程的完整定义。

   PIP 按高级业务功能（也称为聚类）和子功能（也称为段）进行分类。 例如，“3A4”是表示“采购订单”的 PIP，“3”表示“订单管理”功能，而“3A”是“报价和订单输入”子功能。 有关详细信息，请参阅 [RosettaNet 站点](https://resources.gs1us.org)。

* **操作**

  作为 PIP 的一部分，操作消息是在合作伙伴之间交换的业务消息。

* **信号**

   作为 PIP 的一部分，信号消息是为了响应操作消息而发送的确认。

* **单操作和双操作**

  对于单操作 PIP，唯一的响应是确认信号消息。 对于双操作 PIP，发起方将接收响应消息，并使用确认以及单操作消息流做出回复。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 用于存储协议和其他 B2B 项目的[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。 此集成帐户必须与 Azure 订阅相关联。

* 集成帐户中至少定义了两个[合作伙伴](../logic-apps/logic-apps-enterprise-integration-partners.md)，在“业务标识”下为这些合作伙伴配置了“DUNS”限定符 

* 集成帐户中有一个用于发送或接收 RosettaNet 消息的 PIP 过程配置。 该过程配置存储所有 PIP 配置特征。 这样，你可以在与合作伙伴创建协议时引用此配置。 若要在集成帐户中创建 PIP 过程配置，请参阅[添加 PIP 过程配置](#add-pip)。

* 用于对上传到集成帐户的消息进行加密、解密或签名的可选[证书](../logic-apps/logic-apps-enterprise-integration-certificates.md)。 仅当使用签名或加密时，证书才是必需的。

<a name="add-pip"></a>

## <a name="add-pip-process-configuration"></a>添加 PIP 过程配置

若要将 PIP 过程配置添加到集成帐户，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中，找到并打开你的集成帐户。

1. 在“概述”窗格中，选择“RosettaNet PIP”磁贴。  

   ![选择 RosettaNet 磁贴](media/logic-apps-enterprise-integration-rosettanet/select-rosettanet-tile.png)

1. 在“RosettaNet PIP”下，选择“添加”。   提供 PIP 详细信息。

   ![添加 RosettaNet PIP 详细信息](media/logic-apps-enterprise-integration-rosettanet/add-rosettanet-pip.png)

   | 属性 | 必须 | 说明 |
   |----------|----------|-------------|
   | **名称** | 是 | PIP 名称 |
   | **PIP 代码** | 是 | PIP 三位数代码。 有关详细信息，请参阅 [RosettaNet PIP](/biztalk/adapters-and-accelerators/accelerator-rosettanet/rosettanet-pips)。 |
   | **PIP 版本** | 是 | PIP 版本号，系统会根据所选的 PIP 代码提供此号码 |
   ||||

   有关这些 PIP 属性的详细信息，请访问 [RosettaNet 网站](https://resources.gs1us.org/RosettaNet-Standards/Standards-Library/PIP-Directory#1043208-pipsreg)。

1. 完成后请选择“确定”，随即会创建 PIP 配置。 

1. 若要查看或编辑过程配置，请选择该 PIP，然后选择“作为 JSON 进行编辑”。 

   所有过程配置设置都来自 PIP 规范。 逻辑应用将使用默认值（这些属性最常用的值）填充大部分设置。

   ![编辑 RosettaNet PIP 配置](media/logic-apps-enterprise-integration-rosettanet/edit-rosettanet-pip.png)

1. 确认设置是否对应于相应 PIP 规范中的值并符合业务需求。 如果需要，请更新 JSON 中的值并保存更改。

## <a name="create-rosettanet-agreement"></a>创建 RosettaNet 协议

1. 在 [Azure 门户](https://portal.azure.com)中，找到并打开你的集成帐户（如果尚未打开）。

1. 在“概述”窗格中，选择“协议”磁贴。  

   ![选择“协议”磁贴](media/logic-apps-enterprise-integration-rosettanet/select-agreement-tile.png)

1. 在“协议”  下，选择“添加”  。 提供协议详细信息。

   ![添加协议详细信息](media/logic-apps-enterprise-integration-rosettanet/add-agreement-details.png)

   | 属性 | 必须 | 说明 |
   |----------|----------|-------------|
   | **名称** | 是 | 协议的名称 |
   | **协议类型** | 是 | 选择“RosettaNet”。  |
   | **主方合作伙伴** | 是 | 协议需要有主机和来宾合作伙伴。 宿主合作伙伴代表配置协议的组织。 |
   | **主方标识** | 是 | 管理方的标识符 |
   | **客方合作伙伴** | 是 | 协议需要有主机和来宾合作伙伴。 托管方代表与管理方进行交易的组织。 |
   | **客方标识** | 是 | 托管方的标识符 |
   | **接收设置** | 多种多样 | 这些属性适用于主方合作伙伴接收的所有消息 |
   | **发送设置** | 多种多样 | 这些属性适用于主方合作伙伴发送的所有消息 |  
   | **RosettaNet PIP 引用** | 是 | 协议的 PIP 引用。 所有 RosettaNet 消息都需要 PIP 配置。 |
   ||||

1. 若要设置有关从客方合作伙伴接收传入消息的协议，请选择“接收设置”。 

   ![接收设置](media/logic-apps-enterprise-integration-rosettanet/add-agreement-receive-details.png)

   1. 若要为传入消息启用签名或加密，请在“消息”下，分别选择“应将消息签名”或“应将消息加密”。   

      | 属性 | 必须 | 说明 |
      |----------|----------|-------------|
      | **应对消息进行签名** | 否 | 使用所选证书为传入的消息签名。 |
      | **证书** | 是（如果已启用签名） | 用于签名的证书 |
      | **启用消息加密** | 否 | 使用所选证书将传入的消息加密。 |
      | **证书** | 是（如果已启用加密） | 用于加密的证书 |
      ||||

   1. 在每个选项下，选择以前已添加到集成帐户的相应[证书](./logic-apps-enterprise-integration-certificates.md)用于签名或加密。

1. 若要设置有关将消息发送到客方合作伙伴的协议，请选择“发送设置”。 

   ![发送设置](media/logic-apps-enterprise-integration-rosettanet/add-agreement-send-details.png)

   1. 若要为传出的消息启用签名或加密，请在“消息”下，分别选择“启用消息签名”或“启用消息加密”。    在每个选项下选择相应的算法，以及以前已添加到集成帐户的[证书](./logic-apps-enterprise-integration-certificates.md)用于签名或加密。

      | 属性 | 必须 | 说明 |
      |----------|----------|-------------|
      | **启用消息签名** | 否 | 使用所选签名算法和证书将传出的消息签名。 |
      | **签名算法** | 是（如果已启用签名） | 要使用的签名算法，基于所选的证书 |
      | **证书** | 是（如果已启用签名） | 用于签名的证书 |
      | **启用消息加密** | 否 | 使用所选加密算法和证书将传出的消息加密。 |
      | **加密算法** | 是（如果已启用加密） | 要使用的加密算法，基于所选的证书 |
      | **证书** | 是（如果已启用加密） | 用于加密的证书 |
      ||||

   1. 在“终结点”下，指定用于发送操作消息和确认的所需 URL。 

      | 属性 | 必须 | 说明 |
      |----------|----------|-------------|
      | **操作 URL** |  是 | 用于发送操作消息的 URL。 该 URL 是同步和异步消息的必填字段。 |
      | **确认 URL** | 是 | 用于发送确认消息的 URL。 该 URL 是异步消息的必填字段。 |
      ||||

1. 若要使用合作伙伴的 RosettaNet PIP 引用设置协议，请选择“RosettaNet PIP 引用”。  在“PIP 名称”下，选择以前创建的 PIP 的名称。 

   ![PIP 引用](media/logic-apps-enterprise-integration-rosettanet/add-agreement-pip-details.png)

   所做的选择会填充剩余的属性，这些属性基于集成帐户中设置的 PIP。 如果需要，可以更改“PIP 角色”。 

   ![选定的 PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-selected-pip.png)

完成这些步骤后，便可以发送或接收 RosettaNet 消息了。

## <a name="rosettanet-templates"></a>RosettaNet 模板

若要加速开发和推荐集成模式，可以使用逻辑应用模板来解码和编码 RosettaNet 消息。 创建逻辑应用时，可以从逻辑应用设计器的模板库中进行选择。 也可以在 [Azure 逻辑应用的 GitHub 存储库](https://github.com/Azure/logicapps)中找到这些模板。

![RosettaNet 模板](media/logic-apps-enterprise-integration-rosettanet/decode-encode-rosettanet-templates.png)

## <a name="receive-or-decode-rosettanet-messages"></a>接收或解码 RosettaNet 消息

1. [创建空白逻辑应用](quickstart-create-first-logic-app-workflow.md)

1. [将集成帐户链接到](logic-apps-enterprise-integration-create-integration-account.md#link-account)逻辑应用。

1. 在添加用于解码 RosettaNet 消息的操作之前，必须添加一个用于启动逻辑应用的触发器，例如“请求”触发器。

1. 添加触发器后，选择“新建步骤”。 

   ![添加请求触发器](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. 在搜索框中输入“rosettanet”，然后选择以下操作：**RosettaNet 解码**

   ![找到并选择“RosettaNet 解码”操作](media/logic-apps-enterprise-integration-rosettanet/select-decode-rosettanet-action.png)

1. 提供操作属性的信息：

   ![屏幕截图，显示你为操作属性提供信息的位置。](media/logic-apps-enterprise-integration-rosettanet/decode-action-details.png)

   | 属性 | 必须 | 说明 |
   |----------|----------|-------------|
   | **消息** | 是 | 要解码的 RosettaNet 消息  |
   | **标头** | 是 | 提供版本（RNIF 版本）值和响应类型（指示合作伙伴之间的通信类型，可以是同步或异步）的 HTTP 标头 |
   | **角色** | 是 | 主方合作伙伴在 PIP 中的角色 |
   ||||

   在“RosettaNet 解码”操作中，输出和其他属性包括“出站信号”，可以选择将其编码并返回给合作伙伴，或对该输出执行任何其他操作。 

## <a name="send-or-encode-rosettanet-messages"></a>发送或编码 RosettaNet 消息

1. [创建空白逻辑应用](quickstart-create-first-logic-app-workflow.md)

1. [将集成帐户链接到](logic-apps-enterprise-integration-create-integration-account.md#link-account)逻辑应用。

1. 在添加用于编码 RosettaNet 消息的操作之前，必须添加一个用于启动逻辑应用的触发器，例如“请求”触发器。

1. 添加触发器后，选择“新建步骤”。 

   ![添加请求触发器](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. 在搜索框中输入“rosettanet”，然后选择以下操作：**RosettaNet 编码**

   ![找到并选择“RosettaNet 编码”操作](media/logic-apps-enterprise-integration-rosettanet/select-encode-rosettanet-action.png)

1. 提供操作属性的信息：

   ![提供操作详细信息](media/logic-apps-enterprise-integration-rosettanet/encode-action-details.png)

   | 属性 | 必须 | 说明 |
   |----------|----------|-------------|
   | **消息** | 是 | 要编码的 RosettaNet 消息  |
   | **主方合作伙伴** | 是 | 主方合作伙伴名称 |
   | **客方合作伙伴** | 是 | 客方合作伙伴名称 |
   | **PIP 代码** | 是 | PIP 代码 |
   | **PIP 版本** | 是 | PIP 版本 |  
   | **PIP 实例标识** | 是 | 此 PIP 消息的唯一标识符 |  
   | **消息类型** | 是 | 要编码的消息的类型 |  
   | **角色** | 是 | 主方合作伙伴的角色 |
   ||||

   现在，可将已编码的消息发送到合作伙伴。

1. 本示例使用已重命名为“HTTP - 向伙伴发送已编码的消息”的“HTTP”操作发送已编码的消息。 

   ![用于发送 RosettaNet 消息的“HTTP”操作](media/logic-apps-enterprise-integration-rosettanet/send-rosettanet-message-to-partner.png)

   根据 RosettaNet 标准，仅当 PIP 定义的所有步骤都已完成时，才将业务交易视为已完成。

1. 主方将已编码的消息发送到合作伙伴后，主方将等待信号和确认。 若要完成此任务，请添加“RosettaNet 等待响应”操作。 

   ![添加“RosettaNet 等待响应”操作](media/logic-apps-enterprise-integration-rosettanet/rosettanet-wait-for-response-action.png)

   等待所需的持续时间和重试次数取决于集成帐户中的 PIP 配置。 如果未收到响应，此操作将生成失败通知。 若要处理重试，请始终将“编码”和“等待响应”操作放在 Until 循环中。   

   ![包含 RosettaNet 操作的 Until 循环](media/logic-apps-enterprise-integration-rosettanet/rosettanet-loop.png)

## <a name="next-steps"></a>后续步骤

* 了解如何使用 [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) 执行验证、转换和其他消息操作。
* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
