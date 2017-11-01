---
title: "将 Microsoft Advanced Threat Analytics 连接到 Azure 安全中心 | Microsoft Docs"
description: "了解 Azure 安全中心如何与 Microsoft Advanced Threat Analytics 集成。"
services: security-center
documentationcenter: na
author: YuriDio
manager: MBaldwin
editor: 
ms.assetid: 5d80bf91-16c3-40b3-82fc-e0805e6708db
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: e1b9e598af3b55c1d9591e5c1e529a80ae3319ca
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2017
---
# <a name="connecting-microsoft-advanced-threat-analytics-to-azure-security-center"></a>将 Microsoft Advanced Threat Analytics 连接到 Azure 安全中心
本文档可帮助配置 Microsoft Advanced Threat Analytics 和 Azure 安全中心之间的集成。

## <a name="why-add-advanced-threat-analytics-data"></a>为什么要添加 Advanced Threat Analytics 数据？
[Advanced Threat Analytics (ATA)](https://docs.microsoft.com/advanced-threat-analytics/what-is-ata) 是帮助检测可疑用户行为的本地平台。 连接后，可以在安全中心内查看 ATA 检测到的可疑操作。 通过此集成，可以在安全中心内查看、关联和调查与混合云工作负荷相关的所有安全警报。 

## <a name="how-do-i-configure-this-integration"></a>如何配置此集成？
假定你已安装 ATA，并且它正在本地正常运行，请按照下列步骤配置此集成：

1. 登录到 ATA 中心，并访问 ATA 门户。
2. 单击左窗格中的“Syslog 服务器”。

    ![Syslog 服务器](./media/security-center-ata-integration/security-center-ata-integration-fig1.png)

3. 在“Syslog 服务器终结点”字段中，键入 127.0.0.7（必须是此地址），并在端口上键入 5114（建议）。 虽然建议使用此端口号，但任何唯一端口都应能正常运行。 按原样保留所有其他选项，然后单击“保存”。
4. 在左窗格中单击“通知”，然后启用所有的 Syslog 通知（建议），如下图所示：

    ![通知](./media/security-center-ata-integration/security-center-ata-integration-fig2.png)

5. 单击“保存” 。
6. 打开“安全中心”仪表板。
7. 在左窗格中，单击“安全解决方案”。
8. 在“Advanced Threat Analytics”下，单击“添加”。

    ![ATA](./media/security-center-ata-integration/security-center-ata-integration-fig3.png)
    
9. 转到最后一个步骤，并单击“下载代理”。

    ![ATA](./media/security-center-ata-integration/security-center-ata-integration-fig4.png)

10. 在“添加新的非 Azure 计算机”页中，选择工作区。

    ![非 Azure](./media/security-center-ata-integration/security-center-ata-integration-fig5.png)

11. 在“直接代理”页中，下载适当的 Windows 代理，并记录“工作区 ID”和“主键”。

    ![直接代理](./media/security-center-ata-integration/security-center-ata-integration-fig6.png)

12. 在 ATA 中心中安装此代理。 安装时，务必选择“将代理连接到 Azure Log Analytics (OMS)”，并在出现请求时提供“工作区 ID”和“主键”。


完成安装后，集成完成，你将可以在“安全警报”和“搜索”中看到 ATA 发送到安全中心的新警报。 此解决方案将出现在“安全解决方案”页上的“已连接解决方案”下。 

## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Microsoft ATA 连接到安全中心。 若要详细了解安全中心，请参阅以下文章：

* [将 Azure Active Directory Identity Protection 连接到 Azure 安全中心](security-center-aadip-integration.md)
* [在 Azure 安全中心中设置安全策略](security-center-policies.md) - 了解如何配置 Azure 订阅和资源组的安全策略。
* [在 Azure 安全中心中管理安全建议](security-center-recommendations.md) - 了解建议如何帮助保护 Azure 资源。
* [Security health monitoring in Azure Security Center](security-center-monitoring.md) （Azure 安全中心的安全运行状况监视）- 了解如何监视 Azure 资源的运行状况。
* [在 Azure 安全中心管理和响应安全警报](security-center-managing-and-responding-alerts.md) — 了解如何管理和响应安全警报。
* [通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md) - 了解如何监视合作伙伴解决方案的运行状态。
- [Azure 安全中心的数据安全](security-center-data-security.md) - 了解如何在安全中心管理数据和确保数据安全性。
* [Azure Security Center FAQ](security-center-faq.md) （Azure 安全中心常见问题）- 查找有关如何使用服务的常见问题。
* [Azure 安全博客](http://blogs.msdn.com/b/azuresecurity/) — 获取最新的 Azure 安全新闻和信息。


