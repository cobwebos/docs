---
title: "在 Azure 门户中无法在 Windows 设备上切换位置 | Microsoft Docs"
description: "了解在哪些情况下可能会碰到无法切换位置的问题，以及可以检查哪些内容来避免遇到此类对话框。"
services: active-directory
keywords: "基于设备的条件访问, 设备注册, 启用设备注册, 设备注册和 MDM"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/05/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 4d7704c03bece51957efa2a56577b8808cc62c79
ms.contentlocale: zh-cn
ms.lasthandoff: 07/08/2017


---
# 在 Windows 设备上无法切换位置
<a id="you-cant-get-there-from-here-on-a-windows-device" class="xliff"></a>

在尝试访问组织的 SharePoint Online Intranet 或进行其他类似访问时，你可能会看到一个页面，指出“无法从此处转到该位置”。 之所以出现此页面，是因为管理员配置了条件访问策略，阻止在某些情况下访问组织的资源。 有时可能需要联系支持人员或管理员才能解决此问题，但有时你也可以自行尝试一些解决方法。

如果使用的是 **Windows** 设备，应检查以下项目：

- 使用的浏览器是否受支持？

- 在设备上运行的 Windows 版本是否受支持？

- 设备是否合规？






## 支持的浏览器
<a id="supported-browser" class="xliff"></a>

如果管理员配置了条件访问策略，则你只能使用支持的浏览器访问组织的资源。 Windows 设备仅支持 **Internet Explorer** 和 **Edge**。

查看错误页的详细信息部分，可以轻松判断无法访问资源的原因是否为使用了不受支持的浏览器：

![针对不支持的浏览器的“无法从此处访问”消息](./media/active-directory-conditional-access-device-remediation/02.png "方案")

唯一的修复方法是在设备平台上使用应用程序支持的浏览器。 有关受支持浏览器的完整列表，请参阅[支持的浏览器](active-directory-conditional-access-supported-apps.md#supported-browsers-for-device-based-policies)。  


## 支持的 Windows 版本
<a id="supported-versions-of-windows" class="xliff"></a>

设备上的 Windows 操作系统必须满足以下要求： 

- 如果在设备上运行 Windows 桌面操作系统，其版本需为 Windows 7 或更高。
- 如果在设备上运行 Windows Server 操作系统，其版本需为 Windows Server 2008 R2 或更高。 


## 合规的设备
<a id="compliant-device" class="xliff"></a>

管理员可能配置了条件访问策略，只允许从合规的设备访问组织的资源。 若要符合规范，必须将设备加入本地 Active Directory 或 Azure Active Directory。

查看错误页的详细信息部分，可以轻松判断无法访问资源的原因是否为设备不合规：
 
![针对未注册的设备的“无法从此处访问”消息](./media/active-directory-conditional-access-device-remediation/01.png "方案")


### 设备是否已加入本地 Active Directory？
<a id="is-your-device-joined-to-an-on-premises-active-directory" class="xliff"></a>

**如果设备已加入组织中的本地 Active Directory：**

1. 确保你使用工作帐户（Active Directory 帐户）登录到 Windows。
2. 通过虚拟专用网络 (VPN) 或 DirectAccess 连接到公司网络。
3. 建立连接后，按 Windows 徽标键 + L 键来锁定 Windows 会话。
4. 输入工作帐户凭据解锁 Windows 会话。
5. 等待一分钟，然后再次尝试访问应用程序或服务。
6. 如果看到相同页面，请单击“更多详细信息”链接，然后与管理员联系，并提供详细信息。


### 设备是否未加入本地 Active Directory？
<a id="is-your-device-not-joined-to-an-on-premises-active-directory" class="xliff"></a>

如果设备未加入本地 Active Directory 并运行 Windows 10，你可以采用两种做法：

* 运行 Azure AD Join
* 将工作帐户或学校帐户添加到 Windows

有关这些选项有什么不同的信息，请参阅[在工作区中使用 Windows 10 设备](active-directory-azureadjoin-windows10-devices.md)。  
如果设备：

- 属于你的组织，则你应该运行 Azure AD Join。
- 是个人设备或 Windows 手机，则你应该将工作或学校帐户添加到 Windows 



#### Windows 10 上的 Azure AD Join
<a id="azure-ad-join-on-windows-10" class="xliff"></a>

将设备加入 Azure AD 的步骤与设备上运行的 Windows 10 版本相关。 若要确定 Windows 10 操作系统的版本，请运行 **winver** 命令： 

![Windows 版本](./media/active-directory-conditional-access-device-remediation/03.png )


**Windows 10 周年更新（版本 1607）：**

1. 打开“设置”应用。
2. 单击“帐户” > “访问工作或学校”。
3. 单击“连接”。
4. 单击“将此设备加入 Azure AD”。
5. 向组织进行身份验证，提供多重身份验证，如果出现提示，则按照所示的步骤进行操作。
6. 注销，然后使用工作帐户登录。
7. 再次尝试访问应用程序。

**Windows 10 November 2015 Update（版本 1511）：**

1. 打开“设置”应用。
2. 单击“系统” > “关于”。
3. 单击“加入 Azure AD”。
4. 向组织进行身份验证，提供多重身份验证，如果出现提示，则按照所示的步骤进行操作。
5. 注销，然后使用工作帐户（Azure AD 帐户）登录。
6. 再次尝试访问应用程序。


#### Windows 8.1 上的工作区加入
<a id="workplace-join-on-windows-81" class="xliff"></a>

如果设备未加入域，并且运行 Windows 8.1，若要执行工作区加入并在 Microsoft Intune 中注册，请执行以下步骤：

1. 打开“电脑设置”。
2. 单击“网络” > “工作区”。
3. 单击“加入” 。
4. 向组织进行身份验证，提供多重身份验证，如果出现提示，则按照所示的步骤进行操作。
5. 单击“启用”。
6. 再次尝试访问应用程序。



#### 将工作帐户或学校帐户添加到 Windows
<a id="add-your-work-or-school-account-to-windows" class="xliff"></a> 


**Windows 10 周年更新（版本 1607）：**

1. 打开“设置”应用。
2. 单击“帐户” > “访问工作或学校”。
3. 单击“连接”。
4. 向组织进行身份验证，提供多重身份验证，如果出现提示，则按照所示的步骤进行操作。
5. 再次尝试访问应用程序。


**Windows 10 November 2015 Update（版本 1511）：**

1. 打开“设置”应用。
2. 单击“帐户” > “你的帐户”。
3. 单击“工作或学校帐户”。
4. 向组织进行身份验证，提供多重身份验证，如果出现提示，则按照所示的步骤进行操作。
5. 再次尝试访问应用程序。





## 后续步骤
<a id="next-steps" class="xliff"></a>
[Azure Active Directory 条件访问](active-directory-conditional-access.md)


