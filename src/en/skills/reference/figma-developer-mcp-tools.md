# figma-developer-mcp Tools
figma-developer-mcp MCP server tool reference, for quick reference on which tool to use and tool parameter methods, to guide output to your stack.

## Core Tools
`figma_framelink_get_figma_data`:
- fileKey: The key of the Figma file (obtained from URL)
- nodeId: The node ID (optional, to fetch a specific node)
- depth: Traversal depth (optional, default 1 level)
Get data for the entire Figma file, get data for specified nodes, extract layout, content, visual, component and other structured information.

`figma_framelink_download_figma_images`: 
- fileKey: The key of the Figma file
- nodes: Array of nodes (containing nodeId, fileName, etc.)
- localPath: Local save directory
- pngScale: PNG export scale (default 2x)
Download all image resources in the Figma file, including components, layers, text, etc.
