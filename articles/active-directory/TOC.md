# [Azure Active Directory 文档](index.md)

# 概述
## [什么是 Azure Active Directory？](fundamentals/active-directory-whatis.md)
## [关于 Azure 标识管理](fundamentals/identity-fundamentals.md)
## [了解 Azure 标识解决方案](fundamentals/understand-azure-identity-solutions.md)
## [部署混合标识解决方案](choose-hybrid-identity-solution.md)
## [关联 Azure 订阅](fundamentals/active-directory-how-subscriptions-associated-directory.md)
## [驻留和数据注意事项](fundamentals/active-directory-data-storage-eu.md)
## [常见问题](fundamentals/active-directory-faq.md)
## [新增功能](fundamentals/whats-new.md)


# 入门
## [Azure AD 入门](fundamentals/get-started-azure-ad.md)
## [注册 Azure AD Premium](fundamentals/active-directory-get-started-premium.md)
## [添加自定义域名](fundamentals/add-custom-domain.md)
## [配置公司品牌](fundamentals/customize-branding.md)
## [将用户添加到 Azure AD](fundamentals/add-users-azure-active-directory.md)
## [将许可证分配给用户](fundamentals/license-users-groups.md)
## [配置自助服务密码重置](authentication/quickstart-sspr.md)
## [在 Azure AD 中添加组织的隐私信息](active-directory-properties-area.md)


# 如何
## 规划和设计
### [了解 Azure AD 体系结构](fundamentals/active-directory-architecture.md)
### [Azure Active Directory 中的声明映射](active-directory-claims-mapping.md)
### [部署混合标识解决方案](active-directory-hybrid-identity-design-considerations-overview.md)
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
### [将新用户添加到 Azure AD](fundamentals/add-users-azure-active-directory.md)
### [管理用户个人资料](fundamentals/active-directory-users-profile-azure-portal.md)
### [重置用户密码](fundamentals/active-directory-users-reset-password-azure-portal.md)
### [共享帐户](active-directory-sharing-accounts.md)
### [将用户分配到管理员角色](fundamentals/active-directory-users-assign-role-azure-portal.md)
### [还原已删除的用户](fundamentals/active-directory-users-restore.md)
### [添加另一个目录中的来宾用户 (B2B)](b2b/what-is-b2b.md)
#### [管理员添加 B2B 用户](b2b/add-users-administrator.md)
#### [信息工作者添加 B2B 用户](b2b/add-users-information-worker.md)
#### [API 和自定义](b2b/customize-invitation-api.md)
#### [代码和 Azure PowerShell 示例](b2b/code-samples.md)
#### [自助注册门户示例](b2b/self-service-portal.md)
#### [邀请电子邮件](b2b/invitation-email-elements.md)
#### [邀请兑换](b2b/redemption-experience.md)
#### [在没有邀请的情况下添加 B2B 用户](b2b/add-user-without-invite.md)
#### [允许或阻止邀请](b2b/allow-deny-list.md)
#### [用于 B2B 的条件性访问](b2b/conditional-access.md)
#### [B2B 共享策略](b2b/delegate-invitations.md)
#### [将 B2B 用户添加到角色](b2b/add-guest-to-role.md)
#### [动态组和 B2B 用户](b2b/use-dynamic-groups.md)
#### [离开组织](b2b/leave-the-organization.md)
#### [审核和报表](b2b/auditing-and-reporting.md)
#### [适用于混合组织的 B2B](b2b/hybrid-organizations.md)
##### [授予 B2B 用户对本地应用的访问权限](b2b/hybrid-cloud-to-on-premises.md)
##### [授予本地用户对云应用的访问权限](b2b/hybrid-on-premises-to-cloud.md)
#### [B2B 和 Office 365 外部共享](b2b/o365-external-user.md)
#### [B2B 许可](b2b/licensing-guidance.md)
#### [当前限制](b2b/current-limitations.md)
#### [常见问题](b2b/faq.md)
#### [对 B2B 进行故障排除](b2b/troubleshoot.md)
#### [了解 B2B 用户](b2b/user-properties.md)
#### [B2B 用户令牌](b2b/user-token.md)
#### [用于 Azure AD 集成应用的 B2B](b2b/configure-saas-apps.md)
#### [B2B 用户声明映射](b2b/claims-mapping.md)
#### [B2B 协作与 B2C 的比较](b2b/compare-with-b2c.md)
#### [获取 B2B 支持](b2b/get-support.md)

## [管理组和成员](fundamentals/active-directory-manage-groups.md)
### 管理组
#### [Azure 门户](fundamentals/active-directory-groups-create-azure-portal.md)
#### [用于 Graph 的 Azure AD PowerShell (v2)](users-groups-roles/groups-settings-v2-cmdlets.md)
#### [Azure AD PowerShell MSOnline](users-groups-roles/groups-settings-cmdlets.md)
### [管理组成员](fundamentals/active-directory-groups-members-azure-portal.md)
### [管理组所有者](fundamentals/active-directory-accessmanagement-managing-group-owners.md)
### [管理组成员身份](fundamentals/active-directory-groups-membership-azure-portal.md)
### [使用组分配许可证](fundamentals/active-directory-licensing-whatis-azure-portal.md)
#### [将许可证分配到组](users-groups-roles/licensing-groups-assign.md)
#### [识别和解决组中的许可证问题](users-groups-roles/licensing-groups-resolve-problems.md)
#### [将单个许可用户迁移到基于组的许可](users-groups-roles/licensing-groups-migrate-users.md)
#### [在产品许可证之间迁移用户](users-groups-roles/licensing-groups-change-licenses.md)
#### [基于组的许可的其他方案](users-groups-roles/licensing-group-advanced.md)
#### [基于组的许可的 Azure PowerShell 示例](users-groups-roles/licensing-ps-examples.md)
#### [Azure AD 中的产品和服务计划的参考](users-groups-roles/licensing-service-plan-reference.md)
### [设置 Office 365 组过期时间](users-groups-roles/groups-lifecycle.md)
### [为组强制执行命名策略](users-groups-roles/groups-naming-policy.md)
### [查看所有组](fundamentals/active-directory-groups-view-azure-portal.md)
### [添加对 SaaS 应用的组访问权限](users-groups-roles/groups-saasapps.md)
### [还原已删除的 Office 365 组](fundamentals/active-directory-groups-restore-azure-portal.md)
### [管理组设置](fundamentals/active-directory-groups-settings-azure-portal.md)
### [设置自助服务组](users-groups-roles/groups-self-service-management.md)
### 动态组
#### [创建动态组](users-groups-roles/groups-create-rule.md)
#### [规则语法和属性](users-groups-roles/groups-dynamic-membership.md)
#### [更改组成员身份类型](users-groups-roles/groups-change-type.md)
#### [故障排除](users-groups-roles/groups-troubleshooting.md)

## [管理报表](active-directory-reporting-azure-portal.md)
### [登录活动](active-directory-reporting-activity-sign-ins.md)
### [审核活动](active-directory-reporting-activity-audit-logs.md)
### [有风险的用户](active-directory-reporting-security-user-at-risk.md)
### [有风险的登录](active-directory-reporting-security-risky-sign-ins.md)
### [风险事件](active-directory-reporting-risk-events.md)
### [使用 Azure Monitor 监视日志](reporting-azure-monitor-diagnostics-overview.md)
### [常见问题](active-directory-reporting-faq.md)

### 任务
#### [配置命名位置](active-directory-named-locations.md)
#### [查找活动报表](active-directory-reporting-migration.md)
#### [使用 Azure AD Power BI 内容包](active-directory-reporting-power-bi-content-pack-how-to.md)
#### [修复已标记为存在风险的用户](active-directory-report-security-user-at-risk-remediation.md)
#### [将活动日志路由到 Azure 事件中心](reporting-azure-monitor-diagnostics-azure-event-hub.md)
#### [将活动日志存档到 Azure 存储帐户](reporting-azure-monitor-diagnostics-azure-storage-account.md)
#### [使用 Azure Monitor 将活动日志与 Splunk 集成](reporting-azure-monitor-diagnostics-splunk-integration.md)

### 引用
#### [保留](active-directory-reporting-retention.md)
#### [延迟](active-directory-reporting-latencies-azure-portal.md)
#### [审核活动参考](active-directory-reporting-activity-audit-reference.md)
#### [登录活动错误代码](active-directory-reporting-activity-sign-ins-errors.md)
#### [解释 Azure Monitor 中的审核日志架构](reporting-azure-monitor-diagnostics-audit-log-schema.md)
#### [解释 Azure Monitor 中的登录日志架构](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)

### 故障排除
#### [缺少审核数据](active-directory-reporting-troubleshoot-missing-audit-data.md)
#### [在下载项中缺少数据](active-directory-reporting-troubleshoot-missing-data-download.md)
#### [Azure AD 活动日志内容包错误](active-directory-reporting-troubleshoot-content-pack.md)
#### [Azure AD 报告 API 中的错误](active-directory-reporting-troubleshoot-graph-api.md)

### [以编程方式访问](active-directory-reporting-api-getting-started-azure-portal.md)
#### [先决条件](active-directory-reporting-api-prerequisites-azure-portal.md)
#### [审核示例](active-directory-reporting-api-audit-samples.md)
#### [登录示例](active-directory-reporting-api-sign-in-activity-samples.md)
#### [使用证书](active-directory-reporting-api-with-certificates.md)

## [管理密码](authentication/concept-sspr-howitworks.md)
### 用户文档
#### [重置或更改密码](user-help/active-directory-passwords-update-your-own-password.md)
#### [注册自助密码重置](user-help/active-directory-passwords-reset-register.md)


## 管理设备
### [概述](devices/overview.md)

### 快速入门
#### [设置已注册到 Azure AD 的 Windows 10 设备](user-help/device-management-azuread-registered-devices-windows10-setup.md)
#### [设置已加入 Azure AD 的设备](user-help/device-management-azuread-joined-devices-setup.md)

### 教程
#### [为托管域配置混合 Azure AD 加入](devices/hybrid-azuread-join-managed-domains.md)
#### [为联盟域配置混合 Azure AD 加入](devices/hybrid-azuread-join-federated-domains.md)
#### [手动配置混合 Azure AD 加入](devices/hybrid-azuread-join-manual-steps.md)
#### [在 Windows 10 首次运行体验期间配置 Azure AD 加入](devices/azuread-joined-devices-frx.md)

### 操作方法指南
#### [计划 Azure AD 加入](devices/azureadjoin-plan.md)
#### [计划混合 Azure AD 加入实现](devices/hybrid-azuread-join-plan.md)
#### [控制设备的混合 Azure AD 加入](devices/hybrid-azuread-join-control.md)
#### [将本地管理员分配到加入 Azure AD 的设备](devices/assign-local-admin.md)
#### [排查加入混合 Azure AD 的 Windows 当前设备问题](devices/troubleshoot-hybrid-join-windows-current.md)
#### [排查加入混合 Azure AD 的旧式 Windows 设备问题](devices/troubleshoot-hybrid-join-windows-legacy.md)

### 概念
#### [使用 Azure 门户管理设备](devices/device-management-azure-portal.md)
#### [常见问题](devices/faq.md)

## 管理应用
### [概述](manage-apps/what-is-application-management.md)
### [入门](manage-apps/plan-an-application-integration.md)
### [SaaS 应用集成教程](saas-apps/tutorial-list.md)

### [对 SaaS 应用进行用户预配和取消预配](active-directory-saas-app-provisioning.md) 
#### [应用集成教程](saas-apps/tutorial-list.md) 
#### [对启用 SCIM 的应用自动执行预配](manage-apps/use-scim-to-provision-users-and-groups.md) 
#### [自定义属性映射](active-directory-saas-customizing-attribute-mappings.md) 
#### [为属性映射编写表达式](active-directory-saas-writing-expressions-for-attribute-mappings.md) 
#### [使用范围筛选器](active-directory-saas-scoping-filters.md) 
#### [针对自动用户预配进行报告](active-directory-saas-provisioning-reporting.md) 
#### [排查用户预配问题](active-directory-application-provisioning-content-map.md) 

### [使用应用代理远程访问应用](manage-apps/application-proxy.md)
#### 入门
##### [启用应用代理](manage-apps/application-proxy-enable.md)
##### [发布应用](manage-apps/application-proxy-publish-azure-portal.md)
##### [自定义域](manage-apps/application-proxy-configure-custom-domain.md)
#### [单一登录](manage-apps/application-proxy-single-sign-on.md)
##### [使用 KCD 执行 SSO](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
##### [使用标头执行 SSO](manage-apps/application-proxy-configure-single-sign-on-with-ping-access.md)
##### [将 SSO 与密码保管配合使用](manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md)
#### 概念
##### [连接器](manage-apps/application-proxy-connectors.md)
##### [安全性](manage-apps/application-proxy-security.md)
##### [网络](manage-apps/application-proxy-network-topology.md)


##### [从 TMG 或 UAG 升级](manage-apps/application-proxy-migration.md)

#### 高级配置
##### [在单独的网络上发布](manage-apps/application-proxy-connector-groups.md)
##### [代理服务器](manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)
##### [声明感知应用](manage-apps/application-proxy-configure-for-claims-aware-applications.md)
##### [本机客户端应用](manage-apps/application-proxy-configure-native-client-application.md)
##### [无提示安装](manage-apps/application-proxy-register-connector-powershell.md)
##### [自定义主页](manage-apps/application-proxy-configure-custom-home-page.md)
##### [转换内联链接](manage-apps/application-proxy-configure-hard-coded-link-translation.md)
##### [通配符](active-directory-application-proxy-wildcard.md)
##### [删除个人数据](manage-apps/application-proxy-remove-personal-data.md)


#### 发布演练
##### [远程桌面](manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
##### [SharePoint](manage-apps/application-proxy-integrate-with-sharepoint-server.md)
##### [Microsoft Teams](manage-apps/application-proxy-integrate-with-teams.md)
##### [Tableau](manage-apps/application-proxy-integrate-with-tableau.md)
##### [Qlik](active-directory-application-proxy-qlik.md)
#### [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) 

#### [故障排除](manage-apps/application-proxy-troubleshoot.md)

### 管理企业应用
#### [添加应用程序](manage-apps/add-application-portal.md)
#### [查看租户应用](manage-apps/view-applications-portal.md)
#### [分配用户](manage-apps/assign-user-or-group-access-portal.md)
#### [自定义品牌](manage-apps/change-name-or-logo-portal.md)
#### [禁用用户登录](manage-apps/disable-user-sign-in-portal.md)
#### [删除用户](manage-apps/remove-user-or-group-access-portal.md)

#### [管理用户帐户预配](manage-apps/configure-automatic-user-provisioning-portal.md)
#### [管理适用于企业应用的单一登录](manage-apps/configure-single-sign-on-portal.md)
#### [SAML 应用的高级证书签名](manage-apps/certificate-signing-options.md)
#### [从用户体验中隐藏应用程序](manage-apps/hide-application-from-user-portal.md)
### [使用 HRD 策略配置登录自动加速](manage-apps/configure-authentication-for-federated-users-portal.md)
### [将 AD FS 应用迁移到 Azure AD](manage-apps/migrate-adfs-apps-to-azure.md) 
### [管理对应用的访问权限](manage-apps/what-is-access-management.md)
#### [SSO 访问](manage-apps/what-is-single-sign-on.md)
#### [SSO 证书](manage-apps/manage-certificates-for-federated-single-sign-on.md)
#### [租户限制](manage-apps/tenant-restrictions.md)
#### [使用 SCIM 预配用户](manage-apps/use-scim-to-provision-users-and-groups.md)


### 故障排除



#### 访问面板
##### [应用未出现](application-access-panel-unexpected-application-not-appearing.md)
##### [出现意外的应用](application-access-panel-unexpected-application-appears.md)
##### [无法登录](application-access-panel-web-sign-in-problem.md)
##### [安装浏览器扩展时出错](application-access-panel-extension-problem-installing.md)
##### [如何使用自助服务应用访问权限](application-access-panel-self-service-applications-how-to.md)
##### [使用自助服务应用访问权限时出错](application-access-panel-self-service-applications-problem.md)

#### 添加应用
##### [选择应用类型](application-config-add-app-problem-how-to-choose-application-type.md)
##### [常见问题 - 库应用](application-config-add-app-problem-problem-adding-gallery-app.md)
##### [常见问题 - 非库应用](application-config-add-app-problem-problem-adding-non-gallery-app.md)

#### 应用程序代理
##### [显示应用页时出现问题](application-proxy-page-appearance-broken-problem.md)
##### [应用程序加载时间过长](application-proxy-page-load-speed-problem.md)
##### [应用程序页上的链接不起作用](application-proxy-page-links-broken-problem.md)
##### [要为应用打开哪些端口](application-proxy-connectivity-ports-how-to.md)
##### [应用的连接器组中没有起作用的连接器](application-proxy-connectivity-no-working-connector.md)
##### [在管理门户中配置](application-proxy-config-how-to.md)
##### [为应用配置单一登录](application-proxy-config-sso-how-to.md)
##### [在管理门户中创建应用时出现问题](application-proxy-config-problem.md)
##### [配置 Kerberos 约束委派](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
##### [使用 PingAccess 配置](application-proxy-back-end-ping-access-how-to.md)
##### [“无法访问此企业应用程序”错误](application-proxy-sign-in-bad-gateway-timeout-error.md)
##### [安装应用程序代理程序连接器时出现问题](application-proxy-connector-installation-problem.md)


#### 应用程序注册
##### [为应用程序对象填写字段](application-dev-registration-config-specific-application-property-how-to.md)
##### [更改令牌生存期默认值](application-dev-registration-config-change-token-lifetime-how-to.md)

#### 身份验证
##### [配置终结点](application-dev-registration-config-how-to.md)

#### 条件性访问
##### [客户不符合设备注册先决条件](active-directory-conditional-access.md)
##### [由于条件访问策略设置不当，租户被阻止](active-directory-conditional-access-device-remediation.md)
##### [企业网络外部规则如何以及何时生效？](https://aka.ms/calocation)
##### [如何增加允许用户在 Azure AD 中注册的设备数？](active-directory-azureadjoin-setup.md)
##### [如何为 Exchange Online 设置条件访问？](https://aka.ms/csforexchange)
##### [如何为 Windows 7 设备设置条件访问？](active-directory-conditional-access.md#device-based-conditional-access)
##### [哪些应用程序支持条件访问？](active-directory-conditional-access-supported-apps.md)

#### 查找 API
##### [查找 API](application-dev-api-find-an-api-how-to.md)

#### 管理访问权限
##### [为应用分配用户和组](application-access-assignment-how-to-add-assignment.md)
##### [删除对应用的用户访问权限](application-access-assignment-how-to-remove-assignment.md)
##### [配置自助服务应用分配](application-access-self-service-how-to.md)
##### [分配了意外的用户](application-access-unexpected-user-assignment.md)
##### [应用程序列表中的意外应用](application-access-unexpected-application.md)

#### 多租户应用
##### [配置新应用](application-dev-setup-multi-tenant-app.md)
##### [添加到应用库](application-dev-registration-config-multi-tenant-application-add-to-gallery-how-to.md)

#### 权限
##### [选择 API 的权限](application-dev-perms-for-given-api.md)
##### [为应用授予权限](application-dev-registration-config-grant-permissions-how-to.md)
##### [委派权限与应用程序权限](application-dev-delegated-and-app-perms.md)
##### [应用程序许可](application-dev-consent-framework.md)

#### 设置
##### [花费多长时间](application-provisioning-when-will-provisioning-finish-specific-user.md)
##### [花费数小时 - 库应用](application-provisioning-when-will-provisioning-finish.md)
##### [配置用户预配 - 库应用](application-provisioning-config-how-to.md)
##### [配置用户预配时出现问题 - 库应用](application-provisioning-config-problem.md)
##### [配置用户预配时保存管理员凭据出现问题 - 库应用](application-provisioning-config-problem-storage-limit.md)
##### [未预配用户 - 库应用](application-provisioning-config-problem-no-users-provisioned.md)
##### [预配了错误的用户 - 库应用](application-provisioning-config-problem-wrong-users-provisioned.md)

#### 单一登录
##### [选择方法](application-config-sso-how-to-choose-sign-on-method.md)
##### [配置](application-dev-registration-config-sso-how-to.md)
##### [配置联合单一登录 - 库应用](application-config-sso-how-to-configure-federated-sso-gallery.md)
##### [配置联合单一登录时常见的问题 - 库应用](application-config-sso-problem-configure-federated-sso-gallery.md)
##### [配置联合单一登录 - 非库应用](application-config-sso-how-to-configure-federated-sso-non-gallery.md)
##### [配置联合单一登录时常见的问题 - 非库应用](application-config-sso-problem-configure-federated-sso-non-gallery.md)
##### [配置密码 - 库应用](application-config-sso-how-to-configure-password-sso-gallery.md)
##### [配置密码时常见的问题 - 库应用](application-config-sso-problem-configure-password-sso-gallery.md)
##### [配置密码 - 非库应用](application-config-sso-how-to-configure-password-sso-non-gallery.md)
##### [配置密码时常见的问题 - 非库应用](application-config-sso-problem-configure-password-sso-non-gallery.md)

#### 用户登录问题
##### [意外的许可提示](application-sign-in-unexpected-user-consent-prompt.md)
##### [用户许可错误](application-sign-in-unexpected-user-consent-error.md)
##### [从自定义门户登录时出现问题](application-sign-in-other-problem-deeplink.md)
##### [从访问面板登录时出现问题](application-sign-in-other-problem-access-panel.md)
##### [应用程序登录页上的错误](application-sign-in-problem-application-error.md)
##### [使用密码单一登录时出现的问题 - 非库应用](application-sign-in-problem-password-sso-non-gallery.md)
##### [使用密码单一登录时出现的问题 - 库应用](application-sign-in-problem-password-sso-gallery.md)
##### [登录到 Microsoft 应用时出现问题](application-sign-in-problem-first-party-microsoft.md)
##### [使用联合单一登录时出现的问题 - 非库应用](application-sign-in-problem-federated-sso-non-gallery.md)
##### [使用联合单一登录时出现的问题 - 库应用](application-sign-in-problem-federated-sso-gallery.md)
##### [登录自定义开发的应用时出现的问题](application-sign-in-problem-custom-dev.md)
##### [登录本地应用时出现的问题 - 应用程序代理](application-sign-in-problem-on-premises-application-proxy.md)

### [开发应用](active-directory-applications-guiding-developers-for-lob-applications.md)
### [文档库](active-directory-apps-index.md)

## 管理目录
### [Azure AD Connect](./connect/active-directory-aadconnect.md)
### 自定义域名
#### [快速入门](fundamentals/add-custom-domain.md)
#### [添加自定义域名](users-groups-roles/domains-manage.md)
### [管理目录](fundamentals/active-directory-administer.md)
### [删除目录](users-groups-roles/directory-delete-howto.md)
### [多个目录](users-groups-roles/licensing-directory-independence.md)
### [自助服务注册](users-groups-roles/directory-self-service-signup.md)
### [接管非托管目录](users-groups-roles/domains-admin-takeover.md)
### [企业状态漫游](active-directory-windows-enterprise-state-roaming-overview.md)
#### [启用](active-directory-windows-enterprise-state-roaming-enable.md)
#### [组策略设置](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
#### [Windows 10 设置](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
#### [常见问题](active-directory-windows-enterprise-state-roaming-faqs.md)
#### [故障排除](active-directory-windows-enterprise-state-roaming-troubleshooting.md)


### [使用 Azure AD Connect 集成本地标识](./connect/active-directory-aadconnect.md)

## 委托对资源的访问权限
### [管理员角色](users-groups-roles/directory-assign-admin-roles.md)
#### [查看管理员角色的成员](users-groups-roles//directory-manage-roles-portal.md)
#### [向用户分配管理员角色](fundamentals/active-directory-users-assign-role-azure-portal.md)
#### [比较成员和来宾的用户权限](fundamentals/users-default-permissions.md)
### [管理员角色安全性](users-groups-roles/directory-admin-roles-secure.md)  
#### [创建紧急访问管理员帐户](users-groups-roles/directory-emergency-access.md)
### [管理单元](users-groups-roles/directory-administrative-units.md)
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
### [检索访问评审结果](active-directory-azure-ad-controls-retrieve-access-review.md)

## 保护标识
### [条件性访问](active-directory-conditional-access-azure-portal.md)
#### [入门](active-directory-conditional-access-azure-portal-get-started.md)
#### 快速入门
##### [根据云应用 MFA 进行配置](conditional-access/app-based-mfa.md)
##### [需要接受使用条款](conditional-access/require-tou.md)
##### [检测到会话风险时阻止访问](conditional-access/app-sign-in-risk.md)
#### 教程
##### [迁移经典 MFA 策略](conditional-access/policy-migration-mfa.md)
#### 概念
##### [基线保护](conditional-access/baseline-protection.md)
##### [条件](conditional-access/conditions.md)
##### [位置条件](conditional-access/location-condition.md)
##### [控制](conditional-access/controls.md)
##### [假设分析工具](conditional-access/what-if-tool.md)
##### [了解 Office 365 服务的设备策略](active-directory-conditional-access-device-policies.md)
#### 操作指南
##### [最佳实践](conditional-access/best-practices.md)
##### [针对来自不受信任网络的访问尝试配置条件访问策略](active-directory-conditional-access-untrusted-networks.md)
##### [设置基于设备的条件访问](active-directory-conditional-access-policy-connected-applications.md)
##### [设置基于应用的条件访问](active-directory-conditional-access-mam.md)
##### [为用户和应用提供使用条款](active-directory-tou.md)
##### [迁移经典策略](conditional-access/policy-migration.md)
##### [设置 VPN 连接](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/always-on-vpn-deploy)
##### [设置 SharePoint 和 Exchange Online](active-directory-conditional-access-no-modern-authentication.md)
##### [补救方法](active-directory-conditional-access-device-remediation.md)
#### [技术参考](active-directory-conditional-access-technical-reference.md)
#### [常见问题](conditional-access/faqs.md)

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
### [Privileged Identity Management](privileged-identity-management/pim-configure.md?toc=%2fazure%2factive-directory%2ftoc.json)

## 将其他服务与 Azure AD 集成 
### [将 LinkedIn 与 Azure AD 集成](users-groups-roles/linkedin-integration.md)

## [在 Azure 中部署 AD FS](active-directory-aadconnect-azure-adfs.md)
### [高可用性](active-directory-adfs-in-azure-with-azure-traffic-manager.md)
### [更改签名哈希算法](active-directory-federation-sha256-guidance.md)

## [故障排除](fundamentals/active-directory-troubleshooting-support-howto.md)

## 部署 Azure AD 概念证明 (PoC)
### [PoC 演练手册：简介](active-directory-playbook-intro.md)
### [PoC 演练手册：要素](active-directory-playbook-ingredients.md)
### [PoC 演练手册：实现](active-directory-playbook-implementation.md)
### [PoC 演练手册：构建基块](active-directory-playbook-building-blocks.md)


# 引用
## [代码示例](https://azure.microsoft.com/resources/samples/?service=active-directory)
## [Azure PowerShell cmdlet](/powershell/azure/overview)
## [Java API 参考](/java/api)
## [.NET API](/active-directory/adal/microsoft.identitymodel.clients.activedirectory)
## [服务限制和局限性](users-groups-roles/directory-service-limits-restrictions.md)

# 相关内容
## [多重身份验证](/azure/multi-factor-authentication/)
## [Azure AD Connect](./connect/active-directory-aadconnect.md)
## [Azure AD Connect Health](./connect-health/active-directory-aadconnect-health.md)
## [适用于开发人员的 Azure AD](./develop/active-directory-how-to-integrate.md)
## [Azure AD Privileged Identity Management](./privileged-identity-management/pim-configure.md)

# 资源
## [Azure AD 部署计划](./fundamentals/active-directory-deployment-plans.md)
## [Azure 反馈论坛](https://feedback.azure.com/forums/169401-azure-active-directory)
## [Azure 路线图](https://azure.microsoft.com/roadmap/?category=security-identity)
## [MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
## [定价](https://azure.microsoft.com/pricing/details/active-directory/)
## [定价计算器](https://azure.microsoft.com/pricing/calculator/)
## [服务更新](https://azure.microsoft.com/updates/?product=active-directory)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
## [视频](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
