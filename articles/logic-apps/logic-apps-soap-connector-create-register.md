---
title: "创建并注册 SOAP 连接器 - Azure 逻辑应用 | Microsoft Docs"
description: "设置在 Azure 逻辑应用中使用的 SOAP 连接器"
author: divyaswarnkar
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: LADocs; divswa
ms.openlocfilehash: 2d4d009dfc4d43ccc3c69bb3da15ca2c478b9efe
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2017
---
# <a name="create-and-register-soap-connectors-in-azure-logic-apps"></a>在 Azure 逻辑应用中创建并注册 SOAP 连接器

若要在逻辑应用工作流中集成 SOAP 服务，可以使用描述 SOAP 服务的 Web Services 描述语言 (WSDL) 自定义来创建并注册一个自定义的简单对象访问协议 (SOAP) 连接器。 SOAP 连接器的工作方式类似于预先生成的连接器，其使用方式如同在逻辑应用中使用其他连接器。


## <a name="prerequisites"></a>先决条件

若要注册 SOAP 连接器，需要以下各项：

* Azure 订阅。 如果没有订阅，可以从[免费的 Azure 帐户](https://azure.microsoft.com/free/)着手。 或者，也可注册[即用即付订阅](https://azure.microsoft.com/pricing/purchase-options/)。

* 以下任何项：
  * 用于定义 SOAP 服务和 API 的 WSDL 的 URL
  * 用于定义 SOAP 服务和 API 的 WSDL 文件

  在本教程中，可以使用示例[订单 SOAP 服务](http://fazioapisoap.azurewebsites.net/FazioService.svc?singleWsdl)。

* 可选：用作自定义连接器图标的一个图像


## <a name="1-create-your-connector"></a>1.创建连接器

1. 在 Azure 门户中的 Azure 主菜单上，选择“新建”。 在搜索框中，输入“逻辑应用连接器”作为筛选条件，然后按 Enter。

   ![“新建”，搜索“逻辑应用连接器”](./media/logic-apps-soap-connector-create-register/create-logic-apps-connector.png)

2. 从结果列表中选择“逻辑应用连接器” > “创建”。

   ![创建逻辑应用连接器](./media/logic-apps-soap-connector-create-register/choose-logic-apps-connector.png)

3. 如表中所示，提供用于注册连接器的详细信息。 完成后，请选择“固定到仪表板” > “创建”。

   ![逻辑应用自定义连接器详细信息](./media/logic-apps-soap-connector-create-register/logic-apps-soap-connector-details.png)

   | 属性 | 建议的值 | 说明 | 
   | -------- | --------------- | ----------- | 
   | **Name** | *soap-connector-name* | 提供连接器的名称。 | 
   | **订阅** | *Azure-subscription-name* | 选择 Azure 订阅。 | 
   | **资源组** | *Azure-resource-group-name* | 创建或选择用于整理 Azure 资源的 Azure 组。 | 
   | **位置** | *deployment-region* | 选择连接器的部署区域。 | 
   |||| 

   Azure 部署连接器后，逻辑应用连接器菜单会自动打开。 
   如果未打开，请从 Azure 仪表板中选择 SOAP 连接器。

## <a name="2-define-your-connector"></a>2.定义连接器

现在，请指定用于创建连接器的 WSDL 文件或 URL、连接器使用的身份验证，以及 SOAP 连接器提供的操作和触发器


### <a name="2a-specify-the-wsdl-file-or-url-for-your-connector"></a>2a. 为连接器指定 WSDL 文件或 URL

1. 在连接器的菜单中，请选择“逻辑应用连接器”（如果尚未选择）。 在工具栏中，选择“编辑”。

   ![编辑自定义连接器](./media/logic-apps-soap-connector-create-register/edit-soap-connector.png)

2. 选择“常规”，以便在这些表中提供用来为 SOAP 连接器创建、保护和定义操作和触发器的详细信息。

   1. 对于“自定义连接器”，请选择“SOAP”作为“API 终结点”，以便提供用于描述 API 的 WSDL 文件。

      ![为 API 提供 WSDL 文件](./media/logic-apps-soap-connector-create-register/provide-wsdl-file.png)

      | 选项 | 格式 |说明 | 
      | ------ | ------ | ----------- | 
      | **通过文件上传 WSDL** | *WSDL-file* | 浏览到 WSDL 文件的位置并选择该文件。 | 
      | **通过 URL 上传 WSDL** | http://*path-to-wsdl-file* | 提供服务的 WSDL 文件的 URL。 | 
      | **SOAP 到 REST** |   | 将 SOAP 服务中的 API 转换为 REST API。 | 
      |||| 

   2. 对于“常规信息”，请上传连接器的图标。 
   通常，会使用 WSDL 文件中的信息填充“说明”、“主机”和“基 URL”字段。 
   但如果这些信息没有被填充，请如表中所示进行添加并且选择“继续”。 

      ![连接器详细信息](./media/logic-apps-soap-connector-create-register/add-general-details.png)

      | 选项或设置 | 格式 | 说明 | 
      | ----------------- | ------ | ----------- | 
      | **上传图标** | png-or-jpg-file-under-1-MB | 表示连接器的图标 <p>颜色：最好是有色背景衬托的白色徽标。 <p>尺寸：230 像素方框内约 160 像素的徽标 | 
      | **图标背景色** | icon-brand-color-hexadecimal-code | <p>与图标文件中的背景色匹配的图标背景色。 <p>格式：十六进制。 例如，#007ee5 表示蓝色。 | 
      | **说明** | connector-description | 提供连接器的简短说明。 | 
      | **主机** | connector-host | 为 SOAP 服务提供主机域。 | 
      | **基 URL** | connector-base-URL | 为 SOAP 服务提供基 URL。 | 
      |||| 

### <a name="2b-describe-the-authentication-that-your-connector-uses"></a>2b. 描述连接器使用的身份验证

1. 现在选择“安全性”，以此审查或描述连接器使用的身份验证。 身份验证可确保用户的标识在你的服务和任何客户端之间正确地流动。

   默认情况下，连接器的“身份验证类型”设置为“无身份验证”。
   
   ![身份验证类型](./media/logic-apps-soap-connector-create-register/security-authentication-options.png)

   若要更改身份验证类型，请选择“编辑”。 可以选择“基本身份验证”。 若要使用除默认值以外的参数标签，请在“参数标签”下更新标签。

   ![基本身份验证](./media/logic-apps-soap-connector-create-register/security.png)

   
2. 若要在输入安全性信息后保存连接器，请在页面顶部选择“更新连接器”，然后选择“继续”。 

### <a name="2c-review-update-or-define-actions-and-triggers-for-your-connector"></a>2c. 审查、更新或定义连接器的操作和触发器

1. 现在选择“定义”，以此审查、编辑或定义用户可添加到其工作流的新操作和触发器。

   操作和触发器基于 WSDL 文件中定义的操作，这些设置会自动填充“定义”页并包含请求和响应值。 因此，如果所需操作已显示在此处，可转到注册流程中的下一步，而无需在此页面上进行更改。

   ![连接器定义](./media/logic-apps-soap-connector-create-register/definition.png)

2. （可选）如果想要编辑现有的或添加新的操作和触发器，请[继续执行这些步骤](logic-apps-custom-connector-register.md#add-action-or-trigger)。


## <a name="3-finish-creating-your-connector"></a>3.完成创建连接器

准备就绪后，请选择“更新连接器”，以便部署连接器。 

祝贺你！ 现在，当创建逻辑应用时，可以在逻辑应用设计器中找到你的连接器，并将该连接器添加到逻辑应用中。

![在逻辑应用设计器中，找到连接器](./media/logic-apps-soap-connector-create-register/soap-connector-created.png)

## <a name="share-your-connector-with-other-logic-apps-users"></a>与其他逻辑应用用户共享连接器

已注册但未认证的自定义连接器与 Microsoft 托管连接器的工作方式类似，但前者仅对该连接器的创建者以及在部署逻辑应用的区域中对这些应用具有同一 Azure Active Directory 租户和 Azure 订阅的用户可见，并且仅可供他们使用。 尽管共享是可选操作，但也可能存在想要与其他用户共享连接器的情况。 

> [!IMPORTANT]
> 如果共享一个连接器，其他人可能会开始依赖该连接器。 
> 删除连接器会删除到该连接器的所有连接。
 
若要与这些边界外的外部用户共享连接器（例如与所有逻辑应用用户共享），请[提交连接器进行 Microsoft 认证](../logic-apps/custom-connector-submit-certification.md)。

## <a name="faq"></a>常见问题

**问：**自定义连接器是否存在任何限制？ </br>
**答：**是，请参阅[此处的自定义连接器限制](../logic-apps/logic-apps-limits-and-config.md#custom-connector-limits)。

## <a name="get-support"></a>获取支持

* 有关开发和载入支持，或者要请求注册向导中没有提供的功能，请发送电子邮件至 [condevhelp@microsoft.com](mailto:condevhelp@microsoft.com)。Microsoft 会监视此帐户以了解开发人员的问题和疑问，并将他们路由到相应的团队。

* 若要提问或解答问题或者了解其他 Azure 逻辑应用用户的活动，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。

* 为帮助改进逻辑应用，敬请在[逻辑应用用户反馈网站](http://aka.ms/logicapps-wish)上投票或发表看法。 

## <a name="next-steps"></a>后续步骤

* [可选：认证连接器](../logic-apps/custom-connector-submit-certification.md)
* [自定义连接器常见问题解答](../logic-apps/custom-connector-faq.md)