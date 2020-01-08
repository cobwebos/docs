---
title: 无法使用 Key Vault 证书添加自定义域
titleSuffix: Azure API Management
description: 了解如何解决无法通过使用 key vault 证书在 Azure API 管理中添加自定义域的问题。
services: api-management
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/19/2019
ms.author: tehnoonr
ms.openlocfilehash: a09c15466a4a9f62b2696b087cb7ab23cc767379
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75430578"
---
# <a name="failed-to-update-api-management-service-hostnames"></a>未能更新 API 管理服务主机名

本文介绍添加 Azure API 管理服务的自定义域时可能会遇到的 "无法更新 API 管理服务主机名" 错误。 本文提供了疑难解答步骤来帮助您解决问题。

## <a name="symptoms"></a>症状

尝试使用 Azure Key Vault 的证书为 API 管理服务添加自定义域时，会收到以下错误消息：

- 未能更新 API 管理服务的主机名。 对资源 "https://vaultname.vault.azure.net/secrets/secretname/?api-version=7.0" 的请求失败，状态为 "已禁止"，出现 RequestId：。 异常消息：操作返回了无效的状态代码 "禁止"。

## <a name="cause"></a>原因

API 管理服务没有访问你尝试用于自定义域的密钥保管库的权限。

## <a name="solution"></a>解决方案

若要解决此问题，请执行以下步骤：

1. 中转到[Azure 门户](Https://portal.azure.com)，选择你的 API 管理实例，然后选择 "**托管标识**"。 请确保 "**注册 Azure Active Directory** " 选项设置为 **"是"** 。 
    ![注册 Azure Active Director](./media/api-management-troubleshoot-cannot-add-custom-domain/register-with-aad.png)
1. 在 Azure 门户中，打开**密钥保管库**服务，并选择要用于自定义域的密钥保管库。
1. 选择 "**访问策略**"，并检查是否存在与 API 管理服务实例的名称匹配的服务主体。 如果有，请选择该服务主体，并确保其具有在 "**机密权限**" 下列出的 "**获取**" 权限。  
    ![为服务主体](./media/api-management-troubleshoot-cannot-add-custom-domain/access-policy.png) 添加访问策略
1. 如果 API 管理服务不在列表中，请选择 "**添加访问策略**"，然后创建以下访问策略：
    - **从模板配置**：无
    - **选择主体**：搜索 API 管理服务的名称，然后从列表中选择它
    - **密钥权限**：无
    - **机密权限**： Get
    - **证书权限**：无
1. 选择 **"确定"** 以创建访问策略。
1. 选择“保存”，保存更改。

检查是否解决了问题。 为此，请尝试使用 Key Vault 证书在 API 管理服务中创建自定义域。

## <a name="next-steps"></a>后续步骤
详细了解 API 管理服务：

- 观看有关 API 管理的更多[视频](https://azure.microsoft.com/documentation/videos/index/?services=api-management)。
* 有关保护后端服务的其他方法，请参阅[使用证书进行相互身份验证](api-management-howto-mutual-certificates.md)。

* [创建 API 管理服务实例](get-started-create-service-instance.md)。
* [管理第一个 API](import-and-publish.md)。