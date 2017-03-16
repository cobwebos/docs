---
title: "在 Azure 门户中创建认知服务 API 帐户 | Microsoft 文档"
description: "如何在 Azure 门户中创建 Microsoft 认知服务 API 帐户。"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: b6176bb2-3bb6-4ebf-84d1-3598ee6e01c6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: garye;gibattag
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: a19f56f0b87d992e445034989bd35a827a72f367
ms.lasthandoff: 03/03/2017


---

# <a name="create-a-cognitive-services-apis-account-in-the-azure-portal"></a>在 Azure 门户中创建认知服务 API 帐户

若要使用 Microsoft 认知服务 API，首先需要在 Azure 门户中创建帐户。

1. 登录到 [Azure 门户](http://portal.azure.com)。

2. 单击“+ 新建”。

3. 选择“智能 + 分析”，然后选择“认知服务 API（预览版）”。

    ![选择认知服务 API](media/cognitive-services-apis-create-account/select-cognitive-services-apis.png)

4. 在“创建”页中提供以下信息： 

    -   **帐户名：**帐户的名称。 我们建议使用描述性的名称，例如 *AFaceAPIAccount*。

    -   **订阅：**选择一个可用的至少具有参与者权限的 Azure 订阅。

    -   **API 类型：**选择想要使用的认知服务 API。 有关可用的各种认知服务 API 的详细信息，请参考[认知服务](https://azure.microsoft.com/services/cognitive-services/)站点。

    ![选择 API 类型](media/cognitive-services-apis-create-account/list-of-apis.png)

    -   **定价层：**认知服务帐户的费用取决于实际使用情况和你所选的选项。 有关每个 API 的定价的详细信息，请参考[定价页](https://azure.microsoft.com/pricing/details/cognitive-services/)。

    -   **资源组：**资源组是具有共同生命周期、权限和策略的资源的集合。 若要了解有关资源组的详细信息，请参阅[通过门户管理 Azure 资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)。

    -   **资源组位置：**仅当所选 API 是全局的（未绑定到一个位置），才需要填写此信息。 但是，如果 API 是全局的且未绑定到一个位置，则必须为资源组指定一个位置，与认知服务 API 帐户关联的元数据将驻留于此位置。 此位置对你的帐户的运行时可用性没有任何影响。 若要了解有关资源组的详细信息，请参阅[通过门户管理 Azure 资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)。

    -   **API 设置：**默认情况下，在 [Azure 帐户管理员](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles)显式启用帐户创建功能之前，此功能处于禁用状态。

        此设置的更改仅应用于左侧面板上的当前所选的 API 类型和位置或资源组位置。

        ![创建认知服务 API 帐户](media/cognitive-services-apis-create-account/create-account.png)

        > [!NOTE]
        > 如果你收到更新设置失败的通知，那么你未以[帐户管理员](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator#types-of-azure-admin-accounts)的身份进行登录。 帐户管理员必须按照前面的步骤来启用创建。
        >
        > ![更新设置失败消息](./media/cognitive-services-apis-create-account/updatefailed.png)
        
        在某些情况下，帐户管理员可能没有访问订阅的权限。 如果这样，服务管理员必须按照[添加或更改管理订阅或服务的 Azure 管理员角色](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator)文档中的步骤进行操作。
        
        若要查找你的订阅的帐户管理员或服务管理员，请在 [Azure 门户](https://portal.azure.com)中选择你的订阅，然后选择“属性”。 __帐户管理员__和__服务管理员__的信息显示在“属性”边栏选项卡底部。
        
        ![订阅属性](./media/cognitive-services-apis-create-account/subscription-properties.png)
    
    Microsoft 可能会使用发送到认知服务的数据来改进 Microsoft 产品和服务。 有关详细信息，请参阅联机服务条款中的 [Microsoft 认知服务部分](http://www.microsoft.com/Licensing/product-licensing/products.aspx)
   。

5. 若要将帐户固定到 Azure 门户仪表板，请单击“固定到仪表板”。

6. 单击“创建”  以创建帐户。

成功部署认知服务帐户后，单击仪表板中的磁贴以查看帐户信息。

可以使用“概览”部分的“终结点 URL”和“密钥”部分的密钥在应用程序中进行 API 调用。

![显示帐户信息](media/cognitive-services-apis-create-account/display-account.png)

![显示帐户密钥](media/cognitive-services-apis-create-account/account-keys.png)

### <a name="next-steps"></a>后续步骤

- 有关所有可用的 Microsoft 认知服务的详细信息，请参阅[认知服务](https://azure.microsoft.com/services/cognitive-services/)。

- 有关使用某些示例认知服务 API 的快速入门指南，请参阅：
    - [用于检测舆情、关键短语、主题和语言的文本分析 API 入门](cognitive-services-text-analytics-quick-start.md)
    - [认知服务建议 API 快速入门指南](cognitive-services-recommendations-quick-start.md)
