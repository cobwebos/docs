---
title: 将 Azure 企业协议注册到 Cloudyn 中 | Microsoft Docs
description: 本快速入门将详细介绍创建 Cloudyn 试用订阅和登录到 Cloudyn 门户所需的注册过程。
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/24/2020
ms.topic: quickstart
ms.custom: seodec18
ms.service: cost-management-billing
ms.reviewer: benshy
ms.openlocfilehash: dcb4a04ced1d36a6d0717b9a65150faacc702cc5
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76769973"
---
# <a name="register-an-azure-enterprise-agreement-and-view-cost-data"></a>注册 Azure 企业协议并查看成本数据

使用 Azure 企业协议注册到 Cloudyn。 注册后可以访问 Cloudyn 门户。 本快速入门将详细介绍创建 Cloudyn 试用订阅和登录到 Cloudyn 门户所需的注册过程。 此外，还将演示如何立即开始查看成本数据。

Azure 成本管理提供了与 Cloudyn 类似的功能。 Azure 成本管理是一个本机 Azure 成本管理解决方案。 借助它，可以分析成本、创建和管理预算、导出数据，并能查看和采纳优化建议，从而节省资金。 有关详细信息，请参阅 [Azure 成本管理](../cost-management-billing-overview.md)。

## <a name="sign-in-to-azure"></a>登录 Azure

- 通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="register-with-cloudyn"></a>注册 Cloudyn

1. 在 Azure 门户中，单击服务列表中的“成本管理 + 计费”  。
2. 在“概览”  下，单击“Cloudyn”   
    ![显示在 Azure 门户中的 Cloudyn 页](./media/quick-register-ea/cost-mgt-billing-service.png)
3. 在“Cloudyn”  页上，单击“转到 Cloudyn”  ，以在新窗口中打开 Cloudyn 注册页。
4. 在 Cloudyn 门户试用注册页上，键入公司名称，然后选择“Azure 企业合约管理员”  。  
5. 输入企业门户注册 API 密钥。 如果手边没有密钥，请单击[企业门户](https://ea.azure.com)链接并执行以下步骤：  
    ![在“计费”选项卡上粘贴 API 密钥](./media/quick-register-ea/trial-reg.png)
   1. 登录 Azure Enterprise 网站并单击“报告”  ，单击“API 访问密钥”  ，然后复制主密钥。  
    ![EA 门户中的 EA API 密钥示例](./media/quick-register-ea/ea-key.png)
   3. 返回到注册页并粘贴 API 密钥。
6. 同意“使用条款”，并验证密钥。 单击“下一步”  ，授权 Cloudyn 收集 Azure 资源数据。 收集的数据包括订阅的使用情况、性能、计费和标记数据。  
    ![成功的 EA API 密钥验证示例](./media/quick-register-ea/ea-key-validated.png)
7. 在“邀请其他利益干系人”  下，可以通过键入其电子邮件地址来添加用户。 完成后，单击“下一步”  。 将所有计费数据添加到 Cloudyn 可能需要长达 24 小时的时间，具体取决于 Azure 注册的大小。
8. 单击“转到 Cloudyn”  打开 Cloudyn 门户，然后在“云帐户管理”  页上，应看到已注册的 EA 帐户信息。

若要观看有关注册企业协议的教程视频，请参阅[如何在 Cloudyn 中查找要使用的 EA 注册 ID 和 API 密钥](https://youtu.be/u_phLs_udig)。

[!INCLUDE [cost-management-create-account-view-data](../../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，使用 Azure 企业协议信息注册了 Cloudyn。 此外，还会登录到 Cloudyn 门户并开始查看成本数据。 若要了解有关 Cloudyn 的详细信息，请继续学习 Cloudyn 的教程。

> [!div class="nextstepaction"]
> [查看使用情况和成本](tutorial-review-usage.md)
