---
title: "使用 Panda 创建 Azure blob 存储数据功能 | Microsoft Docs"
description: "如何使用 Panda Python 包为存储在 Azure blob 容器中的数据创建功能。"
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 676b5fb0-4c89-4516-b3a8-e78ae3ca078d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev;garye
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 2ef2acfea2372ac7fd52d099a2b4203ee2242d81
ms.contentlocale: zh-cn
ms.lasthandoff: 08/22/2017

---
# <a name="create-features-for-azure-blob-storage-data-using-panda"></a>使用 Panda 创建 Azure blob 存储数据功能
此文档展示如何使用 [Pandas](http://pandas.pydata.org/) Python 包为存储在 Azure blob 容器中的数据创建功能。 在概括了如何加载数据到 Panda 数据帧后，文档介绍如何通过指示器值和装箱功能使用 Python 脚本生成分类功能。

[!INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]

该**菜单**链接到介绍如何在各种环境中为数据创建特征的主题。 此任务是[团队数据科学过程 (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) 中的一个步骤。

## <a name="prerequisites"></a>先决条件
本文假定已创建 Azure blob 存储帐户，且已存入数据。 如果需要帐户设置说明，请参阅[创建 Azure 存储帐户](../storage/common/storage-create-storage-account.md#create-a-storage-account)

## <a name="load-the-data-into-a-pandas-data-frame"></a>将数据加载到 Pandas 数据帧
要浏览和操作数据集，必须从 blob 源下载数据集到本地文件，然后将数据集加载到 Pandas 数据帧。 下面是此过程的所需步骤：

1. 通过 blob 服务使用下方示例 Python 代码从 Azure blob 下载数据。 使用特定值替代下方代码中的变量：
   
        from azure.storage.blob import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        LOCALFILENAME= <local_file_name>        
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        #download from blob
        t1=time.time()
        blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
        blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
        t2=time.time()
        print(("It takes %s seconds to download "+blobname) % (t2 - t1))
2. 从下载的文件中将数据读入 Pandas 数据帧。
   
        #LOCALFILE is the file path
        dataframe_blobdata = pd.read_csv(LOCALFILE)

现在可以准备浏览数据并在此数据集上生成功能了。

## <a name="blob-featuregen"></a>功能生成
接下来的两部分介绍如何通过指示器值和装箱功能使用 Python 脚本生成分类功能。

### <a name="blob-countfeature"></a>基于指示器值生成功能
可以按如下方式创建分类功能：

1. 检查分类列的分布：
   
        dataframe_blobdata['<categorical_column>'].value_counts()
2. 为每个列值生成指示器值
   
        #generate the indicator column
        dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')
3. 联接指示器列与原始数据帧
   
            #Join the dummy variables back to the original data frame
            dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)
4. 删除原始变量本身：
   
        #Remove the original column rate_code in df1_with_dummy
        dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)

### <a name="blob-binningfeature"></a>生成装箱功能
要生成装箱功能，请按如下所示操作：

1. 添加一系列的列，量化数字列
   
        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
2. 将装箱转换为一系列的布尔变量
   
        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
3. 最后，将虚拟变量联接回原始数据帧
   
        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)

## <a name="sql-featuregen"></a>将数据写回 Azure blob 并在 Azure 机器学习中使用
浏览过数据并创建必要功能后，可将数据（示例或定义数据）上传至 Azure blob 并在 Azure 机器学习中使用数据，操作步骤如下：请注意，也可在 Azure 机器学习工作室中创建其他功能。

1. 将数据帧写入本地文件
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. 将数据上传到 Azure blob，操作如下：
   
        from azure.storage.blob import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        LOCALFILENAME= <local_file_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
        localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
   
        try:
   
        #perform upload
        output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
   
        except:            
            print ("Something went wrong with uploading blob:"+BLOBNAME)
3. 现在可使用 Azure 机器学习的[导入数据](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/)模块从 blob 读取数据，如下方屏幕截图所示：

![blob 读取器](./media/machine-learning-data-science-process-data-blob/reader_blob.png)


