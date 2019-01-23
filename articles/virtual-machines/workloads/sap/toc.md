---
ms.openlocfilehash: a9e46bdb97899957f09a847f0fef03a6fec5dc6a
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452884"
---
# 概述
## [入门](get-started.md)
## [认证](sap-certifications.md)
# Azure 上的 SAP HANA（大型实例）
## 概述
### [什么是 Azure 上的 SAP HANA（大型实例）？](hana-overview-architecture.md)
### [了解术语](hana-know-terms.md)
### [认证](hana-certification.md)
### [对 HLI 可用的 SKU](hana-available-skus.md)
### [大小调整](hana-sizing.md)
### [加入要求](hana-onboarding-requirements.md)
### [SAP HANA 数据分层和扩展节点](hana-data-tiering-extension-nodes.md)
### [操作模型和责任](hana-operations-model.md)
## 体系结构
### [一般体系结构](hana-architecture.md)
### [网络体系结构](hana-network-architecture.md)
### [存储体系结构](hana-storage-architecture.md)
### [支持 HLI 的方案](hana-supported-scenario.md)
## 基础结构和连接
### [HLI 部署](hana-overview-infrastructure-connectivity.md)
### [将 Azure VM 连接到 HANA 大型实例](hana-connect-azure-vm-large-instances.md)
### [将 VNet 连接到 HANA 大型实例 ExpressRoute](hana-connect-vnet-express-route.md)
### [其他网络要求](hana-additional-network-requirements.md)
## 安装 SAP HANA
### [验证配置](hana-installation.md)
### [示例 HANA 安装](hana-example-installation.md)
## 高可用性和灾难恢复
### [选项和注意事项](hana-overview-high-availability-disaster-recovery.md)
### [备份和还原](hana-backup-restore.md)
### [原则和准备工作](hana-concept-preparation.md)
### [灾难恢复故障转移过程](hana-failover-procedure.md)
## 故障排除和监视
### [监视 HLI](troubleshooting-monitoring.md)
### [HANA 端的监视和故障排除](hana-monitor-troubleshoot.md)
## 如何
### [设置使用 STONITH 的 HA](ha-setup-with-stonith.md)
### [类型 II SKU 的 OS 备份](os-backup-type-ii-skus.md)
### [HANA 大型实例的操作系统升级](os-upgrade-hana-large-instance.md)
### [为 SUSE Linux 安装 SMT 服务器](hana-setup-smt.md)
# Azure 虚拟机上的 SAP HANA
## [单实例 SAP HANA 安装](hana-get-started.md)
## [S/4 HANA 或 BW/4 HANA SAP CAL 部署指南](cal-s4h.md)
## [Azure 上的 SAP HANA 基础结构配置和操作](hana-vm-operations.md)
## Azure 虚拟机中的 SAP HANA 可用性
### [Azure 上的 SAP HANA 可用性概述](sap-hana-availability-overview.md)
### [一个 Azure 区域内的 Azure 上的 SAP HANA 可用性](sap-hana-availability-one-region.md)
### [跨 Azure 区域的 Azure 上的 SAP HANA 可用性](sap-hana-availability-across-regions.md)
### [在 SLES 上设置 SAP HANA 系统复制](sap-hana-high-availability.md)
### [在 RHEL 上设置 SAP HANA 系统复制](sap-hana-high-availability-rhel.md)
### [排查 SAP HANA 横向扩展和 SLES 上的 Pacemaker 问题](hana-vm-troubleshoot-scale-out-ha-on-sles.md)
## [SAP HANA 备份概述](sap-hana-backup-guide.md)
## [SAP HANA 文件级备份](sap-hana-backup-file-level.md)
## [SAP HANA 存储快照备份](sap-hana-backup-storage-snapshots.md)
# Azure 虚拟机上的 SAP NetWeaver 和 Business One
## [SAP 工作负荷规划和部署清单](sap-deployment-checklist.md)
## [在 Azure 上规划和实施 SAP NetWeaver](planning-guide.md)
## [SAP NetWeaver 部署指南](deployment-guide.md)
## 适用于 SAP 工作负荷的 DBMS 部署指南
### [适用于 SAP 工作负荷的常规 Azure 虚拟机 DBMS 部署](dbms_guide_general.md)
### [适用于 SAP 工作负荷的 SQL Server Azure 虚拟机 DBMS 部署](dbms_guide_sqlserver.md)
### [适用于 SAP 工作负荷的 Oracle Azure 虚拟机 DBMS 部署](dbms_guide_oracle.md)
### [适用于 SAP 工作负荷的 IBM DB2 Azure 虚拟机 DBMS 部署](dbms_guide_ibm.md)
### [适用于 SAP 工作负荷的 SAP ASE Azure 虚拟机 DBMS 部署](dbms_guide_sapase.md)
### [Azure 上的 SAP MaxDB、liveCache 和内容服务器部署](dbms_guide_maxdb.md)
### Azure 虚拟机中的 SAP HANA 可用性
### [Azure 上的 SAP HANA 可用性概述](sap-hana-availability-overview.md)
### [一个 Azure 区域内的 Azure 上的 SAP HANA 可用性](sap-hana-availability-one-region.md)
### [跨 Azure 区域的 Azure 上的 SAP HANA 可用性](sap-hana-availability-across-regions.md)
## [Azure 虚拟机上的 SAP Business One](business-one-azure.md)
## [Windows/SQL Server SAP CAL 上的 SAP IDES 部署指南](cal-ides-erp6-erp7-sp3-sql.md)
## [适用于 Azure 的 SAP LaMa 连接器](lama-installation.md)
## Windows 和 Linux 上的高可用性 (HA)
### [概述](sap-high-availability-guide-start.md)
### 高可用性体系结构
#### [HA 体系结构和方案](sap-high-availability-architecture-scenarios.md)
#### [更高可用性体系结构和方案](sap-higher-availability-architecture-scenarios.md)
#### [使用共享磁盘在 Windows 上为 (A)SCS 实例提供 HA](sap-high-availability-guide-wsfc-shared-disk.md)
#### [使用 SOFS 文件共享在 Windows 上为 (A)SCS 实例提供 HA](sap-high-availability-guide-wsfc-file-share.md)
#### [在 SUSE Linux 上为 (A)SCS 实例提供 HA](high-availability-guide-suse.md)
#### [在 Red Hat Enterprise Linux 上为 (A)SCS 实例提供 HA](high-availability-guide-rhel.md)
### Azure 基础结构准备
#### [Windows 以及用于 (A)SCS 实例的共享磁盘](sap-high-availability-infrastructure-wsfc-shared-disk.md)
#### [Windows 以及用于 (A)SCS 实例的 SOFS 文件共享](sap-high-availability-infrastructure-wsfc-file-share.md)
#### [SLES 中 Azure VM 上的 NFS 的高可用性](high-availability-guide-suse-nfs.md)
#### [适用于 SAP NetWeaver 的 Red Hat Enterprise Linux 上的 Azure VM 上的 GlusterFS](high-availability-guide-rhel-glusterfs.md)
#### [SLES 上的 Pacemaker](high-availability-guide-suse-pacemaker.md)
#### [RHEL 上的 Pacemaker](high-availability-guide-rhel-pacemaker.md)
### SAP 安装
#### [Windows 以及用于 (A)SCS 实例的共享磁盘](sap-high-availability-installation-wsfc-shared-disk.md)
#### [Windows 以及用于 (A)SCS 实例的 SOFS 文件共享](sap-high-availability-installation-wsfc-file-share.md)
#### [SUSE Linux 以及用于 (A)SCS 实例的 NFS](high-availability-guide-suse.md)
#### [Red Hat Enterprise Linux 上的 SAP NetWeaver 的高可用性](high-availability-guide-rhel.md)
### SAP 多 SID
#### [Windows 以及用于 (A)SCS 实例的共享磁盘](sap-ascs-ha-multi-sid-wsfc-shared-disk.md)
#### [Windows 以及用于 (A)SCS 实例的 SOFS 文件共享](sap-ascs-ha-multi-sid-wsfc-file-share.md)
##  [使用 Azure Site Recovery 进行 SAP 灾难恢复](../../../site-recovery/site-recovery-workload.md#protect-sap)
# AAD SAP 标识集成和单一登录
## [与 SAP 云集成](../../../active-directory/saas-apps/sap-customer-cloud-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [AAD 与 SAP Cloud Platform Identity Authentication 集成](../../../active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [设置与 SAP Cloud Platform 配合使用的单一登录](../../../active-directory/saas-apps/sap-hana-cloud-platform-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [AAD 与 SAP NetWeaver 集成](../../../active-directory/saas-apps/sap-netweaver-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [AAD 与 SAP Business ByDesign 集成](../../../active-directory/saas-apps/sapbusinessbydesign-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [AAD 与 SAP HANA DBMS 集成](../../../active-directory/saas-apps/saphana-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
##[通过 Azure AD 执行的 SAP Fiori Launchpad SAML 单一登录](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad)
# 将 Azure 服务集成到 SAP 中
## [在 Power BI Desktop 中使用 SAP HANA](https://docs.microsoft.com/power-bi/desktop-sap-hana)
## [DirectQuery 和 SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
## [在 Power BI Desktop 中使用 SAP BW 连接器](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector)
## [Azure 数据工厂提供 SAP HANA 和业务数据仓库数据集成](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)
# 资源
## [Azure 路线图](https://azure.microsoft.com/roadmap/)

