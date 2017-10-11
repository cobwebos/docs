
### <a name="cacheskuname"></a>cacheSKUName
新的 Azure Redis 缓存定价层。

    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Redis Cache."
      }
    },

模板将定义 （基本或标准），此参数允许的值，如果未不指定任何值，则分配默认值 (Basic)。 Basic 提供单个节点具有多个大小最大为 53 GB。
标准提供双节点主/副本具有多个大小最大 53 GB 和 99.9 %sla。

### <a name="cacheskufamily"></a>cacheSKUFamily
Sku 系列。

    "cacheSKUFamily": {
      "type": "string",
      "allowedValues": [
        "C"
      ],
      "defaultValue": "C",
      "metadata": {
        "description": "The family for the sku."
      }
    },


### <a name="cacheskucapacity"></a>cacheSKUCapacity
新的 Azure Redis 缓存实例的大小。 

    "cacheSKUCapacity": {
      "type": "int",
      "allowedValues": [
        0,
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "defaultValue": 0,
      "metadata": {
        "description": "The size of the new Azure Redis Cache instance. "
      }
    }


模板将定义为 （0、 1、 2、 3、 4、 5 或 6），此参数允许的值，如果未不指定任何值，则分配默认值 (1)。 这些数字对应于以下缓存大小： 0 = 250 MB，1 = 1 GB，2 = 2.5 GB，3 = 6 GB，4 = 13 GB，5 = 26 GB，6 = 53 GB

