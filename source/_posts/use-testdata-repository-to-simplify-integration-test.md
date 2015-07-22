title: Use TestData Repository to simplify your Integration Test
Author: Ji Zhoubo
date: 2015-07-03 16:11:56
tags: [csharp, .Net, Test]
---
## About
It has always been a boring and tough thing to manually insert data to DB for Integration Test, especially when the number of records is huge.  

The old and bad solution is: Maintain a lot of SQL scripts in Test Project. We have gone through the suffering, and finally come up a solution  **TestData Repository** to easily maintain test cases.

In this post, we will share this solution and how it simplifies Integration Test.

## Problems and Solution

### Map TestData Repository to DB Table
 To apply to all entities (tables) we want to create tests for, we create a generic TestDataRepository class, which inherits a base Repository class, so that it can access DB

```csharp
public class TestDataRepository<T> : ReadOnlyRepositoryBase<T> where T : class
{
}

```

### Generate Insert and Delete script dynamically
To generate Insert and Delete script, we need to know if insert, what columns need to be inserted; if delete, what is the WHERE condition.

#### For insert:

 1. loop through all properties of the entity (which is mapped to DB table)

 ```csharp
var properties = PropertyInfoCache.GetPropertyInfos(typeof (T));
foreach (var propertyInfo in properties.Where(propertyInfo => propertyInfo.Name != IdentityColumn))
{
}
```
 2. check if the property is a table column, if yes then use **Reflection** to get the property Name and Value, and add Name and Value to dynamic SQL script

```csharp
var propertyType = propertyInfo.PropertyType;
if (propertyInfo.GetCustomAttributes(typeof(NonDbProperty),true).Length != 0) continue;
if (!propertyInfo.CanWrite) 
    continue;
if (!propertyType.IsPublic) 
    continue;
var value = propertyInfo.GetValue(data);
if (value == null) 
    continue;
if (propertyType == typeof(DateTime) && (DateTime)value == DateTime.MinValue)
    continue;
columns.AppendFormat("[{0}],", propertyInfo.Name);
if (propertyType.IsEnum){values.AppendFormat("{0},", Convert.ChangeType(value, typeof(int)));}
else if (IsNumericType(propertyType))
    values.AppendFormat("{0},", value);
else if (propertyType == typeof(DateTime))
    values.AppendFormat("'{0}',", ((DateTime)value).ToString("yyyy-MM-dd HH:mm:ss"));
else
    values.AppendFormat("'{0}',", value);
paramList.AppendFormat("@{0},", propertyInfo.Name);
```

 3. build up SQL script based on columns and values
```csharp
string sql = string.Empty;
sql += string.Format("INSERT INTO {0} ({1}) Values({2});", TableName,
                                        columns.ToString().TrimEnd(','),
                                        values.ToString().TrimEnd(','));
```

#### For delete:
 1. get all key columns which will be used in WHERE condition

```csharp
var properties = PropertyInfoCache.GetPropertyInfos(typeof (T));
StringBuilder conditions = new StringBuilder();
foreach (var propertyInfo in properties.Where(propertyInfo => KeyColumns.Contains(propertyInfo.Name)))
{

}
 ```
 2. build up WHERE condition by using property Name and Value

```csharp
var value = propertyInfo.GetValue(data);
if (value == null) continue;
if (conditions.Length > 0) conditions.Append(" and ");
var propertyType = propertyInfo.PropertyType;
if (propertyType.IsEnum)
    conditions.AppendFormat("[{0}] = {1}", propertyInfo.Name, (int) value);
else if (IsNumericType(propertyType))
    conditions.AppendFormat("[{0}] = {1}", propertyInfo.Name, value);
else if (propertyType == typeof (DateTime))
    conditions.AppendFormat("[{0}] = '{1}'", propertyInfo.Name,
                            ((DateTime) value).ToString("yyyy-MM-dd HH:mm:ss"));
else
    conditions.AppendFormat("[{0}] = '{1}'", propertyInfo.Name, value);
```

 3. build up delete SQL script

```csharp
var sql = string.Format("delete from {0} where {1}", TableName, conditions);
```


### Base Class for storing Test Data
 
 Now we have our TestData Repository ready, which can help us create / delete test data in DB. Next thing we are interested in is where to store predefined test cases, and pass these test cases to TestData Repository.


```csharp
public abstract class TestData<T> where T : class
{
    abstract public string TableName { get; }
    abstract public string KeyColumn { get; }
    abstract public string DbRole { get; }
             
    public TestDataRepository<T> TestDataRepo { get; set; }
 
    public void AddTestData(List<T> testDataList)
    {
        TestDataRepo = new TestDataRepository<T>(DbContextFactory.GetDbConfig (DbRole))
         {
             DataList = testDataList,
             TableName = TableName,
             KeyColumns = new[] { KeyColumn }
         };
    }
 
    public void InsertAllTestData()
    {
        TestDataRepo.InsertTestDataList();
    }
 
    public void DeleteAllTestData()
    {
        TestDataRepo.DeleteTestDataList();
    }
}
```

 This class is abstract and generic, thus subclasses are forced to override properties.  All test cases are stored in subclasses

```csharp
public class TestEntity : TestData<TestEntity>
{
    private string _TableName = "TestTableName";
    private string _KeyColumn = "TestKeyColumn";
    private string _DbRole = Factories.DbRole.TestDb;
 
    public override string TableName
    {
        get { return _TableName; }
    }
 
    public override string KeyColumn
    {
        get { return _KeyColumn; }
    }
 
    public override string DbRole
    {
        get { return _DbRole; }
    }
 
    public int Prop1 { get; set; }
    public int Prop2 { get; set; }
    public bool Prop3 { get; set; }    
 
    public static TestEntity TestEntityDataI
    {
        get
        {
            return new TestEntity()
            {
                Prop1 = Something1,
                Prop2 = Something2,
                Prop3 = Something3
            };
        }
    }
}
```