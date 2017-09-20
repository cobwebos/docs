# 概述
## [什么是云服务？](cloud-services-choose-me.md)
## [云服务配置文件和打包](cloud-services-model-and-package.md)

# 入门
## [.NET 云服务示例](cloud-services-dotnet-get-started.md)
## [Visual Studio 云服务的 Python 示例](cloud-services-python-ptvs.md)
## [使用 Microsoft HPC Pack 设置混合 HPC 群集](cloud-services-setup-hybrid-hpcpack-cluster.md)

# 如何
## 计划
### [虚拟机大小](cloud-services-sizes-specs.md)
### [更新](cloud-services-update-azure-service.md)

## 开发
### [创建 PHP Web 角色和辅助角色](../cloud-services-php-create-web-role.md)
### [构建和部署 Node.js 应用程序](cloud-services-nodejs-develop-deploy-app.md)
### [使用 Express 构建 Node.js Web 应用程序](cloud-services-nodejs-develop-deploy-express-app.md)
### 存储和 Visual Studio
#### [Blob 存储和连接的服务](../visual-studio/vs-storage-cloud-services-getting-started-blobs.md)
#### [队列存储和连接的服务](../visual-studio/vs-storage-cloud-services-getting-started-queues.md)
#### [表存储和连接的服务](../visual-studio/vs-storage-cloud-services-getting-started-tables.md)
### 配置程序包以实现连续构建和部署
#### [TFS 和 Team Build](cloud-services-dotnet-continuous-delivery.md)
### [配置角色的流量规则](cloud-services-enable-communication-role-instances.md)
### [处理云服务生命周期事件](cloud-services-role-lifecycle-dotnet.md)
### [Socket.io (Node.js)](cloud-services-nodejs-chat-app-socketio.md)
### [使用 Twilio 拨打电话 (.NET)](../partner-twilio-cloud-services-dotnet-phone-call-web-role.md)
### [New Relic](../store-new-relic-cloud-services-dotnet-application-performance-management.md)

### 配置启动任务
#### [创建启动任务](cloud-services-startup-tasks.md)
#### [常见启动任务](cloud-services-startup-tasks-common.md)
#### [使用任务在云服务角色上安装 .NET](cloud-services-dotnet-install-dotnet.md)

### 配置远程桌面
#### [门户](cloud-services-role-enable-remote-desktop-new-portal.md)
#### [经典门户](cloud-services-role-enable-remote-desktop.md)
#### [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)

## 部署
### 在门户中创建和部署云服务
#### [门户](cloud-services-how-to-create-deploy-portal.md)
#### [经典门户](cloud-services-how-to-create-deploy.md)
### [在 PowerShell 中创建空云服务容器](cloud-services-powershell-create-cloud-container.md)
### 配置自定义域名
#### [门户](cloud-services-custom-domain-name-portal.md)
#### [经典门户](cloud-services-custom-domain-name.md)
### [暂存云服务部署 (Node.js)](cloud-services-nodejs-stage-application.md)
### [连接到自定义域控制器](cloud-services-connect-to-custom-domain.md)

## 管理服务
### 常见管理任务
#### [门户](cloud-services-how-to-manage-portal.md)
#### [经典门户](cloud-services-how-to-manage.md)
### 配置云服务
#### [门户](cloud-services-how-to-configure-portal.md)
#### [经典门户](cloud-services-how-to-configure.md)
### [使用 Azure 自动化管理云服务](automation-manage-cloud-services.md)
### 配置自动缩放
#### [门户](cloud-services-how-to-scale-portal.md)
#### [经典门户](cloud-services-how-to-scale.md)
### [使用 Python 管理 Azure 资源](cloud-services-python-how-to-use-service-management.md)

### [来宾 OS 修补程序](cloud-services-guestos-msrc-releases.md)
### 来宾 OS 停用
#### [停用策略](cloud-services-guestos-retirement-policy.md)
#### [系列 1 停用通知](cloud-services-guestos-family1-retirement.md)
### [来宾 OS 发行动态](cloud-services-guestos-update-matrix.md)
### [云服务角色配置 XPath 备忘单](cloud-services-role-config-xpath.md)

## 管理证书
### [云服务和管理证书](cloud-services-certs-create.md)
### 配置 SSL 
#### [门户](cloud-services-configure-ssl-certificate-portal.md)
#### [经典门户](cloud-services-configure-ssl-certificate.md)

## 监视
### [监视云服务](cloud-services-how-to-monitor.md)
### [测试性能](../vs-azure-tools-performance-profiling-cloud-services.md)
#### [使用 Visual Studio 探查器测试](cloud-services-performance-testing-visual-studio-profiler.md)
### 启用诊断
#### [PowerShell](cloud-services-diagnostics-powershell.md)
#### [.NET](cloud-services-dotnet-diagnostics.md)
#### [Visual Studio](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)
### [在 Azure 诊断中使用性能计数器](cloud-services-dotnet-diagnostics-performance-counters.md)
### [在 Azure 存储中存储和查看诊断数据](cloud-services-dotnet-diagnostics-storage.md)
### [使用诊断跟踪云服务](cloud-services-dotnet-diagnostics-trace-flow.md)
### [将诊断数据发送到 App Insights](cloud-services-dotnet-diagnostics-applicationinsights.md)

## 故障排除
### 调试 
#### [通过持续交付启用远程调试](cloud-services-virtual-machines-dotnet-continuous-delivery-remote-debugging.md)
#### [云服务的选项](../vs-azure-tools-debugging-cloud-services-overview.md)
#### [使用 Visual Studio 调试本地云服务](../vs-azure-tools-debug-cloud-services-virtual-machines.md)
#### [使用 Visual Studio 调试已发布的云服务](../vs-azure-tools-intellitrace-debug-published-cloud-services.md)
### [云服务分配失败](cloud-services-allocation-failures.md)
### [云服务角色回收的常见原因](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md)
### [TEMP 文件夹的默认大小太小，不能用于角色](cloud-services-troubleshoot-default-temp-folder-size-too-small-web-worker-role.md)
### [常见部署问题](cloud-services-troubleshoot-deployment-problems.md)
### [角色未能启动](cloud-services-troubleshoot-roles-that-fail-start.md)
### [恢复指南](cloud-services-disaster-recovery-guidance.md)
### 云服务常见问题
#### [应用程序和服务可用性常见问题解答](cloud-services-application-and-service-availability-faq.md)
#### [配置和管理常见问题解答](cloud-services-configuration-and-management-faq.md)
#### [连接和网络常见问题解答](cloud-services-connectivity-and-networking-faq.md)
#### [部署常见问题解答](cloud-services-deployment-faq.md)

# 引用
## [代码示例](https://azure.microsoft.com/en-us/resources/samples/?service=cloud-services)
## [.csdef XMLSchema](https://msdn.microsoft.com/library/azure/ee758711)
## [.cscfg XMLSchema](https://msdn.microsoft.com/library/azure/ee758710)
## [REST](https://msdn.microsoft.com/library/azure/ee460812)

# 资源
## [Azure 路线图](https://azure.microsoft.com/roadmap/?category=compute)
## [学习路径](https://azure.microsoft.com/documentation/learning-paths/cloud-services/)
## [MSDN 论坛](https://social.msdn.microsoft.com/Forums/en-us/home?forum=windowsazuredevelopment)
## [定价](https://azure.microsoft.com/pricing/details/cloud-services/)
## [定价计算器](https://azure.microsoft.com/pricing/calculator/)
## [服务更新](https://azure.microsoft.com/updates/?product=cloud-services&updatetype=&platform=)
## [视频](https://azure.microsoft.com/documentation/videos/index/?services=cloud-services)
