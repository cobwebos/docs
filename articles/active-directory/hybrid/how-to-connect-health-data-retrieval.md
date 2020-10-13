---
title: Azure AD Connect Health 指令数据检索 |Microsoft Docs
description: 本页介绍如何从 Azure AD Connect Health 检索数据。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/02/2020
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d147d2c094923e971e52e1dbfe3f7a19776d38c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89463419"
---
# <a name="azure-ad-connect-health-instructions-for-data-retrieval"></a>数据检索 Azure AD Connect Health 说明

本文档介绍如何使用 Azure AD Connect 从 Azure AD Connect Health 检索数据。

[!INCLUDE [active-directory-app-provisioning.md](../../../includes/gdpr-intro-sentence.md)]

## <a name="retrieve-all-email-addresses-for-users-configured-for-health-alerts"></a>检索为运行状况警报配置的用户的所有电子邮件地址。

若要检索在 Azure AD Connect Health 中配置接收警报的所有用户的电子邮件地址，请使用以下步骤。

1.  开始 Azure Active Directory Connect 运行状况 "边栏选项卡，然后从左侧导航栏中选择" **同步服务** "。
 ![同步服务](./media/how-to-connect-health-data-retrieval/retrieve1.png)

2.  单击 " **警报** " 磁贴。</br>
 ![Alert](./media/how-to-connect-health-data-retrieval/retrieve3.png)

3.  单击 " **通知设置**"。
 ![通知](./media/how-to-connect-health-data-retrieval/retrieve4.png)

4.  在 " **通知设置** " 边栏选项卡上，可找到已作为接收方警报通知的收件人的电子邮件地址列表。
 ![封](./media/how-to-connect-health-data-retrieval/retrieve5a.png)
 
## <a name="retrieve-accounts-that-were-flagged-with-ad-fs-bad-password-attempts"></a>检索用 AD FS 错误密码尝试标记的帐户

若要检索使用 AD FS 错误密码尝试标记的帐户，请使用以下步骤。

1.  从 "Azure Active Directory 运行状况" 边栏选项卡开始，选择 " **同步错误**"。
 ![同步错误](./media/how-to-connect-health-data-retrieval/retrieve6.png)

2.  在 " **同步错误** " 边栏选项卡中，单击 " **导出**"。 这将导出记录的同步错误的列表。
 ![导出](./media/how-to-connect-health-data-retrieval/retrieve7.png)

## <a name="next-steps"></a>后续步骤
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Azure AD Connect Health 代理安装](how-to-connect-health-agent-install.md)
* [Azure AD Connect Health 操作](how-to-connect-health-operations.md)
* [Azure AD Connect Health 常见问题](reference-connect-health-faq.md)
* [Azure AD Connect Health 版本历史记录](reference-connect-health-version-history.md)