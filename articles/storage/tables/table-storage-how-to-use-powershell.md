---
title: PowerShell을 사용하여 Azure Table Storage 작업 수행 | Microsoft Docs
description: 了解如何使用 PowerShell 从 Azure 表存储帐户运行创建、查询和删除数据等常见任务。
author: roygara
ms.service: storage
ms.topic: article
ms.date: 04/05/2019
ms.author: rogarana
ms.subservice: tables
ms.openlocfilehash: f1846fae4cbf473df688a2b184c307d72ab2f8d0
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721466"
---
# <a name="perform-azure-table-storage-operations-with-azure-powershell"></a>Azure PowerShell을 사용하여 Azure Table Storage 작업 수행 
[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

Azure Table Storage는 매우 큰 비관계형 구조적 데이터 집합을 저장하고 쿼리하는 데 사용할 수 있는 NoSQL 데이터 저장소입니다. 서비스의 주요 구성 요소로는 테이블, 엔터티 및 속성이 있습니다. 테이블은 엔터티 컬렉션입니다. 엔터티는 속성의 집합입니다. 각 엔터티는 모두 이름 값 쌍으로 구성된 속성을 최대 252개 가질 수 있습니다. 이 문서에서는 Azure Table Storage 서비스 개념에 이미 익숙하다고 가정합니다. 자세한 내용은 [Table Service 데이터 모델 이해](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) 및 [.NET을 사용하여 Azure Table Storage 시작](../../cosmos-db/table-storage-how-to-use-dotnet.md)을 참조하세요.

이 방법 문서에서는 일반 Azure Table Storage 작업에 대해 설명합니다. 다음 방법을 알아봅니다. 

> [!div class="checklist"]
> * 테이블 만들기
> * 테이블 검색
> * 테이블 엔터티 추가
> * 테이블 쿼리
> * 테이블 엔터티 삭제
> * 테이블 삭제

이 아티클에서는 새 리소스 그룹에 새 Azure Storage 계정을 만드는 방법을 보여주며, 이 경우에 작업을 완료할 때 쉽게 제거할 수 있습니다. 기존 Storage 계정을 사용하려는 경우 해당 스토리지 계정을 대신 사용할 수 있습니다.

这些示例要求 Az PowerShell 模块 `Az.Storage (1.1.0 or greater)` 和 `Az.Resources (1.2.0 or greater)`。 PowerShell 창에서 `Get-Module -ListAvailable Az*`을 실행하여 버전을 확인합니다. 표시되는 항목이 없거나 업그레이드가 필요한 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요.

> [!IMPORTANT]
> PowerShell에서 이 Azure 기능을 사용하려면 `Az` 모듈이 설치되어 있어야 합니다. `AzTable` 的当前版本与较旧的 AzureRM 模块不兼容。
> 如果需要，请遵循[安装 Az module 的最新安装说明](/powershell/azure/install-az-ps)。

安装或更新 Azure PowerShell 之后，必须安装 module **AzTable**，其中包含用于管理实体的命令。 이 모듈을 설치하려면 PowerShell을 관리자 권한으로 실행하고 **Install-Module** 명령을 사용합니다.

> [!IMPORTANT]
> 出于模块名称兼容性原因，我们仍将在 PowerShell 库中的旧名称下发布此相同模块 `AzureRmStorageTables`。 本文档将仅引用新名称。

```powershell
Install-Module AzTable
```

## <a name="sign-in-to-azure"></a>Azure에 로그인

`Add-AzAccount` 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

```powershell
Add-AzAccount
```

## <a name="retrieve-list-of-locations"></a>위치의 목록 검색

사용하려는 위치를 모르는 경우 사용 가능한 위치를 나열할 수 있습니다. 목록이 표시되면 사용할 위치를 찾습니다. 이러한 예제에서는 **eastus**를 사용합니다. 이 값은 나중에 사용하기 위해 변수 **location**에 저장합니다.

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-resource-group"></a>리소스 그룹 만들기

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 명령을 사용하여 리소스 그룹을 만듭니다. 

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 리소스 그룹 이름을 나중에 사용할 수 있도록 변수에 저장합니다. 이 예제에서는 *eastus* 지역에 *pshtablesrg*라는 리소스 그룹을 만듭니다.

```powershell
$resourceGroup = "pshtablesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>스토리지 계정 만들기

[New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount)를 사용하여 LRS(로컬 중복 스토리지)에 표준 범용 스토리지 계정을 만듭니다. 请确保指定唯一的存储帐户名称。 接下来，获取表示存储帐户的上下文。 在存储帐户上操作时，可以引用上下文，而不是重复提供凭据。

```powershell
$storageAccountName = "pshtablestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
```

## <a name="create-a-new-table"></a>새 테이블 만들기

若要创建表，请使用[AzStorageTable](/powershell/module/az.storage/New-AzStorageTable) cmdlet。 이 예제에서는 테이블을 `pshtesttable`이라고 합니다.

```powershell
$tableName = "pshtesttable"
New-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="retrieve-a-list-of-tables-in-the-storage-account"></a>스토리지 계정의 테이블 목록 검색

使用[AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable)在存储帐户中检索表的列表。

```powershell
Get-AzStorageTable –Context $ctx | select Name
```

## <a name="retrieve-a-reference-to-a-specific-table"></a>특정 테이블에 대한 참조 가져오기

테이블에 대한 작업을 수행하려면 특정 테이블에 대한 참조가 필요합니다. 使用[AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable)获取引用。

```powershell
$storageTable = Get-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="reference-cloudtable-property-of-a-specific-table"></a>引用特定表的 CloudTable 属性

> [!IMPORTANT]
> 使用**AzTable** PowerShell 模块时，CloudTable 的使用是必需的。 调用**AzTableTable**命令获取对此对象的引用。 此命令还会创建表（如果该表尚不存在）。

若要使用**AzTable**对表执行操作，需要引用特定表的 CloudTable 属性。

```powershell
$cloudTable = (Get-AzStorageTable –Name $tableName –Context $ctx).CloudTable
```

[!INCLUDE [storage-table-entities-powershell-include](../../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>테이블 삭제

若要删除表，请使用[AzStorageTable](/powershell/module/az.storage/Remove-AzStorageTable)。 이 cmdlet은 테이블 및 포함된 모든 데이터를 제거합니다.

```powershell
Remove-AzStorageTable –Name $tableName –Context $ctx

# Retrieve the list of tables to verify the table has been removed.
Get-AzStorageTable –Context $Ctx | select Name
```

## <a name="clean-up-resources"></a>리소스 정리

이 방법 문서를 시작하면서 새 리소스 그룹 및 스토리지 계정을 만들었으면, 해당 리소스 그룹을 제거하여 이 연습에서 만든 자산을 모두 제거할 수 있습니다. 이 명령은 리소스 그룹 자체뿐만 아니라 해당 그룹에 포함된 모든 리소스를 삭제합니다.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>다음 단계

이 방법 문서에서는 다음 방법을 포함하여 PowerShell을 사용한 일반적인 Azure Table Storage 작업에 대해 알아보았습니다. 

> [!div class="checklist"]
> * 테이블 만들기
> * 테이블 검색
> * 테이블 엔터티 추가
> * 테이블 쿼리
> * 테이블 엔터티 삭제
> * 테이블 삭제

자세한 내용은 다음 문서를 참조하세요.

* [스토리지 PowerShell cmdlet](/powershell/module/az.storage#storage)

* [从 PowerShell 使用 Azure 表-AzureRmStorageTable/AzTable PS Module v2。0](https://paulomarquesc.github.io/working-with-azure-storage-tables-from-powershell)

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md)는 Windows, macOS 및 Linux에서 Azure Storage 데이터로 시각적으로 작업할 수 있도록 해주는 Microsoft의 독립 실행형 무료 앱입니다.
