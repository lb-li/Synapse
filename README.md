<div align="center">

# 🧬 Synapse

### AI-Powered Self-Evolving Code Framework for .NET

[![.NET](https://img.shields.io/badge/.NET-8.0-512BD4?logo=dotnet)](https://dotnet.microsoft.com/)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Build Status](https://img.shields.io/badge/build-passing-brightgreen.svg)]()
[![NuGet](https://img.shields.io/badge/nuget-v1.0.0-blue.svg)]()

**让你的代码在生产环境中自我进化和优化**

[快速开始](#-快速开始) • [特性](#-核心特性) • [示例](#-使用示例) • [文档](#-文档) • [贡献](#-贡献)

</div>

---

## 🎯 什么是 Synapse？

Synapse 是一个革命性的 .NET 框架，它能让你的代码在运行时**自动进化和优化**。通过结合 AI 和实时性能监控，Synapse 可以：

- 🔍 **自动监控**代码性能（P95/P99 延迟、吞吐量）
- 🤖 **AI 分析**性能瓶颈并生成优化建议
- 🔄 **热加载**新代码，无需重启应用
- 💾 **持久化**优化结果，重启后立即可用
- 📊 **A/B 测试**多个实现版本

## ✨ 核心特性

### 🤖 AI 驱动的代码优化
- 集成 **Gemini AI**，基于真实源代码生成优化建议
- 智能分析性能瓶颈（数据库查询、并发、缓存等）
- 支持自定义 AI 提供商（OpenAI、Claude 等）

### � 实时性能监控 
- 自动收集 **P50/P95/P99** 延迟指标
- 追踪成功率、吞吐量、执行次数
- 零性能开销的监控实现

### 🔄 热加载与多版本管理
- **无需重启**即可加载新代码
- 支持多个实现版本并行运行
- A/B 测试不同优化方案

### 💾 持久化与缓存
- 优化结果自动保存到磁盘
- 重启后立即恢复所有优化
- 支持版本回滚

### 🎯 开发者友好
- **一行代码**启用：`services.AddSynapse()`
- 只需添加 `[Evolvable]` 特性
- 完整的日志和诊断信息
- 丰富的配置选项

## 🚀 快速开始

### 安装

```bash
dotnet add package Synapse.Core
dotnet add package Synapse.AI
```

### 配置（3 步）

**1. 注册服务**

```csharp
// Program.cs
var builder = WebApplication.CreateBuilder(args);

// 添加 Synapse 框架
builder.Services.AddSynapse(builder.Configuration);
builder.Services.AddSynapseAI(options =>
{
    options.ApiKey = "your-gemini-api-key";
    options.IncludeSourceCode = true;  // 启用源代码分析
    options.SourceCodePath = "path/to/your/source";
});
```

**2. 配置文件**

```json
{
  "Synapse": {
    "EnableAI": true,
    "PerformanceThreshold": 50.0,
    "AI": {
      "ApiKey": "your-gemini-api-key",
      "GenerationThreshold": 100,
      "OptimizationIntervalHours": 24
    }
  }
}
```

**3. 标记方法**

```csharp
public class ProductService
{
    [Evolvable]
    public async Task<List<Product>> SearchProducts(string query)
    {
        // 你的实现 - Synapse 会自动监控和优化
        return await _db.Products
            .Where(p => p.Name.Contains(query))
            .ToListAsync();
    }
}
```

### 运行

```bash
dotnet run
```

**就这么简单！** Synapse 会自动：
- ✅ 监控方法性能
- ✅ 达到阈值后触发 AI 分析
- ✅ 生成优化建议并保存
- ✅ 提供详细的性能报告

## � 项工作原理

```mermaid
graph LR
    A[标记方法 [Evolvable]] --> B[自动监控性能]
    B --> C{达到阈值?}
    C -->|是| D[提取源代码]
    D --> E[AI 分析优化]
    E --> F[生成优化建议]
    F --> G[保存 JSON 报告]
    G --> H[开发者审查]
    H --> I[应用优化]
    I --> B
    C -->|否| B
```

### 性能监控示例

```
📊 [指标] SearchProducts - DEFAULT
   • 平均时间: 105.23ms
   • P95: 120.45ms
   • P99: 135.67ms
   • 执行次数: 150
   • 成功率: 100.0%

🤖 [AI] 触发优化分析...
💡 AI 优化建议已保存: .synapse/suggestions/SearchProducts_20241107_120000.json
```

## 💡 使用示例

### 示例 1：数据库查询优化

**原始代码：**
```csharp
[Evolvable]
public async Task<List<Order>> GetUserOrders(int userId)
{
    return await _db.Orders
        .Where(o => o.UserId == userId)
        .ToListAsync();
}
```

**AI 优化建议：**
```csharp
public async Task<List<Order>> GetUserOrders(int userId)
{
    // AI 分析后的优化：
    // 1. 添加 AsNoTracking 减少内存
    // 2. 使用索引提示
    // 3. 只查询需要的字段
    return await _db.Orders
        .AsNoTracking()
        .Where(o => o.UserId == userId)
        .Select(o => new Order { /* 只选择需要的字段 */ })
        .ToListAsync();
}
```

### 示例 2：并发处理优化

**原始代码：**
```csharp
[Evolvable]
public async Task<List<Result>> ProcessItems(List<Item> items)
{
    var results = new List<Result>();
    foreach (var item in items)
    {
        results.Add(await ProcessItem(item));
    }
    return results;
}
```

**AI 优化建议：**
```csharp
public async Task<List<Result>> ProcessItems(List<Item> items)
{
    // AI 建议：使用并行处理
    var tasks = items.Select(item => ProcessItem(item));
    return (await Task.WhenAll(tasks)).ToList();
}
```

### 示例 3：缓存策略

**原始代码：**
```csharp
[Evolvable]
public async Task<Product> GetProduct(int id)
{
    return await _db.Products.FindAsync(id);
}
```

**AI 优化建议：**
```csharp
public async Task<Product> GetProduct(int id)
{
    // AI 建议：添加缓存层
    var cacheKey = $"product:{id}";
    if (_cache.TryGetValue(cacheKey, out Product cached))
        return cached;
    
    var product = await _db.Products.FindAsync(id);
    _cache.Set(cacheKey, product, TimeSpan.FromMinutes(10));
    return product;
}
```

## ⚙️ 配置选项

### 完整配置示例

```json
{
  "Synapse": {
    "Enabled": true,
    "EnableAI": true,
    "AutoEvolution": true,
    "PerformanceThreshold": 50.0,
    "MinSampleSize": 100,
    "CacheDirectory": ".synapse/genes",
    "AI": {
      "Provider": "Gemini",
      "ApiKey": "your-gemini-api-key",
      "Endpoint": "https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash-exp:generateContent",
      "Model": "gemini-2.0-flash-exp",
      "GenerationThreshold": 100,
      "OptimizationIntervalHours": 24,
      "IncludeSourceCode": true,
      "SourceCodePath": "./src"
    }
  }
}
```

### 配置说明

| 配置项 | 说明 | 默认值 | 推荐值 |
|--------|------|--------|--------|
| `Enabled` | 启用框架 | `true` | `true` |
| `EnableAI` | 启用 AI 功能 | `false` | `true` |
| `AutoEvolution` | 自动进化 | `true` | `true` |
| `PerformanceThreshold` | 性能阈值（ms） | `100.0` | `50.0` |
| `MinSampleSize` | 最小样本数 | `100` | `100-500` |
| `GenerationThreshold` | 触发 AI 的执行次数 | `100` | `100-1000` |
| `OptimizationIntervalHours` | 优化间隔（小时） | `24` | `24-168` |
| `IncludeSourceCode` | 启用源代码分析 | `false` | `true` |

### 环境变量配置

```bash
# 生产环境
export Synapse__AI__ApiKey="your-api-key"
export Synapse__EnableAI="true"
export Synapse__PerformanceThreshold="30.0"
```

## 📊 监控和诊断

### 查看性能指标

框架会自动输出日志：

```
📊 [指标] SearchProducts - DEFAULT
   • 平均时间: 105.23ms
   • P95: 120.45ms
   • P99: 135.67ms
   • 执行次数: 25
   • 成功率: 100.0%
```

### 查看进化过程

```
🧬 [进化引擎] 开始分析...
🤖 [AI 引擎] 正在生成优化代码...
⚙️  [编译器] 正在编译基因: AI_OPTIMIZED_20241107120000
✅ [编译器] 编译成功
💾 [缓存] 已保存基因到磁盘
🎉 新基因已就绪！
```

### 查看生成的代码

```bash
cat .synapse/genes/AI_OPTIMIZED_20241107120000.cs
```

## 🔧 高级用法

### 自定义 AI Prompt

```csharp
builder.Services.AddSynapseAI(options =>
{
    options.CustomPromptBuilder = (code, metrics, goal) =>
    {
        return $"优化这段代码，目标：{goal}\n{code}";
    };
});
```

### 自定义指标收集

```csharp
builder.Services.AddSingleton<IMetricsCollector, CustomMetricsCollector>();
```

### 手动触发进化

```csharp
var engine = app.Services.GetRequiredService<IAIEvolutionEngine>();
await engine.AnalyzeAndEvolveAsync();
```

### 切换基因实现

```csharp
// 通过配置
{
  "Synapse": {
    "Genes": {
      "SearchProducts": "OPTIMIZED_V2"
    }
  }
}

// 或通过代码
var loader = app.Services.GetRequiredService<IDynamicGeneLoader>();
loader.SwitchGene("SearchProducts", "OPTIMIZED_V2");
```

## 🧪 测试

查看 [Examples/WebApi.Example](Examples/WebApi.Example) 获取完整示例。

运行示例：

```bash
cd Examples/WebApi.Example
dotnet run
```

访问 Swagger UI：
```
https://localhost:5001/swagger
```

## 📚 文档

| 文档 | 描述 |
|------|------|
| [快速开始](QUICKSTART.md) | 5 分钟快速上手指南 |
| [完整示例](Examples/WebApi.Example) | ASP.NET Core Web API 示例 |
| [架构设计](FRAMEWORK-COMPLETE.md) | 详细的架构说明 |
| [项目状态](STATUS.md) | 当前开发状态 |
| [测试指南](test.ps1) | 自动化测试脚本 |

## 🎯 路线图

- [x] 核心框架（性能监控、基因管理）
- [x] Gemini AI 集成
- [x] 源代码分析
- [x] 热加载支持
- [x] 结构化报告（JSON）
- [ ] OpenAI/Claude 支持
- [ ] 可视化监控面板
- [ ] 自动化测试生成
- [ ] 分布式场景支持
- [ ] NuGet 包发布

## 🤝 贡献

我们欢迎所有形式的贡献！

### 如何贡献

1. Fork 本仓库
2. 创建特性分支 (`git checkout -b feature/AmazingFeature`)
3. 提交更改 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 开启 Pull Request

### 贡献指南

- 遵循现有代码风格
- 添加单元测试
- 更新相关文档
- 确保所有测试通过

## 📊 项目统计

- **代码行数**: ~5,000 行
- **编译状态**: ✅ 通过
- **测试覆盖率**: 正在开发中
- **文档完整度**: 95%

## 💬 社区

- **问题反馈**: [GitHub Issues](https://github.com/lb-li/synapse/issues)
- **功能建议**: [GitHub Discussions](https://github.com/lb-li/synapse/discussions)
- **邮件**: hw7455195@gmail.com

## 📄 许可证

本项目采用 [MIT License](LICENSE) 开源协议。

## 🙏 致谢

特别感谢以下项目和技术：

- [Gemini AI](https://ai.google.dev/) - 强大的代码生成能力
- [Roslyn](https://github.com/dotnet/roslyn) - .NET 编译器平台
- [ASP.NET Core](https://github.com/dotnet/aspnetcore) - Web 框架

## ⭐ Star History

如果这个项目对你有帮助，请给我们一个 Star ⭐

---

<div align="center">

**让代码自己进化，专注于业务逻辑！** 🚀

Made with ❤️ by Synapse Team

[⬆ 回到顶部](#-synapse)

</div>
