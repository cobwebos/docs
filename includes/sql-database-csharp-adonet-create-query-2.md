
<a name="cs_0_csharpprogramexample_h2"/>

## <a name="c-program-example"></a>C# 程序示例

本文的后续部分提供使用 ADO.NET 将发送到 SQL 数据库的 TRANSACT-SQL 语句的 C# 程序。 C# 程序执行以下操作：

1. [连接到我们使用 ADO.NET 的 SQL 数据库](#cs_1_connect)。
2. [创建表](#cs_2_createtables)。
3. [通过发出 T-SQL 的 INSERT 语句，从而填充数据，表](#cs_3_insert)。
4. [通过使用联接来更新数据](#cs_4_updatejoin)。
5. [通过使用联接将删除数据](#cs_5_deletejoin)。
6. [通过使用联接选择数据行](#cs_6_selectrows)。
7. 关闭的连接 （这会导致所有临时表断开从 tempdb）。

C# 程序包含：

- C# 代码以连接到数据库。
- 返回 T-SQL 的源代码的方法。
- 提交到数据库 T-SQL 的两种方法。

#### <a name="to-compile-and-run"></a>若要编译并运行

此 C# 程序是逻辑上一个.cs 文件。 但是，程序此处以物理方式划分为多个代码块，以便更轻松地查看并了解每个块。 若要编译并运行此程序，请执行以下操作：

1. 在 Visual Studio 中创建 C# 项目。
    - 项目类型应为*控制台*应用程序中，从类似于以下层次结构：**模板** > **Visual C#** > **Windows 经典桌面** > **控制台应用程序 (.NET Framework)**。
3. 文件中**Program.cs**，擦除小初学者行代码。
3. 到 Program.cs 中，复制并粘贴每个以下块，此处提供的相同顺序。
4. 在 Program.cs 中，编辑中的以下值**Main**方法：

   - **cb。数据源**
   - **cd。用户 Id**
   - **cb。密码**
   - **InitialCatalog**

5. 验证程序集**System.Data.dll**引用。 若要验证，请展开**引用**中的节点**解决方案资源管理器**窗格。
6. 若要生成 Visual Studio 中的程序，请单击**生成**菜单。
7. 若要从 Visual Studio 运行该程序，请单击**启动**按钮。 报表输出显示在 cmd.exe 窗口中。

> [!NOTE]
> 你可以编辑 T-SQL 的选择添加一个前导 **#** 表名称中，这将创建它们中的为临时表**tempdb**。 如果没有测试数据库可用时，可以用于演示目的，这一方法。 当连接关闭时，将自动删除临时表。 对于临时表不强制外键的任何引用。
>

<a name="cs_1_connect"/>
### <a name="c-block-1-connect-by-using-adonet"></a>C# 块 1： 使用 ADO.NET 连接

- [下一步](#cs_2_createtables)


```csharp
using System;
using System.Data.SqlClient;   // System.Data.dll 
//using System.Data;           // For:  SqlDbType , ParameterDirection

namespace csharp_db_test
{
   class Program
   {
      static void Main(string[] args)
      {
         try
         {
            var cb = new SqlConnectionStringBuilder();
            cb.DataSource = "your_server.database.windows.net";
            cb.UserID = "your_user";
            cb.Password = "your_password";
            cb.InitialCatalog = "your_database";

            using (var connection = new SqlConnection(cb.ConnectionString))
            {
               connection.Open();

               Submit_Tsql_NonQuery(connection, "2 - Create-Tables",
                  Build_2_Tsql_CreateTables());

               Submit_Tsql_NonQuery(connection, "3 - Inserts",
                  Build_3_Tsql_Inserts());

               Submit_Tsql_NonQuery(connection, "4 - Update-Join",
                  Build_4_Tsql_UpdateJoin(),
                  "@csharpParmDepartmentName", "Accounting");

               Submit_Tsql_NonQuery(connection, "5 - Delete-Join",
                  Build_5_Tsql_DeleteJoin(),
                  "@csharpParmDepartmentName", "Legal");

               Submit_6_Tsql_SelectEmployees(connection);
            }
         }
         catch (SqlException e)
         {
            Console.WriteLine(e.ToString());
         }
         Console.WriteLine("View the report output here, then press any key to end the program...");
         Console.ReadKey();
      }
```


<a name="cs_2_createtables"/>
### <a name="c-block-2-t-sql-to-create-tables"></a>C# 块 2: T-SQL 以创建表

- [Previous](#cs_1_connect) &nbsp; / &nbsp; [Next](#cs_3_insert)

```csharp
      static string Build_2_Tsql_CreateTables()
      {
         return @"
DROP TABLE IF EXISTS tabEmployee;
DROP TABLE IF EXISTS tabDepartment;  -- Drop parent table last.


CREATE TABLE tabDepartment
(
   DepartmentCode  nchar(4)          not null
      PRIMARY KEY,
   DepartmentName  nvarchar(128)     not null
);

CREATE TABLE tabEmployee
(
   EmployeeGuid    uniqueIdentifier  not null  default NewId()
      PRIMARY KEY,
   EmployeeName    nvarchar(128)     not null,
   EmployeeLevel   int               not null,
   DepartmentCode  nchar(4)              null
      REFERENCES tabDepartment (DepartmentCode)  -- (REFERENCES would be disallowed on temporary tables.)
);
";
      }
```

#### <a name="entity-relationship-diagram-erd"></a>实体关系图 (ERD)

上面的 CREATE TABLE 语句涉及**引用**关键字创建*外键*(FK) 两个表之间的关系。  如果使用 tempdb，注释掉`--REFERENCES`关键字使用一对前导短划线。

接下来是紧急修复盘显示两个表之间的关系。 #TabEmployee.DepartmentCode 中的值*子*列仅限于 #tabDepartment.Department 中存在值*父*列。

![ERD 显示外键](./media/sql-database-csharp-adonet-create-query-2/erd-dept-empl-fky-2.png)


<a name="cs_3_insert"/>
### <a name="c-block-3-t-sql-to-insert-data"></a>C# 块 3: T-SQL 以插入数据

- [Previous](#cs_2_createtables) &nbsp; / &nbsp; [Next](#cs_4_updatejoin)


```csharp
      static string Build_3_Tsql_Inserts()
      {
         return @"
-- The company has these departments.
INSERT INTO tabDepartment
   (DepartmentCode, DepartmentName)
      VALUES
   ('acct', 'Accounting'),
   ('hres', 'Human Resources'),
   ('legl', 'Legal');

-- The company has these employees, each in one department.
INSERT INTO tabEmployee
   (EmployeeName, EmployeeLevel, DepartmentCode)
      VALUES
   ('Alison'  , 19, 'acct'),
   ('Barbara' , 17, 'hres'),
   ('Carol'   , 21, 'acct'),
   ('Deborah' , 24, 'legl'),
   ('Elle'    , 15, null);
";
      }
```


<a name="cs_4_updatejoin"/>
### <a name="c-block-4-t-sql-to-update-join"></a>C# 的块 4： 更新加入 T-SQL

- [Previous](#cs_3_insert) &nbsp; / &nbsp; [Next](#cs_5_deletejoin)


```csharp
      static string Build_4_Tsql_UpdateJoin()
      {
         return @"
DECLARE @DName1  nvarchar(128) = @csharpParmDepartmentName;  --'Accounting';


-- Promote everyone in one department (see @parm...).
UPDATE empl
   SET
      empl.EmployeeLevel += 1
   FROM
      tabEmployee   as empl
      INNER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   WHERE
      dept.DepartmentName = @DName1;
";
      }
```


<a name="cs_5_deletejoin"/>
### <a name="c-block-5-t-sql-to-delete-join"></a>C# 块 5： 针对删除联接的 T-SQL

- [Previous](#cs_4_updatejoin) &nbsp; / &nbsp; [Next](#cs_6_selectrows)


```csharp
      static string Build_5_Tsql_DeleteJoin()
      {
         return @"
DECLARE @DName2  nvarchar(128);
SET @DName2 = @csharpParmDepartmentName;  --'Legal';


-- Right size the Legal department.
DELETE empl
   FROM
      tabEmployee   as empl
      INNER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   WHERE
      dept.DepartmentName = @DName2

-- Disband the Legal department.
DELETE tabDepartment
   WHERE DepartmentName = @DName2;
";
      }
```



<a name="cs_6_selectrows"/>
### <a name="c-block-6-t-sql-to-select-rows"></a>C# 块 6: T-SQL 来选择行

- [Previous](#cs_5_deletejoin) &nbsp; / &nbsp; [Next](#cs_6b_datareader)


```csharp
      static string Build_6_Tsql_SelectEmployees()
      {
         return @"
-- Look at all the final Employees.
SELECT
      empl.EmployeeGuid,
      empl.EmployeeName,
      empl.EmployeeLevel,
      empl.DepartmentCode,
      dept.DepartmentName
   FROM
      tabEmployee   as empl
      LEFT OUTER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   ORDER BY
      EmployeeName;
";
      }
```


<a name="cs_6b_datareader"/>
### <a name="c-block-6b-executereader"></a>C# 块 6b: ExecuteReader

- [Previous](#cs_6_selectrows) &nbsp; / &nbsp; [Next](#cs_7_executenonquery)

此方法旨在运行 T-SQL SELECT 语句所生成**Build_6_Tsql_SelectEmployees**方法。


```csharp
      static void Submit_6_Tsql_SelectEmployees(SqlConnection connection)
      {
         Console.WriteLine();
         Console.WriteLine("=================================");
         Console.WriteLine("Now, SelectEmployees (6)...");

         string tsql = Build_6_Tsql_SelectEmployees();

         using (var command = new SqlCommand(tsql, connection))
         {
            using (SqlDataReader reader = command.ExecuteReader())
            {
               while (reader.Read())
               {
                  Console.WriteLine("{0} , {1} , {2} , {3} , {4}",
                     reader.GetGuid(0),
                     reader.GetString(1),
                     reader.GetInt32(2),
                     (reader.IsDBNull(3)) ? "NULL" : reader.GetString(3),
                     (reader.IsDBNull(4)) ? "NULL" : reader.GetString(4));
               }
            }
         }
      }
```


<a name="cs_7_executenonquery"/>
### <a name="c-block-7-executenonquery"></a>C# 块 7: ExecuteNonQuery

- [Previous](#cs_6b_datareader) &nbsp; / &nbsp; [Next](#cs_8_output)

修改表的数据内容，而不返回任何数据行的操作调用此方法。


```csharp
      static void Submit_Tsql_NonQuery(
         SqlConnection connection,
         string tsqlPurpose,
         string tsqlSourceCode,
         string parameterName = null,
         string parameterValue = null
         )
      {
         Console.WriteLine();
         Console.WriteLine("=================================");
         Console.WriteLine("T-SQL to {0}...", tsqlPurpose);

         using (var command = new SqlCommand(tsqlSourceCode, connection))
         {
            if (parameterName != null)
            {
               command.Parameters.AddWithValue(  // Or, use SqlParameter class.
                  parameterName,
                  parameterValue);
            }
            int rowsAffected = command.ExecuteNonQuery();
            Console.WriteLine(rowsAffected + " = rows affected.");
         }
      }
   } // EndOfClass
}
```


<a name="cs_8_output"/>
### <a name="c-block-8-actual-test-output-to-the-console"></a>C# 块 8： 实际的测试输出到控制台

- [上一个](#cs_7_executenonquery)

本部分捕获程序发送到控制台的输出。 测试运行之间不同时只有 guid 值。


```text
[C:\csharp_db_test\csharp_db_test\bin\Debug\]
>> csharp_db_test.exe

=================================
Now, CreateTables (10)...

=================================
Now, Inserts (20)...

=================================
Now, UpdateJoin (30)...
2 rows affected, by UpdateJoin.

=================================
Now, DeleteJoin (40)...

=================================
Now, SelectEmployees (50)...
0199be49-a2ed-4e35-94b7-e936acf1cd75 , Alison , 20 , acct , Accounting
f0d3d147-64cf-4420-b9f9-76e6e0a32567 , Barbara , 17 , hres , Human Resources
cf4caede-e237-42d2-b61d-72114c7e3afa , Carol , 22 , acct , Accounting
cdde7727-bcfd-4f72-a665-87199c415f8b , Elle , 15 , NULL , NULL

[C:\csharp_db_test\csharp_db_test\bin\Debug\]
>>
```
