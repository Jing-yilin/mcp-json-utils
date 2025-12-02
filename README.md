# @yilin-jing/mcp-json-utils

JSON response utilities for MCP (Model Context Protocol) servers. Provides formatting, item limiting, and file saving capabilities for API responses.

## Features

- **Response Formatting**: Converts API responses to readable markdown with JSON structure tree
- **Item Limiting**: Automatically limits large arrays to prevent context overflow (default: 10 items)
- **Raw Data Saving**: Optionally saves full response data to local files
- **Dynamic Field Detection**: Automatically finds and limits large arrays anywhere in the response structure

## Installation

```bash
npm install @yilin-jing/mcp-json-utils
```

## Usage

```typescript
import { JsonResponseHandler } from '@yilin-jing/mcp-json-utils';

// Create handler with custom max items (default: 10)
const jsonHandler = new JsonResponseHandler({ maxItemsForContext: 10 });

// Format a response
const result = jsonHandler.formatResponse(data, {
  rawDataSaveDir: '/tmp/my_data',  // Optional: save full data to file
  toolName: 'get_posts',           // Optional: tool name for filename
  params: { featured: true }       // Optional: params for filename
});

// Result is a CallToolResult with formatted markdown
```

## Output Format

The formatted response includes:

1. **Header** with tool name
2. **File info** (if `rawDataSaveDir` is provided) with path and size
3. **Limitation note** (if items were limited) showing field path and counts
4. **JSON Structure Tree** showing the shape of the data
5. **JSON Data** with limited items

Example output:
```
## get_posts

> **Raw data saved to**: `/tmp/my_data/get_posts_featured=true_2024-01-15T10-30-00-000Z.json` (15.2 KB)

> **Note**: `edges` limited to 10 items (25 total). Full data saved to file.

### JSON Structure
├── edges: Array[10]
│   └── [item]:
│       ├── id: string
│       ├── name: string
│       └── ...
└── pageInfo:
    ├── hasNextPage: boolean
    └── endCursor: string

### Data (`edges`: 10/25 items)
```json
{
  "edges": [...],
  "pageInfo": {...}
}
```

## API

### `JsonResponseHandler`

#### Constructor

```typescript
new JsonResponseHandler(config: { maxItemsForContext: number })
```

#### Methods

- `formatResponse(data, options?)` - Format data as MCP CallToolResult
- `saveRawData(data, saveDir, toolName, params?)` - Save data to file
- `limitItems(data)` - Limit large arrays in data
- `findLargeArrayField(data)` - Find first array with >maxItems items
- `generateJsonStructureTree(obj)` - Generate tree visualization
- `formatFileSize(bytes)` - Format bytes to human readable string

## License

MIT
