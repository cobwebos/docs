---
title: "使用高级分析处理 Azure Blob 数据 | Microsoft Docs"
description: "处理 Azure blob 存储中的数据。"
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: d8a59078-91d3-4440-b85c-430363c3f4d1
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: garye;bradsev
ms.openlocfilehash: f3388728b2c2ea699a2caf764dc0fd3e9ff19505
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2018
---
# <a name="heading"></a>使用高级分析处理 Azure Blob 数据
本文档介绍了如何浏览数据，以及如何从 Azure Blob 存储中存储的数据生成功能。 

## <a name="load-the-data-into-a-pandas-data-frame"></a>将数据加载到 Pandas 数据帧
要浏览和操作数据集，必须将数据集从 blob 源下载到本地文件，该本地文件随后可加载到 Pandas 数据帧中。 下面是此过程的所需步骤：

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

## <a name="blob-dataexploration"></a>数据浏览
下方为如何使用 Pandas 浏览数据的几个示例：

1. 检查行数和列数 
   
        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
2. 检查数据集中的前面或后面几行，如下所示：
   
        dataframe_blobdata.head(10)
   
        dataframe_blobdata.tail(10)
3. 使用以下示例代码检查每列导入的数据类型
   
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
4. 检查数据集中列的基本统计信息，如下所示
   
        dataframe_blobdata.describe()
5. 如下所示，查看每列值的条目数
   
        dataframe_blobdata['<column_name>'].value_counts()
6. 使用以下示例代码计算每列中缺少的值与实际的条目数
   
        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
7. 如果数据中有特定列存在缺少的值，可按如下方法进行替代：
   
     dataframe_blobdata_noNA = dataframe_blobdata.dropna()   dataframe_blobdata_noNA.shape
   
   另一种替代缺失值的方法是使用模式函数：
   
     dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})        
8. 使用数量不定的量化创建直方图，以绘制变量的分布情况    
   
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
9. 使用散点图或内置的关联函数查看变量之间的关联
   
        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()

## <a name="blob-featuregen"></a>功能生成
可按如下所示使用 Python 生成功能：

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
3. 现在可使用 Azure 机器学习的[导入数据][import-data]模块从 blob 读取数据，如下方屏幕截图所示：

![blob 读取器][1]

[1]: ./media/data-blob/reader_blob.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

