---
title: 如何配置应用程序代理应用程序 | Microsoft Docs
description: 了解如何通过简单几步创建并配置一个应用程序代理应用程序
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: barbkess
ms.reviewer: harshja
ms.openlocfilehash: b297aab75212070aa435c58bf9024bf90e8ffec3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34590148"
---
# <a name="how-to-configure-an-application-proxy-application"></a>如何配置应用程序代理应用程序

本文帮助了解如何在 Azure AD 内配置应用程序代理应用程序，以将本地应用程序公开到云。

## <a name="recommended-documents"></a>建议的文档 

若要了解通过管理门户对应用程序代理应用程序进行初始配置和创建，请遵循[使用 Azure AD 应用程序代理发布应用程序](manage-apps/application-proxy-publish-azure-portal.md)。

有关配置连接器的详细信息，请参阅[在 Azure 门户中启用应用程序代理](manage-apps/application-proxy-enable.md)。

有关上传证书和使用自定义域的详细信息，请参阅[在 Azure AD 应用程序代理中使用自定义域](manage-apps/application-proxy-configure-custom-domain.md)。

## <a name="create-the-applicationsetting-the-urls"></a>创建应用程序/设置 URL

如果按照[使用 Azure AD 应用程序代理发布应用程序](manage-apps/application-proxy-publish-azure-portal.md)文档中的步骤操作，但在创建应用程序时出错，请参阅错误详细信息以获取有关如何修复应用程序的信息和建议。 大多数的错误消息都包含建议的修复方法。 为避免常见的错误，请验证：

-   是有权创建应用程序代理应用程序的管理员

-   内部 URL 唯一

-   外部 URL 唯一

-   URL 以 http 或 https 开头，以“/”结尾

-   URL 应为域名，而非 IP 地址

在创建应用程序时，错误消息应显示在右上角。 还可以选择通知图标以查看错误消息。

   ![通知提示](./media/application-proxy-config-how-to/error-message.png)

## <a name="configure-connectorsconnector-groups"></a>配置连接器/连接器组

由于出现有关连接器和连接器组的警告，导致在配置应用程序时遇到问题，请参阅有关启用应用程序代理的说明，以了解如何下载连接器的详细信息。 如果要了解连接器的详细信息，请参阅[连接器文档](manage-apps/application-proxy-connectors.md)。

如果连接器处于不活动状态，则表示其无法访问服务。 这通常是因为所需的全部端口未处于打开状态。 若要了解所需端口的列表，请参阅启用应用程序代理文档的先决条件部分。

## <a name="upload-certificates-for-custom-domains"></a>上传自定义域证书

自定义域允许指定外部 URL 的域。 若要使用自定义域，需上传该域的证书。 有关使用自定义域和证书的详细信息，请参阅[在 Azure AD 应用程序代理中使用自定义域](manage-apps/application-proxy-configure-custom-domain.md)。 

如果在上传证书时遇到问题，请在门户中查找错误消息，以获取证书问题的其他信息。 常见的证书问题包括：

-   证书已过期

-   证书自签名

-   证书缺少私钥

尝试上传证书时，右上角显示错误消息。 还可以选择通知图标以查看错误消息。

   ![通知提示](./media/application-proxy-config-how-to/error-message2.png)

## <a name="next-steps"></a>后续步骤
[使用 Azure AD 应用程序代理发布应用程序](manage-apps/application-proxy-publish-azure-portal.md)
