---
title: 将本地应用与 Cloud App Security 集成 - Azure Active Directory | Microsoft Docs
description: 在 Azure Active Directory 中配置本地应用程序，以便与 Microsoft Cloud App Security (MCAS) 配合使用。 使用 MCAS 条件访问应用控制以基于条件访问策略实时监视并控制会话。 可以将这些策略应用于 Azure Active Directory (Azure AD) 中使用应用程序代理的本地应用程序。
author: barbkess
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: barbkess
ms.reviewer: japere
ms.openlocfilehash: fbe3ce83cebe556f4248b1d62bb87c000823a31d
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55182058"
---
# <a name="configure-real-time-application-access-monitoring-with-microsoft-cloud-app-security-and-azure-active-directory"></a>使用 Microsoft Cloud App Security 和 Azure Active Directory 配置实时应用程序访问监视
在 Azure Active Directory (Azure AD) 中配置本地应用程序，以使用 Microsoft Cloud App Security (MCAS) 进行实时监视。 MCAS 使用条件访问应用控制以基于条件访问策略实时监视并控制会话。 可以将这些策略应用于 Azure Active Directory (Azure AD) 中使用应用程序代理的本地应用程序。

以下是一些可以使用 MCAS 创建的策略类型示例：

- 阻止或保护对非管理的设备上敏感文档的下载。
- 当高风险用户登录到应用程序时进行监视，然后从会话中记录它们的操作。 有了此信息，即可分析用户行为以决定如何应用会话策略。
- 使用客户端证书或设备符合性阻止非管理的设备访问特定应用程序。
- 限制来自非公司网络的用户会话。 对于从公司外部网络访问应用程序的用户，可以限制他们的访问权限。 例如，受限制的访问权限可阻止用户下载敏感文档。

有关详细信息，请参阅[使用 Microsoft Cloud App Security 条件访问应用控制保护应用](/cloud-app-security/proxy-intro-aad)。

## <a name="requirements"></a>要求

许可证：

- EMS E5 许可证或 
- Azure Active Directory Premium P1 和独立 MCAS。

本地应用程序：

- 本地应用程序必须使用 Kerberos 约束委派 (KCD)

配置应用程序代理：

- 配置 Azure AD 以使用应用程序代理，包括准备环境并安装应用程序代理连接器。 有关教程，请参阅[在 Azure AD 中添加一个本地应用程序以通过应用程序代理进行远程访问](application-proxy-add-on-premises-application.md)。 

## <a name="add-on-premises-application-to-azure-ad"></a>将本地应用程序添加到 Azure AD

将本地应用程序添加到 Azure AD。 有关快速入门信息，请参阅[将本地应用添加到 Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)。 添加应用程序时，请务必设置“添加本地应用程序”边栏选项卡中的以下两个设置：

- **预身份验证**：输入“Azure Active Directory”。
- **转换应用程序主体中的 URL**：选择“是”。

要将应用程序与 MCAS 配合使用，这两个设置是必需项。

## <a name="test-the-on-premises-application"></a>测试本地应用程序

将应用程序添加到 Azure AD 后，使用[测试应用程序](application-proxy-add-on-premises-application.md#test-the-application)中的步骤添加用于测试的用户并测试登录。 

## <a name="deploy-conditional-access-app-control"></a>部署条件访问应用控制

若要使用条件访问应用程序控制配置应用程序，请按照[为 Azure AD 应用部署条件访问应用程序控制](/cloud-app-security/proxy-deployment-aad)中的说明进行操作。


## <a name="test-conditional-access-app-control"></a>测试条件访问应用控制

若要使用条件访问应用程序控制测试 Azure AD 应用程序的部署，请按照[测试 Azure AD 应用的部署](/cloud-app-security/proxy-deployment-aad)中的说明进行操作。





