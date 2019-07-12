---
title: 在 Azure Active Directory B2C 中管理对资源和数据的威胁
description: 了解 Azure Active Directory B2C 中用于拒绝服务攻击和密码攻击的检测和缓解技术。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7232917df6018c9c8afc7e7edd3730a277b193f4
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798217"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中管理对资源和数据的威胁

Azure Active Directory (Azure AD) B2C 具有可以针对对资源和数据的威胁提供保护的内置功能。 这些威胁包括拒绝服务攻击和密码攻击。 拒绝服务攻击可能会使目标用户无法使用资源。 密码攻击会导致未经授权的资源访问。

## <a name="denial-of-service-attacks"></a>拒绝服务攻击

Azure AD B2C 使用 SYN cookie 防御 SYN 洪流攻击。 Azure AD B2C 还使用速率和连接限制防止拒绝服务攻击。

## <a name="password-attacks"></a>密码攻击

要求用户所设密码的复杂性合理。 Azure AD B2C 针对密码攻击实施了缓解技术。 缓解技术包括暴力破解密码攻击和字典密码攻击的检测。 Azure AD B2C 使用各种信号分析请求的完整性。 Azure AD B2C 旨在智能地将目标用户与黑客和僵尸网络区分开来。

Azure AD B2C 使用复杂策略来锁定帐户。 将根据请求的 IP 和输入的密码锁定帐户。 锁定的持续时间也会根据存在攻击的可能性而延长。 密码未成功尝试了 10 次后 （默认尝试阈值） 在一分钟锁定时发生。 下次在该帐户后登录名不成功解锁 （即之后该帐户具有已自动解除锁定服务锁定期限过期后）,，另一个一分钟锁定发生并继续为每个成功的登录名。 重复输入相同的密码不会计为多次不成功登录。

前 10 个锁定期限的长度为一分钟。 接下来的 10 个锁定期限时间稍长，并且每 10 个锁定期限后都会增加持续时间。 当帐户未锁定时，锁定计数器在成功登录后重置为零。 锁定期限可以持续长达五个小时。

## <a name="manage-password-protection-settings"></a>管理密码的保护设置

若要管理密码的保护设置，包括锁定阈值：

1. 导航到 [Azure 门户](https://portal.azure.com)。
1. 选择**目录 + 订阅**在门户的右上方菜单中进行筛选，然后选择你的 Azure AD B2C 租户。
1. 选择**Azure Active Directory**的左侧菜单中 (或选择**的所有服务**在门户的左上角部分中，然后搜索并选择*Azure Active Directory*).
1. 下**安全**，选择**身份验证方法**，然后选择**密码保护**。
1. 输入你所需的密码的保护设置，然后选择**保存**。

    ![在 Azure AD 设置 azure 门户的密码保护页](media/active-directory-b2c-reference-threat-management/portal-02-password-protection.png)
    <br />*将锁定阈值设置为 5 英寸**密码保护**设置*。

## <a name="view-locked-out-accounts"></a>查看锁定帐户

若要获取有关锁定的帐户的信息，可以检查 Active Directory[登录活动报告](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)。 下**状态**，选择**失败**。 失败登录尝试都**登录错误代码**的`50053`指示锁定的帐户：

![Azure AD 登录报告显示锁定帐户部分](media/active-directory-b2c-reference-threat-management/portal-01-locked-account.png)

若要了解如何在 Azure Active Directory 中查看登录活动报表，请参阅[登录活动报告错误代码](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)。
