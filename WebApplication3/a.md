# T4在進行資料庫反向工程

在進行資料庫反向工程（Reverse Engineering）時，Entity Framework（EF）的既有反向工程工具會從資料庫結構生成對應的實體類別（Entity Class），因此手動更改這些類別的命名可能會被之後的反向工程作業覆蓋。為了保持命名規範的一致性，同時避免每次都要手工修改生成的程式碼，可以考慮使用 T4（Text Template Transformation Toolkit）模板來自動化這些更改。

以下是使用 T4 模板自動化命名變更的步驟：

## 安裝 EFCore Power Tools
## 打開 Visual Studio。
## 點擊 Extensions > Manage Extensions。
## 搜索 EFCore Power Tools 並安裝。
## 重新啟動 Visual Studio。
## 使用 EFCore Power Tools 進行反向工程
## 在您的專案中，右鍵點擊專案名稱。
## 選擇 EFCore Power Tools > Reverse Engineer。
## 選擇您的資料庫連接並選擇要生成的資料表。
## 在反向工程設定中，選擇相關選項生成模型。
## 自定義 T4 模板
## 安裝 EF Core 模板：

dotnet new install Microsoft.EntityFrameworkCore.Templates
如果上述指令無效，可以用 dotnet new -i Microsoft.EntityFrameworkCore.Templates 來安裝。

在專案目錄中執行：

dotnet new ef-templates
這會生成一個 CodeTemplate/EFCore 資料夾，內含 DbContext.tt 和 EntityType.tt 文件。

打開 CodeTemplate/EFCore/EntityType.tt 文件，針對您需要的命名規則進行自定義。例如，修改 T4 模板中的欄位生成部分，以包含 DisplayName：

// 找到生成屬性名稱的代碼部分，然後按照需求進行自定義
var propertyDisplayName = propertyName; // 例如，這裡可以根據你的命名規則進行處理
WriteLine($@"[DisplayName(""{propertyDisplayName}"")]");
使用自定義 T4 模板
在 CLI 中使用自定義 T4 模板：
dotnet ef dbcontext scaffold "YourConnectionString" Microsoft.EntityFrameworkCore.SqlServer -o Models --context-dir Context --data-annotations --force --template-directory CodeTemplate/EFCore
通過這個命令，反向工程將使用自定義的 T4 模板來生成程式碼。
這樣，您既可以利用反向工程的便利性，又可以確保生成的模型類別符合您的命名規範。

## 總結
使用 EFCore Power Tools 配合自定義 T4 模板，可以有效避免每次資料庫反向工程时手動修改生成的程式碼，使得程式碼保持一致性和規範性。上述步驟提供了一個自動化生成並自定義命名的完整解決方案。

# XpagelistcoreMvc css要自己引入
# 加入項目可以用精簡檢視

# MVC ACTION create detail
# 路由優先權比querrystring高
# prefix
這是一張關於程式開發中複雜模型綁定技巧的簡報幻燈片,主要內容如下:
標題: "各種複雜模型繫結的技巧"
主要要點:

任何物件屬性都能繫結
可以繫結陣列或集合物件
不要拿 Entity Model 來繫結資料 (標記有火焰emoji,表示重要警告)
善用 prefix 去繫結複雜的表單資料

幻燈片底部有一個代碼示例,展示了如何使用前綴(prefix)綁定:
```csharp
public IActionResult UpdateUser(
    [Bind(Prefix = "User1")] UserModel user1,
    [Bind(Prefix = "User2")] UserModel user2)
{
    return View();
}
```
圖片中有一些紅色手寫筆記,標註了"Name", "User1.Name"和"User2.Name",這似乎是在進一步闡明前綴綁定技術的使用方法。
這段代碼是一個控制器方法的定義，我來詳細解釋一下：

IActionResult UpdateUser(...):
這是一個名為 UpdateUser 的方法，返回類型是 IActionResult。
IActionResult 是 ASP.NET Core 中用於表示動作結果的接口，允許返回各種類型的響應（如視圖、JSON、重定向等）。
參數部分:
方法有兩個參數，都是 UserModel 類型。
[Bind(Prefix = "User1")] 和 [Bind(Prefix = "User2")] 是模型綁定特性。
模型綁定特性 [Bind(Prefix = "...")]:
這個特性指示模型綁定器如何從請求數據中提取值來填充模型。
Prefix 設置了一個前綴，用於區分不同的模型。
實際應用:
當提交表單或發送請求時，數據可能如下格式：
```
User1.Name=John
User1.Age=30
User2.Name=Jane
User2.Age=28
```
框架會自動將 User1 前綴的數據綁定到 user1 參數，User2 前綴的數據綁定到 user2 參數。
return View();:
這行代碼返回與此動作相關聯的默認視圖。
如果需要，可以在 View 中傳遞處理後的模型數據。
這種方法特別有用於處理複雜的表單，例如在同一個頁面上編輯多個用戶的信息。通過使用前綴，可以清晰地區分不同用戶的數據，避免混淆，並簡化了後端處理邏輯。

我來詳細解釋圖片中展示的這六種數據傳輸格式，這些都是用於向 Model Binding 傳遞數組或集合數據的方法：
```csharp
selectedCourses=1050&selectedCourses=2000
```
最簡單的格式，重複使用相同的參數名。
適用於大多數情況，特別是當參數名與模型屬性名相匹配時。

```csharp
selectedCourses[0]=1050&selectedCourses[1]=2000
```
使用索引明確指定數組元素的位置。
當需要確保數據按特定順序排列時很有用。

```csharp
[0]=1050&[1]=2000
```
簡化版的索引格式，省略了參數名。
適用於控制器方法參數名與查詢字符串中的名稱完全匹配的情況。

```csharp
selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
```
使用自定義索引（這裡是 a 和 b）來標識元素。
.index 用於指定這些自定義索引的順序。
適用於需要非數字索引或者元素順序與提交順序不同的情況。

```csharp
[a]=1050&[b]=2000&index=a&index=b
```
上一種方法的簡化版，省略了參數名。
同樣使用自定義索引和 index 指定順序。

```csharp
selectedCourses[]=1050&selectedCourses[]=2000
```

使用空括號 [] 表示數組元素。
注意：圖中特別指出這種格式只適用於表單 POST 請求。



這些格式的存在使得 Model Binding 具有極大的靈活性，可以適應不同的前端數據提交方式和後端數據接收需求。開發者可以根據具體的應用場景選擇最合適的格式。
例如：

如果只是簡單地提交一個選擇列表，第一種格式可能就足夠了。
如果需要保證數據的順序，可以使用帶索引的格式（如第二種或第三種）。
如果需要在前端動態生成表單項並保持它們的關聯性，第四種和第五種格式會很有用。
最後一種格式在處理簡單的多選表單時特別方便。

理解這些格式有助於開發者更好地設計前端表單和 API，並確保數據能夠正確地綁定到後端模型上。

```csharp
selectedCourses[0].name=AAA&selectedCourses[0].desc=DescA&& selectedCourses[1].name=BBB&selectedCourses[1].desc=DescB 
```

是的，這種格式完全正確，而且在 ASP.NET Core 的 Model Binding 中是被支持的。
用途：
這種格式用於綁定一個包含複雜對象的數組或列表，其中每個對象有多個屬性。
解析：

selectedCourses[0].name=AAA 表示數組中第一個對象的 name 屬性。
selectedCourses[0].desc=DescA 表示數組中第一個對象的 desc 屬性。
selectedCourses[1].name=BBB 和 selectedCourses[1].desc=DescB 同理，表示第二個對象的屬性。


```csharp
selectedCourse[0].Name="XXX"&selectedCourse[0].DepartmentId=5&selectedCourse[1].Name="AA"&selectedCourse[1].DepartmentId=5 
```

這段程式碼主要是宣告並設定兩個課程的名稱和所屬部門ID。根據常見的編程語法和變量命名規則，這段程式碼看起來是正確的。如果需要進一步確認，請確保以下幾點：

selectedCourse陣列已經正確初始化。
Name和DepartmentId是selectedCourse陣列中每個物件的正確屬性。
你使用的編程語言支持這樣的語法。
下面是一個更清晰的格式化版本，以便於閱讀：
```csharp
selectedCourse[0].Name = "XXX";
selectedCourse[0].DepartmentId = 5;
selectedCourse[1].Name = "AA";
selectedCourse[1].DepartmentId = 5;
```
確保selectedCourse陣列有足夠的空間來存儲這些物件。

以下是一些進一步的檢查點：

陣列初始化：
```csharp
var selectedCourse = [];
selectedCourse.push({ Name: "", DepartmentId: 0 });
selectedCourse.push({ Name: "", DepartmentId: 0 });
```
賦值操作：
```csharp
selectedCourse[0].Name = "XXX";
selectedCourse[0].DepartmentId = 5;
selectedCourse[1].Name = "AA";
selectedCourse[1].DepartmentId = 5;
```
這樣可以確保陣列中已有物件，並且這些物件的屬性可以被賦值。


#route
1.mvc 裡的 cshtml ，應該算 CSR 還是 SSR  呢? 是 Server-Side Rendering

CSR 和 SSR 都是網頁渲染技術，各有優缺點，適合不同的應用場景。

CSR（Client-Side Rendering，客戶端渲染）是指在客戶端的瀏覽器中使用 JavaScript 來生成 HTML 內容。這種方式的優點是：

初始加載速度快：因為伺服器只返回少量 HTML 和 JavaScript 程式碼，因此可以更快地將頁面呈現給使用者。
更好的使用者體驗：JavaScript 可以用於實現流暢的動畫和互動，從而提供更佳的使用者體驗。
更低的伺服器負載：由於渲染工作是在客戶端完成的，因此可以減少伺服器的負載。
但是，CSR 也有一些缺點：

SEO 效果較差：因為搜尋引擎可能無法解析 JavaScript 生成的內容，因此可能會影響網站的 SEO 效果。
首次訪問速度慢：如果使用者首次訪問網站，則需要下載所有 JavaScript 程式碼才能進行渲染，這可能會導致首次訪問速度較慢。
可能存在安全問題：如果 JavaScript 程式碼存在漏洞，則可能會被攻擊者利用來竊取使用者資料或植入惡意程式碼。
SSR（Server-Side Rendering，伺服器端渲染）是指在伺服器端生成 HTML 內容，然後將其傳送給客戶端。這種方式的優點是：

SEO 效果好：因為搜尋引擎可以解析伺服器生成的 HTML 內容，因此可以提高網站的 SEO 效果。
首次訪問速度快：因為伺服器會返回完整的 HTML 內容，因此使用者可以更快地看到頁面內容。
安全性高：由於渲染工作是在伺服器端完成的，因此可以避免 JavaScript 程式碼存在安全問題的風險。
但是，SSR 也有一些缺點：

初始加載速度慢：因為伺服器需要生成完整的 HTML 內容，因此可能會導致初始加載速度較慢。
使用者體驗較差：由於 JavaScript 程式碼需要在客戶端執行，因此可能會導致動畫和互動效果不夠流暢。
伺服器負載高：由於渲染工作是在伺服器端完成的，因此可能會增加伺服器的負載。
那麼，在什麼情況下應該使用 CSR？在什麼情況下應該使用 SSR 呢？

一般來說，如果網站需要注重 SEO 效果、首次訪問速度和安全性，則建議使用 SSR。如果網站需要注重使用者體驗和降低伺服器負載，則建議使用 CSR。

具體來說，以下情況適合使用 CSR：
```
單頁面應用程式（SPA）
需要流暢動畫和互動效果的網站
需要降低伺服器負載的網站
以下情況適合使用 SSR：

新聞網站、部落格等內容型網站
電商網站
需要注重 SEO 效果的網站
當然，具體選擇哪種渲染技術還需要根據實際情況進行權衡。
```

#slug

加入類別

#actionfilter在之前 model binding之後 驗證寫在modelbinding
權限 Authorization filter 
敏感字遮罩 寫在Result filter
網頁內容繁轉簡 middleware
log
存取權限 Authorize

# 如何在 ASP.NET Core MVC 的 ServiceFilterAttribute 是幹嘛用的？

在 ASP.NET Core MVC 中，ServiceFilterAttribute 是用來實現依賴注入到過濾器的功能。這個屬性可以幫助我們從依賴注入（DI）容器中檢索服務並應用到控制器或動作方法上。以下是 ServiceFilterAttribute 的主要用途和相關細節：

主要用途
依賴注入支持：
ServiceFilterAttribute 可以讓我們在過濾器中使用依賴注入，將所需的服務注入到自定義過濾器中。

過濾器管理：
它方便我們將過濾器註冊為服務，並且能夠在控制器或動作方法上輕鬆應用這些過濾器。

使用方法
1. 註冊過濾器及其依賴服務
首先，我們需要在 ConfigureServices 方法中註冊我們的過濾器和所需的服務：
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<IMyService, MyService>();
    services.AddScoped<FilterInjectAttribute>(); // 註冊過濾器
    services.AddControllers();
    services.AddRazorPages();
}
```
2. 創建自定義過濾器
接著，我們創建一個自定義過濾器並通過構造函數注入需要的服務：
```csharp
public class FilterInjectAttribute : ActionFilterAttribute
{
    private readonly IMyService _myService;

    public FilterInjectAttribute(IMyService myService)
    {
        _myService = myService ?? throw new ArgumentNullException(nameof(myService));
    }

    public override void OnActionExecuting(ActionExecutingContext context)
    {
        // 使用注入的服務
        Console.WriteLine($"Service {_myService.GetServiceName()} was injected.");
        base.OnActionExecuting(context);
    }
}
```
3. 應用 ServiceFilterAttribute
最後，我們在控制器或動作方法上應用 ServiceFilterAttribute 來使用我們註冊的過濾器：
```csharp
[ServiceFilter(typeof(FilterInjectAttribute))]
public IActionResult Index()
{
    Console.WriteLine("HomeController method Index running.");
    return View();
}
```
注意事項
IsReusable 屬性：
ServiceFilterAttribute 有一個 IsReusable 屬性，這個屬性指示過濾器實例是否可以跨請求重複使用。如果設置為 true，多個請求將會重用同一個過濾器實例，這有點像單例模式
1
9
。
總結
ServiceFilterAttribute 為在 ASP.NET Core MVC 中實現過濾器的依賴注入提供了極大的便利。通過將過濾器註冊為服務並從 DI 容器中檢索，我們可以輕鬆地在控制器或動作方法中應用這些過濾器，並確保所需的依賴服務已經正確注入。

#  注入 service  

# linq型別轉換   或 使用 value injector

## 1.linq
```c#
   var data = await _context.Courses.Include(c => c.Department)
    .Select(p => new CourseList
    {
        Title = p.Title,
        Credits = p.Credits,
        Description = p.Description,
        Slug = p.Slug
    }).ToListAsync();
```
## 2. 不好的示範
```c#
var data = _context.Courses.Include(c => c.Department)
    .ToList()
    .Select(p => Mapper.Map<Course, CourseList>(p));
```

## 3. 明確轉型運算子

```c#
#nullable disable
using ...

namespace LiveDemo.Models;

public partial class Course
{
    public static explicit operator CourseList(Course course)
    {
        return new CourseList
        {
            Title = course.Title,
            Credits = course.Credits,
            Description = course.Description,
            Slug = course.Slug
        };
    }
}
```

# 隱含運算子

#  dotnet efr


# mvc 不想寫前端  typescript react angular api

# view component
priority list


```razor
@inject ContosoUniversityContext db
@{
    var data = db.Courses.ToList();
}
@foreach (var item in data)
{
    @item.Title
}
```


```razor _ListofCourseOL.cshtml
@inject ICourseRepository repo
@inject IUnitOfWork uow
@{
    repo.UnitOfWork = uow;
    var data = repo.FindAll();
}
@foreach (var item in data)
{
    @item.Title
}
```
調用partialview

```html
<hr>
<我們公司的部門清單，這是一個下拉選單><name="_ListofCourseOL"></我們公司的部門清單，這是一個下拉選單>
```


# htmlhelper

# httpclient  可以宣告成 static    singleton or ihttpfactory
建一次連線  tcp  65536 port   


```csharp
builder.Services.AddHttpClient();
```

不要using  httpclient 會吃tcp

```csharp
public class DepartmentsController : Controller
{
    private readonly ContosoUniversityContext _context;
    private readonly HttpClient hf;

    public DepartmentsController(ContosoUniversityContext context, IHttpClientFactory hf)
    {
        _context = context;
        this.hf = hf.CreateClient();
    }
}
```

asp.net core 在iis下跑 資源容易被回收

網站部會只跑一台機器

hangfile 

lit 微前端


ASP.NET Core MVC 重點摘要
模型綁定 (Model Binding)

控制器要輕量,模型要肥,視圖要夠笨。
可以使用多種方式接收前端傳來的資料:

單一參數
複雜類型
集合類型 (陣列、List)
字典類型


集合類型的綁定方式:
CopyselectedCourse[0].Name=XXX&selectedCourse[0].DepartmentId=5

字典類型的綁定方式:
CopyselectedCourses[0].name=AAA&selectedCourses[0].desc=DescA

動態數量的參數可以使用 params 關鍵字接收。

驗證 (Validation)

可以使用自訂 Attribute 進行驗證。
驗證 Attribute 建議使用中文命名,更直觀。
複雜的驗證邏輯可以實作 IValidatableObject 介面。

視圖模型 (ViewModel)

建議為每個 Action 建立對應的 ViewModel。
ViewModel 可以包含下拉選單等 UI 元素資料。
實體模型變更時,ViewModel 無法自動同步,但會造成編譯錯誤。

其他注意事項

使用 EF Core Power Tools 可以簡化資料庫模型的生成。
前端 JavaScript 套件可以使用 LibMan 管理。
POST 傳輸資料量雖然沒有硬性限制,但應該在伺服器端設定合理的限制。
要注意防範 XSS 攻擊,可以使用 AntiXSS 函式庫。
中文命名可以提高程式碼的可讀性,特別是對中文母語者。


# unit of Work and Controller Routing in ASP.NET MVC
# Unit of Work

A unit of work is a design pattern that manages a set of operations as a single unit. This means that all of the operations in the unit of work are either all successful or all rolled back. This pattern is often used in data access applications to ensure that data is kept in a consistent state.

In ASP.NET MVC, a unit of work can be used to manage a set of database operations. For example, you could use a unit of work to update a customer's address and email address. If either of the updates fails, the unit of work would roll back both updates. This would ensure that the customer's data is not left in an inconsistent state.

# Controller Routing

Controller routing is the process of mapping URLs to controller actions. In ASP.NET MVC, controller routing is handled by the ASP.NET MVC routing system. The routing system uses a set of rules to match URLs to controller actions.

There are two types of controller routing in ASP.NET MVC:

Programmatic routing: This type of routing is configured in code. You can use the MapRoute method to define a route rule.
Attribute routing: This type of routing is configured using attributes. You can decorate controller actions with the Route attribute to define a route rule.
Differences between Unit of Work and Controller Routing


```form
Feature       ||Unit of Work                               || Controller Routing	
Purpose       ||Manage a set of operations as a single unit||	Map URLs to controller actions	
Scope         ||Data access                                ||Application	              
Implementation||Code	                                   ||Code or attributes	
```

Here is an example of how to use a unit of work in ASP.NET MVC:

```C#
public class CustomerController : Controller
{
    private readonly IUnitOfWork _unitOfWork;

    public CustomerController(IUnitOfWork unitOfWork)
    {
        _unitOfWork = unitOfWork;
    }

    public ActionResult UpdateCustomer(int customerId, string address, string email)
    {
        var customerRepository = new CustomerRepository(_unitOfWork);

        var customer = customerRepository.GetById(customerId);
        customer.Address = address;
        customer.Email = email;

        customerRepository.Update(customer);

        _unitOfWork.SaveChanges();

        return RedirectToAction("Index");
    }
}
```
請謹慎使用程式碼。

In this example, the UpdateCustomer action method uses a unit of work to manage the updates to the customer's address and email address. If either of the updates fails, the unit of work will roll back both updates.

Here is an example of how to use attribute routing in ASP.NET MVC:

```C#
public class CustomerController : Controller
{
    [Route("customers/{id}/update")]
    public ActionResult UpdateCustomer(int id, string address, string email)
    {
        // ...
    }
}
```


Sure, here are some additional key points about the unit of work and controller routing in ASP.NET MVC:

Unit of Work

Benefits:
Encapsulation: The unit of work encapsulates a set of operations, making it easier to manage and maintain code.
Consistency: The unit of work ensures that all of the operations in a set are either all successful or all rolled back. This helps to maintain data consistency.
Transactionality: The unit of work can be used to manage transactions, which ensures that a set of operations is atomic and isolated.
Drawbacks:
Complexity: The unit of work can add complexity to code, especially if it is not used correctly.
Performance: The unit of work can add some overhead to code, especially if it is used in a high-performance application.
Controller Routing

Benefits:
Flexibility: Controller routing is very flexible, and it can be used to create a wide variety of URL structures.
SEO: Controller routing can be used to create SEO-friendly URLs.
Maintainability: Controller routing is relatively easy to maintain, especially if you use attribute routing.
Drawbacks:
Complexity: Controller routing can be complex, especially if you use programmatic routing.
Verbosity: Attribute routing can be verbose, especially if you use a lot of attributes.
Additional Considerations

Use the unit of work only when you need it. The unit of work is a powerful tool, but it should not be used for every data access operation. Only use the unit of work when you need to manage a set of operations that are related to each other.
Keep your controller routing simple. Don't try to create overly complex URL structures. Simple URL structures are easier to understand and maintain.
Use attribute routing if you can. Attribute routing is generally easier to use and maintain than programmatic routing.