---
title: "使用 CSP 合作伙伴信息注册 Azure 成本管理 | Microsoft Docs"
description: "使用 CSP 合作伙伴信息注册到 Azure 成本管理。"
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 01/30/2018
ms.topic: quickstart
ms.custom: mvc
ms.service: cost-management
manager: carmonm
ms.openlocfilehash: cf4fb7e82529388a030fd37da9469bf17c8dc343
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="register-with-the-csp-partner-program-and-view-cost-data"></a>注册 CSP 合作伙伴计划并查看成本数据

作为 CSP 合作伙伴，你可以注册到 Azure 成本管理。 注册后可以访问 Cloudyn 门户。 本快速入门将详细介绍创建 Cloudyn 试用订阅和登录到 Cloudyn 门户所需的注册过程。 此外，还将演示如何立即开始查看成本数据。


>[!NOTE]

>仅 CSP 直接合作伙伴和 CSP 间接提供程序可以完成 Cloudyn 注册。
>
>需要配置合作伙伴中心 API 才能进行身份验证和数据访问。 需要使用合作伙伴中心全局管理员帐户预配 API 访问权限。
有关详细信息，请参阅[连接到合作伙伴中心 API](https://msdn.microsoft.com/library/partnercenter/mt709136.aspx)。
>
>CSP 间接经销商在其 CSP 间接提供程序注册到 Cloudyn 中后，可以访问 Cloudyn。 然后，CSP 间接经销商可以向 Azure 客户和订阅提供 Cloudyn 访问权限。

## <a name="log-in-to-azure"></a>登录 Azure

- 通过 http://portal.azure.com 登录到 Azure 门户。

## <a name="create-a-trial-registration"></a>创建试用注册

1. 在 Azure 门户中，单击服务列表中的“成本管理 + 计费”。
2. 在“概览”下，单击“成本管理”  
    ![成本管理页](./media/quick-register-csp/cost-mgt-billing-service.png)
3. 在“成本管理”页上，单击“转到成本管理”，在新窗口中打开“Cloudyn 注册”页。
4. 在 Cloudyn 门户试用注册页上，键入公司名称，选择“Microsoft CSP 合作伙伴计划管理员”，然后单击“下一步”。  
5. 输入 **应用程序 ID**、**商务 ID**、**应用程序密钥**，然后选择“默认定价计划”。 如果手边没有相关信息，请使用主管理员帐户登录到合作伙伴中心门户（[https://partnercenter.microsoft.com](https://partnercenter.microsoft.com)）并执行以下步骤：
  1. 转到“仪表板” > “帐户设置” > “应用管理”。
  2. 如果之前已创建 Web 应用，请跳过此步骤。 否则，请单击“Web 应用”部分中的“添加新 Web 应用”。
  3. 从 Web 应用程序复制“应用程序 ID”GUID。
  4. 从 Web 应用程序复制“商务 ID”GUID。
  5. 根据需要将密钥有效期选为一年或两年。 选择“添加密钥”，然后复制并保存密钥值。  
    ![CSP 合作伙伴中心](./media/quick-register-csp/csp-partner-center.png)
  6. 返回到注册页并粘贴相应信息。  
      ![CSP 帐户凭据](./media/quick-register-csp/csp-reg.png)
6. 同意“使用条款”，并验证相关信息。 单击“下一步”，授权 Cloudyn 收集 Azure 资源数据。 收集的数据包括订阅的使用情况、性能、计费和标记数据。  
7. 在“邀请其他利益干系人”下，可以通过键入其电子邮件地址来添加用户。 完成后，单击“下一步”。 将所有计费数据添加到 Cloudyn 大约需要两个小时。
8. 单击“转到 Cloudyn”打开 Cloudyn 门户，然后在“云帐户管理”页上，应看到已注册的 CSP 帐户信息。

## <a name="configure-indirect-csp-access-in-cloudyn"></a>在 Cloudyn 中配置间接 CSP 访问权限

默认情况下，仅直接 CSP 可以访问合作伙伴中心 API。 但是，直接 CSP 提供商可以使用 Cloudyn 中的实体组为他们的间接 CSP 客户或合作伙伴配置访问权限。

若要为间接 CSP 客户或合作伙伴启用访问权限，请按照[创建试用注册](#create-a-trial-registration)中的步骤设置试用注册。 接下来，请完成以下步骤，以使用 Cloudyn 实体组将间接 CSP 数据分段。 然后，向实体组分配适当的用户权限。

1. 使用[创建实体](tutorial-user-access.md#create-entities)上的信息创建实体组。
2. 按照[将订阅分配到费用实体](https://support.cloudyn.com/hc/en-us/articles/115005139425-Video-Assigning-subscriptions-to-Cost-Entities)上的步骤操作。 将间接 CSP 客户的帐户及其 Azure 订阅与之前创建的实体关联。
3. 按照[创建具有管理员权限的用户](tutorial-user-access.md#create-a-user-with-admin-access)上的步骤创建具有管理员权限的用户帐户。 然后，确保该用户帐户拥有之前为间接帐户创建的特定实体的管理员权限。

间接 CSP 合作伙伴使用为他们创建的帐户登录到 Cloudyn 门户。


[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，使用 CSP 信息注册了成本管理。 此外，还会登录到 Cloudyn 门户并开始查看成本数据。 若要详细了解 Azure 成本管理，请继续学习成本管理教程。

> [!div class="nextstepaction"]
> [查看使用情况和成本](./tutorial-review-usage.md)
