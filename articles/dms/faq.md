---
title: 常见问题解答-Azure 数据库迁移服务
description: 有关使用 Azure 数据库迁移服务执行数据库迁移的常见问题。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 9fdbf3888b4302946fe20259a333842caa122836
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717790"
---
# <a name="faq-about-using-azure-database-migration-service"></a>有关使用 Azure 数据库迁移服务的常见问题解答

本文列出了有关将 Azure 数据库迁移服务与相关答案一起使用的常见问题。

## <a name="overview"></a>개요

**问：什么是 Azure 数据库迁移服务？**
Azure 数据库迁移服务是一项完全托管的服务，旨在实现从多个数据库源到 Azure 数据平台的无缝迁移，且停机时间最短。 此服务目前处于公开发布状态，持续开发工作重点在于：

* 안정성 및 성능.
* 원본 -대상 쌍의 반복적 추가
* 충돌 없는 마이그레이션에 대한 지속적인 투자

**问： Azure 数据库迁移服务目前支持哪些源/目标对？**
该服务当前支持各种源/目标对或迁移方案。 사용 가능한 각 마이그레이션 시나리오의 상태에 대한 전체 목록은 [Azure Database Migration Service에서 지원하는 마이그레이션 시나리오의 상태](https://docs.microsoft.com/azure/dms/resource-scenario-status) 문서를 참조하세요.

其他迁移方案处于预览阶段，需要通过 DMS 预览网站提交提名。 若要查看预览版中的方案的完整列表并注册参加其中一项产品，请参阅[DMS 预览站点](https://aka.ms/dms-preview/)。

**问： Azure 数据库迁移服务支持哪些版本的 SQL Server 作为源？**
从 SQL Server 迁移时，Azure 数据库迁移服务支持的源 SQL Server 2005 到 SQL Server 2019。

**问：使用 Azure 数据库迁移服务时，脱机和联机迁移的区别是什么？**
可以使用 Azure 数据库迁移服务执行脱机和联机迁移。 通过*脱机*迁移，应用程序停机时间会在迁移启动时启动。 在进行*联机*迁移时，停机时间限制为在迁移结束时要缩减的时间。 오프라인 마이그레이션을 테스트하여 가동 중지 시간이 용납 가능한 수준인지 판단하고, 용납되지 않는다면 온라인 마이그레이션을 수행하는 것이 좋습니다.

> [!NOTE]
> Azure Database Migration Service를 사용하여 온라인 마이그레이션을 수행하려면 프리미엄 가격 책정 계층에 따라 인스턴스를 만들어야 합니다. 자세한 내용은 Azure Database Migration Service [가격 책정](https://azure.microsoft.com/pricing/details/database-migration/) 페이지를 참조하세요.

**问： Azure 数据库迁移服务与其他 Microsoft 数据库迁移工具（例如数据库迁移助手（DMA）或 SQL Server 迁移助手（SSMA））有何不同？**
Azure 数据库迁移服务是用于大规模 Microsoft Azure 数据库迁移的首选方法。 若要详细了解 Azure 数据库迁移服务与其他 Microsoft 数据库迁移工具的比较情况，以及针对各种方案使用该服务的建议，请参阅博客文章[区分 Microsoft 的数据库迁移工具和服务](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/)。

**问： Azure 数据库迁移服务与 Azure Migrate 产品/服务有何不同？**
Azure Migrate 帮助将本地虚拟机迁移到 Azure IaaS。 이 서비스는 마이그레이션 적합성 및 성능 기반 크기 조정을 평가하며, Azure에서 온-프레미스 가성 머신을 실행할 때 드는 비용을 예측합니다. Azure Migrate는 온-프레미스 VM 기반 워크로드를 Azure IaaS VM으로 리프트 앤 시프트 마이그레이션하는 데 유용합니다. 但是，与 Azure 数据库迁移服务不同，Azure Migrate 不是 Azure PaaS 关系数据库平台（如 Azure SQL 数据库或 Azure SQL 数据库托管实例）的专用数据库迁移服务。

## <a name="setup"></a>설치 프로그램

**问：使用 Azure 数据库迁移服务的先决条件是什么？**
若要确保 Azure 数据库迁移服务在执行数据库迁移时顺利运行，需要满足几个先决条件。 일부 필수 구성 요소는 서비스가 지원하는 모든 시나리오(원본-대상 쌍)에 적용되는 반면에 특정 시나리오에만 적용되는 필수 구성 요소도 있습니다.

지원되는 모든 마이그레이션 시나리오에 공통적인 Azure Database Migration Service 필구 구성 요소는 다음을 수행해야 합니다.

* 使用 Azure 资源管理器部署模型创建 Azure 数据库迁移服务的 Microsoft Azure 虚拟网络，该模型通过使用[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)或[VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)为本地源服务器提供站点到站点连接。
* 确保虚拟网络安全组规则不会阻止以下通信端口443、53、9354、445、12000。 有关虚拟网络 NSG 流量筛选的详细信息，请参阅[筛选网络流量和网络安全组](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)一文。
* 원본 데이터베이스 앞에 방화벽 어플라이언스를 사용하는 경우 Azure Database Migration Service에서 원본 데이터베이스에 액세스하여 마이그레이션할 수 있도록 허용하는 방화벽 규칙을 추가해야 합니다.

有关使用 Azure 数据库迁移服务来竞争特定迁移方案所需的所有先决条件的列表，请参阅 docs.microsoft.com 上的 Azure 数据库迁移服务[文档](https://docs.microsoft.com/azure/dms/dms-overview)中的相关教程。

**问：如何实现查找 Azure 数据库迁移服务的 IP 地址，以便可以为用于访问源数据库以进行迁移的防火墙规则创建允许列表吗？**
可能需要添加防火墙规则，以允许 Azure 数据库迁移服务访问源数据库进行迁移。 服务的 IP 地址是动态的，但如果使用的是 ExpressRoute，则该地址由企业网络专用分配。 标识适当的 IP 地址的最简单方法是在与预配的 Azure 数据库迁移服务资源相同的资源组中查找关联的网络接口。 일반적으로 네트워크 인터페이스 리소스의 이름은 NIC 접두사로 시작되고 그 뒤에 고유한 문자와 숫자 시퀀스가 붙습니다(예 : NIC-jj6tnztnmarpsskr82rbndyp). 이 네트워크 인터페이스 리소스를 선택하면 Azure Portal 리소스 개요 페이지에서 허용 목록에 포함되어야 하는 IP 주소를 볼 수 있습니다.

SQL Server가 수신 대기하는 포트 원본을 허용 목록에 포함해야 할 수도 있습니다. 기본적으로 이 포트는 1433이지만 원본 SQL Server는 다른 포트도 수신 대기하도록 구성될 수 있습니다. 이 경우 해당 포트도 허용 목록에 포함시켜야 합니다. 동적 관리 뷰 쿼리를 사용하여 SQL Server가 수신 대기하는 포트를 확인할 수 있습니다.

```sql
    SELECT DISTINCT
        local_tcp_port
    FROM sys.dm_exec_connections
    WHERE local_tcp_port IS NOT NULL
```

SQL Server 오류 로그를 쿼리하여 SQL Server가 수신 대기하는 포트를 확인할 수도 있습니다.

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on'
    GO
```

**问：如何实现设置 Microsoft Azure 虚拟网络？**
尽管有多个 Microsoft 教程可以指导你完成设置虚拟网络的过程，但官方文档出现在[Azure 虚拟网络](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)一文中。

## <a name="usage"></a>사용량

**问：使用 Azure 数据库迁移服务执行数据库迁移所需的步骤摘要是什么？**
일반적이고 간단한 데이터베이스 마이그레이션 단계:

1. 대상 데이터베이스를 만듭니다.
2. 评估你的源数据库。
    * 对于同源迁移，请使用[DMA](https://www.microsoft.com/download/details.aspx?id=53595)评估现有的数据库。
    * 对于异构迁移（从竞争源），请通过[SSMA](https://aka.ms/get-ssma)评估现有的数据库。 还可以使用 SSMA 转换数据库对象，并将架构迁移到目标平台。
3. Azure Database Migration Service 인스턴스를 만듭니다.
4. 创建一个迁移项目，指定源数据库、目标数据库和要迁移的表。
5. 启动完全加载。
6. 후속 유효성 검사를 선택합니다.
7. 새 클라우드 기반 데이터베이스로 프로덕션 환경의 수동 전환을 수행합니다.

## <a name="troubleshooting-and-optimization"></a>故障排除和优化

**问：我在 DM 中设置了一个迁移项目，连接到我的源数据库时遇到困难。我该怎么办？**
如果在迁移时连接到源数据库系统时遇到问题，请在虚拟网络中创建一个虚拟机，并在其中设置 DMS 实例。 在虚拟机中，你应该能够运行连接测试，如使用 UDL 文件测试连接 SQL Server 或下载 Robo 3T 以测试 MongoDB 连接。 如果连接测试成功，则无法连接到源数据库。 如果连接测试失败，请与网络管理员联系。

**问：为什么我的 Azure 数据库迁移服务不可用或停止？**
如果用户显式停止 Azure 数据库迁移服务（DMS），或者服务在24小时内处于非活动状态，则该服务将处于 "已停止" 或 "已暂停" 状态。 각각의 경우에서 서비스는 사용할 수 없으며 중지된 상태에 있게 됩니다.  활성 마이그레이션을 다시 시작하려면 서비스를 다시 시작합니다.

**问：是否有优化 Azure 数据库迁移服务性能的建议？**
몇 가지 작업을 수행하면 서비스를 사용한 데이터베이스 마이그레이션 속도를 높일 수 있습니다.

* 서비스 인스턴스를 만들 때 다중 CPU 범용 가격 책정 계층을 사용하면 서비스에서 다중 vCPU를 활용하여 병렬 처리 및 빠른 데이터 전송이 가능합니다.
* 하위 수준의 SKU를 사용할 때 데이터 전송 작업에 영향을 줄 수 있는 Azure SQL 데이터베이스 제한을 최소화하기 위해 데이터 마이그레이션 작업 중에 Azure SQL Database 대상 인스턴스를 프리미엄 계층 SKU로 일시 강화합니다.

## <a name="next-steps"></a>다음 단계

Azure Database Migration Service 및 국가별 가용성에 대한 개요는 [Azure Database Migration Service란?](dms-overview.md) 문서를 참조하세요.
