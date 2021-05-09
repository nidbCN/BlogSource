title: '如何创建ASP.NET Core Web API——准备工作——ASP.NET Core Web API(1)'
author: Gaein nidb
categories:
  - 代码如诗
tags:
  - CSharp
  - WebAPI
  - dotNET
  - AspNetCore
  - 编程
  - 笔记
  - 后端
date: 2021-1-30 18:47:49

---

ASP.NET Core 构建 RESTful API 学习笔记

<!--more-->

## 前言

> 根据杨旭老师在哔哩哔哩上的课程 [ASP.NET Core 3.x 构建 RESTful API](https://www.bilibili.com/video/BV1XJ411q7yy) 记录的笔记，以后的笔记会根据课程进行更新。

> 这一节主要是做一些准备工作，进行项目的创建。杨旭老师的教程里根据课程而编写了一个 RESTful Web API，我也将同步打代码，相关代码会储存在 GitHub 上。

## Get Start

#### 准备工具

###### IDE

1. Visual Studio 2019
2. 或 Rider、Visual Studio Code、VS for MAC

###### 工具

1. Postman

#### Web API

API:应用程序接口
Web API:使用 HTTP 协议并通过网络调用的 API
Web API 就是一个 Wb 系统，通过访问 URI 可以与其进行信息交互。

###### RESTful API

RESTful API 是一套 API 约束规范或者叫架构凤风格

> 大多数的 Web API 并不是完全符合 RESTful API

###### MVC 模式

> 前面我 MVC 的理解可能不太正确，它并不是一种前后端不分离的模式。MVC 并不是一个完整的应用程序架构，它只是 UI 的一种架构模式。

用于构建 UI 的一种架构模式，特点是松耦合和关注点分离。

######## Model——模型

负责程序数据的逻辑

######## View——视图

负责展示数据，在构建 API 的时候 View 就是数据或资源的展示，通常来说是 JSON 格式的文本。

######## Controller——控制器

负责视图和模型之间的交互。包括 API 消费者传入和请求的数据。

###### 创建项目

模板选择“ASP.NET Core Web 应用程序”，项目模板选择“.NET Core”、“ASP.NET Core 5.0”和“ASP.NET Core Web API”

######## Program.cs

程序入口，创建 HostBuilder，调用 StartUp.cs

######## StartUp.cs

构造函数注入了应用程序的设置 Configuration

方法 `ConfigureServices` 用来注册服务：

```cs
// 添加一个Singleton的服务
services.AddSingleton<接口, 实现>();
```

生命周期分为三种：

1. `Transient` : 每次被请求这个服务的时候都会生成一个新的实例；
2. `Scoped` : 每次 Web 请求的时候生成一个新的实例，生命周期到 Web 请求最终处理完；
3. `Singleton` : 一旦被创建，每次请求都是这一个实例；

方法 `Configure` 用来加入管道中间件：
```cs
// 加入管道中间件路由
app.UseRouting();
```

管道概念是HTTP请求通过的一个“管子”，中间穿插有各种中间件进行处理，比如身份认证、路由等。

如果某个中间件处短路，则不会递给下一中间件而直接返回这个中间件对它处理的结果。比如身份验证未通过等。

如果一切正常则会在管道里一直走，到最后一个中间件进行处理，然后从管道里返回。

**中间件的顺序是非常重要的**

默认的启动配置是在 `./Properties/launchSettings.json` 中

#### 数据存储

> 这里ORM框架使用EF（EntityFrameworkCore），数据库使用SQLite
###### 安装Nuget包
1. Microsoft.EntityFrameworkCore.Sqlite
2. Microsoft.EntityFrameworkCore.Tools

###### 建立Model

1. 创建文件夹 `Entities`；
2. 创建模型类  
   可以键入`prop`然后按两下`TAB`键使用代码片段自动补全字段
   ```cs
    public class Company
    {
        public Guid Id { get; set; }
        public string Name { get; set; }
        public string Introduction { get; set; }
        public ICollection<Employee> Employees { get; set; }
    }

    public class Employee
    {
        // 主键
        public Guid Id { get; set; }
        // 外键
        public Guid CompanyId { get; set; }
        public string EmployeeNo { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public Gender Gender { get; set; }
        public DateTime DateOfBirth { get; set; }
        public Company Company { get; set; }
    }

    public enum Gender
    {
        Man, WoMan
    }
   ```

###### 建立数据库上下文

1. 创建文件夹 `Data`
2. 创建类 `RoutineDbContext` ，继承于 `DbContext` ，构造函数需要包含参数。并且要调用父类的构造函数将其传入。
   ```cs
    public RoutineDbContext(DbContextOptions<RoutineDbContext> options): base(options)
    {

    }
   ```
   添加 `DbSet` 属性
   ```cs
    public DbSet<Company> Companies { get; set; }

    public DbSet<Employee> Employees { get; set; }
   ```
   对于实体一些属性的限制（比如是否必填等），通过重写 `OnModelCreating` 来操作
   ```cs
    // 对实体的限制
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        // 设置名称为必填
        modelBuilder.Entity<Company>()
            .Property(it => it.Name).IsRequired();
        // 设置简介的长度限制
        modelBuilder.Entity<Company>()
            .Property(it => it.Introduction).HasMaxLength(500);

        modelBuilder.Entity<Employee>()
                .Property(it => it.Id).IsRequired();
        modelBuilder.Entity<Employee>()
                .Property(it => it.FirstName).IsRequired();
        modelBuilder.Entity<Employee>()
                .Property(it => it.LastName).IsRequired();

        // 设置实体的关系
        // Employee 对应一个 Company，一个 Company 对应多个 Employee。外键为CompanyId
        // OnDelete 是删除时如果 Company 有 Employee 则无法删除
        modelBuilder.Entity<Employee>()
            .HasOne(it => it.Company)
            .WithMany(it => it.Employees)
            .HasForeignKey(it => it.CompanyId)
            .OnDelete(DeleteBehavior.Restrict);

        // 初始化添加一些成员
        modelBuilder.Entity<Company>()
            .HasData(
                new Company
                {
                    Id = Guid.NewGuid(),
                    Name = "Microsoft",
                    Introduction = "巨硬公司"
                }
            );
    }
   ```

###### 创建服务

1. 创建文件夹 `Services`
2. 创建一个接口 `ICompanyRepository`   
   写入
   ```cs
    public interface ICompanyRepository
    {
        Task<IEnumerable<Company>> GetCompaniesAsync();
        Task<Company> GetCompanyAsync(Guid companyId);
        Task<IEnumerable<Company>> GetCompaniesAsync(IEnumerable<Guid> companyIds);
        void AddCompany(Company company);
        void UpdateCompany(Company company);
        void DeleteCompany(Company company);
        Task<bool> CompanyExistsAsync(Guid companyId);

        Task<IEnumerable<Employee>> GetEmployeesAsync(Guid companyId);
        Task<Employee> GetEmployeeAsync(Guid companyId, Guid employeeId);
        void AddEmployee(Guid companyId, Employee employee);
        void UpdateEmployee(Employee employee);
        void DeleteEmployee(Employee employee);

        Task<bool> SaveAsync();
    }
   ```
3. 创建接口的实体类 `CompanyRepository` 写入
   ```cs
    public class CompanyRepository : ICompanyRepository
    {
        private readonly RoutineDbContext _context;

        public CompanyRepository(RoutineDbContext context)
        {
            _context = context ?? throw new ArgumentNullException(nameof(context));
        }
        public async Task<IEnumerable<Company>> GetCompaniesAsync()
        {
            return await _context.Companies.ToListAsync();
        }

        public async Task<Company> GetCompanyAsync(Guid companyId)
        {
            if (companyId == Guid.Empty)
            {
                throw new ArgumentNullException(nameof(companyId));
            }

            return await _context.Companies
                .FirstOrDefaultAsync(it => it.Id == companyId);
        }

        public async Task<IEnumerable<Company>> GetCompaniesAsync(IEnumerable<Guid> companyIds)
        {
            if (companyIds == null)
            {
                throw new ArgumentNullException(nameof(companyIds));
            }

            return await _context.Companies
                .Where(it => companyIds.Contains(it.Id))
                .OrderBy(it => it.Name)
                .ToListAsync();

        }

        public void AddCompany(Company company)
        {
            if (company == null)
            {
                throw new ArgumentNullException(nameof(company));
            }

            company.Id = new Guid();

            foreach (var employee in company.Employees)
            {
                employee.Id = new Guid();
            }

            _context.Companies.Add(company);
        }
        public void UpdateCompany(Company company)
        {
            // 实际上EF对实体都有追踪，不需要显示的更改它们

            /*
            if (company == null)
            {
                throw new ArgumentNullException(nameof(company));
            }

            _context.Entry(company).State = EntityState.Modified;
            */
        }

        public void DeleteCompany(Company company)
        {
            if (company == null)
            {
                throw new ArgumentNullException(nameof(company));
            }

            _context.Companies.Remove(company);
        }

        public async Task<bool> CompanyExistsAsync(Guid companyId)
        {
            if (companyId == Guid.Empty)
            {
                throw new ArgumentNullException(nameof(companyId));
            }

            return await _context.Companies.AnyAsync(it => it.Id == companyId);
        }

        public async Task<IEnumerable<Employee>> GetEmployeesAsync(Guid companyId)
        {
            if (companyId == Guid.Empty)
            {
                throw new ArgumentNullException(nameof(companyId));
            }

            return await _context.Employees
                .Where(it => it.CompanyId == companyId)
                .OrderBy(it => it.EmployeeNo)
                .ToListAsync();
        }

        public async Task<Employee> GetEmployeeAsync(Guid companyId, Guid employeeId)
        {
            if (companyId == Guid.Empty)
            {
                throw new ArgumentNullException(nameof(companyId));
            }

            if (employeeId == Guid.Empty)
            {
                throw new ArgumentNullException(nameof(employeeId));
            }

            return await _context.Employees
                .Where(it => it.CompanyId == companyId && it.Id == employeeId)
                .FirstOrDefaultAsync();
        }
        public void AddEmployee(Guid companyId, Employee employee)
        {
            if (companyId == Guid.Empty)
            {
                throw new ArgumentNullException(nameof(companyId));
            }

            if (employee == null)
            {
                throw new ArgumentNullException(nameof(employee));
            }

            _context.Employees.Add(employee);
        }

        public void UpdateEmployee(Employee employee)
        {
            // 同样，EF 会追踪其中的对象，不用做更改。
        }

        public void DeleteEmployee(Employee employee)
        {
            if (employee == null)
            {
                throw new ArgumentNullException(nameof(employee));
            }

            _context.Remove(employee);
        }

        public async Task<bool> SaveAsync()
        {
            // 通常保存不写在Repository里
            return await _context.SaveChangesAsync() >= 0;
        }
    }
   ```
   我觉得可以把 `_context.Employees` 等 **看作** 一个 `List<Employee>` 类型的变量。
4. 在 `StartUp` 中注册服务
   ```cs
    // 注册服务  Scoped: 每次HTTP请求实例化一次
    services.AddScoped<ICompanyRepository, CompanyRepository>();

    // 连接字符串
    var connectionStr = @"Data Source=test.db;";

        // 注册数据库上下文
    services.AddDbContext<RoutineDbContext>(option =>
            option.UseSqlite(connectionStr)
    );
   ```

###### 初始化数据库

在Nuget包管理器控制台中依次输入：
```sh
Add-Migration InitialCreate
```
和
```sh
Update-Database
```

> !重要：杨旭老师在这里对Program.cs进行了一些修改，以便于后期讲解，我没有进行这些更改，弹幕说是个坑，先记住，如果出了什么奇怪问题就回来看看。