# 概述
## [什么是 Azure Stack？](azure-stack-poc.md)
## [新增功能](azure-stack-whats-new.md)
## [主要功能和概念](azure-stack-key-features.md)
## [POC 体系结构](azure-stack-architecture.md)

# 评估和部署
## 入门
### [部署先决条件](azure-stack-deploy.md)
### [部署](azure-stack-run-powershell-script.md)
### [注册](azure-stack-register.md)
## 如何
### [连接到 Azure Stack POC](azure-stack-connect-azure-stack.md)
### [添加默认映像](azure-stack-add-default-image.md)
### [预配虚拟机](azure-stack-provision-vm.md)
### [创建存储帐户](azure-stack-provision-storage-account.md)
### [添加 Azure Stack 租户](azure-stack-add-new-user-aad.md)
### [重新部署 Azure Stack POC](azure-stack-redeploy.md)

# 管理
## 概述
### [区域管理](azure-stack-region-management.md)
### [使用门户](azure-stack-manage-portals.md)
## 入门
### [连接到 Azure Stack](azure-stack-connect-azure-stack.md)
### 设置管理环境
#### [安装 PowerShell](azure-stack-powershell-install.md)
#### [下载工具](azure-stack-powershell-download.md)
#### [配置 PowerShell](azure-stack-powershell-configure.md)
## 如何
### [管理更新](azure-stack-updates.md)
### [监视运行状况和警报](azure-stack-monitor-health.md)
### [管理网络资源](azure-stack-viewing-public-ip-address-consumption-in-tp2.md)
### [管理存储资源](azure-stack-manage-storage-accounts.md)
### [管理 Windows Azure Pack VM](azure-stack-manage-windows-azure-pack.md)

# 安全性和遵从性
## 如何
### [管理用户权限](azure-stack-manage-permissions.md)
### [为 AD FS 添加用户](azure-stack-add-users-adfs.md)

# 提供服务
## 入门
### 创建计划、产品/服务和配额
#### [设置配额](azure-stack-setting-quotas.md)
#### [创建计划](azure-stack-create-plan.md)
#### [创建产品/服务](azure-stack-create-offer.md)
#### [订阅产品/服务](azure-stack-subscribe-plan-provision-vm.md)
#### [在 Azure Stack 中委托产品](azure-stack-delegated-provider.md)
## 如何
### 提供 SQL 或 MySQL 作为 PaaS
#### [部署 MySQL 资源提供程序](azure-stack-mysql-resource-provider-deploy.md)
#### [部署 SQL 资源提供程序](azure-stack-sql-resource-provider-deploy.md)
### 提供应用服务作为 PaaS
#### [Azure Stack 中的应用服务概述](azure-stack-app-service-overview.md)
#### [准备工作](azure-stack-app-service-before-you-get-started.md)
#### [部署应用服务资源提供程序](azure-stack-app-service-deploy.md)
#### [添加更多 Web 辅助角色](azure-stack-app-service-add-worker-roles.md)
#### [配置部署源](azure-stack-app-service-configure-deployment-sources.md)
#### [在 Azure Stack 上的应用服务中启用 FTP](azure-stack-app-service-enable-ftp.md)
### 填充应用商店
#### [应用商店概述](azure-stack-marketplace.md)
#### [下载应用商店项](azure-stack-download-azure-marketplace-item.md)
#### [创建和发布应用商店项目](azure-stack-create-and-publish-marketplace-item.md)
#### [添加自定义虚拟机映像](azure-stack-add-vm-image.md)
#### [部署 Linux 虚拟机](azure-stack-linux.md)
### 帐单和退款
#### [帐单和退款概述](azure-stack-billing-and-chargeback.md)
#### [提供程序资源使用情况 API](azure-stack-provider-resource-api.md)
#### [租户资源使用情况 API](azure-stack-tenant-resource-usage-api.md)
#### [使用情况常见问题](azure-stack-usage-related-faq.md)

# 使用服务
## 计算
### [添加 VM 映像](azure-stack-add-vm-image.md)
### [使用 Linux 来宾](azure-stack-linux.md)
## 存储
### [概述](azure-stack-storage-overview.md)
### [差异和注意事项](azure-stack-acs-differences-tp2.md)
## 网络
### [适用于 Azure Stack 的 iDNS](azure-stack-understanding-dns-in-tp2.md)
### [Azure Stack 中的 DNS](azure-stack-dns.md)
### [了解站点到站点的 VPN 连接](azure-stack-create-vpn-connection-one-node-tp2.md)
## 密钥保管库
### [概述](azure-stack-kv-intro.md)
### 入门
#### [使用门户进行管理](azure-stack-kv-manage-portal.md)
#### [使用 PowerShell 进行管理](azure-stack-kv-manage-powershell.md)
### 如何
#### [使用证书创建 VM](azure-stack-kv-push-secret-into-vm.md)
#### [密钥保管库示例应用](azure-stack-kv-sample-app.md)

# 生成应用
## 概述
### [Azure Stack 开发](azure-stack-developer.md)
## 入门
### [连接到 Azure Stack](azure-stack-connect-azure-stack.md)
### 设置开发环境
#### [安装 PowerShell](azure-stack-powershell-install.md)
#### [下载工具](azure-stack-powershell-download.md)
#### [配置 PowerShell](azure-stack-powershell-configure.md)
#### [安装 Visual Studio](azure-stack-install-visual-studio.md)
## 如何
### [使用策略模块](azure-stack-policy-module.md)
### 使用模板
#### [模板概述](azure-stack-arm-templates.md)
#### [开发模板](azure-stack-develop-templates.md)
#### [检查模板](azure-stack-validate-templates.md)
#### [使用门户进行部署](azure-stack-deploy-template-portal.md)
#### [使用 PowerShell 进行部署](azure-stack-deploy-template-powershell.md)
#### [使用 Visual Studio 进行部署](azure-stack-deploy-template-visual-studio.md)

# 故障排除
## [已知问题](azure-stack-troubleshooting.md)
## [Azure Stack 中的诊断](azure-stack-diagnostics.md)

# 资源
## [MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStack)  
## [堆栈溢出](http://stackoverflow.com/questions/tagged/azure-stack)

