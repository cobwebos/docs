---
title: 排除常见身份验证错误 |Azure 应用商店
description: 提供帮助以排查使用云合作伙伴门户 API 时常见的身份验证错误。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: d8fd1eb4bef987b4a8605e4be780512a914ec8b5
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255985"
---
# <a name="troubleshooting-common-authentication-errors"></a>排查常见的身份验证错误

> [!NOTE]
> 云合作伙伴门户 API 与合作伙伴中心集成，在您的产品/服务迁移到合作伙伴中心后将继续工作。 集成引入了小更改。 查看[云合作伙伴门户 API 参考](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)中列出的更改，以确保代码在迁移到合作伙伴中心后继续工作。

此文章提供帮助以排查使用云合作伙伴门户 API 时常见的身份验证错误。

## <a name="unauthorized-error"></a>未授权的错误

如果一直收到 `401 unauthorized` 错误，请验证你访问令牌是否有效。  如果尚未执行此操作，请创建基本 Azure Active Directory (Azure AD) 应用程序和服务主体，如[使用门户创建可访问资源的 Azure Active Directory 应用程序和服务主体](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)中所述。 然后，使用应用程序或简单的 HTTP POST 请求来验证访问权限。  将需要提供租户 ID、应用程序 ID、对象 ID 和密钥以获取如下图中所示的访问令牌：

![排查 401 错误](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-401-error.jpg)


## <a name="forbidden-error"></a>禁止错误

如果收到 `403 forbidden` 错误，请确保已将正确的服务主体添加到云合作伙伴门户中的发布者帐户。
按照[先决条件](./cloud-partner-portal-api-prerequisites.md)页面中的步骤将服务主体添加到门户。

如果添加了正确的服务主体，则验证所有其他信息。 特别注意在门户中输入的对象 ID。 Azure Active Directory 应用注册页中有两个对象 ID，你必须使用本地对象 ID。 通过转到应用的“应用注册”**** 页，然后单击“本地目录中的托管应用程序”**** 下的应用名称，可以找到正确的值。 这会将你转到应用的本地属性，你可以在****“属性”页中找到正确的对象 ID，如下图所示。 此外，请确保在添加服务主体并进行 API 调用时使用正确的发布者 ID。

![排查 403 错误](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-403-error.jpg)
