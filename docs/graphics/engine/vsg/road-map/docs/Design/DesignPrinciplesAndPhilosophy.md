# 设计原则与哲学

> *"Successful Software Lives For Ever" --- "成功的软件永生不灭"*

本文介绍了VSG项目领导者Robert Ossfield的设计原则与哲学。 这些原则借鉴了Robert Ossfield在担任[OpenSceneGraph](https://www.openscenegraph.org)项目领导时的经验，并从实时计算机图形学、C++、以及开源/自由软件社区汲取了经验。

首先我们来剖析一下开场白, *"成功的软件永生不灭"*, 这句话是Robert Ossfield在十年前的一个培训课上首次提出的:

* *"成功的软件"* 是什么: 成功的软件是一个足够有用与可靠的软件，该软件能够在开发人员或用户的工作或个人生活中占据举足轻重的地位。

* *"永生不灭"* 有多久 : 该软件的维护与进化持续的时间远远超出开发该软件的第一个稳定发行版本所需的时间。不可靠、难以维护与进化的软件是无法持久的，终究会失败。成功的软件需要好的设计与实现、以及长期的响应式管理与维护。

决定VulkanSceneGraph成功与否的两个主要因素是：1.Performance (性能)  2.Productivity (生产效率) 。

## Performance

对于不同的应用来说，性能要求也不一样。某些应用要求最大化帧率；某些应用要求避免掉帧、提供一个稳定的帧率；VR应用则要求最小化延迟；还有一些应用要求在达到预期的帧率和视觉效果的同时最小化能耗。但对于所有上述情况，如何高效率的进行2D/3D世界的表示，以及如何高效率的在GPU上进行渲染是决定因素。应用程序占据的系统负载越低，则效率越高，潜在的性能表现也越佳。（注意这里的“性能(Performance)”与“效率(Efficiency)”不是同一个概念）

高效率图形应用需要考虑下述每一个阶段的效率：
1. 场景的创建与销毁；分页场景必须支持并行绘制
1. 场景的更新
1. 剔除遍历 - 例如视锥体剔除； 目的是生成一个调度图
1. 调度遍历 - 将调度图中的数据发送给GPU
1. 图形处理 - 在GPU上进行图形渲染以及通用计算等相关工作

采用Vulkan相比于OpenGL和Direct3D(11及之前版本)的主要优势在于大大降低了CPU向GPU分发数据时的工作负载，这减小了阶段4（调度遍历）的开销。

高效率图形应用的编程指南：
* [Benchmarking](https://en.wikipedia.org/wiki/Benchmarking)是确定性能瓶颈和衡量变更有效性的标准 - 避免不成熟的优化!
* 最小化场景图对象的内存占用
* 避免不必要的数据存储
* 将经常访问的数据打包到一起，这样做有利于通过缓存机制快速访问
* 避免不必要的条件语句
* 选择对编译器优化和CPU并行性友好的编码技术

## Productivity

### 提高生产效率之 ~ 通用项目开发原则:

* 使用业界广泛认可和采纳的最佳做法:
    * [FOSS Best Practices](https://github.com/coreinfrastructure/best-practices-badge/blob/master/doc/criteria.md)用于指导如何组织和维护项目
    * [CppCoreGuidelines](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines)用于指导设计和实现
* 合理利用c++17的优势，使得代码更加清晰与易于维护
    * 使用对场景图有益的特性和语法
    * 不要仅仅因为“新”、“潮流”而接受无益的特性-可以接受的特性必须能够使得代码更加清晰、易于维护，并且不会造成性能损失（举个例子：不要使用std::shared_ptr，因为它会造成巨大的性能损失）
    * 不要臆测，要先实现，然后通过benchmark评估一个新特性是否有益
* 使用CMake作为跨平台开发工具-因为CMake是高效的，并且是实时图形行业中使用的标准构建工具，也大多开发者比较熟悉的。
* 最小化VukanSceneGraph库的依赖项从而能够快速方便的构建：
    * C++17
    * Vulkan
    * CMake
    * Native Windowing
* 编写文档，指导用户如何使用VSG、以及如何为VSG的开发做出贡献
* 构建一个社区，使得开发者能够互相帮助，协同测试、调试、开发新特性，以及与他人分享心得收获

### 提高生产效率之 ~ 高质量代码准则:

* Short cuts in code quality are *"Fools Gold"* when it comes to Productivity
* Take pride in clarity and efficiency of code
* Take time to understand and appreciate the code and techniques used by others - such as CppCoreGuidelines
* You want your work to be a *Success*, so write it like it'll *Live Forever* (and be looked at forever:-)
* If a problem exists, be honest about it, solve it right way and learn from the error and solution found
* *"Prepare to throw one away, you will anyhow"* (source : Mythical Man Month): don't get too wrapped up solving all the worlds problems in one place, solve the problems you have at hand and test the results, and if in the future it's found to be insufficient then refactor/rewrite it with everything you've learnt and now need to achieve
* Make use of static and dynamic analysis tools straightforward and a regular practice for developers and users