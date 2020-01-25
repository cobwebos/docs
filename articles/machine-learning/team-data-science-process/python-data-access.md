---
title: Python 클라이언트 라이브러리를 사용하여 데이터 세트에 액세스 - Team Data Science Process
description: Python 클라이언트 라이브러리를 설치하고 사용하여 로컬 Python 환경에서 안전하게 Azure Machine Learning 데이터에 액세스하고 관리합니다.
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
ms.openlocfilehash: 93ec5e740ac6acf9420a9d980092ed772ac1618e
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720973"
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Azure Machine Learning Python 클라이언트 라이브러리를 사용하여 Python으로 데이터 세트에 액세스
Microsoft Azure Machine Learning Python 클라이언트 라이브러리 미리보기를 사용하면 로컬 Python 환경에서 Azure Machine Learning 데이터 세트로 안전하게 액세스할 수 있고, 작업 영역에 데이터 세트를 생성하여 관리할 수 있습니다.

이 항목에서는 다음 수행 방법에 대한 지침을 제공합니다.

* Machine Learning Python 클라이언트 라이브러리 설치
* 로컬 Python 환경에서 Azure Machine Learning 데이터 세트에 액세스하는 권한을 얻는 방법에 대한 지침을 비롯하여 데이터 세트에 액세스 및 업로드
* 실험에서 중간 데이터 세트에 액세스
* 使用 Python 客户端库枚举数据集、访问元数据、读取数据集的内容、创建新的数据集和更新现有数据集

## <a name="prerequisites"></a>필수 조건
Python 클라이언트 라이브러리는 다음과 같은 환경에서 테스트되었습니다.

* Windows、Mac 和 Linux
* Python 2.7, 3.3 및 3.4

다음 패키지에 대한 종속성이 있습니다.

* requests
* python-dateutil
* pandas

Python, IPython 및 설치된 것으로 위에 나열된 세 가지 패키지와 함께 제공되는 [Anaconda](http://continuum.io/downloads#all) 또는 [Canopy](https://store.enthought.com/downloads/) 등의 Python 배포판을 사용하는 것이 좋습니다. IPython은 반드시 필요하지는 않지만 데이터를 대화식으로 조작하고 시각화는 데 훌륭한 환경입니다.

### <a name="installation"></a>Azure 기계 학습 Python 클라이언트 라이브러리를 설치하는 방법
安装 Azure 机器学习 Python 客户端库来完成本主题中所述的任务。 此库可从[Python 包索引](https://pypi.python.org/pypi/azureml)中获取。 Python 환경에 설치하려면 로컬 Python 환경에서 다음 명령을 실행합니다.

    pip install azureml

또는 [GitHub](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python)의 원본에서 다운로드하고 설치할 수 있습니다.

    python setup.py install

컴퓨터에 git가 설치된 경우 pip를 사용하여 git 리포지토리에서 직접 설치할 수 있습니다.

    pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git


## <a name="datasetAccess"></a>使用代码段访问数据集
Python 클라이언트 라이브러리를 사용하면 실행된 기존 데이터 세트에 프로그래밍 방식으로 액세스할 수 있습니다.

在 Azure 机器学习 Studio （经典） web 界面中，你可以生成代码段，其中包含在本地计算机上下载数据集并将其反序列化为 pandas 数据帧对象所需的所有必要信息。

### <a name="security"></a>데이터 액세스를 위한 보안
Azure 机器学习 Studio （经典）提供的用于 Python 客户端库的代码段包括你的工作区 ID 和授权令牌。 이러한 코드 조각은 작업 영역에 대한 전체 액세스 권한을 제공하고 암호와 같이 보호되어야 합니다.

보안상의 이유로 코드 조각은 이전에 역할이 작업 영역의 **소유자** 로 설정된 사용자만 사용할 수 있습니다. 你的角色会显示在 "**用户**" 页上的 "**设置**" 中 Azure 机器学习 Studio （经典） "。

![보안][security]

역할이 **소유자**로 설정되지 않은 경우 소유자로 다시 초대되도록 요청하거나 작업 영역의 소유자에게 코드 조각을 제공하도록 요청할 수 있습니다.

若要获取授权令牌，可以选择以下选项之一：

* 소유자에서 토큰을 요청합니다. 所有者可以在 Azure 机器学习 Studio （经典）中从其工作区的 "设置" 页访问其授权令牌。 왼쪽 창에서 **설정**을 선택하고 **권한 부여 토큰**을 클릭하여 기본 및 보조 토큰을 확인할 수 있습니다. 기본 또는 보조 권한 부여 토큰을 코드 조각에서 사용할 수 있지만, 소유자가 보조 권한 부여 토크만 공유하는 것이 좋습니다.

   ![권한 부여 토큰](./media/python-data-access/ml-python-access-settings-tokens.png)

* 要求升级为所有者的角色：工作区的当前所有者需要先将你从工作区中删除，然后将你 reinvite 为所有者。

开发人员获取工作区 ID 和授权令牌后，就可以使用代码片段访问工作区，而不考虑其角色。

권한 부여 토큰은 **설정**의 **권한 부여 토큰** 페이지에서 관리합니다. 토큰을 다시 생성할 수 있지만 이 절차를 수행하면 이전 토큰에 대한 액세스 권한이 취소됩니다.

### <a name="accessingDatasets"></a>로컬 Python 애플리케이션에서 데이터 세트에 액세스
1. 在机器学习 Studio （经典）中，单击左侧导航栏中的 "**数据集**"。
2. 액세스하려는 데이터 세트를 선택합니다. **내 데이터 세트** 목록 또는 **샘플** 목록에서 데이터 세트를 선택할 수 있습니다.
3. 아래 쪽 도구 모음에서 **데이터 액세스 코드 생성**을 클릭합니다. 데이터가 Python 클라이언트 라이브러리와 호환되지 않는 형식이면 이 단추는 비활성화됩니다.
   
    ![데이터 세트][datasets]
4. 표시되는 창에서 코드 조각을 선택하여 클립보드에 복사합니다.
   
    ![데이터 액세스 코드 생성 단추][dataset-access-code]
5. 코드를 로컬 Python 애플리케이션의 Notebook에 붙여넣습니다.
   
    ![Notebook에 코드 붙여넣기][ipython-dataset]

## <a name="accessingIntermediateDatasets"></a>기계 학습 실험에서 중간 데이터 세트에 액세스
在机器学习 Studio （经典）中运行试验后，可以从模块的输出节点访问中间数据集。 중간 데이터 세트는 모델 도구가 실행될 때 중간 단계를 위해 생성되고 사용된 데이터입니다.

데이터 형식이 Python 클라이언트 라이브러리와 호환되는 한 중간 데이터 세트에 액세스할 수 있습니다.

支持以下格式（这些格式的常量位于 `azureml.DataTypeIds` 类中）：

* 일반 텍스트
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

모듈 출력 노드 위에 포인터를 두면 형식을 확인할 수 있습니다. 도구 설명에 노드 이름과 함께 표시됩니다.

某些模块（如[Split][split]模块）输出为名为 `Dataset`的格式，而 Python 客户端库不支持此格式。

![데이터 세트 형식][dataset-format]

需要使用转换模块，如[转换为 CSV][convert-to-csv]，以将输出获取为支持的格式。

![GenericCSV 형식][csv-format]

다음 단계에서는 실험을 만들고 실행하며 중간 데이터 세트에 액세스하는 예를 보여줍니다.

1. 새로운 실험 만들기
2. **성인 인구 조사 소득 이진 분류 데이터 세트** 모듈을 삽입합니다.
3. 插入[拆分][split]模块，并将其输入连接到数据集模块输出。
4. 插入 "[转换为 CSV][convert-to-csv] " 模块，并将其输入连接到[拆分][split]模块输出之一。
5. 保存试验，运行它并等待作业完成。
6. 单击 "[转换为 CSV][convert-to-csv] " 模块上的 "输出" 节点。
7. 상황에 맞는 메뉴가 표시되면 **데이터 액세스 코드 생성**을 선택합니다.
   
    ![상황에 맞는 메뉴][experiment]
8. 표시되는 창에서 코드 조각을 선택하여 클립보드에 복사합니다.
   
    ![바로 가기 메뉴에서 액세스 코드 생성][intermediate-dataset-access-code]
9. 노트북에 코드를 붙여넣습니다.
   
    ![Notebook에 코드 붙여넣기][ipython-intermediate-dataset]
10. Matplotlib를 사용하여 데이터를 시각화할 수 있습니다. 그러면 나이 열에 대한 히스토그램에 표시됩니다.
    
    ![히스토그램][ipython-histogram]

## <a name="clientApis"></a>기계 학습 Python 클라이언트 라이브러리를 사용하여 데이터 세트에 액세스, 읽기, 만들기 및 관리
### <a name="workspace"></a>작업 영역
작업 영역은 Python 클라이언트 라이브러리의 진입점입니다. 向 `Workspace` 类提供工作区 ID 和授权令牌来创建实例：

    ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
                   authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')


### <a name="enumerate-datasets"></a>데이터 세트 열거
지정된 작업 영역에 모든 데이터 세트 열거:

    for ds in ws.datasets:
        print(ds.name)

사용자 생성 데이터 세트만 열거:

    for ds in ws.user_datasets:
        print(ds.name)

예제 데이터 세트만 열거:

    for ds in ws.example_datasets:
        print(ds.name)

데이터 세트 이름(대/소문자 구분)으로 액세스할 수 있습니다.

    ds = ws.datasets['my dataset name']

또는 인덱스별로 액세스할 수 있습니다.

    ds = ws.datasets[0]


### <a name="metadata"></a>메타데이터
데이터 세트에는 콘텐츠 외에도 메타데이터가 있습니다. (중간 데이터 세트는 이 규칙의 예외로서, 메타데이터가 없습니다.)

일부 메타 데이터 값은 사용자가 생성 시 할당:

    print(ds.name)
    print(ds.description)
    print(ds.family_id)
    print(ds.data_type_id)

다른 메타 데이터 값은 Azure 기계 학습에서 할당:

    print(ds.id)
    print(ds.created_date)
    print(ds.size)

사용 가능한 메타 데이터에 대한 자세한 내용은 `SourceDataset` 클래스를 참조하세요.

### <a name="read-contents"></a>콘텐츠 읽기
机器学习 Studio （经典）提供的代码段会自动下载数据集并将其反序列化为 pandas 数据帧对象。 이 작업은 `to_dataframe` 메서드를 통해 수행됩니다.

    frame = ds.to_dataframe()

원시 데이터를 다운로드하고 직접 역직렬화를 수행하려는 경우 옵션으로 선택할 수 있습니다. 현재로서는 Python 클라이언트 라이브러리에서 역직렬화할 수 없는 ‘ARFF’와 같은 형식의 경우에만 선택 가능합니다.

텍스트로 콘텐츠 읽기:

    text_data = ds.read_as_text()

이진을 콘텐츠 읽기:

    binary_data = ds.read_as_binary()

콘텐츠에 스트림만 열 수도 있음:

    with ds.open() as file:
        binary_data_chunk = file.read(1000)


### <a name="create-a-new-dataset"></a>새 데이터 세트 만들기
Python 클라이언트 라이브러리를 사용하면 Python 프로그램에서 데이터 세트를 업로드할 수 있습니다. 이러한 데이터 세트는 작업 영역에서 사용할 수 있습니다.

Pandas DataFrame에 데이터가 있는 경우 다음 코드를 사용합니다.

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_dataframe(
        dataframe=frame,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

데이터가 이미 직렬화된 경우 다음을 사용할 수 있습니다.

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_raw_data(
        raw_data=raw_data,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Python 클라이언트 라이브러리에서 Pandas DataFrame을 다음 형식으로 직렬화할 수 있습니다(해당 상수는 `azureml.DataTypeIds` 클래스에 있음).

* 일반 텍스트
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

### <a name="update-an-existing-dataset"></a>기존 데이터 세트 업데이트
기존 데이터 세트와 일치하는 이름으로 새 데이터 세트를 업로드하려고 하면 충돌 오류가 발생합니다.

기존 데이터 세트를 업데이트하려면 먼저 기존 데이터 세트에 대한 참조를 얻어야 합니다.

    dataset = ws.datasets['existing dataset']

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

그러면 `update_from_dataframe` 을 사용하여 Azure에서 데이터 세트의 콘텐츠를 직렬화하고 바꿀 수 있습니다.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(frame2)

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

데이터를 다른 형식으로 직렬화하려면 선택적 `data_type_id` 매개 변수의 값을 지정합니다.

    from azureml import DataTypeIds

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        data_type_id=DataTypeIds.GenericTSV,
    )

    print(dataset.data_type_id) # 'GenericTSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

`description` 매개 변수의 값을 지정하여 선택적으로 새 설명을 설정할 수 있습니다.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        description='data up to feb 2015',
    )

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to feb 2015'

`name` 매개 변수의 값을 지정하여 선택적으로 새로운 이름을 설정할 수 있습니다. 지금부터 새 이름만 사용하여 데이터 세트를 검색합니다. 以下代码将更新数据、名称和说明。

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        name='existing dataset v2',
        description='data up to feb 2015',
    )

    print(dataset.data_type_id)                    # 'GenericCSV'
    print(dataset.name)                            # 'existing dataset v2'
    print(dataset.description)                     # 'data up to feb 2015'

    print(ws.datasets['existing dataset v2'].name) # 'existing dataset v2'
    print(ws.datasets['existing dataset'].name)    # IndexError

`data_type_id`, `name` 및 `description` 매개 변수는 선택 사항이고 기본적으로 이전 값이 됩니다. `dataframe` 매개 변수는 항상 필요합니다.

데이터가 이미 직렬화된 경우 `update_from_dataframe` 대신 `update_from_raw_data`를 사용합니다. `dataframe` 대신 `raw_data`만 전달하면, 비슷하게 작동합니다.

<!-- Images -->
[security]:./media/python-data-access/security.png
[dataset-format]:./media/python-data-access/dataset-format.png
[csv-format]:./media/python-data-access/csv-format.png
[datasets]:./media/python-data-access/datasets.png
[dataset-access-code]:./media/python-data-access/dataset-access-code.png
[ipython-dataset]:./media/python-data-access/ipython-dataset.png
[experiment]:./media/python-data-access/experiment.png
[intermediate-dataset-access-code]:./media/python-data-access/intermediate-dataset-access-code.png
[ipython-intermediate-dataset]:./media/python-data-access/ipython-intermediate-dataset.png
[ipython-histogram]:./media/python-data-access/ipython-histogram.png


<!-- Module References -->
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

