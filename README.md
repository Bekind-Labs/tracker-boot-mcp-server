# Tracker Boot MCP Server

A **Model Context Protocol (MCP)** server that integrates with the **Tracker Boot**, enabling AI assistants to fetch stories and analytics for current iteration.

## Required Environment Variables

- Before running the MCP server, you need to set the following environment variables:
    - `TRACKER_BOOT_API_KEY` (required): Your Tracker Boot API Key.

  Get your API key from the [Tracker Boot Settings](https://trackerboot.com/settings/api).
  


## Available Tools
The MCP server provides the following tools for interacting with the Tracker Boot API:

| Tool                       | Description                          | Parameters | Returns                                                          |
|----------------------------|--------------------------------------|------------|------------------------------------------------------------------|
| `tb_get_story`             | Fetch story details                  | storyId    | Complete story details                                           |
| `tb_get_story_tasks`       | Fetch tasks in a story               | storyId    | List of tasks (title, finished) in a story                       |
| `tb_get_story_comments`    | Fetch comments in a story            | storyId    | List of comments (content) in a story                            |
| `tb_get_current_iteration` | Fetch current iteration with stories | projectId  | Iteration data with list of stories including cycle time details |

&nbsp;

# Claude Applications

## Claude Desktop
<hr>
Prerequisites
Claude Desktop installed (latest version)
Tracker Boot API Key (https://trackerboot.com/settings/api)
For local setup: Docker installed and running

### Configuration File Location
- macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`
- Windows: `%APPDATA%\Claude\claude_desktop_config.json`
- Linux: `~/.config/Claude/claude_desktop_config.json`

### Local Server Setup (Docker)
Add this codeblock to your `claude_desktop_config.json` and restart Claude Desktop:
```json
{
  "mcpServers": {
    "tracker-boot": {
      "command": "docker",
      "args": [
        "run",
        "-i",
        "--rm",
        "-e", "TRACKER_BOOT_API_KEY",
        "-e", "TRACKER_BOOT_BASE_URL",
        "public.ecr.aws/e0q3y4e2/mcp-server:latest"
      ],
      "env": {
        "TRACKER_BOOT_API_KEY": "YOUR_TRACKER_BOOT_API_KEY"
      }
    }
  }
}


```

## Claude Code CLI
<hr>

### Prerequisites
- Claude Code CLI installed
- Tracker Boot API Key (https://trackerboot.com/settings/api)
- For local setup: Docker installed and running
- Open Claude Code inside the directory for your project (recommended for best experience and clear scope of configuration)

### Local Server Setup (Docker)
1. Run the following command in the Claude Code CLI:
```shell
claude mcp add tracker-boot -e TRACKER_BOOT_API_KEY=YOUR_TRACKER_BOOT_API_KEY -- docker run -i --rm -e TRACKER_BOOT_API_KEY
  public.ecr.aws/e0q3y4e2/mcp-server:latest
```
2. Restart Claude Code
3. Run `claude mcp list` to see the Tracker Boot server is configured from the terminal

<br>

# Github Copilot Coding Agent
- Ensure that Copilot Coding Agent is enabled for your repository
- Add the following to Copilot's MCP Configuration in Repo Settings > Copilot > Coding Agent > MCP configuration
```json
{
  "mcpServers": {
    "tracker-boot-mcp-server": {
      "type": "local",
      "tools": ["tb_get_story_tasks","tb_get_current_iteration","tb_get_story_comments","tb_get_story"],
      "command": "docker",
      "args": [
        "run",
        "-i",
        "-e",
        "TRACKER_BOOT_API_KEY",
        "public.ecr.aws/e0q3y4e2/mcp-server:latest"
      ],
      "env": {
        "TRACKER_BOOT_API_KEY": "COPILOT_MCP_TRACKER_BOOT_API_KEY"
      }
    }
  }
}
``` 
- Create a secret called `COPILOT_MCP_TRACKER_BOOT_API_KEY` containing your API Key in Repo Settings > Environments > copilot > Environment secrets
  - Get your Tracker Boot API Key from here: https://trackerboot.com/settings/api

<br/>

# Devin
- Navigate to https://app.devin.ai/settings/mcp-marketplace
- Click "Add Your Own"
- Create an MCP config with the following settings:

| Field             | Value                                                              | 
|-------------------|--------------------------------------------------------------------|
| Server Name       | Tracker Boot MCP                                                   |
| Transport Type    | STDIO                                                              |
| Short Description | Get Story and Iteration details from Tracker Boot                  |
| Secrets           | Click + (Add a new secret)                                         |
| * Secret Name     | API_TOKEN                                                          |
| * Secret Value    | your_api_key ([get it here](https://trackerboot.com/settings/api)) |
| Command           | docker                                                             |
| Arguments         |                                                                    |
|                   | run                                                                |
|                   | -ie                                                                |
|                   | TRACKER_BOOT_API_KEY=$API_TOKEN                                    |
|                   | public.ecr.aws/e0q3y4e2/mcp-server:latest                          |

* Click `Save Changes`

