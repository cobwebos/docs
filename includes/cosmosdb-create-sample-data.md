现在可以使用数据资源管理器将数据添加到新集合。

1. 在数据资源管理器中，新数据库将显示在“集合”窗格中。 展开 **Items** 数据库，展开 **ToDoList** 集合，单击“文档”，然后单击“新建文档”。 

   ![在 Azure 门户的数据资源管理器中创建新文档](./media/cosmosdb-create-sample-data/azure-cosmosdb-data-explorer-emulator-new-document.png)
  
2. 现在使用以下结构将几个新文档添加到集合，其中在每个文档中插入 ID 的唯一值，并根据需要更改其他属性。 新文档可以具有所需的任何结构，因为 Azure Cosmos DB 不对数据施加任何架构。

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries."
     }
     ```

     现在可以在数据资源管理器中使用查询来检索数据。 默认情况下，数据资源管理器使用“SELECT * FROM c”来检索集合中的所有文档，但你可以将其更改为“SELECT * FROM c ORDER BY c.name ASC”，按 name 属性的字母升序返回所有文档。 
 
     还可以使用数据资源管理器创建存储过程、UDF 和触发器以执行服务器端业务逻辑。 数据资源管理器公开 API 中提供的所有内置编程数据访问，但你可以使用它轻松访问 Azure 门户中的数据。