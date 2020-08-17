---
title: Azure AD 中的应用程序设置的已知问题
description: 了解在 Azure AD 中使用自动应用程序预配时的已知问题。
author: kenwith
ms.author: kenwith
manager: celestedg
services: active-directory
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 08/12/2020
ms.reviewer: arvinh
ms.openlocfilehash: 23c3dfc6670c96f44a10b2ad5d5bfeb3ff96382c
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2020
ms.locfileid: "88270999"
---
# <a name="known-issues-application-provisioning"></a>已知问题：应用程序预配
使用应用程序设置时应注意的已知问题。 你可以在 UserVoice 上提供有关应用程序预配服务的反馈，请参阅 [Azure AD 应用程序预配 UserVoice](https://aka.ms/appprovisioningfeaturerequest)。 我们密切关注 UserVoice，以便我们改进服务。 

> [!NOTE]
> 这并不是已知问题的完整列表。 如果你知道未列出的问题，请在页面底部提供反馈。

## <a name="authorization"></a>授权 

**成功连接测试后无法保存**

如果可以成功测试连接，但无法保存，则已超出允许的凭据存储限制。 若要了解详细信息，请参阅 [保存管理员凭据时出现问题](application-provisioning-config-problem-storage-limit.md)。

**无法保存**

必须填写 "租户 URL"、"机密令牌" 和 "通知电子邮件" 才能保存。 不能仅提供其中一个。 

**无法将预配模式改回手动**

首次配置预配后，你会注意到预配模式已从手动切换为自动。 不能将其改回手动。 但你可以通过 UI 关闭预配。 在 UI 中关闭预配与将下拉列表设置为 "手动" 完全相同。  


## <a name="attribute-mappings"></a>属性映射 

**特性 SamAccountName 或 userType 不可用作源属性**

默认情况下，特性 SamAccountName 和 userType 不可用作源特性。 扩展架构以添加属性。 可以通过扩展架构将属性添加到可用源属性列表。 若要了解详细信息，请参阅 [缺少 source 属性](user-provisioning-sync-attributes-for-mapping.md)。 

**架构扩展缺少源属性下拉列表**

有时，UI 中的 "源属性" 下拉列表中会缺少架构的扩展。 进入属性映射的高级设置，并手动添加属性。 若要了解详细信息，请参阅 [自定义属性映射](customize-application-attributes.md)。

**无法设置 Null 属性**

Azure AD 当前无法预配 null 属性。 如果用户对象上的属性为 null，则将跳过它。 

**特性映射表达式的最大字符数**

特性映射表达式最多可以有10000个字符。 


## <a name="service-issues"></a>服务问题 

**不支持的方案**

- 不支持预配密码。 
- 不支持预配嵌套组。 
- 由于租户规模的原因，不支持预配到 B2C 租户。 

**预配间隔已修复** 预配周期的间隔 [时间](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user#how-long-will-it-take-to-provision-users) 当前不可配置。 

**更改不会从目标应用移动到 Azure AD**

应用预配服务不知道外部应用中所做的更改。 因此，不会执行回滚操作。 应用预配服务依赖于在 Azure AD 所做的更改。 

**预配周期持续到完成**

设置设置 `enabled = off` 或命中停止时，当前的预配周期将继续运行，直到完成。 该服务将停止执行任何将来的循环，直到再次启用设置。

**未预配组的成员**

当某个组处于范围内并且某个成员超出范围时，将设置该组。 不会设置超出范围的用户。 如果成员返回到范围中，服务将不会立即检测到更改。 重新启动预配将解决此问题。 建议定期重启服务，以确保所有用户均已正确设置。  


## <a name="next-steps"></a>后续步骤
- [预配工作原理](how-provisioning-works.md)
