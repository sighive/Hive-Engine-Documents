# 上层设计决策

**项目名称:** VulkanSceneGraph (VkSceneGraph备选)

**编程语言:** C++17

**构建工具:** CMake

**源代码管理:** git && github

**数学库 :** GLSL风格的[maths](https://github.com/vsg-dev/VulkanSceneGraphPrototype/tree/master/include/vsg/maths)库, 借鉴GLSL、 GLM 以及 Vulkan conventions

**Windowing:** 本地原生Windowing与核心VSG库集成，能够使用第三方Windowing（短期内使用GLFW快速入门）

**Vulkan集成:** 

* Vulkan对象的轻量级[C++封装](https://github.com/vsg-dev/VulkanSceneGraphPrototype/tree/master/include/vsg/vk), 命名和风格借鉴Vulkan C API
* 提供方便、鲁棒的资源设置和清理方式
* 命名标准： VkFeature -> vsg::Feature，对应include/vsg/vk/Feature文件
* Cmd naming VkCmdFeature -> vsg::Feature, sub-classed from [vsg::Command](https://github.com/vsg-dev/VulkanSceneGraphPrototype/blob/master/include/vsg/vk/Command.h)
* State VkCmdFeature -> vsg::Feature, sub-classed from vsg::StateComponent and aggregated within a [vsg::StateGroup](https://github.com/vsg-dev/VulkanSceneGraphPrototype/blob/master/include/vsg/nodes/StateGroup.h) node

**单一库:** 所有core, maths, nodes, utilities, vulkan和viewer等相关类都放到libvsg单个库中, 既可以用作静态链接库、也可以用作动态链接库。

**命名空间:** vsg

**头文件:** 使用 .h 后缀

所有文件按照功能类别放到合适的子目录下，例如：

* [include/vsg/core](https://github.com/vsg-dev/VulkanSceneGraphPrototype/tree/master/include/vsg/core/)/Object.h
* [include/vsg/nodes/](https://github.com/vsg-dev/VulkanSceneGraphPrototype/tree/master/include/vsg/nodes/)Group.h
* [include/vsg/vk/](https://github.com/vsg-dev/VulkanSceneGraphPrototype/tree/master/include/vsg/vk/)Instance.h
* 为方便起见可以使用 include/vsg/all.h从而包含所有vsg/*/*.h

**源文件:** 使用 .cpp 后缀

所有文件按照功能类别放到合适的子目录下，例如：

* [src/vsg/core/](https://github.com/vsg-dev/VulkanSceneGraphPrototype/tree/master/src/vsg/core/)Visitor.cpp
* [src/vsg/viewer/](https://github.com/vsg-dev/VulkanSceneGraphPrototype/tree/master/src/vsg/viewer/)Viewer.cpp

**内存:** 为了解决场景图性能瓶颈的问题，主要目标是提高缓存一致性、并降低内存带宽负载。

侵入式引用计数效率是std::shared_ptr<>的两倍, 进而导致遍历速度上提高了50%. 
使用 [vsg::ref_ptr<>](https://github.com/vsg-dev/VulkanSceneGraphPrototype/tree/master/include/vsg/core/ref_ptr.h), [vsg::observer_ptr<>](https://github.com/vsg-dev/VulkanSceneGraphPrototype/tree/master/include/vsg/core/observer_ptr.h)和vsg::Object。

使用std::atomic提供高效的、线程安全的引用计数。

为了最小化场景图中大多数内部节点的大小，少数节点需要的辅助数据从vsg::Object/Node 类移植到了[vsg::Auxiliary](https://github.com/vsg-dev/VulkanSceneGraphPrototype/tree/master/include/vsg/core/Auxiliary.h) 对象。

为了更强大的内存管理控制， [vsg::Allocator](https://github.com/vsg-dev/VulkanSceneGraphPrototype/tree/master/include/vsg/core/Allocator.h) 类允许应用程序控制场景图如何分配以及释放内存。

**Introspection:** 在探索阶段尚未探索，需要未来再做考虑。目的是为所有场景图核心类提供introspection/reflectio机制，从而提供对通过脚本读写场景对象功能的支持。