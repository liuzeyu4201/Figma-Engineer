---
name: Figma-Engineer
description: 使用figma-developer-mcp将Figma设计转换为程序代码，同时保证程序架构可扩展性和设计的一致性
---

# Figma Engineer

该技能提供了一个结构化的工作流程，用于将 Figma 设计转换为具有像素完美精度的生产就绪代码。它确保与 Figma MCP 服务器的一致集成、设计令牌的正确使用以及与设计 1:1 的视觉对等。
对于figma-developer-mcp MCP server的具体细节，请参考reference/figma-developer-mcp-tools.md
## 配置信息
1. 项目程序代码默认生成在当前工作目录`$work_dir`
2. Figma设计数据信息默认生成在`$work_dir/.figma`目录

## 环境检查
### 先决条件(环境检查)
以下先决条件和环境检查是必要的，以确保转换过程顺利进行，只要有某一步失败，就应该暂停并向用户提示错误和解决方法,以及重启Agent：
- figma-developer-mcp MCP server 必须已经链接且可访问。\
    1. 检查 figma-developer-mcp MCP server 是否可以正常访问，如果无法访问,请给用户提示这个github地址[Figma-Context-MCP](https://github.com/GLips/Figma-Context-MCP)
- 用户必须提供以下格式的Figma URL:
    1. `https://figma.com/design/:fileKey/:fileName?node-id=1-2`
        - :fileKey 是当前文件密钥
        - :fileName 是当前文件名
        - :node-id 是要实现的特定组件或框架的节点ID
    2. 如果用户未提供，请暂停并要求用户提供。

### 补充条件
以下条件非必须，但是请进行下一步时，一并发送用户提供。如未提供，也可继续后续步骤
1. 用户需提供实现UI的技术栈。如未提供，默认使用React + Tailwind
2. 代码生成目录，如果未指定，默认在当前目录生成代码

## 核心流程
注意这是获取Figma数据的标准流程，需要严格执行，而且每次提交新的URL都需要重新执行全流程。
### Step0: 整合Figma设计数据整合流程
整合流程(不要跳过):
1. 运行figma_framelink_get_figma_data 工具，尽可能多的获取Figma设计数据，并且创建`$work_dir/.figma/$node-id/meta.json`目录。按照新提交的节点ID创建新文件夹。
    - 如果因为相应过大被截断，可以按深度遍历获取，但是注意meta.json的完整性
2. 运行figma_framelink_download_figma_images 工具，获取当前node-id对应的所有图片资源，并且保存到`$work_dir/.figma/$node-id/images`目录下。
3. 仅当同时拥有meta数据以及其他所有图片资源时，认为该节点数据是完整的，才进行Step 1。
4. 如果有无法获取文件，请暂停并要求用户确认是否跳过。
当有设计数据更新时，需要重新执行Step 0，并更新meta.json文件。

### Step1: 实施代码
在实施代码前请检查是否有其他skill可以帮助程序实现。
1. 根据meta.json文件以及图片资源作为设计和行为的表示，使用用户的指定的技术栈，生成对应的程序代码。首先检查是否已经有项目代码，如果有，先阅读项目代码，加入到对应的位置实现功能，如果没有程序默认生成在`$work_dir/src`目录下。
- 每一个`$node-id`对应一个页面，注意模块化设计。
2. 实现时如果该项目以及已经存在的组件，注意更多重用现有组件（例如按钮、输入、排版、图标包装）而不是重复功能。当存在匹配的组件时，扩展它而不是创建一个新组件。
3. 一致地使用项目的颜色系统、版式比例和间距标记。
4. 尊重现有的路由、状态管理和数据获取模式
5. 配置自动布局（Auto Layout）：设置内边距（上、右、下、左）、项目间距，以及主轴对齐方式（紧凑排列 packed、两端对齐 space-between），以实现更灵活的容器行为。
6. 使用变体（Variant）属性面板创建组件变体：定义诸如尺寸（sm、md、lg）、状态（default、hover、disabled）和类型（primary、secondary）等维度。
7. 使用 Figma 文本样式定义一套字阶系统（type scale）：保持字号、字重和行高比例一致，并映射为语义化名称（如 heading-lg、body-md、caption）。
8. 构建交互原型：在组件变体之间使用 Smart Animate（智能动画）过渡，用于展示微交互效果。
9. 使用 Figma Plugin API 自动化重复性任务：例如批量重命名图层、生成配色方案，或将设计令牌（design tokens）导出为 JSON。
10. 利用 Dev Mode 进行设计交付：检查间距、导出资源，并可直接从设计中复制 CSS / iOS / Android 代码片段。
11. 组织设计系统文件结构：包含封面页、变更日志页，以及按组件类别划分的独立页面（如按钮、输入框、导航、反馈等）。
### Step1.2 代码质量
1. 注意项目模块化设计，遵守SOLID原则，提升接口的可维护性和可扩展性。
* 避免硬编码数值——应提取为常量或设计令牌（design tokens）。
* 保持组件具备良好的可组合性与可复用性。
* 加入必要注释，包括函数、类、变量等，以提高代码的可读性和可维护性。
* 不要在自动布局（Auto Layout）框架中使用绝对定位，除非该元素确实需要脱离正常布局流；否则这会违背响应式布局的初衷。
* 不要为了单次场景创建脱离主组件的独立实例（detached instances）；如果可以通过变体或属性实现，就应优先使用它们，因为源组件更新后，脱离的实例不会同步，容易过时。
* 不要忽略图层的命名与组织；工程师在 Dev Mode 中查看设计时，需要依赖有意义的图层名称来将设计映射到代码组件。
* 不要在未优化的情况下直接嵌入全分辨率的位图图像；过大的资源会拖慢 Figma 文件性能，并导致导出的资源体积不必要地增大。
### step 3：代码检查
1. 检查实现代码是否与Figma设计一致。要求严格的视觉1:1实现。
2. 检查代码是否符合技术栈的规范。
3. 检查代码是否符合项目的代码质量。

## Examples 
## example1:构建仪表板布局

用户说"实现这个Figma 设计中的仪表板布局,地址在https://figma.com/design/:fileKey/:fileName?node-id=1-2"

Action:
1. 解析URL,获取fileKey和fileName,node-id
2. 检查是否创建了对应的目录结构以及设计文件。
3. 运行figma_framelink_get_figma_data 工具，获取Figma设计数据。如果已经有meta.json文件，对比是否需要更新。如果需要更新，更新meta.json文件，否则直接写入文件。
4. 运行figma_framelink_download_figma_images 工具，获取当前node-id对应的所有图片资源，并且保存到`$work_dir/.figma/$node-id/images`目录下。
5. 检查是否已经获取了所有图片资源，如果没有再次请求下载，直到所有图片资源都获取到。
6. 检查现有系统是否已经实现了该功能，如果没有，使用项目规范实现该功能。
7. 检查实现代码是否与Figma设计一致。要求严格的视觉1:1实现。
重点检查：
- 功能是否实现,是否与其他page有交互功能。
- 组件样式、布局、交互等是否与设计igma一致
- 代码是否符合项目的代码质量
最终结果：仪表板布局匹配 Figma 设计，与项目设计系统集成。
