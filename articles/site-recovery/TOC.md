# 概述
## [什么是 Site Recovery？](site-recovery-overview.md)
## Site Recovery 的工作原理是什么？
### [Azure 到 Azure 体系结构](site-recovery-azure-to-azure-architecture.md)
### [VMware 到 Azure 体系结构](site-recovery-architecture-vmware-to-azure.md)
### [Hyper-V 到 Azure 体系结构](site-recovery-architecture-hyper-v-to-azure.md)
### [复制到辅助站点体系结构](site-recovery-architecture-to-secondary-site.md)
## [可以保护哪些工作负荷？](site-recovery-workload.md)
## Site Recovery 支持矩阵
### [Azure 到 Azure 支持](site-recovery-support-matrix-azure-to-azure.md)
### [本地到 Azure 支持](site-recovery-support-matrix-to-azure.md)
### [本地到辅助站点支持](site-recovery-support-matrix-to-sec-site.md)
## [常见问题](site-recovery-faq.md)
## [观看简介](https://azure.microsoft.com/resources/videos/index/?services=site-recovery)

# 入门
## [复制 Azure VM（预览）](site-recovery-azure-to-azure.md)
## [将物理服务器复制到 Azure](site-recovery-physical-servers-to-azure.md)
## [将 Hyper-V VM 复制到 Azure（包含 VMM）](site-recovery-vmm-to-azure.md)
## [将 Hyper-V VM 复制到 Azure](site-recovery-hyper-v-site-to-azure.md)
## [将 Hyper-V VM 复制到辅助站点（包含 VMM）](site-recovery-vmm-to-vmm.md)
## [将 VMware VM 和物理服务器复制到辅助站点](site-recovery-vmware-to-vmware.md)
## [在多租户部署中将 VMware VM 复制到 Azure (CSP)](site-recovery-multi-tenant-support-vmware-using-csp.md)

# 如何
## 计划
### [Azure 复制的先决条件](site-recovery-azure-to-azure-prereq.md)
### 规划网络
#### [为 Azure 到 Azure 复制规划网络（预览）](site-recovery-azure-to-azure-networking-guidance.md)
#### [为本地计算机复制规划网络](site-recovery-network-design.md)
#### [为 Azure VM 复制规划网络映射](site-recovery-network-mapping-azure-to-azure.md)
#### [为 Hyper-V VM 复制规划网络映射](site-recovery-network-mapping.md)
### 规划容量和可伸缩性
#### [为从 VMware 复制到 Azure 规划容量](site-recovery-plan-capacity-vmware.md)
#### [用于将 VMware 复制到 Azure 的 Deployment Planner](site-recovery-deployment-planner.md)
#### [用于 Hyper-V 复制的 Capacity Planner](site-recovery-capacity-planner.md)
### [为 VM 复制规划基于角色的访问](site-recovery-role-based-linked-access-control.md)
## 部署
### [将 VMware VM 复制到 Azure](vmware-walkthrough-overview.md)
#### [步骤 1：查看体系结构](vmware-walkthrough-architecture.md)
#### [步骤 2：验证先决条件和限制](vmware-walkthrough-prerequisites.md)
#### [步骤 3：规划容量](vmware-walkthrough-capacity.md)
#### [步骤 4：规划网络](vmware-walkthrough-network.md)
#### [步骤 5：准备 Azure](vmware-walkthrough-prepare-azure.md)
#### [步骤 6：准备 VMware](vmware-walkthrough-prepare-vmware.md)
#### [步骤 7：创建保管库](vmware-walkthrough-create-vault.md)
#### [步骤 8：设置源和目标](vmware-walkthrough-source-target.md)
#### [步骤 9：设置复制策略](vmware-walkthrough-replication.md)
#### [步骤 10：安装移动服务](vmware-walkthrough-install-mobility.md)
#### [步骤 11：启用复制](vmware-walkthrough-enable-replication.md)
#### [步骤 12：运行测试故障转移](vmware-walkthrough-test-failover.md)
## 配置
### 设置源环境
#### [VMware 到 Azure 的源环境](site-recovery-set-up-vmware-to-azure.md)
#### [物理到 Azure 的源环境](site-recovery-set-up-physical-to-azure.md)
### 设置目标环境
#### [VMware 到 Azure 的目标环境](site-recovery-prepare-target-vmware-to-azure.md)
#### [物理到 Azure 的目标环境](site-recovery-prepare-target-physical-to-azure.md)
### [配置复制设置](site-recovery-setup-replication-settings-vmware.md)
### [部署移动服务用于 VMware 复制](site-recovery-vmware-to-azure-install-mob-svc.md)
#### [使用 System Center Configuration Manager 部署移动服务](site-recovery-install-mobility-service-using-sccm.md)
#### [使用 Azure Automation DSC 部署移动服务](site-recovery-automate-mobility-service-install.md)
### 启用复制
#### [启用 Azure 到 Azure 复制](site-recovery-replicate-azure-to-azure.md)
#### [启用 VMware 到 Azure 复制](site-recovery-replicate-vmware-to-azure.md)
## 故障转移和故障回复
### [设置恢复计划](site-recovery-create-recovery-plans.md)
#### [将 Azure Runbook 添加到恢复计划](site-recovery-runbook-automation.md)
### 运行测试故障转移
#### [运行测试故障转移到 Azure](site-recovery-test-failover-to-azure.md)
#### [在 VMM 云之间运行测试故障转移](site-recovery-test-failover-vmm-to-vmm.md)
### [故障转移受保护的计算机](site-recovery-failover.md)
### 在故障转移后重新保护计算机
#### [从 Azure 次要区域到主区域进行重新保护](site-recovery-how-to-reprotect-azure-to-azure.md)
#### [从 Azure 到本地进行重新保护](site-recovery-how-to-reprotect.md)
### 从 Azure 进行故障回复
#### [从 Azure 到 VMware 进行故障回复](site-recovery-failback-azure-to-vmware.md)
#### [从 Azure 到 Hyper-V 进行故障回复](site-recovery-failback-from-azure-to-hyper-v.md)
## 迁移
### [迁移到 Azure](site-recovery-migrate-to-azure.md)
### [在 Azure 区域之间进行迁移](site-recovery-migrate-azure-to-azure.md)
### [将 AWS Windows 实例迁移到 Azure](site-recovery-migrate-aws-to-azure.md)
### [将已迁移的计算机复制到另一个 Azure 区域](site-recovery-azure-to-azure-after-migration.md)
## 工作负荷
### [Active Directory 和 DNS](site-recovery-active-directory.md)
### [复制 SQL Server](site-recovery-sql.md)
### [SharePoint](site-recovery-sharepoint.md)
### [Dynamics AX](site-recovery-dynamicsax.md)
### [RDS](site-recovery-workload.md#protect-rds)
### [Exchange](site-recovery-workload.md#protect-exchange)
### [SAP](site-recovery-workload.md#protect-sap)
### [基于 IIS 的 Web 应用程序](site-recovery-iis.md)
### [Citrix XenApp 和 XenDesktop](site-recovery-citrix-xenapp-and-xendesktop.md)
### [其他工作负荷](site-recovery-workload.md#workload-summary)
## 自动复制
### [将 Hyper-V 自动复制到 Azure（不包含 VMM）](site-recovery-deploy-with-powershell-resource-manager.md)
### [将 Hyper-V 自动复制到 Azure（包含 VMM）](site-recovery-vmm-to-azure-powershell-resource-manager.md)
### [将 Hyper-V 自动复制到辅助站点（包含 VMM）](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
## 管理
### [在 Azure 中管理进程服务器](site-recovery-vmware-setup-azure-ps-resource-manager.md)
### [管理配置服务器](site-recovery-vmware-to-azure-manage-configuration-server.md)
### [管理横向扩展的进程服务器](site-recovery-vmware-to-azure-manage-scaleout-process-server.md)
### [管理 vCenter 服务器](site-recovery-vmware-to-azure-manage-vCenter.md)
### [删除服务器并禁用保护](site-recovery-manage-registration-and-protection.md)

## 监视和故障排除
### [Azure 到 Azure 复制问题](site-recovery-azure-to-azure-troubleshoot-errors.md)
### [本地到 Azure 的复制问题](site-recovery-vmware-to-azure-protection-troubleshoot.md)
### [收集日志并解决本地问题](site-recovery-monitoring-and-troubleshooting.md)

# 引用
## [PowerShell](/powershell/module/azurerm.siterecovery)
## [PowerShell 经典](/powershell/module/azure/?view=azuresmps-3.7.0)
## [REST](https://msdn.microsoft.com/en-us/library/mt750497)

# 相关内容
## [Azure 自动化](/azure/automation/)

# 资源
## [学习路径](https://azure.microsoft.com/documentation/learning-paths/site-recovery/)
## [论坛](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hypervrecovmgr)
## [博客](http://azure.microsoft.com/blog/tag/azure-site-recovery/)
## [定价](https://azure.microsoft.com/pricing/details/site-recovery/)
## [服务更新](https://azure.microsoft.com/updates/?product=site-recovery)
