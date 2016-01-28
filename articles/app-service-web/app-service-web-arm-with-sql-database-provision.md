<properties 
	pageTitle="预配使用 SQL 数据库的网站" 
	description="使用 Azure 资源管理器模板来部署包含 SQL 数据库的网站。" 
	services="app-service" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="jimbe"/>

<tags
	ms.service="app-service"
	ms.date="12/16/2015"
	wacn.date=""/>

# 预配包含 SQL 数据库的网站

在本主题中，你将学习如何创建用于部署网站和 SQL 数据库的 Azure 资源管理器模板。你将了解如何定义要部署的资源以及如何定义执行部署时指定的参数。可将此模板用于自己的部署，或自定义此模板以满足要求。

有关创建模板的详细信息，请参阅[创作 Azure 资源管理器模板](/documentation/articles/resource-group-authoring-templates)。

有关完整的模板，请参阅[包含 SQL 数据库的网站模板](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-sql-database/azuredeploy.json)。

## 将部署的内容

在此模板中，你将部署：

- 一个网站
- SQL 数据库服务器
- SQL 数据库
- 自动缩放设置
- 警报规则
- App Insights

## 要指定的参数

[AZURE.INCLUDE [app-service-web-deploy-web-parameters](../includes/app-service-web-deploy-web-parameters.md)]

### serverName

要创建的新数据库服务器的名称。

    "serverName": {
      "type": "string"
    }

### serverLocation

数据库服务器的位置。为了获得最佳性能，此位置应与网站的位置相同。

    "serverLocation": {
      "type": "string"
    }

### administratorLogin

要用于数据库服务器管理员的帐户名称。

    "administratorLogin": {
      "type": "string"
    }

### administratorLoginPassword

要用于数据库服务器管理员的密码。

    "administratorLoginPassword": {
      "type": "securestring"
    }

### databaseName

要创建的新数据库的名称。

    "databaseName": {
      "type": "string"
    }

### collation

要用于管理字符的正确使用的数据库排序规则。

    "collation": {
      "type": "string",
      "defaultValue": "SQL_Latin1_General_CP1_CI_AS"
    }

### edition

要创建的数据库类型。

    "edition": {
      "type": "string",
      "defaultValue": "Standard",
      "metadata": {
        "description": "The type of database to create. The available options are: Web, Business, Basic, Standard, and Premium."
      }
    }

### maxSizeBytes

数据库的最大大小（以字节为单位）。

    "maxSizeBytes": {
      "type": "string",
      "defaultValue": "1073741824"
    }

### requestedServiceObjectiveName

版本的性能级别所对应的名称。

    "requestedServiceObjectiveName": {
      "type": "string",
      "defaultValue": "S0",
      "metadata": {
        "description": "The name corresponding to the performance level for edition. The available options are: Shared, Basic, S0, S1, S2, S3, P1, P2, and P3."
      }
    }


## 要部署的资源

### SQL Server 和数据库

创建一个新的 SQL Server 和数据库。在 **serverName** 参数中指定服务器的名称，在 **serverLocation** 参数中指定其位置。在创建新服务器时，必须提供数据库服务器管理员的登录名和密码。

    {
      "name": "[parameters('serverName')]",
      "type": "Microsoft.Sql/servers",
      "location": "[parameters('serverLocation')]",
      "apiVersion": "2014-04-01-preview",
      "properties": {
        "administratorLogin": "[parameters('administratorLogin')]",
        "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
        "version": "12.0"
      },
      "resources": [
        {
          "name": "[parameters('databaseName')]",
          "type": "databases",
          "location": "[parameters('serverLocation')]",
          "apiVersion": "2014-04-01-preview",
          "dependsOn": [
            "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
          ],
          "properties": {
            "edition": "[parameters('edition')]",
            "collation": "[parameters('collation')]",
            "maxSizeBytes": "[parameters('maxSizeBytes')]",
            "requestedServiceObjectiveName": "[parameters('requestedServiceObjectiveName')]"
          }
        },
        {
          "apiVersion": "2014-04-01-preview",
          "dependsOn": [
            "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
          ],
          "location": "[parameters('serverLocation')]",
          "name": "AllowAllWindowsAzureIps",
          "properties": {
            "endIpAddress": "0.0.0.0",
            "startIpAddress": "0.0.0.0"
          },
          "type": "firewallrules"
        }
      ]
    },


[AZURE.INCLUDE [app-service-web-deploy-web-host](../includes/app-service-web-deploy-web-host.md)]


### 网站

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/sites",
      "location": "[parameters('siteLocation')]",
      "dependsOn": [
        "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]"
      ],
      "tags": {
        "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]": "empty"
      },
      "properties": {
        "name": "[parameters('siteName')]",
        "serverFarmId": "[parameters('hostingPlanName')]"
      },
      "resources": [
        {
          "apiVersion": "2015-08-01",
          "type": "config",
          "name": "connectionstrings",
          "dependsOn": [
            "[concat('Microsoft.Web/sites/', parameters('siteName'))]"
          ],
          "properties": {
            "DefaultConnection": {
              "value": "[concat('Data Source=tcp:', reference(concat('Microsoft.Sql/servers/', parameters('serverName'))).fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('databaseName'), ';User Id=', parameters('administratorLogin'), '@', parameters('serverName'), ';Password=', parameters('administratorLoginPassword'), ';')]",
              "type": "SQLAzure"
            }
          }
        }
      ]
    },

### 自动缩放

    {
      "apiVersion": "2014-04-01",
      "name": "[concat(parameters('hostingPlanName'), '-', resourceGroup().name)]",
      "type": "Microsoft.Insights/autoscalesettings",
      "location": "China East",
      "tags": {
        "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]": "Resource"
      },
      "dependsOn": [
        "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]"
      ],
      "properties": {
        "profiles": [
          {
            "name": "Default",
            "capacity": {
              "minimum": 1,
              "maximum": 2,
              "default": 1
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "CpuPercentage",
                  "metricResourceUri": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT10M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 80
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": 1,
                  "cooldown": "PT10M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "CpuPercentage",
                  "metricResourceUri": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT1H",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 60
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": 1,
                  "cooldown": "PT1H"
                }
              }
            ]
          }
        ],
        "enabled": false,
        "name": "[concat(parameters('hostingPlanName'), '-', resourceGroup().name)]",
        "targetResourceUri": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]"
      }
    },

### 状态代码 403 和 500 的警报规则、高 CPU 及 HTTP 队列长度 

    //Alert-Rules --> 5xx
    {
      "apiVersion": "2014-04-01",
      "name": "[concat('ServerErrors ', parameters('siteName'))]",
      "type": "Microsoft.Insights/alertrules",
      "location": "China East",
      "dependsOn": [
        "[concat('Microsoft.Web/sites/', parameters('siteName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/sites/', parameters('siteName'))]": "Resource"
      },
      "properties": {
        "name": "[concat('ServerErrors ', parameters('siteName'))]",
        "description": "[concat(parameters('siteName'), ' has some server errors, status code 5xx.')]",
        "isEnabled": false,
        "condition": {
          "odata.type": "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
          "dataSource": {
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[concat(resourceGroup().id, '/providers/Microsoft.Web/sites/', parameters('siteName'))]",
            "metricName": "Http5xx"
          },
          "operator": "GreaterThan",
          "threshold": 0,
          "windowSize": "PT5M"
        },
        "action": {
          "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
          "sendToServiceOwners": true,
          "customEmails": [ ]
        }
      }
    },
    //Alert-Rules --> 403
    {
      "apiVersion": "2014-04-01",
      "name": "[concat('ForbiddenRequests ', parameters('siteName'))]",
      "type": "Microsoft.Insights/alertrules",
      "location": "China East",
      "dependsOn": [
        "[concat('Microsoft.Web/sites/', parameters('siteName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/sites/', parameters('siteName'))]": "Resource"
      },
      "properties": {
        "name": "[concat('ForbiddenRequests ', parameters('siteName'))]",
        "description": "[concat(parameters('siteName'), ' has some requests that are forbidden, status code 403.')]",
        "isEnabled": false,
        "condition": {
          "odata.type": "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
          "dataSource": {
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[concat(resourceGroup().id, '/providers/Microsoft.Web/sites/', parameters('siteName'))]",
            "metricName": "Http403"
          },
          "operator": "GreaterThan",
          "threshold": 0,
          "windowSize": "PT5M"
        },
        "action": {
          "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
          "sendToServiceOwners": true,
          "customEmails": [ ]
        }
      }
    },
    //Alert-Rules --> High CPU
    {
      "apiVersion": "2014-04-01",
      "name": "[concat('CPUHigh ', parameters('hostingPlanName'))]",
      "type": "Microsoft.Insights/alertrules",
      "location": "China East",
      "dependsOn": [
        "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]": "Resource"
      },
      "properties": {
        "name": "[concat('CPUHigh ', parameters('hostingPlanName'))]",
        "description": "[concat('The average CPU is high across all the instances of ', parameters('hostingPlanName'))]",
        "isEnabled": false,
        "condition": {
          "odata.type": "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
          "dataSource": {
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
            "metricName": "CpuPercentage"
          },
          "operator": "GreaterThan",
          "threshold": 90,
          "windowSize": "PT15M"
        },
        "action": {
          "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
          "sendToServiceOwners": true,
          "customEmails": [ ]
        }
      }
    },
    //Alert-Rules --> HTTP Queue Length
    {
      "apiVersion": "2014-04-01",
      "name": "[concat('LongHttpQueue ', parameters('hostingPlanName'))]",
      "type": "Microsoft.Insights/alertrules",
      "location": "China East",
      "dependsOn": [
        "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]": "Resource"
      },
      "properties": {
        "name": "[concat('LongHttpQueue ', parameters('hostingPlanName'))]",
        "description": "[concat('The HTTP queue for the instances of ', parameters('hostingPlanName'), ' has a large number of pending requests.')]",
        "isEnabled": false,
        "condition": {
          "odata.type": "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
          "dataSource": {
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
            "metricName": "HttpQueueLength"
          },
          "operator": "GreaterThan",
          "threshold": 100,
          "windowSize": "PT5M"
        },
        "action": {
          "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
          "sendToServiceOwners": true,
          "customEmails": [ ]
        }
      }
    }


## 运行部署的命令

[AZURE.INCLUDE [app-service-deploy-commands](../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json

### Azure CLI

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json


 

<!---HONumber=Mooncake_0118_2016-->