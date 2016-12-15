
---
标题：Azure Active Directory 条件性访问技术参考 | Microsoft Docs 说明：借助条件性访问控制，Azure Active Directory 在验证用户身份时先检查你选取的特定条件，然后才允许访问应用程序。 一旦符合这些条件，用户就会通过身份验证并获权访问应用程序。
服务：active-directory 文档中心：'' 作者：MarkusVi 管理员：femila 编辑：''

ms.assetid：56a5bade-7dcc-4dcf-8092-a7d4bf5df3c1 ms.service：active-directory ms.devlang：na ms.topic：文章 ms.tgt_pltfrm：na ms.workload：标识 ms.date：2016/10/20/ ms.author：markvi

---
# <a name="azure-active-directory-conditional-access-technical-reference"></a>Azure Active Directory 条件性访问技术参考
## <a name="services-enabled-with-conditional-access"></a>通过条件性访问启用的服务
各种 Azure AD 应用程序类型支持条件性访问规则。 这些应用程序包括：

* Azure AD 应用程序库中的联合应用程序
* Azure AD 应用程序库中的密码 SSO 应用程序
* 向 Azure 应用程序代理注册的应用程序
* 向 Azure AD 注册的开发业务应用程序和多租户应用程序
* Visual Studio Online
* Azure 远程应用
* Dynamics CRM
* Microsoft Office 365 Yammer
* Microsoft Office 365 Exchange Online
* Microsoft Office 365 SharePoint Online（包括 OneDrive for Business）

## <a name="enable-access-rules"></a>启用访问规则
可以对每个应用程序启用或禁用每个规则。 当规则处于“启用”状态时，可以对访问应用程序的用户启用并执行规则。 当规则处于“禁用”状态时，则不使用规则，并且规则不会影响用户的登录体验。

## <a name="applying-rules-to-specific-users"></a>将规则应用于特定用户
通过设置“应用于”可将规则应用于基于安全组的特定用户集。 可将“应用于”设置为“所有用户”或“组”。 当设置为“所有用户”时，规则应用于有权访问应用程序的任何用户。 “组”选项允许选择特定安全组和通讯组，将仅对这些组执行规则。

部署规则时，通常首先将它应用于有限的一组用户，这些用户是试验组的成员。 一旦完成后，可以将规则应用到**所有用户**。 此操作可对组织中的所有用户执行规则。

使用“排除”选项也可以将选择的组从策略中免除。 这些组的任何成员将被免除，即使他们出现在包含的组中也是如此。

## <a name="at-work-networks"></a>“工作”网络
使用“工作”网络的条件性访问规则依赖于在 Azure AD 中配置的受信任的 IP 地址范围或 AD FS 中“内部公司网络”声明的使用。 这些规则包括：

* 不工作时要求多重身份验证
* 不工作时阻止访问

用于指定“工作”网络的选项

1. 在[多重身份验证配置页](../multi-factor-authentication/multi-factor-authentication-whats-next.md)上配置受信任的 IP 地址范围。 条件性访问策略在每个身份验证请求和令牌颁发中使用此配置的范围来计算规则。 
2. 配置内部公司网络声明的使用，此选项可以通过 AD FS 用于联合目录。 [了解有关内部公司网络声明的更多信息](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips)。
3. 配置公共 IP 地址范围。 在“配置”选项卡上针对你的目录可以设置公共 IP 地址。 条件性访问将这些地址用作“工作”IP 地址，这样可以配置其他地址范围，超出了 MFA 设置页所实施的 50 个 IP 地址的限制。

## <a name="rules-based-on-application-sensitivity"></a>基于应用程序敏感性的规则
对每个应用程序配置规则，可以保护高价值的服务且不会影响访问其他服务。 可以在应用程序的“配置”选项卡上配置条件性访问规则。 

当前提供的规则：

* **需要多重身份验证**
  
  * 此策略应用到的所有用户需要至少一次通过多重身份验证进行验证。
* **不在工作网络时要求多重身份验证**
  
  * 如果应用此策略，那么用户从非工作的远程位置访问服务时，要求所有用户已执行至少一次多重身份验证。 如果用户从工作网络移至远程位置，那么当他们访问服务时需要执行多重身份验证。
* **不在工作网络时阻止访问** 
  
  * 如果将“不在工作网络时阻止访问”策略应用于用户，那么当用户从工作网络移到远程位置时，将阻止他们访问。  当用户位于工作网络时将重新允许他们访问。

## <a name="related-topics"></a>相关主题
* [保护对 Office 365 和其他连接到 Azure Active Directory 的应用的访问](active-directory-conditional-access.md)
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)




<!--HONumber=Nov16_HO3-->


