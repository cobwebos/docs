# 概述
## [什么是 Azure AD Connect？](active-directory-aadconnect.md)
## [什么是 Azure AD Connect 同步？](active-directory-aadconnectsync-whatis.md)
### [用户和联系人](active-directory-aadconnectsync-understanding-users-and-contacts.md)
### [体系结构](active-directory-aadconnectsync-understanding-architecture.md)
### [声明性预配](active-directory-aadconnectsync-understanding-declarative-provisioning.md)
#### [声明性预配表达式](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)
### [默认配置](active-directory-aadconnectsync-understanding-default-configuration.md)
## [什么是 Azure AD Connect 和联合身份验证？](active-directory-aadconnectfed-whatis.md)


# 入门
## [先决条件](active-directory-aadconnect-prerequisites.md)
## [安装 Azure AD Connect](active-directory-aadconnect-select-installation.md)
### [快速设置](active-directory-aadconnect-get-started-express.md)
### [自定义设置](active-directory-aadconnect-get-started-custom.md)
### [从 DirSync 升级](active-directory-aadconnect-dirsync-upgrade-get-started.md)
### [从以前的版本升级](active-directory-aadconnect-upgrade-previous-version.md)
### [使用现有 ADSync 数据库进行安装](active-directory-aadconnect-existing-database.md)

# 如何
## 规划和设计
### [设计概念](active-directory-aadconnect-design-concepts.md)
### [Azure AD Connect 的拓扑](active-directory-aadconnect-topologies.md)
### [Azure 中的 Active Directory 联合身份验证服务](active-directory-aadconnect-azure-adfs.md)
### [有关实例的特殊注意事项](active-directory-aadconnect-instances.md)
### [如果已有 Azure AD](active-directory-aadconnect-existing-tenant.md)
## [管理 Azure AD Connect](active-directory-aadconnect-whats-next.md)
### [续订 O365 和 Azure AD 的证书](active-directory-aadconnect-o365-certs.md)
### [更新 Active Directory 联合身份验证服务 (AD FS) 场的 SSL 证书](active-directory-aadconnectfed-ssl-update.md)
### [启用设备写回](active-directory-aadconnect-feature-device-writeback.md)
### [用户登录选项](active-directory-aadconnect-user-signin.md)
#### [无缝单一登录](active-directory-aadconnect-sso.md)
##### [快速入门](active-directory-aadconnect-sso-quick-start.md)
##### [工作原理](active-directory-aadconnect-sso-how-it-works.md)
##### [常见问题](active-directory-aadconnect-sso-faq.md)
##### [故障排除](active-directory-aadconnect-troubleshoot-sso.md)
#### [直通身份验证](active-directory-aadconnect-pass-through-authentication.md)
##### [快速入门](active-directory-aadconnect-pass-through-authentication-quick-start.md)
##### [当前限制](active-directory-aadconnect-pass-through-authentication-current-limitations.md)
##### [工作原理](active-directory-aadconnect-pass-through-authentication-how-it-works.md)
##### [升级预览代理](active-directory-aadconnect-pass-through-authentication-upgrade-preview-authentication-agents.md)
##### [智能锁定](active-directory-aadconnect-pass-through-authentication-smart-lockout.md)
##### [常见问题](active-directory-aadconnect-pass-through-authentication-faq.md)
##### [故障排除](active-directory-aadconnect-troubleshoot-pass-through-authentication.md)
### [对联合的多域支持](active-directory-aadconnect-multiple-domains.md)
### [自动升级](active-directory-aadconnect-feature-automatic-upgrade.md)
### [将 SAML 2.0 标识提供者 (IdP) 用于单一登录](active-directory-aadconnect-federation-saml-idp.md)



## 管理 Azure AD Connect Sync
### [防止意外删除](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
### [密码同步](active-directory-aadconnectsync-implement-password-synchronization.md)
### [Azure AD 服务帐户](active-directory-aadconnectsync-howto-azureadaccount.md)
### [安装向导](active-directory-aadconnectsync-installation-wizard.md)
### [更改默认配置](active-directory-aadconnectsync-best-practices-changing-default-configuration.md)
### [配置筛选](active-directory-aadconnectsync-configure-filtering.md)
### [计划程序](active-directory-aadconnectsync-feature-scheduler.md)
### [目录扩展](active-directory-aadconnectsync-feature-directory-extensions.md)

### [更改 Azure AD 同步服务帐户密码](active-directory-aadconnectsync-change-serviceacct-pass.md)
### [更改 AD DS 帐户密码](active-directory-aadconnectsync-change-addsacct-pass.md)
### [启用 AD 回收站](active-directory-aadconnectsync-recycle-bin.md)

### [Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md)
#### [操作](active-directory-aadconnectsync-service-manager-ui-operations.md)
#### [连接器](active-directory-aadconnectsync-service-manager-ui-connectors.md)
#### [Metaverse 设计器](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md)
#### [Metaverse 搜索](active-directory-aadconnectsync-service-manager-ui-mvsearch.md)


## 管理联合身份验证服务
### [管理和自定义](active-directory-aadconnect-federation-management.md)
### [将 Azure AD 的多个实例与 AD FS 的单个实例联合](active-directory-aadconnectfed-single-adfs-multitenant-federation.md)


## 故障排除
### [连接](active-directory-aadconnect-troubleshoot-connectivity.md)
### [同步过程中出错](active-directory-aadconnect-troubleshoot-sync-errors.md)
### [对象不同步](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md)
### [密码同步](active-directory-aadconnectsync-troubleshoot-password-synchronization.md)
### [userCertificate 导致的 LargeObject 错误](active-directory-aadconnectsync-largeobjecterror-usercertificate.md)
### [如何从 LocalDB 10 GB 的限制恢复](active-directory-aadconnect-recover-from-localdb-10gb-limit.md)

# 引用
## [代码示例](https://azure.microsoft.com/en-us/resources/samples/?service=active-directory)
## [标识同步和重复属性复原](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md)
## [混合标识所需的端口和协议](active-directory-aadconnect-ports.md)
## [预览版功能](active-directory-aadconnect-feature-preview.md)
## [版本历史记录](active-directory-aadconnect-version-history.md)
## [帐户和权限](active-directory-aadconnect-accounts-permissions.md)

## Azure AD Connect Sync
### [与 Azure Active Directory 同步的属性](active-directory-aadconnectsync-attributes-synchronized.md)
### [连接器版本发行历史记录](active-directory-aadconnectsync-connector-version-history.md)
### [函数引用](active-directory-aadconnectsync-functions-reference.md)
### [操作任务和注意事项](active-directory-aadconnectsync-operations.md)
### [Azure AD 联合身份验证兼容性列表](active-directory-aadconnect-federation-compatibility.md)
### [技术概念](active-directory-aadconnectsync-technical-concepts.md)
### [服务功能](active-directory-aadconnectsyncservice-features.md)




# 相关内容
## [在云中监视本地标识基础结构和同步服务](../connect-health/active-directory-aadconnect-health.md)
## [混合标识设计指南](https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-overview/)


# 资源
## [Azure 路线图](https://azure.microsoft.com/roadmap/?category=security-identity)
##[Azure AD Connect 常见问题](active-directory-aadconnect-faq.md)
##[DirSync 弃用](active-directory-aadconnect-dirsync-deprecated.md)
## [定价计算器](https://azure.microsoft.com/pricing/calculator/)

