---
title: 支持托管服务标识的 Azure 服务
description: 支持托管服务标识和 Azure AD 身份验证的服务列表
services: active-directory
author: daveba
ms.author: daveba
ms.date: 03/28/2018
ms.topic: reference
ms.service: active-directory
ms.component: msi
manager: mtillman
ms.openlocfilehash: d31c169600c594fc4764262cb8d080e9aee96b21
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
ms.locfileid: "34353605"
---
# <a name="services-that-support-managed-service-identity"></a>支持托管服务标识的服务 

托管服务标识为 Azure 服务提供了 Azure Active Directory 中的自动托管标识。 使用托管标识，可对支持 Azure AD 身份验证的任何服务进行身份验证，而无需在代码中插入凭据。 我们正在跨 Azure 将 MSI 与 Azure AD 身份验证相集成。 请经常再回来看看，确定这部分内容是否有更新。

## <a name="azure-services-that-support-managed-service-identity"></a>支持托管服务标识的 Azure 服务

以下 Azure 服务支持托管服务标识。

| 服务 | 状态 | 日期 | 配置 | 获取令牌 |
| ------- | ------ | ---- | --------- | ----------- |
| Azure 虚拟机 | 预览 | 2017 年 9 月 | [Azure 门户](qs-configure-portal-windows-vm.md)<br>[PowerShell](qs-configure-powershell-windows-vm.md)<br>[Azure CLI](qs-configure-cli-windows-vm.md)<br>[Azure 资源管理器模板](qs-configure-template-windows-vm.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell) |
| Azure 应用服务 | 预览 | 2017 年 9 月 | [Azure 门户](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Azure 资源管理器模板](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Azure Functions | 预览 | 2017 年 9 月 | [Azure 门户](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Azure 资源管理器模板](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Azure 数据工厂 V2 | 预览 | 2017 年 11 月 | [Azure 门户](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |
| Azure API 管理 | 预览 | 2017 年 10 月 | [Azure 资源管理器模板](/azure/api-management/api-management-howto-use-managed-service-identity) |

## <a name="azure-services-that-support-azure-ad-authentication"></a>支持 Azure AD 身份验证的 Azure 服务

以下服务支持 Azure AD 身份验证，已通过使用托管服务标识的客户端服务进行测试。

| 服务 | 资源 ID | 状态 | 日期 | 分配访问权限 |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure 资源管理器 | https://management.azure.com/ | 可用 | 2017 年 9 月 | [Azure 门户](howto-assign-access-portal.md) <br>[PowerShell](howto-assign-access-powershell.md) <br>[Azure CLI](howto-assign-access-CLI.md) |
| Azure 密钥保管库 | https://vault.azure.net | 可用 | 2017 年 9 月 | |
| Azure Data Lake | https://datalake.azure.net/ | 可用 | 2017 年 9 月 | |
| Azure SQL | https://database.windows.net/ | 可用 | 2017 年 10 月 | |
| Azure 事件中心 | https://eventhubs.azure.net | 可用 | 2017 年 12 月 | |
| Azure 服务总线 | https://servicebus.azure.net | 可用 | 2017 年 12 月 | |
