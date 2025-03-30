# Prometheus TSDB 2个月深度学习计划

## 📅 第一阶段：存储模型剖析（2周）
### 目标：掌握Prometheus TSDB核心架构与存储模型

**Week 1**  
- **Day 1-3**：官方文档精读  
  - [Prometheus官方文档 - Storage章节](https://prometheus.io/docs/prometheus/latest/storage/)  
  - 重点：LSM-Tree变种结构、Block-Chunk模型  
- **Day 4-5**：书籍学习  
  - 《深入浅出Prometheus》第5章“存储机制”  
  - 《Prometheus技术秘笈》第8章“TSDB深度解析”  
- **Day 6-7**：扩展阅读  
  - 技术博客：[Prometheus TSDB Internals](https://ganeshvernekar.com/blog/prometheus-tsdb-the-head-block/)  
  - 对比分析：[InfluxDB vs Prometheus存储模型](https://www.influxdata.com/blog/influxdb-vs-prometheus/)  

**Week 2**  
- **Day 1-3**：源码分析  
  - TSDB源码目录：[GitHub源码](https://github.com/prometheus/prometheus/tree/main/tsdb)  
  - 《Prometheus技术秘笈》第9章“源码解析” + `tsdb/head.go`分析  
- **Day 4-5**：实践验证  
  - 使用`tsdb`工具创建数据库（[工具文档](https://prometheus.io/docs/prometheus/latest/storage/#tooling)）  
  - 生成测试数据并观察目录结构（`wal`与`chunks`）  
- **Day 6-7**：总结输出  
  - 绘制TSDB写入/查询流程图  
  - 整理Block Metadata格式（[Meta.json结构](https://prometheus.io/docs/prometheus/latest/storage/#on-disk-layout)）  

---

## 🔧 第二阶段：核心实现剖析（3周）
### 目标：掌握压缩模块源码实现与性能关键点

**Week 3**  
- **Day 1-3**：压缩流程分析  
  - 《深入浅出Prometheus》第6章 + `tsdb/compact.go`的`Compact()`函数跟踪  
- **Day 4-5**：数据结构解析  
  - `Block`结构体（源码：`tsdb/block.go`）  
  - [Chunk编码规范](https://prometheus.io/docs/prometheus/latest/storage/#chunk-encoding)  
- **Day 6-7**：接口与优化点  
  - `HeadBlock` vs `PersistedBlock`接口对比  

**Week 4**  
- **Day 1-3**：压缩算法拆解  
  - [Vertical/Horizontal Compaction源码注释](https://github.com/prometheus/prometheus/blob/main/tsdb/compact.go#L47-L63)  
- **Day 4-5**：性能指标分析  
  - 使用`tsdb analyze`命令统计chunk合并率  
  - `go tool pprof`监控GC行为  
- **Day 6-7**：数据流图  
  - 用PlantUML绘制压缩状态机（参考《技术秘笈》P217）  

**Week 5**  
- **Day 1-3**：参数实验  
  - 调整`compaction.Concurrency`参数（[配置文档](https://prometheus.io/docs/prometheus/latest/configuration/configuration/)）  
- **Day 4-7**：分析报告  
  - 输出压缩模块瓶颈文档（含函数调用树）  

---

## 📊 第三阶段：压缩性能测试（2周）
### 目标：验证压缩性能并可视化结果

**Week 6**  
- **Day 1-2**：环境部署  
  - 在AWS EC2部署Prometheus v2.40+  
  - 使用`promtool tsdb create-blocks-from`导入数据  
- **Day 3-5**：基准测试  
  - [tsdb-bench工具](https://github.com/prometheus-community/tsdb-bench)  
  - 记录`du -sh data/`原始与压缩体积  
- **Day 6-7**：指标采集  
  - 修改源码添加`time.Since()`日志  

**Week 7**  
- **Day 1-3**：参数调优  
  - 调整`--storage.tsdb.max-block-chunk-segment-size`  
  - 对比XOR vs Delta编码  
- **Day 4-7**：数据可视化  
  - [Grafana压缩比仪表盘](https://grafana.com/grafana/dashboards/12611)  

---

## 🚀 第四阶段：优化方案设计（1周）
### 目标：提出可落地的性能优化方案

**Week 8**  
- **Day 1-2**：瓶颈定位  
  - 生成pprof火焰图（[教程](https://blog.golang.org/pprof)）  
- **Day 3-5**：方案设计  
  - 参考论文《Time-series Compression Algorithms: A Survey》[PDF](https://www.vldb.org/pvldb/vol13/p1491-li.pdf)  
- **Day 6-7**：验证计划  
  - 设计A/B测试方案  
  - 编写优化提案（参考[Prometheus改进提案模板](https://github.com/prometheus/prometheus/tree/main/tsdb/docs/design)）  

---

## 📌 关键资源索引
### 书籍章节
- 《深入浅出Prometheus》第5-6章  
- 《Prometheus技术秘笈》第8-9章  

### 源码文件
- 存储核心：`tsdb/head.go`, `tsdb/block.go`  
- 压缩模块：`tsdb/compact.go`, `tsdb/chunkenc/`  

### 工具与数据
- 数据生成工具：[prometheus/sample-generator](https://github.com/prometheus/client_golang/tree/main/examples/random)  
- 性能分析工具：[go-pprof](https://pkg.go.dev/net/http/pprof)  

---

## ⚙️ 执行建议
- **每日投入**：工作日3小时+周末6小时  
- **推荐工具**：  
  - IDE：GoLand/VSCode（Go插件）  
  - 文档管理：Obsidian/Typora  
- **检查点**：每周提交学习笔记至GitHub仓库（Markdown格式）  