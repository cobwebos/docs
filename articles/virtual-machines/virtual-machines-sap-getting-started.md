<properties
    pageTitle="使用 Azure 虚拟机 (VM) 上的 SAP | Microsoft Azure"
    description="使用 Azure 虚拟机 (VM) 上的 SAP"
    services="virtual-machines,virtual-network,storage"
    documentationCenter="saponazure"
    authors="MSSedusch"
    manager="juergent"
    editor=""
    tags="azure-service-management"
    keywords=""/>

<tags
    ms.service="virtual-machines"
    ms.date="11/18/2015"
    wacn.date=""/>
   
# 使用 Azure 虚拟机 (VM) 上的 SAP

云计算是一个广泛使用的术语，它在 IT 行业（从小公司到大型跨国企业）中受到越来越多的重视。Microsoft Azure 是 Microsoft 提供的一个云服务平台，它提供了各种新的可能性。现在客户将能够快速将应用程序预配为云服务并可取消预配，因此他们不会受到技术或预算方面的限制。公司不用在硬件基础结构中投入时间和预算，而是可以重点关注应用程序、业务流程以及它为客户和用户带来的好处。

借助 Microsoft Azure 虚拟机服务，Microsoft 提供了全面的基础结构即服务 (IaaS) 平台。Azure 虚拟机 (IaaS) 支持基于 SAP NetWeaver 的应用程序。以下白皮书介绍在作为所选平台的 Microsoft Azure 中如何规划和实现基于 SAP NetWeaver 的应用程序。

## 规划和实现

标题：Azure 虚拟机上的 SAP NetWeaver - 规划和实现指南

摘要：如果你正在考虑在 Azure 虚拟机中运行 SAP NetWeaver，可从本文开始着手。此规划和实现指南可帮助你评估现有或计划的基于 SAP NetWeaver 的系统是否可以部署到 Azure 虚拟机环境。它介绍了多个 SAP NetWeaver 部署方案，并包括特定于 Azure 的 SAP 配置。该文列出并说明了在 SAP/Azure 端运行混合 SAP 布局产品时所需的所有必要配置信息。此外，还介绍了为确保 IaaS 上基于 SAP NetWeaver 的系统实现高可用性可以采取的措施。

更新时间：2015 年 8 月

[立即下载此指南](http://go.microsoft.com/fwlink/?LinkId=397963)
## 部署
标题：Azure 虚拟机上的 SAP NetWeaver - 部署指南

摘要：本文档提供将 SAP NetWeaver 软件部署到 Azure 中虚拟机的过程指导。此文重点介绍三种特定部署方案，主要侧重于启用 SAP 的 Azure 监视扩展，包括针对 SAP 的 Azure 监视扩展的故障排除建议。此文假定你已阅读规划和实现指南。

更新时间：2015 年 9 月

[立即下载此指南](http://go.microsoft.com/fwlink/?LinkId=397964)

## Azure 上的 SAP DBMS
标题：Azure 中的 SAP DBMS - 部署指南

摘要：此文介绍了应与 SAP 一起运行的 DBMS 系统的规划和实现注意事项。在第一部分中，列出并提供了一般注意事项。此文的以下部分与在 Azure 中部署 SAP 所支持的不同 DBMS 相关。提供的不同 DBMS 是适用于 Linux、 Unix 和 Windows 的 SQL Server、SAP ASE、Oracle、SAP MaxDB 和 IBM DB2。在这些特定部分中，讨论了在 Azure 上将 SAP 系统与这些 DBMS 一起运行时必须考虑的注意事项。提供了 Azure 上的不同 DBMS 支持的备份和高可用性方法等主题，以便用于 SAP 应用程序。

更新时间：2015 年 12 月

[立即下载此指南](http://go.microsoft.com/fwlink/?LinkId=397965)

## Azure 上的 SAP NetWeaver

标题：SAP NetWeaver - 构建基于 Azure 的灾难恢复解决方案

摘要：本文档提供为 SAP NetWeaver 构建基于 Azure 的灾难恢复解决方案的分步指导。所述的解决方案假设 SAP 布局产品正在本地基于 Hyper-V 虚拟运行。在此文档的第一部分中，按组件介绍了 Azure Site Recovery (ASR) 服务。此文档的第二部分介绍了基于 SAP NetWeaver 的布局产品的详细信息。讨论并说明了将 ASR 与 SAP NetWeaver 应用程序实例和 SAP 中心服务配合使用的可能性。第二部分的重点是利用 ASR 实现使用 Windows Server 故障转移群集配置保护的 SAP 中心服务。

更新时间：2015 年 9 月

[立即下载此指南](http://go.microsoft.com/fwlink/?LinkID=521971)

## Azure 上的 SAP NetWeaver - HA

标题：Azure 上的 SAP NetWeaver - 将 Azure 上的 Windows Server 故障转移群集与 SIOS DataKeeper 配合使用来群集 SAP ASCS/SCS 实例

摘要：本文档介绍如何使用 SIOS DataKeeper 在 Azure 上设置高度可用的 SAP ASCS/SCS 配置。SAP 使用需要共享磁盘的 Windows Server 故障转移群集配置保护其单点故障组件，如 SAP ASCS/SCS 或入队复制服务。这些 SAP 组件对于 SAP 系统的功能是非常必要的。因此，高可用性功能需要落实到位，以确保这些组件可以承受服务器或 VM 失败，就像祼机和 Hyper-V 环境的 Windows 群集配置所做的那样。截至 2015 年 8 月，Azure 自身不能提供这些关键 SAP 组件所需的基于 Windows 的高度可用配置所需的共享磁盘。但是借助于 SIOS 开发的 DataKeeper 产品的帮助，可以在 Azure IaaS 平台上构建 SAP ASCS/SCS 所需的 Windows Server 故障转移群集配置。此文以分步方式介绍如何在 Azure 中使用 SIOS Datakeeper 提供的共享磁盘安装 Windows Server 故障转移群集配置。此文将介绍 Azure 上 Windows 和 SAP 端配置的详细信息，这些配置将以最佳方式使高可用性配置正常工作。此文是对 SAP 安装文档和 SAP 说明的补充，这些文档代表在给定平台上安装和部署 SAP 软件的主要资源。

更新时间：2015 年 8 月

[立即下载此指南](http://go.microsoft.com/fwlink/?LinkId=613056)

## Azure SUSE Linux 虚拟机上的 SAP NetWeaver

标题：测试 Microsoft Azure SUSE Linux VM 上的 SAP NetWeaver

摘要：目前，在 Azure Linux VM 上运行 SAP NetWeaver 没有官方 SAP 支持。然而，客户可能想要执行一些测试或者可能会考虑在 Azure Linux VM 上运行 SAP 演示或培训系统，只要无需联系 SAP 技术支持即可。此文可帮助针对运行 SAP 设置 Azure SUSE Linux VM，并提供一些基本提示以避免常见的潜在陷阱。

更新时间：2015 年 12 月

[此文可在此处找到](/documentation/articles/virtual-machines-sap-on-linux-suse-quickstart)

<!---HONumber=Mooncake_0104_2016-->