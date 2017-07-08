
# 概述
## [什么是 Service Fabric？](service-fabric-overview.md)
## [了解微服务](service-fabric-overview-microservices.md)
## [大图](service-fabric-content-roadmap.md)
## [应用程序方案](service-fabric-application-scenarios.md)
## [模式和方案](service-fabric-patterns-and-scenarios.md)
## [体系结构](service-fabric-architecture.md)
## [术语](service-fabric-technical-overview.md)

# 入门
## 设置开发环境
### [Windows](service-fabric-get-started.md)
### [Linux](service-fabric-get-started-linux.md)
### [Mac OS](service-fabric-get-started-mac.md)
## 创建第一个应用程序
### [Windows 上的 C#](service-fabric-create-your-first-application-in-visual-studio.md)
### [Linux 上的 Java](service-fabric-create-your-first-linux-application-with-java.md)
### [Linux 上的 C#](service-fabric-create-your-first-linux-application-with-csharp.md)
## [在本地群集上部署应用](service-fabric-get-started-with-a-local-cluster.md)
## [在容器中部署 .NET 应用](service-fabric-host-app-in-a-container.md)
## [在 Azure 上创建第一个群集](service-fabric-get-started-azure-cluster.md)
## [创建第一个独立群集](service-fabric-get-started-standalone-cluster.md)
## [创建第一个容器应用](service-fabric-get-started-containers.md)

# 如何
## 构建应用程序
  
### 概念
#### [支持的编程模型](service-fabric-choose-framework.md)
#### [应用程序模型](service-fabric-application-model.md)
#### [托管模型](service-fabric-hosting-model.md)
#### [服务清单资源](service-fabric-service-manifest-resources.md)
#### [服务状态](service-fabric-concepts-state.md)
#### [服务分区](service-fabric-concepts-partitioning.md)
#### [服务的可用性](service-fabric-availability-services.md)
#### [应用程序的可伸缩性](service-fabric-concepts-scalability.md)
#### [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

### [规划应用容量](service-fabric-capacity-planning.md)

### 生成来宾可执行服务
#### [部署来宾可执行文件](service-fabric-deploy-existing-app.md)
#### [部署多个来宾可执行文件](service-fabric-deploy-multiple-apps.md)

### 生成容器服务
#### [概述](service-fabric-containers-overview.md)
#### [部署 Windows 容器](service-fabric-deploy-container.md)
#### [部署 Linux 容器](service-fabric-deploy-container-linux.md)
#### [Docker Compose（预览）](service-fabric-docker-compose.md)
#### [容器和服务的资源调控](service-fabric-resource-governance.md)
#### [卷和日志记录驱动程序](service-fabric-containers-volume-logging-drivers.md)

### 生成 Reliable Services 服务
#### [概述](service-fabric-reliable-services-introduction.md)
#### 概念
##### [Reliable Services 生命周期 - C#](service-fabric-reliable-services-lifecycle.md)
##### [Reliable Services 生命周期 - Java](service-fabric-reliable-services-lifecycle-java.md)

#### Reliable Collections
##### [Reliable Collections](service-fabric-reliable-services-reliable-collections.md)
##### [Reliable Collection 指导原则和建议](service-fabric-reliable-services-reliable-collections-guidelines.md)
##### [使用 Reliable Collections](service-fabric-work-with-reliable-collections.md)
##### [事务和锁](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
##### [可靠的并发队列](service-fabric-reliable-services-reliable-concurrent-queue.md)
##### [Reliable Collection 序列化](service-fabric-reliable-services-reliable-collections-serialization.md)
##### [可靠状态管理器和 Reliable Collection 内部](service-fabric-reliable-services-reliable-collections-internals.md)

#### 入门
##### [Windows 上的 C#](service-fabric-reliable-services-quick-start.md)
##### [Linux 上的 Java](service-fabric-reliable-services-quick-start-java.md)

#### [配置](service-fabric-reliable-services-configuration.md)
#### [发送通知](service-fabric-reliable-services-notifications.md)
#### [备份和还原](service-fabric-reliable-services-backup-restore.md)

#### 与服务进行通信
##### [与 Reliable Services 通信](service-fabric-reliable-services-communication.md)
##### [服务远程处理 - C#](service-fabric-reliable-services-communication-remoting.md)
##### [服务远程处理 - Java](service-fabric-reliable-services-communication-remoting-java.md)
##### [WCF](service-fabric-reliable-services-communication-wcf.md)
##### [保护通信 - C#](service-fabric-reliable-services-secure-communication.md)
##### [保护通信 - Java](service-fabric-reliable-services-secure-communication-java.md)

#### [高级使用率](service-fabric-reliable-services-advanced-usage.md)

### 生成 Reliable Actor 服务
#### [概述](service-fabric-reliable-actors-introduction.md)
#### 概念
##### [体系结构](service-fabric-reliable-actors-platform.md)
##### [生命周期和垃圾回收](service-fabric-reliable-actors-lifecycle.md)
##### [状态管理](service-fabric-reliable-actors-state-management.md)
##### [多形性](service-fabric-reliable-actors-polymorphism.md)
##### [重新进入](service-fabric-reliable-actors-reentrancy.md)
##### [类型序列化](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

#### 入门
##### [Windows 上的 C#](service-fabric-reliable-actors-get-started.md)
##### [Linux 上的 Java](service-fabric-reliable-actors-get-started-java.md)

#### [发送通知](service-fabric-reliable-actors-events.md) 
#### [设置计时器和提醒](service-fabric-reliable-actors-timers-reminders.md)
#### [配置 KvsActorStateProvider](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)
#### [配置通信设置](service-fabric-reliable-actors-fabrictransportsettings.md) 
#### [配置 ReliableDictionaryActorStateProvider](service-fabric-reliable-actors-reliabledictionarystateprovider-configuration.md)

### 与服务进行通信
#### [服务通信](service-fabric-connect-and-communicate-with-services.md)
#### [DNS 服务](service-fabric-dnsservice.md)
#### [反向代理](service-fabric-reverseproxy.md)
#### [配置反向代理以进行安全通信](service-fabric-reverseproxy-configure-secure-communication.md)

### [添加 Web 前端](service-fabric-add-a-web-frontend.md)

### 在 IDE 中工作
#### [用于 Java 开发的 Eclipse 插件入门](service-fabric-get-started-eclipse.md)
#### [在 Visual Studio 中管理应用](service-fabric-manage-application-in-visual-studio.md)
#### [在 Visual Studio 中配置安全连接](service-fabric-visualstudio-configure-secure-connections.md)
#### [为多个环境配置应用程序](service-fabric-manage-multiple-environment-app-configuration.md)

### 配置安全性
#### [管理应用程序机密](service-fabric-application-secret-management.md)  
#### [配置应用程序的安全策略](service-fabric-application-runas-security.md)

### 调试
#### [在 VS 中调试 C# 服务](service-fabric-debugging-your-application.md)
#### [在 Eclipse 中调试 Java 服务](service-fabric-debugging-your-application-java.md)
#### [常见错误和异常](service-fabric-errors-and-exceptions.md)

### 在本地监视和诊断
#### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
#### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

### 与 API 管理集成
#### [概述](service-fabric-api-management-overview.md)
#### [快速入门](service-fabric-api-management-quick-start.md)

### 从云服务迁移
#### [比较云服务和 Service Fabric](service-fabric-cloud-services-migration-differences.md)
#### [迁移到 Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md)
#### [建议的做法](/azure/architecture/service-fabric/migrate-from-cloud-services)

## 管理应用程序生命周期
### [概述](service-fabric-application-lifecycle.md)
### [打包应用程序](service-fabric-package-apps.md)
### [了解 ImageStoreConnectionString 设置](service-fabric-image-store-connection-string.md)
### 部署或删除应用程序
#### [PowerShell](service-fabric-deploy-remove-applications.md)
#### [Azure CLI 2.0](service-fabric-application-lifecycle-azure-cli-2-0.md)
#### [Visual Studio](service-fabric-publish-app-remote-cluster.md)
#### [FabricClient API](service-fabric-deploy-remove-applications-fabricclient.md)
### 升级应用程序
#### [概述](service-fabric-application-upgrade.md)
#### [配置应用程序升级](service-fabric-visualstudio-configure-upgrade.md)
#### [应用程序升级参数](service-fabric-application-upgrade-parameters.md)
#### 升级
##### [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
##### [Visual Studio](service-fabric-application-upgrade-tutorial.md)
#### [排查应用程序升级故障](service-fabric-application-upgrade-troubleshooting.md)
#### [应用程序升级中的数据序列化](service-fabric-application-upgrade-data-serialization.md)
#### [应用程序升级的高级主题](service-fabric-application-upgrade-advanced.md)

### 测试应用程序和服务
#### [故障分析概述](service-fabric-testability-overview.md)
#### [测试服务到服务通信](service-fabric-testability-scenarios-service-communication.md)
#### 模拟失败
##### [使用受控的混沌](service-fabric-controlled-chaos.md)
##### [使用测试操作](service-fabric-testability-actions.md)
##### [运行工作负荷期间](service-fabric-testability-workload-tests.md)
##### [使用测试方案](service-fabric-testability-scenarios.md)
##### [使用节点转换 API](service-fabric-node-transition-apis.md)
#### [对应用程序进行负载测试](service-fabric-vso-load-test.md)

### 设置持续集成
#### [使用 VSTS 设置持续集成](service-fabric-set-up-continuous-integration.md)
#### [使用 Jenkins 部署 Linux Java 应用](service-fabric-cicd-your-linux-java-application-with-jenkins.md)

## 创建和管理群集

### [概述](service-fabric-deploy-anywhere.md)
### 概念
#### [描述群集](service-fabric-cluster-resource-manager-cluster-description.md)
#### [群集安全性](service-fabric-cluster-security.md)
#### [Linux 和 Windows 之间的功能差异](service-fabric-linux-windows-differences.md)

### 规划和准备
#### [容量规划](service-fabric-cluster-capacity.md)
#### [灾难恢复](service-fabric-disaster-recovery.md)

### Azure 上的群集
#### 概念
##### [节点类型和 VM 规模集](service-fabric-cluster-nodetypes.md)
##### [群集网络模式](service-fabric-patterns-networking.md)
#### 创建 
##### [Azure 门户](service-fabric-cluster-creation-via-portal.md)
##### [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
##### [Visual Studio 和 Azure Resource Manager](service-fabric-cluster-creation-via-visual-studio.md)
#### 缩放 
##### [手动](service-fabric-cluster-scale-up-down.md)
##### [以编程方式](service-fabric-cluster-programmatic-scaling.md)
#### [升级](service-fabric-cluster-upgrade.md)
#### [设置访问控制](service-fabric-cluster-security-roles.md)
#### [配置](service-fabric-cluster-fabric-settings.md)
#### [管理群集证书](service-fabric-cluster-security-update-certs-azure.md) 
#### [删除](service-fabric-cluster-delete.md)

### 独立群集
#### [规划和准备部署](service-fabric-cluster-standalone-deployment-preparation.md)
#### 创建
##### [在本地创建](service-fabric-cluster-creation-for-windows-server.md)
##### [在 Azure 虚拟机上创建](service-fabric-cluster-creation-with-windows-azure-vms.md)
##### [使用证书进行保护](service-fabric-windows-cluster-x509-security.md)  
##### [使用 Windows 安全进行保护](service-fabric-windows-cluster-windows-security.md)
##### [独立包的内容](service-fabric-cluster-standalone-package-contents.md)
#### [缩放](service-fabric-cluster-windows-server-add-remove-nodes.md)
#### [设置访问控制](service-fabric-cluster-security-roles.md)
#### [配置](service-fabric-cluster-manifest.md)
#### [升级](service-fabric-cluster-upgrade-windows-server.md) 

### [可视化群集](service-fabric-visualizing-your-cluster.md)
### [连接到安全群集](service-fabric-connect-to-secure-cluster.md)

### [使用 XPlat CLI 管理群集](service-fabric-azure-cli.md)
### [使用 Azure CLI 2.0 命令管理群集](service-fabric-azure-cli-2-0.md)
### [修补群集节点](service-fabric-patch-orchestration-application.md)

### 管理和协调群集资源
#### [群集 Resource Manager 概述](service-fabric-cluster-resource-manager-introduction.md)
#### [群集 Resource Manager 体系结构](service-fabric-cluster-resource-manager-architecture.md)
#### [描述群集](service-fabric-cluster-resource-manager-cluster-description.md)
#### [应用程序组概述](service-fabric-cluster-resource-manager-application-groups.md)
#### [配置群集 Resource Manager 设置](service-fabric-cluster-resource-manager-configure-services.md)
#### [资源消耗度量值](service-fabric-cluster-resource-manager-metrics.md)
#### [使用服务相关性](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
#### [服务放置策略](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
#### [管理群集](service-fabric-cluster-resource-manager-management-integration.md)
#### [群集碎片整理](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
#### [均衡群集](service-fabric-cluster-resource-manager-balancing.md)
#### [限制](service-fabric-cluster-resource-manager-advanced-throttling.md)
#### [服务移动](service-fabric-cluster-resource-manager-movement-cost.md)

## 监视和诊断
### [监视和诊断应用程序](service-fabric-diagnostics-overview.md)
### 生成事件
#### [生成基础结构级别事件](service-fabric-diagnostics-event-generation-infra.md)
##### [Reliable Services 事件](service-fabric-reliable-services-diagnostics.md)
##### [Reliable Actors 事件](service-fabric-reliable-actors-diagnostics.md)
#### [生成应用程序级别事件](service-fabric-diagnostics-event-generation-app.md)
### 检查应用程序和群集运行状况
#### [监视 Service Fabric 运行状况](service-fabric-health-introduction.md)
#### [报告并检查服务运行状况](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
#### [添加自定义运行状况报告](service-fabric-report-health.md)
#### [使用系统运行状况报告进行故障排除](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
#### [查看运行状况报告](service-fabric-view-entities-aggregated-health.md)
### 聚合事件
#### [使用 EventFlow 聚合事件](service-fabric-diagnostics-event-aggregation-eventflow.md)
#### 使用 Azure 诊断聚合事件
##### [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
##### [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
### 分析事件
#### [使用 Application Insights 分析事件](service-fabric-diagnostics-event-analysis-appinsights.md)
#### [使用 OMS 分析事件](service-fabric-diagnostics-event-analysis-oms.md)
### [排查本地群集的故障](service-fabric-troubleshoot-local-cluster-setup.md)

# 引用
## [PowerShell (Azure)](/powershell/module/azurerm.servicefabric/)
## [PowerShell](/powershell/module/servicefabric/?view=azureservicefabricps)
## [Azure CLI](/cli/azure/sf)
## [Java API](/java/api/overview/azure/servicefabric)
## [.NET](/dotnet/api/overview/azure/servicefabric?view=azure-dotnet)
## [REST](/rest/api/servicefabric)

# 资源
## [Azure 路线图](https://azure.microsoft.com/roadmap/)
## [有关 Service Fabric 的常见问题](service-fabric-common-questions.md)
## [学习路径](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
## [MSDN 论坛](https://social.msdn.microsoft.com/Forums/home?forum=AzureServiceFabric)
## [定价](https://azure.microsoft.com/pricing/details/service-fabric/)
## [代码示例](http://aka.ms/servicefabricsamples)
## [Service Fabric 支持选项](service-fabric-support.md)
## [服务更新](https://azure.microsoft.com/updates/?product=service-fabric)
## [视频](https://azure.microsoft.com/documentation/videos/index/?services=service-fabric)
