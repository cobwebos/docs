---
title: 如何配置应用程序代理应用程序 | Microsoft Docs
description: 了解如何创建和配置应用程序代理应用程序在几个简单步骤
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7aaf2eb282bc3fd0b9f3853ce493c479a3d3c3a9
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807853"
---
# <a name="how-to-configure-an-application-proxy-application"></a>如何配置应用程序代理应用程序

本文可帮助你了解如何配置应用程序代理应用程序 Azure AD 中公开你的本地应用程序到云。

## <a name="recommended-documents"></a>建议的文档

若要了解通过管理门户对应用程序代理应用程序进行初始配置和创建，请遵循[使用 Azure AD 应用程序代理发布应用程序](application-proxy-add-on-premises-application.md)。

有关配置连接器的详细信息，请参阅[在 Azure 门户中启用应用程序代理](application-proxy-add-on-premises-application.md)。

有关上传证书和使用自定义域的详细信息，请参阅[在 Azure AD 应用程序代理中使用自定义域](application-proxy-configure-custom-domain.md)。

## <a name="create-the-applicationsetting-the-urls"></a>创建应用程序/设置 URL

如果按照[使用 Azure AD 应用程序代理发布应用程序](application-proxy-add-on-premises-application.md)文档中的步骤操作，但在创建应用程序时出错，请参阅错误详细信息以获取有关如何修复应用程序的信息和建议。 大多数的错误消息都包含建议的修复方法。 为避免常见的错误，请验证：

- 是有权创建应用程序代理应用程序的管理员
- 内部 URL 唯一
- 外部 URL 唯一
- URL 以 http 或 https 开头，以“/”结尾
- URL 应为域名，而非 IP 地址

创建应用程序时，应在右上角中显示的错误消息。 还可以选择通知图标以查看错误消息。

![显示在 Azure 门户中找到的通知提示的位置](./media/application-proxy-config-how-to/error-message.png)

## <a name="configure-connectorsconnector-groups"></a>配置连接器/连接器组

由于出现有关连接器和连接器组的警告，导致在配置应用程序时遇到问题，请参阅有关启用应用程序代理的说明，以了解如何下载连接器的详细信息。 如果要了解连接器的详细信息，请参阅[连接器文档](application-proxy-connectors.md)。

如果连接器处于不活动状态，则表示其无法访问服务。 这通常是因为所需的全部端口未处于打开状态。 若要了解所需端口的列表，请参阅启用应用程序代理文档的先决条件部分。

## <a name="upload-certificates-for-custom-domains"></a>上传自定义域证书

自定义域允许指定外部 URL 的域。 若要使用自定义域，需上传该域的证书。 有关使用自定义域和证书的详细信息，请参阅[在 Azure AD 应用程序代理中使用自定义域](application-proxy-configure-custom-domain.md)。

如果在上传证书时遇到问题，请在门户中查找错误消息，以获取证书问题的其他信息。 常见的证书问题包括：

- 证书已过期
- 证书自签名
- 证书缺少私钥

当您尝试上传的证书，则将显示错误消息中右上角。 还可以选择通知图标以查看错误消息。

## <a name="next-steps"></a>后续步骤

[使用 Azure AD 应用程序代理发布应用程序](application-proxy-add-on-premises-application.md)
