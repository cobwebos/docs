## <a name="azure-storage-linked-service"></a>Azure 存储链接服务
**Azure 存储链接服务**可让你使用**帐户密钥**将 Azure 存储帐户链接到 Azure 数据工厂。 这样，便可以将 Azure 存储的全局访问权限提供给数据工厂。 下表提供 Azure 存储链接服务专属 JSON 元素的描述。

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type |type 属性必须设置为：**AzureStorage** |是 |
| connectionString |为 connectionString 属性指定连接到 Azure 存储所需的信息。 |是 |

请参阅以下文章中的步骤来查看/复制 Azure 存储帐户密钥：[查看、复制和重新生成存储访问密钥](../articles/storage/storage-create-storage-account.md#manage-your-storage-account)。

**示例：**  

```json
{  
    "name": "StorageLinkedService",  
    "properties": {  
        "type": "AzureStorage",  
        "typeProperties": {  
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"  
        }  
    }  
}  
```

## <a name="azure-storage-sas-linked-service"></a>Azure 存储 SAS 链接服务
共享访问签名 (SAS) 用于对存储帐户中的资源进行委托访问。 这意味着你可以授权客户端在指定时间段内，以一组指定权限有限地访问你的存储帐户中的对象，而不必共享你的帐户访问密钥。 SAS 是在其查询参数中包含对存储资源进行验证了身份的访问所需的所有信息的 URI。 若要使用 SAS 访问存储资源，客户端只需将 SAS 传入到相应的构造函数或方法。 有关 SAS 的详细信息，请参阅[共享访问签名：了解 SAS 模型](../articles/storage/storage-dotnet-shared-access-signature-part-1.md)。

Azure 存储 SAS 链接服务可让你使用共享访问签名 (SAS) 将 Azure 存储帐户链接到 Azure 数据工厂。 这样，便可以将存储中所有/特定资源（Blob/容器）的受限/限时访问权限提供给数据工厂。 下表提供了 Azure 存储 SAS 链接服务特定的 JSON 元素的描述。 

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type |type 属性必须设置为：**AzureStorageSas** |是 |
| sasUri |指定 Azure 存储资源（例如 Blob、容器或表）的共享访问签名 URI。 有关详细信息，请参阅下面的说明。 |是 |

**示例：**

```json
{  
    "name": "StorageSasLinkedService",  
    "properties": {  
        "type": "AzureStorageSas",  
        "typeProperties": {  
            "sasUri": "<storageUri>?<sasToken>"   
        }  
    }  
}  
```

创建 **SAS URI** 时，请注意以下事项：  

* Azure 数据工厂仅支持**服务 SAS**，不支持帐户 SAS。 有关这两种类型的详细信息，请参阅[共享访问签名的类型](../articles/storage/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures)。
* 需要根据链接服务（读取、写入、读/写）在数据工厂中的用法，设置针对对象的适当读/写**权限**。
* 需要适当设置**过期时间**。 确保 Azure 存储对象的访问权限不会在管道的活动期限内过期。
* 应该根据需要在正确的容器/Blob 或表级别创建 URI。 数据工厂服务可以使用 Azure Blob 的 SAS URI 访问特定的 Blob。 数据工厂服务可以使用 Azure Blob 容器的 SAS URI 迭代该容器中的 Blob。 如果稍后需要提供更多/更少对象的访问权限或需要更新 SAS URI，请记得使用新 URI 更新链接服务。   

