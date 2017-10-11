| 资源 | 默认限制 | 最大限制 |
| --- | --- | --- |
| 每个资源[资源组](../articles/azure-resource-manager/resource-group-overview.md#resource-groups)（按资源类型） |800 |每种资源类型而异 |
| 每个资源组的部署 |800 |800 |
| 每个部署的资源 |800 |800 |
| （每个唯一的作用域） 的管理锁 |20 |20 |
| （每个资源或资源组） 的标记数 |15 |15 |
| 标记密钥长度 |512 |512 |
| 标记值长度 |256 |256 |


#### <a name="template-limits"></a>模板限制

| 值 | 默认限制 | 最大限制 |
| --- | --- | --- |
| 参数 |256 |256 |
| 变量 |256 |256 |
| 资源 （包括复制计数） |800 |800 |
| 输出 |64 |64 |
| 模板表达式 |24576 个字符 |24576 个字符 |
| 导出的模板中的资源 |200 |200 | 
| 模板的大小 |1 MB |1 MB |
| 参数文件大小 |64 KB |64 KB |

你可以通过使用嵌套的模板超出某些模板限制。 有关详细信息，请参阅[部署 Azure 资源时使用链接的模板](../articles/azure-resource-manager/resource-group-linked-templates.md)。 若要减少的参数、 变量或输出数，可以将多个值合并到一个对象。 有关详细信息，请参阅[对象作为参数](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md)。