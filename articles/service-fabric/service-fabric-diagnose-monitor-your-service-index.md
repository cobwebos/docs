<properties
   pageTitle="对 Service Fabric 服务进行诊断和故障排除"
   description="帮助你对 Service Fabric 服务进行诊断、监视和故障排除的概念性信息和教程。"
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="08/04/2015"
   wacn.date=""/>

# 诊断和监视 Service Fabric 服务
监视、检测、诊断和故障排除允许服务继续，同时对用户体验造成最小中断。若要了解更多信息，请阅读以下文章：

- [如何在本地监视和诊断服务](documentation/articles/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally)
- [为你的 Service Fabric 应用程序设置 Application Insights](documentation/articles/service-fabric-diagnostics-application-insights-setup)
- [应用程序升级失败故障排除](documentation/articles/service-fabric-application-upgrade-troubleshooting)
- [对所监视应用程序升级失败进行故障排除](documentation/articles/service-fabric-application-monitored-upgrade-troubleshooting)
- [Reliable Actors 的诊断和性能监视](documentation/articles/service-fabric-reliable-actors-diagnostics)
- [Reliable Services 的诊断和性能监视](documentation/articles/service-fabric-reliable-services-diagnostics)

## 排除群集故障
以下信息将帮助你排除本地开发群集的故障：

- [排除本地开发群集安装的故障](documentation/articles/service-fabric-troubleshoot-local-cluster-setup)

## 运行状况模型
Service Fabric 引入了一个运行状况模型，它为 Service Fabric 实体提供了内容丰富、灵活且可扩展的报告和评估功能。Service Fabric 组件报告所有实体在初始状态下的运行状况。用户服务可以用报告的有关群集中其自身实体或其他实体的且特定于其逻辑的信息丰富运行状况数据。若要了解更多信息，请阅读以下文章：

- [Service Fabric 运行状况监视简介](documentation/articles/service-fabric-health-introduction)
- [如何查看 Service Fabric 运行状况报告](documentation/articles/service-fabric-view-entities-aggregated-health)
- [使用系统运行状况报告进行故障排除](documentation/articles/service-fabric-understand-and-troubleshoot-with-system-health-reports)
- [添加自定义 Service Fabric 运行状况报告](documentation/articles/service-fabric-report-health)
 

<!---HONumber=74-->