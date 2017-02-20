---
title: "在 Azure 逻辑应用中创建 AS2 协议 | Microsoft Docs"
description: "为 Enterprise Integration Pack 创建 AS2 协议 | Azure 逻辑应用"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: c9b7e1a9-4791-474c-855f-988bd7bf4b7f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 6a947ea997bbcfe1b6b28c7cbb49911836750e6a
ms.openlocfilehash: a490b89c5420aecdfb3f79289979faab9a4630e9


---
# <a name="enterprise-integration-with-as2"></a>与 AS2 的企业集成
若要使用逻辑应用中的企业功能，必须首先创建协议。

## <a name="prerequisites"></a>先决条件
* 必须在 Azure 订阅中定义了[集成帐户](../logic-apps/logic-apps-enterprise-integration-accounts.md)。  
* 必须已在集成帐户中定义了至少两个[合作伙伴](logic-apps-enterprise-integration-partners.md)。  

> [!NOTE]
> 创建协议时，协议文件中的内容必须与协议类型匹配。    

创建了集成帐户并添加了合作伙伴之后，可以使用以下部分中的过程创建协议。  

## <a name="create-an-agreement"></a>创建协议

1. 登录 [Azure 门户](http://portal.azure.com "Azure portal")。  
2. 选择“更多服务”，在筛选器搜索框中输入 **integration**，然后从结果列表中选择“集成帐户”。

 ![在搜索结果列表中选择“集成帐户”](./media/logic-apps-enterprise-integration-agreements/overview-1.png)    
3. 选择集成帐户以添加证书。

 ![选择集成帐户](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. 选择“协议”磁贴。 如果未显示该磁贴，请添加。

 ![选择“协议”磁贴](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)   
5. 在“协议”边栏选项卡上，选择“添加”。

 ![选择“添加”](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)  
6. 为协议输入名称，在“协议类型”列表中选择“AS2”，然后在“主机合作伙伴”、“主机标识”、“来宾合作伙伴”和“来宾标识”列表中输入相应信息。

 ![输入协议名称](./media/logic-apps-enterprise-integration-agreements/agreement-3.png)  

 下表介绍了“添加”对话框中的属性：

    | 属性 | 说明 |
    | --- | --- |
    | 管理方 | 协议需要有主机和来宾合作伙伴。 主机合作伙伴代表配置协议的组织。 |
    | 管理方标识 | 管理方的标识符。 |
    | 托管方 | 协议需要有主机和来宾合作伙伴。 托管方代表与管理方进行交易的组织。 |
    | 托管方标识 | 托管方的标识符。 |
    | 接收设置 | 这些属性适用于协议接收的所有消息。 |
    | 发送设置 | 这些属性适用于协议发送的所有消息。 |

7. 若要配置通过此协议收到的消息的处理方式，请执行以下操作：

 a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 选择“接收设置”。

 b.保留“数据库类型”设置，即设置为“共享”。 或者，可以选择“替代消息属性”复选框来替代传入消息的属性。

 c. 如需对所有传入消息进行签名，请选择“应对消息进行签名”复选框。 如果选择此选项，需在“证书”列表中选择“来宾合作伙伴公共证书”来验证消息的签名。

 d. 如果要对所有传入消息进行加密，请选择“应对消息进行加密”。 如果选择此选项，则需在“证书”列表中选择“主机合作伙伴私有证书”来解密传入的消息。

 e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 对于要压缩的消息，请选择“应对消息进行压缩”复选框。    

 f. 如需为接收的消息发送同步消息处置通知 (MDN)，请选择“发送 MDN”复选框。

 g. 如需为接收的消息发送已签名 MDN，请选择“发送已签名的 MDN”复选框。

 h. 如需为接收的消息发送异步 MDN，请选择“发送异步 MDN”复选框。

 ![设置“接收设置”属性](./media/logic-apps-enterprise-integration-agreements/agreement-4.png)  

 下表描述了“接收设置”属性：  

 | 属性 | 说明 |
 | --- | --- |
 | 替代消息属性 | 表示可替代接收消息中的属性。 |
 | 对消息进行签名 | 要求对消息进行数字签名。 配置来宾合作伙伴公共证书以进行签名验证。  |
 | 对消息进行加密 | 要求对消息进行加密。 非加密的消息将被拒绝。 配置主机合作伙伴私有证书以对消息进行解密。  |
 | 对消息进行压缩 | 要求对消息进行压缩。 非压缩的消息将被拒绝。 |
 | MDN 文本 | 要发送到消息发送方的默认消息处置通知 (MDN)。 |
 | 发送 MDN | 要求对 MDN 进行发送。 |
 | 发送已签名的 MDN | 要求对 MDN 进行签名。 |
 | MIC 算法 | |
 | 发送异步 MDN | 要求对消息进行异步发送。 |
 | 代码 | 要向其发送 MDN 的 URL。 |

8. 若要配置通过此协议发送的消息的处理方式，请执行以下操作：

 a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 选择“发送设置”。  

 b.保留“数据库类型”设置，即设置为“共享”。 要将已签名消息发送到合作伙伴，请选择“启用消息签名”复选框。 如果选择此选项，则需在“MIC 算法”列表中选择“主机合作伙伴私有证书 MIC 算法”，在“证书”列表中选择“主机合作伙伴私有证书”来对消息进行签名。

 c. 要将已加密消息发送到合作伙伴，请选择“启用消息加密”复选框。 如果选择此选项，则需在“加密算法”列表中选择“来宾合作伙伴公共证书算法”，在“证书”列表中选择“来宾合作伙伴公共证书”来对消息进行解密。

 d. 要对消息进行压缩，请选择“启用消息压缩”复选框。

 e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 要将 HTTP 内容类型标头展开为单行，请选择“展开 HTTP 标头”复选框。

 f. 要接收所发送消息的同步 MDN，选择“请求 MDN”复选框。

 g. 要接收所发送消息的已签名 MDN，选择“请求已签名的 MDN”复选框。

 h. 要接收所发送消息的异步 MDN，选择“请求异步 MDN”复选框。 如果选择此选项，请输入要向其发送 MDN 的 URL。  

 i. 要启用回执的不可否认性，请选择“启用 NRR”复选框。

 j. 选择“确定”。

 ![设置“发送设置”属性](./media/logic-apps-enterprise-integration-agreements/agreement-5.png)  

 下表描述了“发送设置”属性：  

 | 属性 | 说明 |
 | --- | --- |
 | 启用消息签名 | 要求对所有发送自此协议的消息进行签名。 |
 | MIC 算法 | 要用于对消息进行签名的算法。 配置主机合作伙伴私有证书 MIC 算法以对消息进行签名。 |
 | 证书 | 要用于对消息进行签名的证书。 配置主机合作伙伴私有证书以对消息进行签名。 |
 | 启用消息加密 | 要求对所有发送自协议的消息进行加密。 配置来宾合作伙伴公共证书算法以对消息进行加密。 |
 | 加密算法 | 要用于消息加密的加密算法。 配置来宾合作伙伴公共证书以对消息进行加密。 |
 | 证书 | 要用于对消息进行加密的证书。 配置来宾合作伙伴私有证书以对消息进行加密。 |
 | 启用消息压缩 | 要求对所有发送自协议的消息进行压缩。 |
 | 展开 HTTP 标头 | 将 HTTP 内容类型标头展开为单行。 |
 | 请求 MDN | 需要所有发送自此协议的消息的 MDN。 |
 | 要求对 MDN 进行签名 | 要求对所有发送到此协议的 MDN 进行签名。 |
 | 要求发送异步 MDN | 要求将异步 MDN 发送到此协议。 |
 | 代码 | 要向其发送 MDN 的 URL。 |
 | 启用 NRR | 需要回执的不可否认性 (NRR)，这是一种通信属性，提供数据已按址接收到的证据。 |

## <a name="view-the-agreements-list"></a>查看“协议”列表
要查看新添加的协议，请在“集成帐户”边栏选项卡上选择“协议”磁贴。

![查看“协议”列表](./media/logic-apps-enterprise-integration-agreements/agreement-6.png)

## <a name="next-steps"></a>后续步骤
* [了解有关 Enterprise Integration Pack 的详细信息](logic-apps-enterprise-integration-overview.md "了解 Enterprise Integration Pack")  



<!--HONumber=Feb17_HO1-->


