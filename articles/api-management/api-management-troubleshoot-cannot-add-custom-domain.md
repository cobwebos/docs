---
title: 无法使用 Key Vault 证书添加自定义域
titleSuffix: Azure API Management
description: 了解如何排查无法使用密钥保管库证书在 Azure API 管理中添加自定义域的问题。
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "75430578"
---
# <a name="failed-to-update-api-management-service-hostnames"></a>无法更新 API 管理服务主机名

本文介绍在为 Azure API 管理服务添加自定义域时可能会遇到的“无法更新 API 管理服务主机名”错误。 本文提供有助于解决此问题的故障排除步骤。

## <a name="symptoms"></a>症状

尝试使用 Azure Key Vault 中的证书为 API 管理服务添加自定义域时，出现以下错误消息：

- 无法更新 API 管理服务主机名。 向资源 "https://vaultname.vault.azure.net/secrets/secretname/?api-version=7.0" 发出的请求失败，StatusCode 为:对以下 RequestId 禁止: 。 异常消息:操作返回了无效的状态代码“禁止”。

## <a name="cause"></a>原因

API 管理服务无权访问你尝试用于自定义域的密钥保管库。

## <a name="solution"></a>解决方案

若要解决此问题，请执行以下步骤：

1. 转到 [Azure 门户](Https://portal.azure.com)，选择 API 管理实例，然后选择“托管标识”。  确保将“注册到 Azure Active Directory”选项设置为“是”。   
    ![注册到 Azure Active Directory](./media/api-management-troubleshoot-cannot-add-custom-domain/register-with-aad.png)
1. 在 Azure 门户中打开“密钥保管库”服务，选择尝试用于自定义域的密钥保管库。 
1. 选择“访问策略”，  检查是否有服务主体与 API 管理服务实例的名称匹配。 如果有，请选择该服务主体，并确保它有“获取”  权限列在“机密权限”下。   
    ![添加服务主体的访问策略](./media/api-management-troubleshoot-cannot-add-custom-domain/access-policy.png)
1. 如果 API 管理服务未在列表中，请选择“添加访问策略”，然后创建以下访问策略： 
    - **从模板配置**：无
    - **选择主体**：搜索 API 管理服务的名称，然后从列表中选择它
    - **密钥权限**：无
    - **机密权限**：Get
    - **证书权限**：无
1. 选择“确定”  以创建访问策略。
1. 选择“保存”  ，保存更改。

检查是否解决了问题。 为此，请尝试使用 Key Vault 证书在 API 管理服务中创建自定义域。

## <a name="next-steps"></a>后续步骤
详细了解 API 管理服务：

- 观看有关 API 管理的更多[视频](https://azure.microsoft.com/documentation/videos/index/?services=api-management)。
* 有关保护后端服务的其他方法，请参阅[使用证书进行相互身份验证](api-management-howto-mutual-certificates.md)。

* [创建 API 管理服务实例](get-started-create-service-instance.md)。
* [管理第一个 API](import-and-publish.md)。