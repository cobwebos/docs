---
title: Azure Synapse Analytics （以前称为 SQL DW）体系结构
description: 了解 Azure Synapse Analytics （以前称为 SQL DW）如何将大规模并行处理（MPP）与 Azure 存储结合起来，实现高性能和可伸缩性。
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 1d808210861d971b2915206e7be0fe9b955616c5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720310"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-architecture"></a>Azure Synapse Analytics （以前称为 SQL DW）体系结构 

Azure Synapse는 엔터프라이즈 데이터 웨어하우징과 빅 데이터 분석을 결합한 무제한 분석 서비스입니다. 또한 서버리스 주문형 리소스 또는 프로비저닝된 리소스를 규모에 맞게 사용하여 사용자의 용어로 데이터를 자유롭게 쿼리할 수 있습니다. Azure Synapse는 이 두 가지 기능을 즉각적인 BI 및 기계 학습 애플리케이션을 위해 데이터를 수집, 준비, 관리 및 제공할 수 있는 통합 환경에 통합합니다.

 Azure Synapse에는 다음 네 가지 구성 요소가 있습니다.
- SQL Analytics：完整的 T-sql 分析 
    - SQL 池（按预配的 DWU 付费）-正式发布
    - SQL 주문형(처리되는 TB당 요금 지불) – (미리 보기)
- Spark：深度集成的 Apache Spark （预览） 
- 数据集成：混合数据集成（预览版）
- Studio：统一的用户体验。  (Preview)

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="sql-analytics-mpp-architecture-components"></a>SQL Analytics MPP 体系结构组件

[SQL Analytics](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse)利用扩展体系结构来跨多个节点分布数据的计算处理。 규모 단위는 [데이터 웨어하우스 단위](what-is-a-data-warehouse-unit-dwu-cdwu.md)로 알려진 컴퓨팅 능력의 추상화입니다. 计算与存储分离，这使你能够在系统中独立于数据扩展计算。

![SQL 分析体系结构](media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

SQL Analytics 使用基于节点的体系结构。 应用程序将 T-sql 命令连接并颁发给控制节点，该节点是 SQL Analytics 的单一入口点。 控制节点运行 MPP 引擎，该引擎优化查询以进行并行处理，然后将操作传递到计算节点以并行完成工作。 

컴퓨팅 노드는 모든 사용자 데이터를 Azure Storage에 저장하고 병렬 쿼리를 실행합니다. DMS(Data Movement Service)는 쿼리를 병렬로 실행하고 정확한 결과를 반환하기 위해 필요할 때 노드에서 데이터를 이동시키는 시스템 수준의 내부 서비스입니다. 

使用分离的存储和计算，使用 SQL Analytics 时可以：

* 스토리지 요구 사항에 관계없이 컴퓨팅 능력을 독립적으로 조정합니다.
* 在 SQL 池（数据仓库）内扩大或缩小计算能力，而无需移动数据。
* 데이터를 그대로 둔 채 컴퓨팅 용량을 일시 중지하여 스토리지 비용만 지불합니다.
* 운영 시간 동안 컴퓨팅 용량을 다시 시작합니다.

### <a name="azure-storage"></a>Azure Storage

SQL Analytics 利用 Azure 存储空间来确保用户数据的安全。  由于你的数据由 Azure 存储进行存储和管理，因此，你的存储使用量会收取单独的费用。 数据会分片到**分发**中，以优化系统的性能。 테이블을 정의할 때 데이터 분산에 사용할 분할 패턴을 선택할 수 있습니다. 支持以下分片模式：

* Hash
* 라운드 로빈
* 복제

### <a name="control-node"></a>제어 노드

控制节点是体系结构的大脑。 모든 애플리케이션 및 연결과 상호 작용하는 프런트 엔드입니다. MPP 엔진은 병렬 쿼리를 최적화하고 조정하기 위해 제어 노드에서 실행됩니다. 将 T-sql 查询提交到 SQL Analytics 时，控制节点会将其转换为并行运行的查询。

### <a name="compute-nodes"></a>컴퓨팅 노드

컴퓨팅 노드는 컴퓨팅 능력을 제공합니다. 분산은 처리를 위해 컴퓨팅 노드로 매핑됩니다. 当你为更多计算资源付费时，SQL Analytics 会将分发重新映射到可用的计算节点。 计算节点数范围从1到60，由 SQL Analytics 的服务级别确定。

각 컴퓨팅 노드에는 시스템 뷰에 표시되는 노드 ID가 있습니다. 시스템 뷰에서 이름이 sys.pdw_nodes로 시작하는 node_id 열을 검색하여 Compute 노드 ID를 볼 수 있습니다. 시스템 뷰 목록은 [MPP 시스템 뷰](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=aps-pdw-2016-au7)를 참조하세요.

### <a name="data-movement-service"></a>데이터 이동 서비스
DMS(데이터 이동 서비스)는 컴퓨팅 노드 간의 데이터 이동을 조정하는 데이터 전송 기술입니다. 일부 쿼리는 병렬 쿼리가 정확한 결과를 반환하도록 하려면 데이터 이동이 필요합니다. 데이터 이동이 필요할 때 DMS는 올바른 데이터가 올바른 위치에 도달하도록 보장합니다. 

## <a name="distributions"></a>배포

분산은 분산 데이터에 실행되는 병렬 쿼리의 스토리지 및 처리의 기본 단위입니다. 当 SQL Analytics 运行查询时，工作将被划分为60的更小查询，这些查询将并行运行。 

60개의 작은 쿼리는 각각 데이터 분산 중 하나에서 실행됩니다. 각 컴퓨팅 노드는 60개 중 하나 이상의 분산을 관리합니다. 具有最大计算资源的 SQL 池对每个计算节点都有一个分配。 具有最小计算资源的 SQL 池在一个计算节点上具有所有分布。  

## <a name="hash-distributed-tables"></a>해시 분산 테이블
해시 분산 테이블은 대형 테이블의 조인 및 집계에 대해 가장 높은 쿼리 성능을 제공할 수 있습니다. 

若要将数据分片到哈希分布式表中，SQL Analytics 将使用哈希函数来明确地将每一行分配给一个分布。 테이블 정의에서 열 중 하나는 분산 열로 지정됩니다. 해시 함수는 분산 열의 값을 사용하여 각 행을 분산에 할당합니다.

다음은 전체(분산되지 않은 테이블)가 해시 분산 테이블로 저장되는 방식을 보여 주는 다이어그램입니다. 

![分布式表](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "분산 테이블")  

* 각 행은 하나의 분산에 속합니다.  
* 결정적 해시 알고리즘은 각 행을 하나의 분산에 할당합니다.  
* 분산당 테이블 행의 수는 테이블 크기에 따라 달라집니다.

분산 열을 선택할 때 고유성, 데이터 기울이기, 시스템에서 실행되는 쿼리 종류 등 성능에 대해 고려할 사항이 있습니다.

## <a name="round-robin-distributed-tables"></a>라운드 로빈 분산 테이블
라운드 로빈 테이블은 만들기 가장 간단한 테이블이며 로드를 위한 준비 테이블로 사용될 때 빠른 성능을 제공합니다.

라운드 로빈 분산 테이블은 데이터를 테이블 전체에 고르게 분산하지만 최적화는 하지 않습니다. 먼저 분산이 무작위로 선택되고 행 버퍼가 순차적으로 분산에 할당됩니다. 데이터는 로드는 라운드 로빈 테이블이 빠르지만 쿼리 성능은 해시 분산 테이블이 더 뛰어난 경우가 많습니다. 轮循机制表上的联接需要重新组织的数据，这需要额外的时间。


## <a name="replicated-tables"></a>복제된 테이블
복제된 테이블은 작은 테이블에 가장 빠른 쿼리 성능을 제공합니다.

복제된 테이블은 각 컴퓨팅 노드에 테이블의 전체 복사본을 캐시합니다. 결과적으로 테이블을 복제하면 조인 또는 집계 전에 컴퓨팅 노드 간에 데이터를 전송하지 않아도 됩니다. 복제된 테이블은 작은 테이블에서 가장 잘 활용됩니다. 需要额外的存储空间，并且在写入数据时会产生额外的开销，这会使大型表不切实际。  

下图显示了在每个计算节点上的第一个分布上缓存的复制表。  

![复制的表](media/sql-data-warehouse-distributed-data/replicated-table.png "복제 테이블") 

## <a name="next-steps"></a>다음 단계
现在，你已了解有关 Azure Synapse 的一点，接下来了解如何快速[创建 SQL 池](./sql-data-warehouse-get-started-provision.md)和[加载示例数据](./sql-data-warehouse-load-sample-databases.md)。 Azure을 처음 접하는 경우 새 용어를 발견하면 [Azure 용어집](../azure-glossary-cloud-terminology.md) 을 유용하게 사용할 수 있습니다. 或者查看其中一些其他 Azure Synapse 资源。  

* [고객 성공 사례](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
* [블로그](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [기능 요청](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [비디오](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [지원 티켓 만들기](./sql-data-warehouse-get-started-create-support-ticket.md)
* [MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse)
* [Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)

