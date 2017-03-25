# 概述    
## [关于 Web 应用](app-service-web-overview.md)
## [比较托管选项](choose-web-site-cloud-service-vm.md)

# 快速入门    
## [创建静态 HTML 站点](app-service-web-get-started-html.md)
## [创建 ASP.NET 应用](app-service-web-get-started-dotnet.md)        
## [创建 PHP 应用](app-service-web-get-started-php.md) 
## [创建 Node.js 应用](app-service-web-get-started-nodejs.md) 
## [创建 Java 应用](app-service-web-get-started-java.md)        
## [创建 Python 应用](app-service-web-get-started-python.md)    

# 示例
## [Azure CLI](app-service-cli-samples.md) 
## [PowerShell](app-service-powershell-samples.md) 

# 教程
## [将功能添加到 Web 应用](app-service-web-get-started-2.md)
## [将 ASP.NET 应用与 SQL 数据库配合使用](web-sites-dotnet-get-started.md)
## [将 Laravel 应用与 MySQL 配合使用](app-service-web-php-get-started.md)
## [将 Sails.js 应用与 NOSQL DB 配合使用](app-service-web-nodejs-sails.md)
## [将 Java 应用与 Eclipse 配合使用](app-service-web-eclipse-create-hello-world-web-app.md)
## [将 Java 应用与 IntelliJ 配合使用](app-service-web-intellij-create-hello-world-web-app.md)
## [将 Django 应用与 MySQL 配合使用](web-sites-python-ptvs-django-mysql.md)

# 概念
## [应用服务的工作方式](../app-service/app-service-how-works-readme.md?toc=%2fazure%2fapp-service-web%2ftoc.json)    
## [应用服务计划](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md?toc=%2fazure%2fapp-service-web%2ftoc.json)    
## [应用服务环境](app-service-app-service-environment-intro.md)
## [身份验证和授权](../app-service/app-service-authentication-overview.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
## [使用本地 AD 进行身份验证](web-sites-authentication-authorization.md)


# 操作指南
## 开发应用    
### ASP.NET

#### [使用 VS Code 开发 ASP.NET Core 应用](web-sites-create-web-app-using-vscode.md)
### PHP
#### [设置 PHP 项目](web-sites-php-configure.md)
#### [设置 WordPress Multisite](web-sites-php-convert-wordpress-multisite.md) 
### Node.js
#### [使用 io.js](web-sites-nodejs-iojs.md)
#### [调试 Node.js 应用](web-sites-nodejs-debug.md)
### Java

#### [使用用于 Java 的 Azure SDK](java-create-azure-website-using-java-sdk.md)
#### [上载现有应用](web-sites-java-add-app.md)
#### [远程调试 Eclipse](app-service-web-debug-java-web-app-in-eclipse.md)
#### [远程调试 IntelliJ](app-service-web-debug-java-web-app-in-intellij.md)

### [使用 SendGrid 发送电子邮件](sendgrid-dotnet-how-to-send-email.md)

### 配置运行时    
#### [Windows 上的 PHP](web-sites-php-configure.md)

#### [Java](web-sites-java-add-app.md)
#### [Linux 上的 Node.js](app-service-linux-using-nodejs-pm2.md)
#### [Python](web-sites-python-configure.md)
            
### 配置应用程序
#### [使用应用设置](web-sites-configure.md)
            
## [部署到 Azure](web-sites-deploy.md)
### [通过 FTP 部署](app-service-deploy-content-sync.md)
### [通过云同步部署](web-sites-deploy.md)
### [持续部署](app-service-continuous-deployment.md)
### [部署到过渡环境](web-sites-staged-publishing.md)
### [从本地 Git 部署](app-service-deploy-local-git.md)
### [使用模板部署](app-service-deploy-complex-application-predictably.md)
### [敏捷部署](app-service-agile-software-development.md)
### [Beta 测试](app-service-web-test-in-production-controlled-test-flight.md)

### [设置部署凭据](app-service-deployment-credentials.md)

### 映射自定义域
#### [购买域](custom-dns-web-site-buydomains-web-app.md)
#### [映射第三方域](web-sites-custom-domain-name.md)
#### [使用流量管理器映射域](web-sites-traffic-manager-custom-domain-name.md)
#### [映射 GoDaddy 域](web-sites-godaddy-custom-domain-name.md)
#### [迁移活动域](app-service-custom-domain-name-migrate.md)

### [从 IIS 迁移](web-sites-migration-from-iis-server.md)
### [在生产中测试](app-service-web-test-in-production-get-start.md)

## 连接到数据库/资源        

### [连接到本地数据](web-sites-hybrid-connection-get-started.md) 
### [连接到 Azure VNet](web-sites-integrate-with-vnet.md)
### [使用 PowerShell 连接到 Azure VNet](app-service-vnet-integration-powershell.md)
### [连接到 Azure VM 上的 MongoDB](web-sites-dotnet-store-data-mongodb-vm.md)
            
##安全应用
### 对用户进行身份验证        
#### [使用 Azure AD 进行身份验证](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
#### [使用 Facebook 进行身份验证](../app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
#### [使用 Google 进行身份验证](../app-service-mobile/app-service-mobile-how-to-configure-google-authentication.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
#### [使用 Microsoft 帐户进行身份验证](../app-service-mobile/app-service-mobile-how-to-configure-microsoft-authentication.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
#### [使用 Twitter 进行身份验证](../app-service-mobile/app-service-mobile-how-to-configure-twitter-authentication.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
#### [使用本地 AD 进行身份验证](web-sites-authentication-authorization.md)
#### [将应用与多租户数据库配合使用](web-sites-dotnet-entity-framework-row-level-security.md)
### 分配自定义 SSL
#### [购买 SSL 证书](web-sites-purchase-ssl-web-site.md)
#### [配置第三方 SSL 证书](web-sites-configure-ssl-certificate.md)
### [实施 HTTPS](web-sites-configure-ssl-certificate.md#enforce-https-on-your-app)
### [配置 TLS 相互身份验证](app-service-web-configure-tls-mutual-auth.md)
            
##缩放应用        
### [纵向扩展](web-sites-scale.md)
### [横向扩展](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
### [使用流量管理器进行负载均衡](web-sites-traffic-manager.md)
### [应用服务环境的高度缩放](../app-service/app-service-app-service-environments-readme.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
### [使用 Azure CDN 扩大全球覆盖范围](cdn-websites-with-cdn.md)
### [通过 Memcache 连接到 Redis 缓存](web-sites-connect-to-redis-using-memcache-protocol.md)
### [创建 Redis 缓存](../redis-cache/cache-redis-cache-arm-provision.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
### [使用 Azure Redis 缓存管理会话状态](web-sites-dotnet-session-state-caching.md)

## 监视 
### [监视应用](web-sites-monitor.md)
### [启用日志](web-sites-enable-diagnostic-log.md)
### [流式传输日志](web-sites-streaming-logs-and-console.md)

## 备份内容        
### [备份应用](web-sites-backup.md)
### [从备份还原应用](web-sites-restore.md)
### [使用 REST API 备份](websites-csm-backup.md)

## 管理应用资源
### [使用 PowerShell 克隆应用](app-service-web-app-cloning.md)
### [使用门户克隆应用](app-service-web-app-cloning-portal.md)
### [移动资源](app-service-move-resources.md)
### [将 Azure Resource Manager 与 PowerShell 配合使用](app-service-web-app-azure-resource-manager-powershell.md)
### [使用 Azure 自动化管理应用](automation-manage-web-app.md)



# 引用    
## [CLI 2.0](/cli/azure/appservice)
## [PowerShell](/powershell)
## [REST API](/rest/api/appservice/) 
        
# 资源    
## 故障排除        
### [使用 Visual Studio 排除故障](web-sites-dotnet-troubleshoot-visual-studio.md)
### [对 Node.js 应用进行故障排除](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md)
### [排查 HTTP 502 和 503 错误](app-service-web-troubleshoot-http-502-http-503.md)
### [排查性能问题](app-service-web-troubleshoot-performance-degradation.md)
## [定价](https://azure.microsoft.com/pricing/details/app-service/)     
## [配额信息](../azure-subscription-service-limits.md#app-service-limits)    
## [服务更新和发行说明](https://azure.microsoft.com/updates/?product=app-service)    
## [最佳实践](app-service-best-practices.md)
## [示例](https://azure.microsoft.com/resources/samples/?service=app-service)    
## [视频](https://azure.microsoft.com/resources/videos/index/?services=app-service)
## Cookbook    
### [参考体系结构](../guidance/guidance-ra-app-service.md)    
### [部署脚本](https://azure.microsoft.com/documentation/scripts/)    
