## <a name="specifying-compression"></a>指定压缩
处理大型数据集可能导致 I/O 和网络瓶颈。 因此，存储中的压缩数据不但可以跨网络加速数据传输和节省磁盘空间，也能在处理大数据时带来显著的性能提升。 目前，Azure Blob 或本地文件系统等基于文件的数据存储支持压缩。  

> [!NOTE]
> 目前采用 **AvroFormat**、**OrcFormat** 或 **ParquetFormat** 的数据不支持压缩设置。 在读取采用这些格式的文件时，数据工厂将检测并使用元数据中的压缩编解码器；写入采用这些格式的文件时，数据工厂将选择适用于该格式的默认压缩编解码器。 例如，为 OrcFormat 使用 ZLIB，为 ParquetFormat 使用 SNAPPY。
>
>

若要为数据集指定压缩，请在数据集 JSON 中使用 **compression** 属性，如以下示例所示：   

```json
{  
    "name": "AzureBlobDataSet",  
    "properties": {  
        "availability": {  
            "frequency": "Day",  
              "interval": 1  
        },  
        "type": "AzureBlob",  
        "linkedServiceName": "StorageLinkedService",  
        "typeProperties": {  
            "fileName": "pagecounts.csv.gz",  
            "folderPath": "compression/file/",  
            "compression": {  
                "type": "GZip",  
                "level": "Optimal"  
            }  
        }  
    }  
}  
```
假设上述示例数据集作为复制活动的输出，复制活动通过使用最佳比率的 GZIP 编解码器压缩输出数据，然后将压缩的数据写入 Azure Blob 存储中名为 pagecounts.csv.gz 的文件。   

**compression** 节包含两个属性：  

* **Type：**压缩编解码器，可以是 **GZIP**、**Deflate**、**BZIP2** 或 **ZipDeflate**。  
* **Level：**压缩比，可以是 **Optimal** 或 **Fastest**。

  * **Fastest：**尽快完成压缩操作，不过，无法以最佳方式压缩生成的文件。
  * **Optimal：**以最佳方式完成压缩操作，不过，需要耗费更长的时间。

    有关详细信息，请参阅 [Compression Level](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx)（压缩级别）主题。

在输入数据集 JSON 中指定 `compression` 属性时，管道可以从源读取压缩的数据；在输出数据集 JSON 中指定属性时，复制活动可以将压缩的数据写入到目标。 下面是一些示例方案：

* 从 Azure Blob 读取 GZIP 压缩的数据，将其解压缩，然后将结果数据写入 Azure SQL 数据库。 在此情况下，可以使用值为 GZIP 的 `compression` `type` JSON 属性来定义输入 Azure Blob 数据集。
* 从来自本地文件系统的纯文本文件读取数据、使用 GZip 格式进行压缩并将压缩的数据写入到 Azure Blob。 在此情况下，可以使用值为 GZip 的 `compression` `type` JSON 属性来定义输出 Azure Blob 数据集。
* 从 FTP 服务器读取 .zip 文件，将它解压缩以获取文件内容，然后将这些文件加入 Azure Data Lake Store。 可以使用值为 ZipDeflate 的 `compression` `type` JSON 属性来定义输入 FTP 数据集。
* 从 Azure Blob 读取 GZIP 压缩的数据，将其解压缩、使用 BZIP2 将其压缩，然后将结果数据写入 Azure Blob。 在此情况下，可以使用设置为 GZIP 的 `compression` `type` 来定义输入 Azure Blob 数据集，使用设置为 BZIP2 的 `compression` `type` 来定义输出数据集。   
