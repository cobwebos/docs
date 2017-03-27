---
title: "对 Azure Blob 存储中的数据采样 | Microsoft Docs"
description: "对 Azure Blob 存储中的数据采样"
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: e8d9ad2c-86c5-43d6-80b8-d355b5c0dccf
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: fashah;garye;bradsev
translationtype: Human Translation
ms.sourcegitcommit: a6bc79b2cb5b73109cddd6cf57caeba754b52e2e
ms.openlocfilehash: 7873ad084c9d77eea556ae3fec846bebd4c99ebd


---
# <a name="a-nameheadingasample-data-in-azure-blob-storage"></a><a name="heading"></a>对 Azure blob 存储中的数据采样
本文档介绍对在 Azure blob 存储中数据进行取样的方法：通过以编程方式下载，然后使用以 Python 编写的过程对其进行采样。

以下**菜单**所链接到的主题将描述如何从不同的存储环境采样数据。 

[!INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

**为什么对数据进行采样？**
如果计划要分析的数据集很大，通常最好是对数据进行向下采样，以将数据减至较小但具备代表性且更易于管理的规模。 这有利于数据了解、探索和功能设计。 它在 Cortana Analytics 进程中的作用是能够快速建立数据处理函数和机器学习模型的快速原型。

此采样任务是[团队数据科学流程 (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) 中的一个步骤。

## <a name="download-and-down-sample-data"></a>下载和向下采样数据
1. 从下列 Python 代码示例中，通过 blob 服务，从 Azure blob 存储下载数据： 
   
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

2. 从以上下载的文件中将数据读入 Pandas 数据帧。
   
        import pandas as pd
   
        #directly ready from file on disk
        dataframe_blobdata = pd.read_csv(LOCALFILE)

3. 使用 `numpy` 的 `random.choice` 向下采样数据，如下所示：
   
        # A 1 percent sample
        sample_ratio = 0.01 
        sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
        sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
        dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]

现在可以使用上述具有 1% 样本的数据框架，执行进一步的研究和功能生成。

## <a name="a-nameheadingaupload-data-and-read-it-into-azure-machine-learning"></a><a name="heading"></a>上载数据并读入 Azure 机器学习
你可以使用以下示例代码向下采样数据，并直接在 Azure 机器学习中使用：

1. 将数据帧写入本地文件
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. 使用以下示例代码将本地文件上载到 Azure blob：
   
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
            print ("Something went wrong with uploading to the blob:"+ BLOBNAME)

3. 使用 Azure 机器学习[导入数据](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/)从 Azure blob 中读取数据，如下图中所示：

![blob 读取器](./media/machine-learning-data-science-sample-data-blob/reader_blob.png)




<!--HONumber=Dec16_HO3-->


