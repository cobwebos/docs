---
title: Azure SQL Database에 데이터 이동 - Team Data Science Process
description: 将数据从平面文件（CSV 或 TSV 格式）或从本地 SQL Server 中存储的数据移到 Azure SQL 数据库。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: f9a1424f2afe6c5153e208601b21dff9651880a8
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722452"
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Azure Machine Learning을 위해 Azure SQL Database로 데이터 이동

本文概述了用于将数据从平面文件（CSV 或 TSV 格式）或从本地 SQL Server 中存储的数据移到 Azure SQL 数据库的选项。 클라우드로 데이터를 이동하는 이러한 작업은 팀 데이터 과학 프로세스의 일부입니다.

Machine Learning을 위해 온-프레미스 SQL Server로 데이터를 이동하기 위한 옵션을 간략히 설명하는 토픽은 [Azure Virtual Machine의 SQL Server로 데이터 이동](move-sql-server-virtual-machine.md)을 참조하세요.

다음 표에서는 Azure SQL Database로 데이터를 이동하는 옵션을 요약합니다.

| <b>원본</b> | <b>대상: Azure SQL Database</b> |
| --- | --- |
| <b>플랫 파일(CSV 또는 TSV 형식)</b> |[대량 삽입 SQL 쿼리](#bulk-insert-sql-query) |
| <b>온-프레미스 SQL Server</b> |1.[플랫 파일로 내보내기](#export-flat-file)<br> 2. [SQL 数据库迁移向导](#insert-tables-bcp)<br> 3.[数据库备份和还原](#db-migration)<br> 4. [Azure 数据工厂](#adf) |

## <a name="prereqs"></a>필수 조건
여기에 설명된 절차를 위해서는 다음이 필요합니다.

* **Azure 구독**. 구독이 없는 경우 [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 등록할 수 있습니다.
* **Azure Storage 계정**. 이 자습서에서는 데이터 저장을 위해 Azure Storage 계정을 사용합니다. Azure Storage 계정이 없는 경우 [스토리지 계정 만들기](../../storage/common/storage-account-create.md) 문서를 참조하세요. 스토리지 계정을 만든 후에는 스토리지 액세스에 사용되는 계정 키를 확보해야 합니다. 请参阅[管理存储帐户访问密钥](../../storage/common/storage-account-keys-manage.md)。
* **Azure SQL Database**에 대한 액세스. Azure SQL Database를 설정해야 하는 경우, [Microsoft Azure SQL Database 시작](../../sql-database/sql-database-get-started.md)에서 Azure SQL Database의 새 인스턴스를 프로비전하는 방법에 대한 정보를 제공합니다.
* 로컬로 설치 및 구성된 **Azure PowerShell** . 자세한 내용은 [Azure PowerShell 설치 및 구성법](/powershell/azure/overview)을 참조하세요.

**데이터**: [NYC Taxi 데이터 세트](https://chriswhong.com/open-data/foil_nyc_taxi/)를 사용하여 마이그레이션 프로세스를 시연합니다. NYC Taxi 데이터 세트는 여정 데이터 및 요금에 대한 정보를 포함하며 Azure Blob Storage [NYC Taxi 데이터](https://www.andresmh.com/nyctaxitrips/)에서 제공됩니다. 이러한 파일의 샘플 및 설명은 [NYC Taxi Trips 데이터 세트 설명](sql-walkthrough.md#dataset)에 제공됩니다.

자신의 데이터 세트에 여기에 설명된 절차를 도입하거나 NYC Taxi 데이터 세트를 사용하여 설명된 대로 단계를 따릅니다. NYC Taxi 데이터 세트를 온-프레미스 SQL Server 데이터베이스에 업로드하려면 [SQL Server Database로 대량 데이터 가져오기](sql-walkthrough.md#dbload)에 설명된 절차를 따릅니다. 이러한 지침은 Azure Virtual Machine의 SQL Server에 대한 내용이지만 온-프레미스 SQL Server로 업로드하는 절차는 동일합니다.

## <a name="file-to-azure-sql-database"></a>将数据从平面文件源移动到 Azure SQL 数据库
可以使用大容量插入 SQL 查询将平面文件（CSV 或 TSV 格式）中的数据移动到 Azure SQL 数据库。

### <a name="bulk-insert-sql-query"></a> 대량 삽입 SQL 쿼리
使用大容量插入 SQL 查询过程的步骤类似于将数据从平面文件源移动到 Azure VM SQL Server。 자세한 내용은 [대량 삽입 SQL 쿼리](move-sql-server-virtual-machine.md#insert-tables-bulkquery)를 참조하세요.

## <a name="sql-on-prem-to-sazure-sql-database"></a>将数据从本地 SQL Server 移到 Azure SQL 数据库
如果源数据存储在本地 SQL Server 中，则有多种可能将数据移动到 Azure SQL 数据库：

1. [플랫 파일로 내보내기](#export-flat-file)
2. [SQL Database 마이그레이션 마법사](#insert-tables-bcp)
3. [데이터베이스 백업 및 복원](#db-migration)
4. [Azure Data Factory](#adf)

前三个步骤的步骤类似于[将数据移到 Azure 虚拟机上 SQL Server](move-sql-server-virtual-machine.md)的那些部分涵盖了这些相同的过程。 이 항목의 해당 섹션에 대한 링크가 다음 절차에 제공됩니다.

### <a name="export-flat-file"></a>플랫 파일로 내보내기
导出到平面文件的步骤类似于[导出到平面文件](move-sql-server-virtual-machine.md#export-flat-file)中所述的步骤。

### <a name="insert-tables-bcp"></a>SQL Database 마이그레이션 마법사
使用 SQL 数据库迁移向导的步骤类似于[Sql 数据库迁移向导](move-sql-server-virtual-machine.md#sql-migration)中所述的说明。

### <a name="db-migration"></a>데이터베이스 백업 및 복원
使用数据库备份和还原的步骤类似于 "[数据库备份和还原](move-sql-server-virtual-machine.md#sql-backup)" 中列出的那些指导。

### <a name="adf"></a>Azure Data Factory
了解如何使用 Azure 数据工厂（ADF）将数据移至 Azure SQL 数据库。在本主题中，使用[Azure 数据工厂将数据从本地 SQL Server 移动到 SQL Azure](move-sql-azure-adf.md)。 本主题演示如何使用 ADF 通过 Azure Blob 存储将数据从本地 SQL Server 数据库移到 Azure SQL 数据库。

如果需要使用混合本地和云源继续迁移数据，请考虑使用 ADF。  ADF 还有助于在迁移过程中需要转换数据或需要新的业务逻辑。 ADF에서는 정기적으로 데이터 이동을 관리하는 간단한 JSON 스크립트를 사용하여 작업 예약 및 모니터링이 가능합니다. 또한 복잡한 작업을 지원하는 기타 기능도 포함하고 있습니다.
