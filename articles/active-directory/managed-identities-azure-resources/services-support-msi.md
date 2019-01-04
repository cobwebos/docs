---
title: 支持 Azure 资源托管标识的 Azure 服务
description: 支持 Azure 资源托管标识和 Azure AD 身份验证的服务列表
services: active-directory
author: daveba
ms.author: daveba
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: active-directory
ms.component: msi
manager: mtillman
ms.openlocfilehash: 3fdbac019849bc97e8d336b75f26a8fe0a05c449
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2018
ms.locfileid: "53713108"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>支持 Azure 资源托管标识的服务

Azure 资源的托管标识在 Azure Active Directory 中为 Azure 服务提供了一个自动托管标识。 使用托管标识，可对支持 Azure AD 身份验证的任何服务进行身份验证，而无需在代码中插入凭据。 我们正在跨 Azure 将 Azure 资源托管标识与 Azure AD 身份验证集成。 请经常再回来看看，确定这部分内容是否有更新。

> [!NOTE]
> Azure 资源托管标识是以前称为托管服务标识 (MSI) 的服务的新名称。

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>支持 Azure 资源托管标识的 Azure 服务

以下 Azure 服务支持 Azure 资源托管标识：

| 服务 | 系统分配的状态 | 用户分配的状态| 配置 | 获取令牌 |
| ------- | ------ | ---- | --------- | ----------- |
| Azure 虚拟机 | 可用 | 预览 | [Azure 门户](qs-configure-portal-windows-vm.md)<br>[PowerShell](qs-configure-powershell-windows-vm.md)<br>[Azure CLI](qs-configure-cli-windows-vm.md)<br>[Azure 资源管理器模板](qs-configure-template-windows-vm.md)<br>[REST](qs-configure-rest-vm.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell) |
| 虚拟机规模集 | 可用 | 预览 | [Azure 门户](qs-configure-portal-windows-vmss.md)<br>[PowerShell](qs-configure-powershell-windows-vmss.md)<br>[Azure CLI](qs-configure-cli-windows-vmss.md)<br>[Azure 资源管理器模板](qs-configure-template-windows-vmss.md)<br>[REST](qs-configure-rest-vmss.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell)
| Azure 应用服务 | Windows:可用 <br> Linux：预览 | 预览 | [Azure 门户](/azure/app-service/overview-managed-identity#using-the-azure-portal)<br>[Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)<br>[Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)<br>[Azure 资源管理器模板](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template) | [REST](/azure/app-service/overview-managed-identity#using-the-rest-protocol)<br>[.NET](/azure/app-service/overview-managed-identity#asal)<br>[JavaScript](/azure/app-service/overview-managed-identity#token-js)<br>[PowerShell](/azure/app-service/overview-managed-identity#token-powershell)  |
| Azure Functions | 可用 | 预览 | [Azure 门户](/azure/app-service/overview-managed-identity#using-the-azure-portal)<br>[Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)<br>[Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)<br>[Azure 资源管理器模板](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template) | [REST](/azure/app-service/overview-managed-identity#using-the-rest-protocol)<br>[.NET](/azure/app-service/overview-managed-identity#asal)<br>[JavaScript](/azure/app-service/overview-managed-identity#token-js)<br>[PowerShell](/azure/app-service/overview-managed-identity#token-powershell) |
| Azure 逻辑应用 | 可用 | 不可用 | [Azure 门户](/azure/logic-apps/create-managed-service-identity#azure-portal)<br>[Azure 资源管理器模板](/azure/app-service/overview-managed-identity#deployment-template) |  |
| Azure 数据工厂 V2 | 可用 | 不可用 | [Azure 门户](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |
| Azure API 管理 | 可用 | 不可用 | [Azure 资源管理器模板](/azure/api-management/api-management-howto-use-managed-service-identity) |
| Azure 容器实例 | Linux：预览<br>Windows:不可用 | Linux：预览<br>Windows:不可用 | [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)<br>[Azure 资源管理器模板](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)<br>[YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file) |  |


## <a name="azure-services-that-support-azure-ad-authentication"></a>支持 Azure AD 身份验证的 Azure 服务

以下服务支持 Azure AD 身份验证，已通过使用 Azure 资源托管标识的客户端服务进行测试。

| 服务 | 资源 ID | 状态 | 日期 | 分配访问权限 |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure 资源管理器 | `https://management.azure.com/` | 可用 | 2017 年 9 月 | [Azure 门户](howto-assign-access-portal.md) <br>[PowerShell](howto-assign-access-powershell.md) <br>[Azure CLI](howto-assign-access-CLI.md) <br>[Azure 资源管理器模板](../../role-based-access-control/role-assignments-template.md) |
| Azure 密钥保管库 | `https://vault.azure.net` | 可用 | 2017 年 9 月 | |
| Azure Data Lake | `https://datalake.azure.net/` | 可用 | 2017 年 9 月 | |
| Azure SQL | `https://database.windows.net/` | 可用 | 2017 年 10 月 | |
| Azure 事件中心 | `https://eventhubs.azure.net` | 预览 | 2017 年 12 月 | |
| Azure 服务总线 | `https://servicebus.azure.net` | 预览 | 2017 年 12 月 | |
| Azure 存储 | `https://storage.azure.com/` | 预览 | 2018 年 5 月 | |