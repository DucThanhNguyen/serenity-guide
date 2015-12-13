# SqlQuery Object

[**namespace**: *Serenity.Data*] - [**assembly**: *Serenity.Data*]

SqlQuery is an object to compose dynamic SQL SELECT queries through a fluent interface.

# Advantages

SqlQuery offers some advantages over hand crafted SQL:

* Using IntelliSense feature of Visual Studio while composing SQL

* Fluent interface with minimal overhead

* Reduced syntax errors as the query is checked compile time, not execution time.

* Clauses like Select, Where, Order By can be used in any order. They are placed at correct positions when converting the query to string. Similary, such clauses can be used more than once and they are merged during string conversion. So you can conditionally build SQL depending on input parameters.

* No need to mess up with parameters and parameter names. All values used are converted to auto named parameters. You can also use manually named parameters if required.

* It can generate a special query to perform paging on server types that doesn't support it natively (e.g. SQL Server 2000)

* With the dialect system, query can be targeted at specific server type and version.

* If it is used along with Serenity entities (it can also be used with micro ORMs like Dapper), helps to load query results from a data reader with zero reflection. Also it does left/right joins automatically.


## How To Try Samples Here

I recommend using LinqPad to try samples given here. 

You should add reference to *Serenity.Core*, *Serenity.Data* and *Serenity.Data.Entity* NuGet packages.

Another option is to locate and reference these DLLs directly from a Serene application's *bin* or packages directory.

Make sure you add *Serenity* and *Serenity.Data* to Additional Namespace Imports in Query Properties dialog.

## A Simple Select Query

```csharp
void Main()
{
    var query = new SqlQuery();
    query.Select("Firstname");
    query.Select("Surname");
    query.From("People");
    query.OrderBy("Age");
    
    Console.WriteLine(query.ToString());
}
```

This will result in output:

```sql
SELECT 
Firstname,
Surname 
FROM People 
ORDER BY Age
```

In the first line of our program, we called SqlQuery with its sole parameterless constructor. If *ToString()* was called at this point, the output would be:

```sql
SELECT FROM
```

SqlQuery doesn't perform any syntax validation. It just converts the query you build yourself, by calling its methods. Even if you don't select any fields or call from methods, it will generate this basic *SELECT FROM* statement.

> SqlQuery can't generate empty queries.

Next, we called `Select` method with string parameter `"FirstName"`. Our query is now like this:

```sql
SELECT Firstname FROM
```

When `Select("Surname")` statement is executed, SqlQuery put a comma between previously selected field (*Firstname*) and this one:

```sql
SELECT Firstname, Surname FROM
```

After executing *From* and *OrderBy* methods, our final output is:

```sql
SELECT Firstname, Surname FROM People ORDER BY Age
```


## Method Call Order and Its Effects

In previous sample, output wouldn't change even if we reordered *From*, *OrderBy* and *Select* lines. It would change only if we changed order of *Select* statements...

```csharp
void Main()
{
    var query = new SqlQuery();
    query.From("People");
    query.OrderBy("Age");
    query.Select("Surname");
	query.Select("Firstname");
    
    Console.WriteLine(query.ToString());
}
```

...but, only the column ordering inside the SELECT statement would change:

```sql
SELECT 
Surname,
Firstname 
FROM People 
ORDER BY Age
```

You may use method like Select, From, OrderBy, GroupBy in any order, and can also mix them (e.g. call Select, then OrderBy, then Select again...)

> Putting FROM at start is recommended, especially when used with Serenity entities, as it helps with auto joins and determining database dialect etc.


## Method Chaining

It is a bit verbose and not so readable to start every line `query.`. Almost all *SqlQuery* methods are chainable, and returns the query itself as result. 

We may rewrite the query like this:

```csharp
void Main()
{
    var query = new SqlQuery()
    	.From("People")
    	.Select("Firstname")
    	.Select("Surname")
    	.OrderBy("Age");
    
    Console.WriteLine(query.ToString());
}
```

> This feature is similar to jQuery and LINQ enumerable method chaining.

We could even get rid of the query variable:

```csharp
void Main()
{
    Console.WriteLine(
        new SqlQuery()
        	.From("People")
        	.Select("Firstname")
        	.Select("Surname")
        	.OrderBy("Age")
    		.ToString());
}```


> It is strongly recommended to put every method on its own line, and indent properly for readability and consistency reasons.

## Select Method

```csharp
public SqlQuery Select(string expression)
```

In the samples we had so far, we used the overload of the *Select* method shown above (it has about 11 overloads).

Expression parameter can be a simple field name or an expression like `"FirstName + ' ' + LastName"`

Whenever this method is called, the expression you set is added to the SELECT statement of resulting query with a comma between.

There is also a SelectMany method to select multiple fields in one call:

```csharp
public SqlQuery SelectMany(params string[] expressions)
```

For example:

```csharp
void Main()
{
    var query = new SqlQuery()
        .From("People")
        .SelectMany("Firstname", "Surname", "Age", "Gender")
        .ToString();
    
    Console.WriteLine(query.ToString());
}
```

```sql
SELECT 
Firstname,
Surname,
Age,
Gender 
FROM People
```

> I'd personally prefer calling Select method multiple times.

You might be wondering, why multiple selection is not just another *Select* overload. It's because *Select* has a more commonly used overload to select a column with alias:

```csharp
public SqlQuery Select(string expression, string alias)
```

```csharp
void Main()
{
    var query = new SqlQuery()
        .Select("(Firstname + ' ' + Surname)", "Fullname")
        .From("People")
        .ToString();
    
    Console.WriteLine(query.ToString());
}```

```sql
SELECT 
(Firstname + ' ' + Surname) AS [Fullname] 
FROM People
```


## From Method

```csharp
public SqlQuery From(string table)
```

SqlQuery.From method should be called at least once (and usually once). 

> ..and it is recommended to be called first.

When you call it a second time, table name will be added to FROM statement with a comma between. Thus, it will be a CROSS JOIN:

```csharp
void Main()
{
	var query = new SqlQuery()
		.From("People")
		.From("City")
		.From("Country")
		.Select("Firstname")
		.Select("Surname")
		.OrderBy("Age");
    
    Console.WriteLine(query.ToString());
}
```

```sql
SELECT 
Firstname,
Surname 
FROM People, City, Country 
ORDER BY Age
```

## Using Alias Object with SqlQuery

It is common to use table aliases when number of referenced tables increase and our queries become longer:


```csharp
void Main()
{
	var query = new SqlQuery()
		.From("Person p")
		.From("City c")
		.From("Country o")
		.Select("p.Firstname")
		.Select("p.Surname")
		.Select("c.Name", "CityName")
		.Select("o.Name", "CountryName")
		.OrderBy("p.Age")
		.ToString();
    
    Console.WriteLine(query.ToString());
}
```

```sql
SELECT 
p.Firstname,
p.Surname,
c.Name AS [CityName],
o.Name AS [CountryName] 
FROM Person p, City c, Country o 
ORDER BY p.Age
```

Although it works like this, it is better to define `p`, `c`, and `o` as *Alias* objects.

```csharp
var p = new Alias("Person", "p");
```

Alias object is like a short name assigned to a table. It has an indexer and operator overloads to generate SQL member access expressions like `p.Surname`.

```csharp
void Main()
{
	var p = new Alias("Person", "p");
	Console.WriteLine(p + "Surname"); // + operator overload
	Console.WriteLine(p["Firstname"]); // through indexer
}
```

```
p.Surname
p.Firstname
```

> Unfortunately C# member access operator (.) can't be overridden, so we had to use (+). A workaround could be possible with dynamic, but it would perform poorly.

Let's modify our query making use of Alias objects:

```csharp
void Main()
{
	var p = new Alias("Person", "p");
	var c = new Alias("City", "c");
	var o = new Alias("Country", "o");
	
	var query = new SqlQuery()
		.From(p)
		.From(c)
		.From(o)
		.Select(p + "Firstname")
		.Select(p + "Surname")
		.Select(c + "Name", "CityName")
		.Select(o + "Name", "CountryName")
		.OrderBy(p + "Age")
		.ToString();
    
    Console.WriteLine(query.ToString());
}
```

```sql
SELECT 
p.Firstname,
p.Surname,
c.Name AS [CityName],
o.Name AS [CountryName] 
FROM Person p, City c, Country o 
ORDER BY p.Age
```

As seen above, result is the same, but the code we wrote is a bit longer. So what is the advantage of using an alias? 

If we had a list of constants with field names…

```csharp
void Main()
{
	const string Firstname = "Firstname";
	const string Surname = "Surname";
	const string Name = "Name";
	const string Age = "Age";

	var p = new Alias("Person", "p");
	var c = new Alias("City", "c");
	var o = new Alias("Country", "o");
	var query = new SqlQuery()
		.From(p)
		.From(c)
		.From(o)
		.Select(p + Firstname)
		.Select(p + Surname)
		.Select(c + Name, "CityName")
		.Select(o + Name, "CountryName")
		.OrderBy(p + Age)
		.ToString();
    
    Console.WriteLine(query.ToString());
}
```

…we would take advantage of intellisense feature and have some more compile time checks.

Obviously, it is not logical and easy to define field names for every query. This should be in a central location, or our entity declarations.

Let's create a poor mans simple ORM using Alias:

```csharp
public class PeopleAlias : Alias
{
	public PeopleAlias(string alias) 
        : base("People", alias) { }

    public string ID { get { return this["ID"]; } }
    public string Firstname { get { return this["Firstname"]; } }
    public string Surname { get { return this["Surname"]; } }
    public string Age { get { return this["Age"]; } }
}

public class CityAlias : Alias
{
  public CityAlias(string alias)
      : base("City", alias) { }

  public string ID { get { return this["ID"]; } }
  public string CountryID { get { return this["CountryID"]; } }
  public new string Name { get { return this["Name"]; } }
}

public class CountryAlias : Alias
{
  public CountryAlias(string alias)
      : base("Country", alias) { }

  public string ID { get { return this["ID"]; } }
  public new string Name { get { return this["Name"]; } }
}

void Main()
{
	var p = new PeopleAlias("p");
	var c = new CityAlias("c");
	var o = new CountryAlias("o");
	var query = new SqlQuery()
		.From(p)
		.From(c)
		.From(o)
		.Select(p.Firstname)
		.Select(p.Surname)
		.Select(c.Name, "CityName")
		.Select(o.Name, "CountryName")
		.OrderBy(p.Age)
		.ToString();
    
    Console.WriteLine(query.ToString());
}```


Now we have a set of table alias classes with field names and they can be reused in all queries.

> This is just a sample to explain aliases. I don't recommend writing such classes.

In sample above, we used *SqlQuery.From* overload that takes an *Alias* parameter:

```csharp
public SqlQuery From(Alias alias)
```

When this method is called, table name and its aliased name is added to *FROM* statement of query.


##OrderBy Metodu

```csharp
public SqlQuery OrderBy(string expression, bool desc = false)
```

OrderBy metodu da Select gibi bir alan adı ya da ifadesiyle çağrılabilir. "Desc" opsiyonel parametresine true atarsanız, alan adı ya da ifadenizin sonuna " DESC" getirilir.

OrderBy metodu, verdiğiniz ifadeleri ORDER BY deyiminin sonuna ekler. Bazen alanı listenin başına getirmek te isteyebiliriz. Örneğin çeşitli alanlara göre sıralanmış bir sorgu hazırladıktan sonra, kullanıcı arayüzünde tıklanan kolona göre sıralamanın değişmesi (önceki sıralamayı tümüyle kaybetmeden) gerekebilir. Bunun için SqlQuery, OrderByFirst metodunu sağlar:


```csharp
public SqlQuery OrderByFirst(string expression, bool desc = false)
```

```csharp
namespace Samples
{
    using Serenity;
    using Serenity.Data;

    public partial class SqlQuerySamples
    {
        public static string OrderByFirst()
        {
            var query = new SqlQuery()
                .Select("Firstname")
                .Select("Surname")
                .From("Person")
                .OrderBy("PersonID");

            return query.OrderByFirst("Age")
                .ToString();
        }
    }
}
```

```sql
SELECT Firstname, Surname FROM Person ORDER BY Age, PersonID
```

Order by kullanmış olsaydık şunu elde edecektik:

```sql
SELECT Firstname, Surname FROM Person ORDER BY PersonID, Age
```

##Distinct Metodu

```csharp
public SqlQuery Distinct(bool distinct)
```

DISTINCT deyimini içeren bir sorgu üretmek istediğinizde bu metodu kullanabilirsiniz:

```csharp
namespace Samples
{
    using Serenity;
    using Serenity.Data;

    public partial class SqlQuerySamples
    {
        public static string DistinctMethod()
        {
            return new SqlQuery()
                .From("Person")
                .Distinct(true)
                .Select("Firstname")
                .ToString();
        }
    }
}
```

```sql
SELECT DISTINCT Firstname FROM Person 
```

##Group By Metodu

```csharp
public SqlQuery GroupBy(string expression)
```

GroupBy metodu bir alan adı ya da ifadesiyle çağrılır ve bu ifadeyi sorgunun GROUP BY deyiminin sonuna ekler.


```csharp
namespace Samples
{
    using Serenity;
    using Serenity.Data;

    public partial class SqlQuerySamples
    {
        public static string GroupByMethod()
        {
            new SqlQuery()
                .From("Person")
                .Select("Firstname", "Lastname", "Count(*)")
                .GroupBy("Firstname")
                .GroupBy("LastName")
                .ToString();
        }
    }
}
```

```sql
SELECT Firstname, Lastname, Count(*) FROM Person GROUP BY Firstname, LastName
```


##Having Metodu

```csharp
public SqlQuery Having(string expression)
```

GroupBy metodu ile birlikte kullanabileceğiniz Having metodu, mantıksal bir ifadeyle çağrılır ve bu ifadeyi sorgunun HAVING deyiminin sonuna ekler.


```csharp
namespace Samples
{
    using Serenity;
    using Serenity.Data;

    public partial class SqlQuerySamples
    {
        public static string HavingMethod()
        {
            new SqlQuery()
                .From("Person")
                .Select("Firstname", "Lastname", "Count(*)")
                .GroupBy("Firstname")
                .GroupBy("LastName")
                .Having("Count(*) > 5")
                .ToString();
        }
    }
}
```

```sql
SELECT Firstname, Lastname, Count(*) FROM Person GROUP BY Firstname, LastName HAVING Count(*) > 5
```

##Sayfalama İşlemleri (SKIP / TAKE / TOP / LIMIT)

```csharp
public SqlQuery Skip(int skipRows)

public SqlQuery Take(int rowCount)
```

SqlQuery, LINQ'de Take ve Skip olarak geçen sayfalama metodlarını destekler. Bunlardan Take, Microsoft SQL Server'da TOP'a karşılık gelirken, SKIP'in direk bir karşılığı olmadığından SqlQuery, ROW_NUMBER() fonksiyonundan faydalanır. Bu nedenle SQL server için SKIP kullanmak istediğinizde, sorgunuzda en az bir ORDER BY ifadesinin de olması gerekir.

```csharp
namespace Samples
{
    using Serenity;
    using Serenity.Data;

    public partial class SqlQuerySamples
    {
        public static string SkipTakePaging()
        {
            new SqlQuery()
                .From("Person")
                .Select("Firstname", "Lastname")
                .OrderBy("PersonId")
                .Skip(300)
                .Take(100)
                .ToString();
        }
    }
}
```

```sql
SELECT * FROM (
    SELECT TOP 400 Firstname, Lastname, ROW_NUMBER() OVER (ORDER BY PersonId) AS _row_number_ FROM Person
) _row_number_results_ WHERE _row_number_ > 300
```

##Farklı Veritabanları Desteği

Sayfalama işlemlerindeki örneğimizde, SqlQuery, Microsoft SQL Server 2008'e uygun bir sayfalama metodu kullandı.

Dialect metodu ile SQL query nin kullandığı DIALECT ya da sunucu tipini değiştirebiliriz:

```csharp
public SqlQuery Dialect(SqlDialect dialect)
```

Şu an için aşağıdaki sunucu tipleri desteklenmektedir:

```csharp
[Flags]
public enum SqlDialect
{
    MsSql = 1,
    Firebird = 2,
    UseSkipKeyword = 512,
    UseRowNumber = 1024,
    UseOffsetFetch = 2048,
    MsSql2005 = MsSql | UseRowNumber,
    MsSql2012 = MsSql | UseOffsetFetch | UseRowNumber
}
```

Örneğin SqlDialect.MsSql2012'yi seçip, SQL Server 2012 ile gelen OFFSET FETCH deyimlerinden faydalanmak isteseydik:

```csharp
namespace Samples
{
    using Serenity;
    using Serenity.Data;

    public partial class SqlQuerySamples
    {
        public static string SkipTakePaging()
        {
            new SqlQuery()
                .Dialect(SqlDialect.MsSql2012)
                .From("Person")
                .Select("Firstname", "Lastname")
                .OrderBy("PersonId")
                .Skip(300)
                .Take(100)
                .ToString();
        }
    }
}
```

```sql
SELECT Firstname, Lastname FROM Person ORDER BY PersonId 
OFFSET 300 ROWS FETCH NEXT 100 ROWS ONLY
```

SqlDialect.Firebird'ü tercih etseydik şu şekilde bir sorgu oluşurdu:

```sql
SELECT FIRST 100 SKIP 300 Firstname, Lastname FROM Person ORDER BY PersonId
```

SqlQuery'nin Sunucu/Dialect desteği henüz mükemmel olmasa da, temel işlemlerde sorun çıkarmayacak düzeydedir.

Uygulamanızda tek tipte sunucu kullanıyorsanız, her sorgunuzda, dialect ayarlamak istemeyebilirsiniz. Bunun için SqlSettings.CurrentDialect'i değiştirmelisiniz. Örneğin aşağıdaki kodu program başlangıcında, ya da global.asax dosyanızdan çağırabilirsiniz: 

```csharp
SqlSettings.CurrentDialect = SqlDialect.MsSql2012;
```
