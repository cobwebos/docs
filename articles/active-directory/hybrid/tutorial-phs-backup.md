---
title: 教程：在 Azure AD Connect 中将 PHS 设置为 AD FS 的后备 | Microsoft Docs
description: 演示如何启用密码哈希同步作为 AD FS 的后备。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e5ad7badfa44a006fd7e71d3b0e42ee95ac698d
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64919009"
---
# <a name="tutorial--setting-up-phs-as-backup-for-ad-fs-in-azure-ad-connect"></a>教程：在 Azure AD Connect 中将 PHS 设置为 AD FS 的后备

下面的教程将指导你设置密码哈希同步作为 AD FS 的后备和故障转移。  本文档还将演示在 AD FS 失败或不可用的情况下，如何启用密码哈希同步作为主要身份验证方法。

>[!NOTE] 
>虽然这些步骤通常在紧急情况或服务中断情况下执行，但建议你在发生服务中断之前测试这些步骤并验证相关过程。

>[!NOTE]
>如果你无法访问 Azure AD Connect 服务器或者该服务器无法访问 Internet，则可联系 [Microsoft 支持部门](https://support.microsoft.com/en-us/contactus/)，要求其更改 Azure AD 端。

## <a name="prerequisites"></a>先决条件
本教程以[教程：将单个 AD 林环境与云联合起来](tutorial-federation.md)为基础，在尝试本教程之前，必须完成此教程。  如果尚未完成，请在尝试本文档中的步骤之前完成该教程。

>[!IMPORTANT]
>在切换到 PHS 之前，应创建 AD FS 环境的备份。  可使用 [AD FS 快速还原工具](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool#how-to-use-the-tool)完成此操作。

## <a name="enable-phs-in-azure-ad-connect"></a>在 Azure AD Connect 中启用 PHS
我们已经有一个使用联合身份验证的 Azure AD Connect 环境，那么第一步就是打开密码哈希同步，并允许 Azure AD Connect 同步哈希。

请执行以下操作：

1.  双击在桌面上创建的 Azure AD Connect 图标
2.  单击 **“配置”** 。
3.  从“其他任务”页中选择“自定义同步选项”，并单击“下一步”。
4.  输入全局管理员的用户名和密码。  上一教程中已在[此处](tutorial-federation.md#create-a-global-administrator-in-azure-ad)创建该帐户。
5.  在“连接目录”屏幕上，单击“下一步”。
6.  在“域和 OU 筛选”屏幕上，单击“下一步”。
7.  在“可选功能”屏幕上，选中“密码哈希同步”并单击“下一步”。
![Select](media/tutorial-phs-backup/backup1.png)</br>
8.  在“准备好配置”屏幕上，单击“配置”。
9.  一旦配置完成，单击“退出”。
10. 就这么简单！  完成。  密码哈希同步即将发生，如果 AD FS 变得不可用，即可用作后备。

## <a name="switch-to-password-hash-synchronization"></a>切换到密码哈希同步
现在，我们将展示如何切换到密码哈希同步。 在开始之前，请考虑在哪些情况下应进行切换。 不要因为暂时性原因（例如网络中断、微小的 AD FS 问题，或影响部分用户的问题）进行切换。 如果由于解决问题的时间过长而决定进行切换，请执行以下操作：

> [!IMPORTANT]
> 请注意，密码哈希同步到 Azure AD 需要一些时间。  这意味着完成同步过程可能需要长达 3 小时的时间，然后你才能开始使用密码哈希进行身份验证。

1. 双击在桌面上创建的 Azure AD Connect 图标
2.  单击 **“配置”** 。
3.  选择“更改用户登录”并单击“下一步”。
![更改](media/tutorial-phs-backup/backup2.png)</br>
4.  输入全局管理员的用户名和密码。  上一教程中已在[此处](tutorial-federation.md#create-a-global-administrator-in-azure-ad)创建该帐户。
5.  在“用户登录”屏幕上，选择“密码哈希同步”，并选中“请勿切换用户帐户”框。  
6.  保留默认选中的“启用单一登录”并单击“下一步”。
7.  在“启用单一登录”屏幕上单击“下一步”。
8.  在“准备好配置”屏幕上，单击“配置”。
9.  配置完成后，单击“退出”。
10. 用户现在可以使用他们的密码登录到 Azure 和 Azure 服务。

## <a name="test-signing-in-with-one-of-our-users"></a>使用我们的某位用户测试登录

1. 浏览到 [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. 使用在我们的新租户中创建的用户帐户登录。  需要使用以下格式登录：(user@domain.onmicrosoft.com)。 使用用户用于在本地登录的相同密码。</br>
   ![验证](media/tutorial-password-hash-sync/verify1.png)</br>

## <a name="switch-back-to-federation"></a>切换回联合身份验证
现在，我们将展示如何切换回联合身份验证。  为此，请执行以下步骤：

1.  双击在桌面上创建的 Azure AD Connect 图标
2.  单击 **“配置”** 。
3.  选择“更改用户登录”并单击“下一步”。
4.  输入全局管理员的用户名和密码。  这是上一教程中在[此处](tutorial-federation.md#create-a-global-administrator-in-azure-ad)创建的帐户。
5.  在“用户登录”屏幕上，选择“使用 AD FS 进行联合身份验证”，然后单击“下一步”。  
6. 在“域管理员凭据”页上，输入 contoso\管理员用户名和密码，然后单击“下一步”。
7. 在“AD FS 场”屏幕上，单击“下一步”。
8. 在“Azure AD 域”屏幕上，从下拉列表中选择域，然后单击“下一步”。
9. 在“准备好配置”屏幕上，单击“配置”。
10. 配置完成后，单击“下一步”。
![配置](media/tutorial-phs-backup/backup4.png)</br>
11. 在“验证联合身份验证连接性”屏幕上，单击“验证”。  可能需要配置 DNS 记录（添加 A 和 AAAA 记录），然后此操作才能成功完成。
![验证](media/tutorial-phs-backup/backup5.png)</br>
12. 单击“退出”。

## <a name="reset-the-ad-fs-and-azure-trust"></a>重置 AD FS 和 Azure 信任
现在需重置 AD FS 与 Azure 之间的信任。

1.  双击在桌面上创建的 Azure AD Connect 图标
2.  单击 **“配置”** 。
3.  选择“管理联合身份验证”，然后单击“下一步”。
4.  选择“重置 Azure AD 信任”，然后单击“下一步”。
![重置](media/tutorial-phs-backup/backup6.png)</br>
5.  在“连接到 Azure AD”屏幕上，输入全局管理员的用户名和密码。
6.  在“连接到 AD FS”屏幕上，输入 contoso\管理员用户名和密码，然后单击“下一步”。
7.  在“证书”屏幕上，单击“下一步”。

## <a name="test-signing-in-with-one-of-our-users"></a>使用我们的某位用户测试登录

1.  浏览到 [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. 使用在我们的新租户中创建的用户帐户登录。  需要使用以下格式登录：(user@domain.onmicrosoft.com)。 使用用户用于在本地登录的相同密码。
![验证](media/tutorial-password-hash-sync/verify1.png)

现在已经成功设置了一个混合标识环境，可以使用它来测试和熟悉 Azure 提供的功能。

## <a name="next-steps"></a>后续步骤


- [硬件和先决条件](how-to-connect-install-prerequisites.md) 
- [快速设置](how-to-connect-install-express.md)
- [密码哈希同步](how-to-connect-password-hash-synchronization.md)
