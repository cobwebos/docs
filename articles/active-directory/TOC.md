# 概述
## [什么是 Azure Active Directory？](active-directory-whatis.md)
## [关于 Azure 标识管理](identity-fundamentals.md)
## [了解 Azure 标识解决方案](understand-azure-identity-solutions.md)
## [部署混合标识解决方案](choose-hybrid-identity-solution.md)
## [关联 Azure 订阅](active-directory-how-subscriptions-associated-directory.md)
## [常见问题](active-directory-faq.md)
## [新增功能](whats-new.md)


# 入门
## [Azure AD 入门](get-started-azure-ad.md)
## [注册 Azure AD Premium](active-directory-get-started-premium.md)
## [添加自定义域名](add-custom-domain.md)
## [配置公司品牌](customize-branding.md)
## [将用户添加到 Azure AD](add-users-azure-active-directory.md)
## [将许可证分配给用户](license-users-groups.md)
## [配置自助服务密码重置](active-directory-passwords-getting-started.md)


# 如何
## 规划和设计
### [了解 Azure AD 体系结构](active-directory-architecture.md)
### [部署混合标识解决方案](active-directory-hybrid-identity-design-considerations-overview.md)
### [Azure Active Directory 中的声明映射](active-directory-claims-mapping.md)
#### 确定要求
##### [标识](active-directory-hybrid-identity-design-considerations-business-needs.md)
##### [目录同步](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
##### [多重身份验证](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)
##### [标识生命周期策略](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)
#### [数据安全性规划](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)
##### [数据保护](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
##### [内容管理](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
##### [访问控制](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
##### [事件响应](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)
#### 规划标识生命周期
##### [任务](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)
##### [采用策略](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)
#### [后续步骤](active-directory-hybrid-identity-design-considerations-nextsteps.md)
#### [工具比较](active-directory-hybrid-identity-design-considerations-tools-comparison.md)

## 管理用户
### [使用组分配许可证](active-directory-licensing-whatis-azure-portal.md)
#### [将许可证分配到组](active-directory-licensing-group-assignment-azure-portal.md)
#### [识别和解决组中的许可证问题](active-directory-licensing-group-problem-resolution-azure-portal.md)
#### [将单个许可用户迁移到基于组的许可](active-directory-licensing-group-migration-azure-portal.md)
#### [基于组的许可的其他方案](active-directory-licensing-group-advanced.md)
#### [基于组的许可的 Azure PowerShell 示例](active-directory-licensing-ps-examples.md)
#### [Azure AD 中的产品和服务计划的参考](active-directory-licensing-product-and-service-plan-reference.md)
### [管理用户个人资料](active-directory-users-profile-azure-portal.md)
### [重置密码](active-directory-users-reset-password-azure-portal.md)
### [管理用户工作信息](active-directory-users-work-info-azure-portal.md)
### [共享帐户](active-directory-sharing-accounts.md)



## [管理组和成员](active-directory-manage-groups.md)
### 管理组
#### [Azure 门户](active-directory-groups-create-azure-portal.md)
#### [经典门户](active-directory-accessmanagement-manage-groups.md)
#### [Azure PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
### [管理组成员](active-directory-groups-members-azure-portal.md)
### [管理组所有者](active-directory-accessmanagement-managing-group-owners.md)
### [管理组成员身份](active-directory-groups-membership-azure-portal.md)
### [使用组分配许可证](active-directory-licensing-whatis-azure-portal.md)
#### [将许可证分配到组](active-directory-licensing-group-assignment-azure-portal.md)
#### [识别和解决组中的许可证问题](active-directory-licensing-group-problem-resolution-azure-portal.md)
#### [将单个许可用户迁移到基于组的许可](active-directory-licensing-group-migration-azure-portal.md)
#### [基于组的许可的其他方案](active-directory-licensing-group-advanced.md)
#### [基于组的许可的 Azure PowerShell 示例](active-directory-licensing-ps-examples.md)
#### [Azure AD 中的产品和服务计划的参考](active-directory-licensing-product-and-service-plan-reference.md)
### [设置 Office 365 组过期时间](active-directory-groups-lifecycle-azure-portal.md)
### [查看所有组](active-directory-groups-view-azure-portal.md)
### [启用专用组](active-directory-accessmanagement-dedicated-groups.md)
### [添加对 SaaS 应用的组访问权限](active-directory-accessmanagement-group-saasapps.md)
### [还原已删除的 Office 365 组](active-directory-groups-restore-azure-portal.md)
### 管理组设置
#### [Azure 门户](active-directory-groups-settings-azure-portal.md)
#### [Cmdlet](active-directory-accessmanagement-groups-settings-cmdlets.md)
### 创建高级规则
#### [Azure 门户](active-directory-groups-dynamic-membership-azure-portal.md)
#### [经典门户](active-directory-accessmanagement-groups-with-advanced-rules.md)
### [设置自助服务组](active-directory-accessmanagement-self-service-group-management.md)
### [故障排除](active-directory-accessmanagement-troubleshooting.md)

## [管理报表](active-directory-reporting-azure-portal.md)
### [登录活动](active-directory-reporting-activity-sign-ins.md)
### [审核活动](active-directory-reporting-activity-audit-logs.md)
### [有风险的用户](active-directory-reporting-security-user-at-risk.md)
### [有风险的登录](active-directory-reporting-security-risky-sign-ins.md)
### [风险事件](active-directory-reporting-risk-events.md)
### [常见问题](active-directory-reporting-faq.md)
### 任务
#### [配置命名位置](active-directory-named-locations.md)
#### [查找活动报表](active-directory-reporting-migration.md)
#### [使用 Azure Active Directory Power BI 内容包](active-directory-reporting-power-bi-content-pack-how-to.md)
### 引用
#### [保留](active-directory-reporting-retention.md)
#### [延迟](active-directory-reporting-latencies-azure-portal.md)
#### [通知](active-directory-reporting-notifications.md)
#### [登录活动错误代码](active-directory-reporting-activity-sign-ins-errors.md)
#### [多重身份验证](active-directory-reporting-activity-sign-ins-mfa.md)
### 故障排除
#### [缺少审核数据](active-directory-reporting-troubleshoot-missing-audit-data.md)
#### [在下载项中缺少数据](active-directory-reporting-troubleshoot-missing-data-download.md)
#### [Azure Active Directory 活动日志内容包错误](active-directory-reporting-troubleshoot-content-pack.md)
### [以编程方式访问](active-directory-reporting-api-getting-started-azure-portal.md)
#### [审核参考](active-directory-reporting-api-audit-reference.md)
#### [登录参考](active-directory-reporting-api-sign-in-activity-reference.md)
#### [先决条件](active-directory-reporting-api-prerequisites-azure-portal.md)
#### [审核示例](active-directory-reporting-api-audit-samples.md)
#### [登录示例](active-directory-reporting-api-sign-in-activity-samples.md)
#### [使用证书](active-directory-reporting-api-with-certificates.md)

## [管理密码](active-directory-passwords-overview.md)
### 用户文档
#### [重置或更改密码](active-directory-passwords-update-your-own-password.md)
#### [密码最佳做法](active-directory-secure-passwords.md)
#### [注册自助密码重置](active-directory-passwords-reset-register.md)
### [许可 SSPR](active-directory-passwords-licensing.md)
### [部署 SSPR](active-directory-passwords-best-practices.md)
### IT 管理员：重置密码
#### [Azure 门户](active-directory-users-reset-password-azure-portal.md)
#### [Azure 经典门户](active-directory-create-users-reset-password.md)
### [了解 SSPR 策略](active-directory-passwords-policy.md)
### [了解密码重置](active-directory-passwords-how-it-works.md)
### [自定义 SSPR](active-directory-passwords-customize.md)
### [SSPR 使用的数据](active-directory-passwords-data.md)
### [针对 SSPR 进行报告](active-directory-passwords-reporting.md)
### [Azure AD Connect](./connect/active-directory-aadconnect.md)
### [密码写回](active-directory-passwords-writeback.md)
### [密码哈希同步](./connect/active-directory-aadconnectsync-implement-password-synchronization.md#how-password-synchronization-works)
### [故障排除](active-directory-passwords-troubleshoot.md)
### [常见问题](active-directory-passwords-faq.md)


## 管理设备
### [介绍](device-management-introduction.md)
### [使用 Azure 门户](device-management-azure-portal.md)
### [计划 Azure AD 加入](active-directory-azureadjoin-deployment-aadjoindirect.md)
### [常见问题](device-management-faq.md)
### 任务
#### [设置已注册到 Azure AD 的 Windows 10 设备](device-management-azuread-registered-devices-windows10-setup.md)
#### [设置已加入 Azure AD 的设备](device-management-azuread-joined-devices-setup.md)
#### [设置已加入混合 Azure AD 的设备](device-management-hybrid-azuread-joined-devices-setup.md)
#### [本地部署](active-directory-device-registration-on-premises-setup.md)
#### [Windows 10 首次运行体验中的 Azure AD 加入](device-management-azuread-joined-devices-frx.md)
### 故障排除
#### [加入混合 Azure AD 的 Windows 10 和 Windows Server 2016 设备](device-management-troubleshoot-hybrid-join-windows-current.md)
#### [加入混合 Azure AD 的旧式 Windows 设备](device-management-troubleshoot-hybrid-join-windows-legacy.md)

## 管理应用
### [概述](active-directory-enable-sso-scenario.md)
### [入门](active-directory-integrating-applications-getting-started.md)
### [SaaS 应用集成教程](active-directory-saas-tutorial-list.md)
### [Cloud App Discovery](cloudappdiscovery-get-started.md)
#### [创建快照报表](cloudappdiscovery-set-up-snapshots.md)
#### [配置持续报告](https://docs.microsoft.com/cloud-app-security/discovery-docker)
#### [使用自定义日志分析器](https://docs.microsoft.com/cloud-app-security/custom-log-parser)
#### 基于代理的发现
##### [什么是 Cloud App Discovery？](active-directory-cloudappdiscovery-whatis.md)
##### [更新注册表设置](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
##### [了解安全性和隐私性](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)


### [使用应用代理远程访问应用](active-directory-application-proxy-get-started.md)
#### 入门
##### [启用应用代理](active-directory-application-proxy-enable.md)
##### [发布应用](application-proxy-publish-azure-portal.md)
##### [自定义域](active-directory-application-proxy-custom-domains.md)
#### [单一登录](application-proxy-sso-overview.md)
##### [使用 KCD 执行 SSO](active-directory-application-proxy-sso-using-kcd.md)
##### [使用标头执行 SSO](application-proxy-ping-access.md)
##### [将 SSO 与密码保管配合使用](application-proxy-sso-azure-portal.md)
#### 概念
##### [连接器](application-proxy-understand-connectors.md)
##### [安全性](application-proxy-security-considerations.md)
##### [网络](application-proxy-network-topology-considerations.md)


##### [从 TMG 或 UAG 升级](application-proxy-transition-from-uag-tmg.md)

#### 高级配置
##### [在单独的网络上发布](active-directory-application-proxy-connectors-azure-portal.md)
##### [代理服务器](application-proxy-working-with-proxy-servers.md)
##### [声明感知应用](active-directory-application-proxy-claims-aware-apps.md)
##### [本机客户端应用](active-directory-application-proxy-native-client.md)
##### [无提示安装](active-directory-application-proxy-silent-installation.md)
##### [自定义主页](application-proxy-office365-app-launcher.md)
##### [转换内联链接](application-proxy-link-translation.md)
#### 发布演练
##### [远程桌面](application-proxy-publish-remote-desktop.md)
##### [SharePoint](application-proxy-enable-remote-access-sharepoint.md)
##### [Microsoft Teams](application-proxy-teams.md)
#### [故障排除](active-directory-application-proxy-troubleshoot.md)
#### 使用经典门户
##### [下载连接器](application-proxy-enable-classic-portal.md)
##### [发布应用](active-directory-application-proxy-publish.md)
##### [使用连接器](active-directory-application-proxy-connectors.md)
##### [条件性访问](active-directory-application-proxy-conditional-access.md)

### 管理企业应用
#### [分配用户](active-directory-coreapps-assign-user-azure-portal.md)
#### [自定义品牌](active-directory-coreapps-change-app-logo-user-azure-portal.md)
#### [禁用用户登录](active-directory-coreapps-disable-app-azure-portal.md)
#### [删除用户](active-directory-coreapps-remove-assignment-azure-portal.md)
#### [查看所有应用](active-directory-coreapps-view-azure-portal.md)
#### [管理用户帐户预配](active-directory-enterprise-apps-manage-provisioning.md)
#### [管理适用于企业应用的单一登录](active-directory-enterprise-apps-manage-sso.md)
#### [SAML 应用的高级证书签名](active-directory-enterprise-apps-advance-certificate-options.md)
####[在 Azure Active Directory 中隐藏用户体验](active-directory-coreapps-hide-third-party-app.md)

### [管理对应用的访问权限](active-directory-managing-access-to-apps.md)
#### [自助服务访问权限](active-directory-self-service-application-access.md)
#### [SSO 访问](active-directory-appssoaccess-whatis.md)
#### [SSO 证书](active-directory-sso-certs.md)
#### [租户限制](active-directory-tenant-restrictions.md)
#### [使用 SCIM 预配用户](active-directory-scim-provisioning.md)

### [故障排除](active-directory-application-troubleshoot-content-map.md)
#### [应用程序开发](active-directory-application-dev-troubleshoot-content-map.md)
##### [配置和注册](active-directory-application-dev-config-content-map.md)
##### [开发](active-directory-application-dev-development-content-map.md)
#### [应用程序管理](active-directory-application-management-troubleshoot-content-map.md)
##### [配置](active-directory-application-config-content-map.md)
##### [登录](active-directory-application-sign-in-content-map.md)
##### [预配](active-directory-application-provisioning-content-map.md)
##### [管理访问](active-directory-application-access-content-map.md)
##### [访问面板](active-directory-application-access-panel-content-map.md)
##### [应用程序代理](active-directory-application-proxy-content-map.md)
##### [条件性访问](active-directory-application-conditional-access-content-map.md)
### [开发应用](active-directory-applications-guiding-developers-for-lob-applications.md)
### [文档库](active-directory-apps-index.md)

## 管理目录
### [Azure AD Connect](./connect/active-directory-aadconnect.md)
### 自定义域名
#### [概述](active-directory-add-domain-concepts.md)
#### 管理域名
##### [Azure 门户](active-directory-domains-manage-azure-portal.md)
##### [经典门户](active-directory-add-manage-domain-names.md)
### [管理目录](active-directory-administer.md)
### [多个目录](active-directory-licensing-directory-independence.md)
### [O365 目录](active-directory-manage-o365-subscription.md)
### [自助服务注册](active-directory-self-service-signup.md)
### [企业状态漫游](active-directory-windows-enterprise-state-roaming-overview.md)
#### [启用](active-directory-windows-enterprise-state-roaming-enable.md)
#### [组策略设置](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
#### [Windows 10 设置](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
#### [常见问题](active-directory-windows-enterprise-state-roaming-faqs.md)
#### [故障排除](active-directory-windows-enterprise-state-roaming-troubleshooting.md)

### [将合作伙伴与 Azure AD B2B 集成](active-directory-b2b-what-is-azure-ad-b2b.md)
#### [管理员添加 B2B 用户](active-directory-b2b-admin-add-users.md)
#### [信息工作者添加 B2B 用户](active-directory-b2b-iw-add-users.md)
#### [API 和自定义](active-directory-b2b-api.md)
#### [代码和 Azure PowerShell 示例](active-directory-b2b-code-samples.md)
#### [自助注册门户示例](active-directory-b2b-self-service-portal.md)
#### [邀请电子邮件](active-directory-b2b-invitation-email.md)
#### [邀请兑换](active-directory-b2b-redemption-experience.md)
#### [在没有邀请的情况下添加 B2B 用户](active-directory-b2b-add-user-without-invite.md)
#### [用于 B2B 的条件性访问](active-directory-b2b-mfa-instructions.md)
#### [B2B 共享策略](active-directory-b2b-delegate-invitations.md)
#### [将 B2B 用户添加到角色](active-directory-b2b-add-guest-to-role.md)
#### [动态组和 B2B 用户](active-directory-b2b-dynamic-groups.md)
#### [审核和报表](active-directory-b2b-auditing-and-reporting.md)
#### [B2B 和 Office 365 外部共享](active-directory-b2b-o365-external-user.md)
#### [许可](active-directory-b2b-licensing.md)
#### [当前限制](active-directory-b2b-current-limitations.md)
#### [常见问题](active-directory-b2b-faq.md)
#### [对 B2B 进行故障排除](active-directory-b2b-troubleshooting.md)
#### [了解 B2B 用户](active-directory-b2b-user-properties.md)
#### [B2B 用户令牌](active-directory-b2b-user-token.md)
#### [用于 Azure AD 集成应用的 B2B](active-directory-b2b-configure-saas-apps.md)
#### [B2B 用户声明映射](active-directory-b2b-claims-mapping.md)
#### [B2B 协作与 B2C 的比较](active-directory-b2b-compare-b2c.md)
#### [获取 B2B 支持](active-directory-b2b-support.md)

### [使用 Azure AD Connect 集成本地标识](./connect/active-directory-aadconnect.md)

## [管理对 Azure 的访问权限](toc.yml)

## 委托对资源的访问权限
### [管理员角色](active-directory-assign-admin-roles.md)
#### [分配管理员角色](active-directory-users-assign-role-azure-portal.md)
### [管理单元](active-directory-administrative-units-management.md)
### [配置令牌生存期](active-directory-configurable-token-lifetimes.md)

## 访问评审
### [访问评审概述](active-directory-azure-ad-controls-access-reviews-overview.md)
### [完成访问评审](active-directory-azure-ad-controls-complete-access-review.md)
### [创建访问评审](active-directory-azure-ad-controls-create-access-review.md)
### [如何执行访问审阅](active-directory-azure-ad-controls-perform-access-review.md)
### [如何评审访问权限](active-directory-azure-ad-controls-how-to-review-your-access.md)
### [使用访问评审的来宾访问](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
### [通过评审管理用于访问](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
### [管理计划和控件](active-directory-azure-ad-controls-manage-programs-controls.md)


## 保护标识
### [条件性访问](active-directory-conditional-access-azure-portal.md)
#### [控制](active-directory-conditional-access-controls.md)
#### [入门](active-directory-conditional-access-azure-portal-get-started.md)
#### [最佳实践](active-directory-conditional-access-best-practices.md)
#### [了解 Office 365 服务的设备策略](active-directory-conditional-access-device-policies.md)
#### 任务
##### [迁移经典策略](active-directory-conditional-access-migration.md)
##### [设置基于设备的条件访问](active-directory-conditional-access-policy-connected-applications.md)
##### [设置基于应用的条件访问](active-directory-conditional-access-mam.md)
##### [为用户和应用提供使用条款](active-directory-tou.md)
##### [设置 VPN 连接](active-directory-conditional-access-vpn-connectivity-windows10.md)
##### [设置 SharePoint 和 Exchange Online](active-directory-conditional-access-no-modern-authentication.md)
##### [补救方法](active-directory-conditional-access-device-remediation.md)
#### [技术参考](active-directory-conditional-access-technical-reference.md)
#### [常见问题](active-directory-conditional-faqs.md)
#### [经典门户](active-directory-conditional-access.md)
##### [入门](active-directory-conditional-access-azuread-connected-apps.md)

### Windows Hello
#### [无密码身份验证](active-directory-azureadjoin-passport.md)
#### [启用 Windows Hello for Business](active-directory-azureadjoin-passport-deployment.md)
### 基于证书的身份验证
#### [Android](active-directory-certificate-based-authentication-android.md)
#### [iOS](active-directory-certificate-based-authentication-ios.md)
#### [入门](active-directory-certificate-based-authentication-get-started.md)

### [Azure AD Identity Protection](active-directory-identityprotection.md)
#### [启用](active-directory-identityprotection-enable.md)
#### [检测漏洞](active-directory-identityprotection-vulnerabilities.md)
#### [风险事件](active-directory-identity-protection-risk-events.md)
#### [通知](active-directory-identityprotection-notifications.md)
#### [登录体验](active-directory-identityprotection-flows.md)
#### [模拟风险事件](active-directory-identityprotection-playbook.md)
#### [取消阻止用户](active-directory-identityprotection-unblock-howto.md)
#### [常见问题](active-directory-identity-protection-faqs.md)
#### [术语表](active-directory-identityprotection-glossary.md)
#### [Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)
### [Privileged Identity Management](./privileged-identity-management/active-directory-securing-privileged-access.md)

## [将其他服务与 Azure AD 集成]()
### [启用 LinkedIn 集成](linkedin-integration.md)

## [在 Azure VM 上部署 AD DS](virtual-networks-windows-server-active-directory-virtual-machines.md)
### [Azure VM 上的 Windows Server Active Directory](active-directory-deploying-ws-ad-guidelines.md)
### [Azure 虚拟网络中的副本域控制器](active-directory-install-replica-active-directory-domain-controller.md)
### [在 Azure 虚拟网络中新建林](active-directory-new-forest-virtual-machine.md)



## [在 Azure 中部署 AD FS](active-directory-aadconnect-azure-adfs.md)
### [高可用性](active-directory-adfs-in-azure-with-azure-traffic-manager.md)
### [更改签名哈希算法](active-directory-federation-sha256-guidance.md)

## [故障排除](active-directory-troubleshooting-support-howto.md)
### [排查 Active Directory 项缺失或不可用的问题](active-directory-troubleshooting.md)

## 部署 Azure AD 概念证明 (PoC)
### [PoC 演练手册：简介](active-directory-playbook-intro.md)
### [PoC 演练手册：要素](active-directory-playbook-ingredients.md)
### [PoC 演练手册：实现](active-directory-playbook-implementation.md)
### [PoC 演练手册：构建基块](active-directory-playbook-building-blocks.md)


# 引用
## [代码示例](https://azure.microsoft.com/en-us/resources/samples/?service=active-directory)
## [Azure PowerShell cmdlet](/powershell/azure/overview)
## [Java API 参考](/java/api)
## [.NET API](/active-directory/adal/microsoft.identitymodel.clients.activedirectory)
## [服务限制和局限性](active-directory-service-limits-restrictions.md)

# 相关内容
## [多重身份验证](/azure/multi-factor-authentication/)
## [Azure AD Connect](./connect/active-directory-aadconnect.md)
## [Azure AD Connect Health](./connect-health/active-directory-aadconnect-health.md)
## [适用于开发人员的 Azure AD](./develop/active-directory-how-to-integrate.md)
## [Azure AD Privileged Identity Management](./privileged-identity-management/active-directory-securing-privileged-access.md)

# 资源
## [Azure 反馈论坛](https://feedback.azure.com/forums/169401-azure-active-directory)
## [Azure 路线图](https://azure.microsoft.com/roadmap/?category=security-identity)
## [MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
## [定价](https://azure.microsoft.com/pricing/details/active-directory/)
## [定价计算器](https://azure.microsoft.com/pricing/calculator/)
## [服务更新](https://azure.microsoft.com/updates/?product=active-directory)
## [堆栈溢出](http://stackoverflow.com/questions/tagged/azure-active-directory)
## [视频](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
