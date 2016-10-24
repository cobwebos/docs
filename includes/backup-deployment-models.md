Azure 备份服务提供两种类型的保管库：备份保管库和恢复服务保管库。备份保管库优先级最高。随后是恢复服务保管库，用于支持扩展的 Resource Manager 部署。Microsoft 建议使用 Resource Manager 部署，除非明确要求使用经典部署。

| **部署** | **门户** | **保管库** |
|-----------|------|-----|
|经典|[经典](https://manage.windowsazure.com)|备份|
|资源管理器|[Azure](https://portal.azure.com)|恢复服务|

> [AZURE.NOTE] 备份保管库无法保护 Resource Manager 部署的解决方案。不过，可以使用恢复服务保管库保护经典部署的服务器和 VM。

<!---HONumber=AcomDC_0921_2016-->