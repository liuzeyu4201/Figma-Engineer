---
name: Figma-Engineer
description: Use figma-developer-mcp to convert Figma designs into program code while ensuring architectural scalability and design consistency
---

# Figma Engineer

This skill provides a structured workflow for converting Figma designs into production-ready code with pixel-perfect precision. It ensures consistent integration with the Figma MCP server, proper use of design tokens, and 1:1 visual parity with the design.
For specific details about figma-developer-mcp MCP server, please refer to reference/figma-developer-mcp-tools.md
## Configuration
1. Project code is generated in the current working directory `$work_dir` by default
2. Figma design data is generated in `$work_dir/.figma` directory by default

## Environment Check
### Prerequisites
The following prerequisites and environment checks are necessary to ensure a smooth conversion process. If any step fails, pause and present the error and solution to the user, and restart the Agent:
- figma-developer-mcp MCP server must be connected and accessible.\
    1. Check if figma-developer-mcp MCP server is accessible. If not accessible, provide the user with this GitHub address [Figma-Context-MCP](https://github.com/GLips/Figma-Context-MCP)
- The user must provide the Figma URL in the following format:
    1. `https://figma.com/design/:fileKey/:fileName?node-id=1-2`
        - :fileKey is the current file key
        - :fileName is the current file name
        - :node-id is the node ID for the specific component or page to implement
    2. If the user does not provide it, pause and ask the user to provide it.

### Supplementary Conditions
The following conditions are not mandatory, but please send them together with the user when proceeding to the next step. If not provided, you can also continue with the subsequent steps
1. The user needs to provide the tech stack for implementing the UI. If not provided, React + Tailwind is used by default
2. The code generation directory. If not specified, code is generated in the current directory by default

## Core Workflow
Note: This is the standard workflow for fetching Figma data and must be strictly followed. The entire workflow must be re-executed for each new URL submitted.
### Step0: Integrate Figma Design Data
Integration workflow (do not skip):
1. Run the figma_framelink_get_figma_data tool to get as much Figma design data as possible, and create the `$work_dir/.figma/$node-id/meta.json` directory. Create a new folder according to the newly submitted node ID.
    - If the data is truncated due to being too large, you can fetch it by depth traversal, but pay attention to the completeness of meta.json
2. Run the figma_framelink_download_figma_images tool to get all image resources corresponding to the current node-id, and save them to the `$work_dir/.figma/$node-id/images` directory.
3. Only when both meta data and all other image resources are available, the node data is considered complete before proceeding to Step 1.
4. If there are files that cannot be fetched, pause and ask the user to confirm whether to skip.
When the design data is updated, you need to re-execute Step 0 and update the meta.json file.

### Step1: Implement Code
Before implementing the code, please check if there are other skills that can help with the implementation.
1. Based on the meta.json file and image resources as the representation of design and behavior, generate the corresponding program code using the user's specified tech stack. First check if there is already project code. If so, read the project code first and add it to the corresponding position to implement the function. If there is no code, it is generated in the `$work_dir/src` directory by default.
- Each `$node-id` corresponds to a page. Pay attention to modular design.
2. When implementing, if there are already existing components in the project, pay attention to reuse existing components (such as buttons, inputs, typography, icon wrappers) rather than duplicating functions. When a matching component exists, extend it instead of creating a new component.
3. Consistently use the project's color system, typography scale, and spacing tokens.
4. Respect existing routing, state management, and data fetching patterns.
5. Configure Auto Layout: Set padding (top, right, bottom, left), item spacing, and main axis alignment (packed, space-between) to achieve more flexible container behavior.
6. Use Variant properties panel to create component variants: define dimensions such as size (sm, md, lg), state (default, hover, disabled), and type (primary, secondary).
7. Use Figma text styles to define a type scale: Keep font size, font weight, and line height proportions consistent, and map to semantic names (such as heading-lg, body-md, caption).
8. Build interactive prototypes: Use Smart Animate transitions between component variants to showcase micro-interaction effects.
9. Use Figma Plugin API to automate repetitive tasks: such as batch renaming layers, generating color schemes, or exporting design tokens as JSON.
10. Use Dev Mode for design handoff: Check spacing, export assets, and directly copy CSS / iOS / Android code snippets from the design.
11. Organize design system file structure: Include cover page, changelog page, and separate pages by component category (buttons, inputs, navigation, feedback, etc.).
### Step1.2 Code Quality
1. Pay attention to project modular design, comply with SOLID principles, and improve interface maintainability and scalability.
* Avoid hardcoding values - should be extracted as constants or design tokens.
* Keep components with good composability and reusability.
* Add necessary comments, including functions, classes, variables, etc., to improve code readability and maintainability.
* Do not use absolute positioning in Auto Layout framework unless the element really needs to deviate from the normal layout flow; otherwise it goes against the purpose of responsive layout.
* Do not create detached instances from the main component for single scenarios; if it can be implemented through variants or properties, use them first because detached instances will not sync after the source component updates and can easily become outdated.
* Do not ignore layer naming and organization; engineers need meaningful layer names when viewing designs in Dev Mode to map designs to code components.
* Do not directly embed full-resolution bitmap images without optimization; oversized assets will slow down Figma file performance and cause unnecessarily large exported assets.
### Step3: Code Check
1. Check if the implemented code is consistent with the Figma design. Strict 1:1 visual implementation is required.
2. Check if the code complies with the tech stack specifications.
3. Check if the code complies with the project's code quality.

## Examples
## example1: Build Dashboard Layout

User says: "Implement the dashboard layout in this Figma design, address at https://figma.com/design/:fileKey/:fileName?node-id=1-2"

Action:
1. Parse URL to get fileKey, fileName, and node-id
2. Check if the corresponding directory structure and design files have been created.
3. Run figma_framelink_get_figma_data tool to get Figma design data. If meta.json file already exists, compare whether it needs to be updated. If it needs to be updated, update the meta.json file, otherwise write directly.
4. Run figma_framelink_download_figma_images tool to get all image resources corresponding to the current node-id, and save them to the `$work_dir/.figma/$node-id/images` directory.
5. Check if all image resources have been fetched. If not, request download again until all image resources are fetched.
6. Check if the existing system has already implemented this function. If not, implement this function using project specifications.
7. Check if the implemented code is consistent with the Figma design. Strict 1:1 visual implementation is required.
Key checks:
- Is the function implemented, does it have interaction functions with other pages.
- Are component styles, layout, interactions, etc. consistent with Figma design.
- Does the code comply with the project's code quality.
Final result: Dashboard layout matches Figma design, integrated with the project design system.
