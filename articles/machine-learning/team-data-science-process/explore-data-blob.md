---
title: pandas를 사용하여 Azure Blob Storage에서 데이터 탐색 - Team Data Science Process
description: pandas Python 패키지를 사용하여 Azure Blob 컨테이너에 저장된 데이터를 탐색하는 방법
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
ms.openlocfilehash: 29011760a94a05020150ceddeba4303b87c2f610
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722180"
---
# <a name="explore-data-in-azure-blob-storage-with-pandas"></a>pandas를 사용하여 Azure Blob Storage의 데이터 탐색

이 문서는 [pandas](https://pandas.pydata.org/) Python 패키지를 사용하여 Azure Blob 컨테이너에 저장된 데이터를 탐색하는 방법에 대해 설명합니다.

이 작업은 [팀 데이터 과학 프로세스](overview.md)의 단계입니다.

## <a name="prerequisites"></a>필수 조건
이 문서에서는 사용자가 다음 작업을 수행한 것으로 가정합니다.

* Azure Storage 계정을 만들었습니다. 지침이 필요한 경우 [Azure Storage 계정 만들기](../../storage/common/storage-account-create.md)
* Azure Blob Storage 계정에 데이터를 저장합니다. 지침이 필요한 경우 [Azure Storage에서 데이터 이동](../../storage/common/storage-moving-data.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>pandas DataFrame에 데이터 로드
데이터 세트를 탐색 및 조작하려면 먼저 Blob 원본에서 로컬 파일로 다운로드한 다음, pandas DataFrame에 로드해야 합니다. 이 절차를 수행하는 단계는 다음과 같습니다.

1. 通过以下使用 Blob 服务的 Python 代码示例，从 Azure blob 下载数据。 다음 코드의 변수를 사용자가 원하는 값으로 대체합니다.

```python
from azure.storage.blob import BlockBlobService
import tables

STORAGEACCOUNTNAME= <storage_account_name>
STORAGEACCOUNTKEY= <storage_account_key>
LOCALFILENAME= <local_file_name>
CONTAINERNAME= <container_name>
BLOBNAME= <blob_name>

#download from blob
t1=time.time()
blob_service=BlockBlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
t2=time.time()
print(("It takes %s seconds to download "+blobname) % (t2 - t1))
```

1. 다운로드한 파일에서 pandas DataFrame으로 데이터를 읽습니다.

```python
# LOCALFILE is the file path
dataframe_blobdata = pd.read_csv(LOCALFILE)
```

이제 데이터를 탐색하고 이 데이터 세트에 기능을 생성할 준비가 완료되었습니다.

## <a name="blob-dataexploration"></a>pandas를 사용 하 여 데이터 탐색의 예
다음은 pandas를 사용하여 데이터를 탐색하는 방식의 예입니다.

1. **행** 및 열 수

```python
print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
```

1. 다음 데이터 세트에서 첫 번째 또는 마지막 몇 **행**을 **검사**합니다.

```python
dataframe_blobdata.head(10)

dataframe_blobdata.tail(10)
```

1. 다음 샘플 코드를 사용하여 각 열을 가져온 **데이터 유형** 을 확인합니다.

```python
for col in dataframe_blobdata.columns:
    print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
```

1. 다음과 같이 데이터 집합의 열에 대한 **기본 통계** 를 확인합니다.

```python
dataframe_blobdata.describe()
```

1. 다음과 같이 각 열 값에 대한 항목 수를 확인합니다.

```python
dataframe_blobdata['<column_name>'].value_counts()
```

1. **누락된 값을 계산** 합니다.

```python
miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
print miss_num
```

1. 데이터의 특정 열에 대해 **누락된 값** 이 있는 경우 다음과 같이 해당 데이터를 삭제할 수 있습니다.

```python
dataframe_blobdata_noNA = dataframe_blobdata.dropna()
dataframe_blobdata_noNA.shape
```

누락된 값을 대체하는 또 다른 방법으로 mode 함수가 있습니다.

```python
dataframe_blobdata_mode = dataframe_blobdata.fillna(
    {'<column_name>': dataframe_blobdata['<column_name>'].mode()[0]})
```

1. 가변 bin을 사용하여 **히스토그램** 플롯을 만들고 변수 분포 그리기

```python
dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')

np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
```

1. 산점도 또는 기본 제공 상관관계 함수를 사용하여 변수 간의 **상관관계** 를 살펴봅니다.

```python
# relationship between column_a and column_b using scatter plot
plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])

# correlation between column_a and column_b
dataframe_blobdata[['<column_a>', '<column_b>']].corr()
```
