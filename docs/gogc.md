---
hide:
  - toc
---

# Go GC 策略

<style>
.gc-guide-graph {
  display: inline-block;
  position: relative;
  width: 100%;
  vertical-align: top;
  overflow: hidden;
}

.gc-guide-graph-controls {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  justify-content: space-around;
  align-items: center;
  width: 100%;
}

.gc-guide-graph-controls div {
  display: flex;
  flex-direction: row;
  flex-wrap: nowrap;
  align-items: center;
  padding-left: 5px;
  padding-right: 5px;
  height: 24px;
  width: auto;
}

.gc-guide-equation {
  display: block;
  text-align: center;
}
</style>

Go 程序当前支持两种 GC 策略，[官网](https://tip.golang.org/doc/gc-guide) 也有非常直观的图示表明了这两种策略间的关系：

| 策略                                                                          | 默认 | 修改方法                                                                                                                                                                  |
| --------------------------------------------------------------------------- | --- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **[GOGC](https://tip.golang.org/doc/gc-guide#GOGC)**<br/>每次 GC 后计算目标堆内存，下次达到目标堆内存时触发 GC，其中 \\[ 目标堆内存 = 活跃堆 + \frac{(活跃堆 + GC根) \times GOGC}{100} \\] | 100 | **环境变量：** `GOGC=80`，关闭时 `GOGC=off`<br/>**代码方式：** `debug.SetGCPercent(80)`，关闭时 `debug.SetGCPercent(-1)`                                                                       |
| **[Memory Limit](https://tip.golang.org/doc/gc-guide#Memory_limit)**<br/>限制 Go 运行时可使用的内存总量（Sys - HeapReleased）软上限，超过时触发 GC                         | 无   | **环境变量：** `GOMEMLIMIT=200MiB` 支持 `B, KiB, MiB, GiB, TiB` 后缀<br/>**代码方式：** `debug.SetMemoryLimit(200*1024*1024)` 单位 byte，关闭时 `debug.SetMemoryLimit(math.MaxInt64)` |

<div class="gc-guide-graph" data-workload='[
 {"duration": 1.0, "allocRate": 20, "scanRate": 1024, "newSurvivalRate": 1.00, "oldDeathRate": 0.00},
 {"duration": 4.0, "allocRate": 20, "scanRate": 1024, "newSurvivalRate": 0.00, "oldDeathRate": 0.00},
 {"duration": 0.5, "allocRate": 20, "scanRate": 1024, "newSurvivalRate": 1.00, "oldDeathRate": 0.00},
 {"duration": 0.5, "allocRate": 20, "scanRate": 1024, "newSurvivalRate": 0.00, "oldDeathRate": 0.00},
 {"duration": 0.5, "allocRate": 20, "scanRate": 1024, "newSurvivalRate": 0.00, "oldDeathRate": 0.50},
 {"duration": 3.5, "allocRate": 20, "scanRate": 1024, "newSurvivalRate": 0.00, "oldDeathRate": 0.00}
]' data-config='{
 "fixedCost": 0.04,
 "otherMem": 0,
 "GOGC": "graph5-gogc",
 "memoryLimit": "graph5-memlimit"
}'></div>
<div class="gc-guide-graph-controls">
 <div>
  GOGC
  <input type="range" min="0" max="10" step="0.005" value="6.64" id="graph5-gogc">
  <div class="gc-guide-counter" id="graph5-gogc-display"></div>
 </div>
 <div>
  Memory Limit
  <input type="range" min="1" max="100" step="0.5" value="100" id="graph5-memlimit">
  <div class="gc-guide-counter" id="graph5-memlimit-display"></div>
 </div>
</div>

按照策略的默认值，如果某次 GC 回收后活跃堆（live heap）较大（比如 30MB），因为 `GOGC=100`，目标堆经过计算得到约 60MB，而堆达到 60MB 时早已触发了容器 OOM，根本没有机会进行 GC。

所以在容器场景下最佳实践是设置 GOMEMLIMIT 软限制，但软限制太小会导致频繁 GC，建议是最大堆 +5% ~ +10% 的预留余量，或者容器 memory limit 的 80% ~ 95%。**<font color=red>注意不能等于容器的内存 limit，会导致未 GC 先 OOM。</font>**

另外可以在环境变量中设置 `GODEBUG=gctrace=1` 帮助定位，这样会在 go 程序的**标准错误流**里输出每次 GC 的日志。

<script src="https://tip.golang.org/js/d3.js"></script>
<script async src="https://tip.golang.org/doc/gc-guide.js"></script>
