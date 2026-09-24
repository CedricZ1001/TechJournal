# UE 一帧画面是怎样生成的？从游戏线程到渲染线程的学习记录

> 作者：[CedricZ1001](https://github.com/CedricZ1001) · 状态：大纲 / 学习中
>
> 开始记录：2026-09-24 · 最近更新：2026-09-24
>
> 发布日期 / 知乎链接：未发布
>
> 本文目前用于组织学习和实验，尚未完成源码追踪或验证。

## 要回答的问题

在 Tick 中移动一个物体后，这次位置变化如何从游戏世界的数据，经过游戏线程、渲染侧和 GPU，最终出现在屏幕上？

文章以一个持续移动的立方体为例，重点记录游戏线程和渲染线程之间的数据交接，再补齐 RHI、GPU 和呈现环节。

## 实验环境

| 项目 | 记录 |
| --- | --- |
| UE 版本 / 源码提交 | 待记录 |
| 操作系统 / GPU | 待记录 |
| 图形 API | 待记录 |
| 渲染路径 | 计划使用桌面端延迟渲染，实际配置待记录 |
| 测试对象 | 计划使用非 Nanite 静态网格立方体，Mobility 设为 Movable |
| 运行方式 | 待记录：编辑器、独立运行或打包程序 |
| RHI 线程及相关配置 | 待记录 |
| 实验日期 | 待记录 |

## 正文大纲

### 1. 从一次位置变化出发

- 展示最小场景：相机、灯光、立方体，以及 Tick 中的位置更新代码。
- 说明需要追踪的是“位置数据变化如何影响画面”，列出本次实验的配置和边界。
- 待回答：改变 Transform 后，哪些数据已经变化，哪些渲染工作还没有发生？

### 2. 游戏线程怎样推进一帧

- 记录主循环、World Tick、Actor / Component Tick 与视口绘制入口之间的关系。
- 检索入口：`FEngineLoop::Tick`、`UWorld::Tick`、`UGameViewportClient::Draw`。
- 待回答：物体状态和相机状态分别在哪里更新？本次绘制请求从哪里发起？
- 待补材料：选定版本的源码路径、关键调用栈和实际执行线程。

### 3. 游戏对象如何获得渲染表示

- 学习 `UPrimitiveComponent`、`FPrimitiveSceneProxy`、`FScene` 的职责与关系。
- 追踪组件注册、渲染表示建立和移除过程。
- 待回答：渲染侧需要哪些信息？这些信息由谁持有，何时释放？
- 待回答：哪些数据只在状态改变时更新，哪些工作需要逐帧进行？

### 4. 一次 Transform 更新怎样跨过线程边界

- 检索入口：`MarkRenderTransformDirty`、`SendRenderTransform_Concurrent`、`UpdatePrimitiveTransform`、`ENQUEUE_RENDER_COMMAND`。
- 记录脏标记、延迟更新、命令入队与执行之间的实际关系。
- 待回答：传递的是副本、引用还是其他表示？怎样保证数据与对象的生命周期？
- 待回答：哪些步骤可以并发？分别执行在哪些线程？
- 待补材料：一张带有线程归属、数据所有者和交接位置的数据流图。

### 5. 渲染侧怎样组织可见物体的绘制

- 学习 View / ViewFamily、可见性、Mesh Pass，以及 `FMeshBatch` 和 `FMeshDrawCommand`。
- 记录已有绘制数据的缓存、更新，以及工作任务参与的位置。
- 待回答：立方体参与哪些 Pass？哪些绘制描述可以复用？
- 待回答：哪些任务由渲染线程组织，又有哪些任务交给工作线程执行？

### 6. 多个 Pass 如何形成最终图像

- 结合抓帧结果介绍深度、GBuffer、光照和后处理的输入与输出。
- 学习 RDG 如何描述 Pass 与资源依赖，关注 `FRDGBuilder` 的使用位置。
- 渲染器检索入口：`FDeferredShadingSceneRenderer::Render`。
- 待回答：中间纹理如何被后续阶段使用？哪些依赖影响执行顺序？

### 7. 从 RHI 命令到 GPU，再到呈现

- 学习 RHI、命令列表、图形 API、GPU 队列、交换链与 Present 的职责。
- 记录本次配置下，命令记录、转换和提交分别在哪里进行。
- 待回答：CPU 完成命令处理、GPU 完成执行、画面显示之间有什么区别？
- 待回答：本次环境是否启用独立 RHI 线程？它在时间线上承担什么工作？

### 8. 用时间线重新看“一帧”

- 对照 Unreal Insights，标出更新、渲染准备、任务执行和等待位置。
- 待回答：线程与 GPU 如何重叠工作？等待在等谁？
- 对每个结论注明证据，并区分本次观察结果与需要进一步验证的推测。
- 待补材料：学习中修正的理解，以及暂时没有追踪清楚的问题。

## 按顺序完成的学习任务

- [ ] 确定 UE 版本和实验配置，补齐环境表。
- [ ] 补齐 CPU / GPU、缓冲区、Shader、Draw Call、Render Target 与光栅化基础。
- [ ] 搭建最小场景，保存可复现的位置更新代码和运行步骤。
- [ ] 追踪游戏主循环与 Tick，记录关键入口和线程归属。
- [ ] 追踪组件注册及渲染表示的创建、更新、释放。
- [ ] 跟踪一次 Transform 变化，补齐跨线程数据交接图。
- [ ] 学习可见性、Mesh Pass、绘制命令与缓存关系。
- [ ] 学习主要渲染 Pass 和 RDG 的资源依赖。
- [ ] 追踪 RHI 命令提交与呈现，核对平台配置。
- [ ] 录制正常运行的 Unreal Insights Trace，观察重叠与等待。
- [ ] 用 RenderDoc 捕获一帧，关联立方体绘制事件与中间纹理。
- [ ] 将源码、时间线和抓帧结果对应起来，完成正文与引用。

## 需要收集的三类证据

| 证据 | 内容 | 用来回答 |
| --- | --- | --- |
| 数据流图与源码记录 | 关键调用栈、源码位置、数据所有权、线程边界 | 位置变化经过哪些环节？ |
| 线程时间线 | Unreal Insights 截图与采集配置，标明时间范围 | 工作如何重叠，哪里发生等待？ |
| GPU 帧捕获 | RenderDoc 事件、绑定资源、中间纹理和最终输出 | 绘制命令怎样逐步形成画面？ |

图片存放在 [assets](assets/README.md)。每条重要结论应能对应到源码记录或实验结果；尚未验证的内容明确标注。

## 写作核对

- [ ] 未把检索入口列表写成未经核对的严格调用链。
- [ ] 未把数据依赖图写成所有线程严格串行执行的时序图。
- [ ] 未将渲染线程完成、GPU 完成与屏幕显示混为一谈。
- [ ] 已注明线程配置及版本差异，帧间延迟结论有对应实验依据。
- [ ] 外部代码、图片和引用均注明来源及原有使用条款。

## 官方参考资料

- [Threaded Rendering](https://dev.epicgames.com/documentation/unreal-engine/threaded-rendering-in-unreal-engine)：线程间的数据访问和生命周期。
- [Parallel Rendering Overview](https://dev.epicgames.com/documentation/unreal-engine/parallel-rendering-overview-for-unreal-engine)：渲染任务与线程分工。
- [Mesh Drawing Pipeline](https://dev.epicgames.com/documentation/unreal-engine/mesh-drawing-pipeline-in-unreal-engine)：传统网格绘制路径。
- [Render Dependency Graph](https://dev.epicgames.com/documentation/unreal-engine/render-dependency-graph-in-unreal-engine)：Pass 与资源依赖。
- [SendRenderTransform_Concurrent](https://dev.epicgames.com/documentation/unreal-engine/API/Runtime/Engine/UPrimitiveComponent/SendRenderTransform_Concurrent)：组件渲染 Transform 更新入口。

阅读时对照选定的 UE 版本；正式引用源码时记录文件路径、提交版本和对应位置。

## 许可

本文由 CedricZ1001 撰写，原创文字与原创配图采用 [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/deed.zh-hans)。外部代码、图片及其他素材遵循各自原有条款，不在本仓库的原创内容授权范围内。完整协议见仓库 [LICENSE](../../../LICENSE)。

分享时请保留作者署名、原文与许可链接，并注明修改情况。原文位置：[TechJournal / 本篇文章](https://github.com/CedricZ1001/TechJournal/blob/main/articles/unreal-engine/frame-from-game-thread-to-screen/README.md)。
