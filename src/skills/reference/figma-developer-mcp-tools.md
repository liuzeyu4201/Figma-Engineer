# figma-developer-mcp  工具
figma-developer-mcp MCP server 工具参考，用于快速参考，合适使用哪个工具以及工具的参数方法，以引导输出到您的堆栈中。

## Core Tools
`figma_framelink_get_figma_data`:
- fileKey: Figma 文件的 key（URL 中获取）
- nodeId: 节点 ID（可选，指定获取特定节点）
- depth: 遍历深度（可选，默认 1 层）
获取整个 Figma 文件的数据，获取指定节点的数据，提取布局、内容、视觉、组件等结构化信息。

`figma_framelink_download_figma_images`: 
- fileKey: Figma 文件的 key
- nodes: 节点数组（包含 nodeId、fileName 等）
- localPath: 本地保存目录
- pngScale: PNG 导出倍数（默认 2x）
下载 Figma 文件中的所有图片资源，包括组件、图层、文本等。