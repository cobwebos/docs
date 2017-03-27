# 体系结构

## 云基础知识

### [设计可复原应用程序](guidance-resiliency-overview.md)
#### [复原清单](guidance-resiliency-checklist.md)
#### [故障模式分析](guidance-resiliency-failure-mode-analysis.md)
#### [灾难恢复](..\resiliency\resiliency-disaster-recovery-azure-applications.md)
#### [灾难恢复和高可用性](..\resiliency\resiliency-disaster-recovery-high-availability-azure-applications.md)
#### [高可用性](..\resiliency\resiliency-high-availability-azure-applications.md)
#### [高可用性清单](..\resiliency\resiliency-high-availability-checklist.md)
#### [特定于 Azure 服务的复原指南](..\resiliency\resiliency-service-guidance-index.md)
#### [发生数据损坏或意外删除后进行恢复](..\resiliency\resiliency-technical-guidance-recovery-data-corruption.md)
#### [从本地故障中恢复](..\resiliency\resiliency-technical-guidance-recovery-local-failures.md)
#### [发生区域范围的服务中断后进行恢复](..\resiliency\resiliency-technical-guidance-recovery-loss-azure-region.md)
#### [从本地恢复到 Azure](..\resiliency\resiliency-technical-guidance-recovery-on-premises-azure.md)
#### [Azure 复原技术指南](..\resiliency\resiliency-technical-guidance.md)


## 参考体系结构

### [在 Azure 中运行 VM 工作负荷](guidance-ra-compute.md)
#### [在 Azure 上运行 Linux VM](guidance-compute-single-vm-linux.md)
#### [在 Azure 上运行 Windows VM](guidance-compute-single-vm.md)
#### [在 Azure 上运行多个 VM 以提高可伸缩性和可用性](guidance-compute-multi-vm.md)
#### [运行用于 N 层体系结构的 Linux VM](guidance-compute-n-tier-vm-linux.md)
#### [运行用于 N 层体系结构的 Windows VM](guidance-compute-n-tier-vm.md)
#### [在多个区域中运行 Linux VM 以实现高可用性](guidance-compute-multiple-datacenters-linux.md)
#### [在多个区域中运行 Windows VM 以实现高可用性](guidance-compute-multiple-datacenters.md)

### [将本地网络连接到 Azure](guidance-ra-hybrid-networking.md)
#### [带有 Azure ExpressRoute 的混合网络体系结构](guidance-hybrid-network-expressroute.md)
#### [带有 Azure 和本地 VPN 的混合网络体系结构](guidance-hybrid-network-vpn.md)
#### [高可用性混合网络体系结构](guidance-hybrid-network-expressroute-vpn-failover.md)

### [保护 Azure 中的云边界](guidance-ra-network-security.md)
#### [保护 Azure 中的混合网络体系结构](guidance-iaas-ra-secure-vnet-hybrid.md)
#### [Azure 与 Internet 之间的外围网络](guidance-iaas-ra-secure-vnet-dmz.md)

### [管理 Azure 中的标识](guidance-ra-identity.md)
#### [实现 Azure Active Directory](guidance-identity-aad.md)
#### [将 ADDS 扩展到 Azure](guidance-identity-adds-extend-domain.md)
#### [在 Azure 中创建 ADDS 资源林](guidance-identity-adds-resource-forest.md)
#### [在 Azure 中实现 ADFS](guidance-identity-adfs.md)

### [用于 Azure 应用服务的 Web 应用程序体系结构](guidance-ra-app-service.md)
#### [基本 Web 应用程序](guidance-web-apps-basic.md)
#### [高可用性 Web 应用程序](guidance-web-apps-multi-region.md)
#### [提高 Web 应用程序的可伸缩性](guidance-web-apps-scalability.md)


## 云应用程序最佳实践

### [API 设计指南](..\best-practices-api-design.md)
### [API 实现指南](..\best-practices-api-implementation.md)
### [自动缩放指南](..\best-practices-auto-scaling.md)
### [可用性清单](..\best-practices-availability-checklist.md)
### [后台作业指南](..\best-practices-background-jobs.md)
### [Azure 配对区域](..\best-practices-availability-paired-regions.md)
### [缓存指南](..\best-practices-caching.md)
### [内容交付网络指南](..\best-practices-cdn.md)
### [数据分区指南](..\best-practices-data-partitioning.md)
### [监视和诊断指南](..\best-practices-monitoring.md)
### [Microsoft 云服务和网络安全](..\best-practices-network-security.md)
### [用于设计 Azure Resource Manager 模板的模式](..\best-practices-resource-manager-design-templates.md)
### [Azure 资源的建议命名约定](guidance-naming-conventions.md)
### [Azure Resource Manager 安全注意事项](..\best-practices-resource-manager-security.md)
### [在 Azure Resource Manager 模板中共享状态](..\best-practices-resource-manager-state.md)
### [有关重试的常规指南](..\best-practices-retry-general.md)
### [重试服务指南](..\best-practices-retry-service-specific.md)
### [可缩放性清单](..\best-practices-scalability-checklist.md)


## 方案指南

### [Azure 上的 Elasticsearch 指南](guidance-elasticsearch.md)
#### [在 Azure 上运行 Elasticsearch](guidance-elasticsearch-running-on-azure.md)
#### [优化数据提取性能](guidance-elasticsearch-tuning-data-ingestion-performance.md)
#### [优化数据聚合和查询性能](guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md)
#### [配置复原和恢复](guidance-elasticsearch-configuring-resilience-and-recovery.md)
#### [创建性能测试环境](guidance-elasticsearch-creating-performance-testing-environment.md)
#### [实现 JMeter 测试计划](guidance-elasticsearch-implementing-jmeter-test-plan.md)
#### [部署 JMeter JUnit 采样器](guidance-elasticsearch-deploying-jmeter-junit-sampler.md)
#### [运行自动复原测试](guidance-elasticsearch-running-automated-resilience-tests.md)
#### [运行自动性能测试](guidance-elasticsearch-running-automated-performance-tests.md)

### [多租户应用程序的标识管理](guidance-multitenant-identity.md)
#### [概述](guidance-multitenant-identity-intro.md)
#### [Tailspin Surveys 应用程序](guidance-multitenant-identity-tailspin.md)
#### [使用 Azure AD 和 OpenID Connect 进行身份验证](guidance-multitenant-identity-authenticate.md)
#### [基于声明的标识](guidance-multitenant-identity-claims.md)
#### [注册和租户加入](guidance-multitenant-identity-signup.md)
#### [应用程序角色](guidance-multitenant-identity-app-roles.md)
#### [基于角色和基于资源的授权](guidance-multitenant-identity-authorize.md)
#### [保护后端 Web API](guidance-multitenant-identity-web-api.md)
#### [缓存访问令牌](guidance-multitenant-identity-token-cache.md)
#### [使用客户的 AD FS 进行联合身份验证](guidance-multitenant-identity-adfs.md)
#### [使用客户端断言获取访问令牌](guidance-multitenant-identity-client-assertion.md)
#### [使用 Azure 密钥保管库保护应用程序机密](guidance-multitenant-identity-keyvault.md)
#### [部署高可用性虚拟设备](guidance-nva-ha.md)


<!--HONumber=Nov16_HO4-->


