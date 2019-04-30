---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 3cfdca99c91dc54a711801d92aa0da91fb9703e4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61484229"
---
> [!IMPORTANT]
> 存储帐户密钥类似于存储帐户的根密码。 始终要小心保护帐户密钥。 避免将其分发给其他用户、对其进行硬编码或将其以纯文本形式保存在其他人可以访问的任何位置。 如果认为帐户密钥可能已泄漏，请使用 Azure 门户重新生成帐户密钥。
> 
> 就像帐户访问密钥一样，对 SAS（共享访问签名）令牌进行保护至关重要。 然而，提供粒度 SAS 会授权客户端访问存储帐户中的资源，不应当将其公开共享。 如果出于故障排除原因而需要共享，请考虑使用日志文件的修订版本或者将 SAS 令牌从日志文件中删除（如果存在），并确保屏幕截图也不包含 SAS 信息。
> 
> Microsoft 建议尽量对 blob 和队列存储应用程序（预览版）使用 Azure Active Directory (Azure AD) 身份验证，以便增强安全性。 有关详细信息，请参阅[使用 Azure Active Directory 对 Azure blob 和队列访问进行身份验证（预览版）](https://docs.microsoft.com/azure/storage/common/storage-auth-aad)。
