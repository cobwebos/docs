---
title: 查看 Azure Maps 的身份验证详细信息
description: 使用 Azure 门户查看 Azure Maps 的身份验证详细信息。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: a6ffcbf5a8c36958dd3ea74de4d826fe25a1139c
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2020
ms.locfileid: "87126443"
---
可以在 Azure 门户中查看 Azure Maps 帐户身份验证详细信息。 在帐户的 "**设置**" 菜单上，选择 "**身份验证**"。

![身份验证详细信息](../media/how-to-manage-authentication/how-to-view-auth.png)

创建 Azure Maps 帐户后，Azure Maps `x-ms-client-id` 值就会出现在 Azure 门户身份验证详细信息页中。 此值表示将用于 REST API 请求的帐户。 此值应存储在应用程序配置中，并在对 Azure Maps 使用 Azure AD 身份验证时在发出 http 请求之前检索。
