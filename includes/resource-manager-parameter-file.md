如果你使用参数文件在部署期间将参数值，你需要使用一种格式类似于下面的示例创建一个 JSON 文件：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "webSiteName": {
            "value": "ExampleSite"
        },
        "webSiteHostingPlanName": {
            "value": "DefaultPlan"
        },
        "webSiteLocation": {
            "value": "West US"
        },
        "adminPassword": {
            "reference": {
               "keyVault": {
                  "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
               }, 
               "secretName": "sqlAdminPassword" 
            }   
        }
   }
}
```

参数文件的大小不能超过 64 KB。

如果需要 （如密码） 的参数提供敏感值，将该值添加到密钥保管库。 检索密钥保管库在部署过程中，在前面的示例所示。 有关详细信息，请参阅[在部署期间将安全值](../articles/azure-resource-manager/resource-manager-keyvault-parameter.md)。 

