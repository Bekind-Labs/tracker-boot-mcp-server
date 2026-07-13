# Tracker Boot MCP Server

A **Model Context Protocol (MCP)** server that integrates with the **Tracker Boot**, enabling AI assistants to fetch stories and analytics for current iteration.

## Prerequisites

- Before running the MCP server, you need to set the following environment variables:
    - `TRACKER_BOOT_API_KEY` (required): Your Tracker Boot API Key.

  Get your API key from the [Tracker Boot Settings](https://trackerboot.com/settings/api).


- Pull the latest image from the public repository:
  ```shell
  $ docker pull public.ecr.aws/tracker-boot/mcp-server:latest
  ```

## Available Tools
The MCP server provides the following tools for interacting with the Tracker Boot API:

| Tool                          | Description                          | Parameters                                                              | Returns                                                          |
|-------------------------------|--------------------------------------|-------------------------------------------------------------------------|------------------------------------------------------------------|
| `tb_get_story`                | Fetch story details                  | storyId                                                                 | Complete story details                                           |
| `tb_get_story_tasks`          | Fetch tasks in a story               | storyId                                                                 | List of tasks (title, finished) in a story                       |
| `tb_get_story_comments`       | Fetch comments in a story            | storyId                                                                 | List of comments (content) in a story                            |
| `tb_get_current_iteration`    | Fetch current iteration with stories | projectId                                                               | Iteration data with list of stories including cycle time details |
| `tb_create_story`             | Create a new story                   | projectId,<br>title,<br>description (optional),<br>storyType (optional) | Details of the created story                                     |
| `tb_update_story_title`       | Update the title of a story          | storyId,<br>title                                                       | Details of the updated story                                     |
| `tb_update_story_description` | Update the description of a story    | storyId,<br>description                                                 | Details of the updated story                                     |
| `tb_update_story_status`      | Update the status of a story         | storyId,<br>status                                                      | Details of the updated story                                     |
| `tb_create_task`              | Creat a new task in a story          | storyId,<br>title                                                       | Details of the created task                                      |
| `tb_batch_create_tasks`       | Create multiple tasks in a story     | storyId, <br>titles                                                     | Details of the created tasks                                     |
| `tb_update_task`              | Update a task in a story             | taskId,<br>storyId,<br>finished,<br>title (optional)                    | Details of the updated task                                      |
| `tb_create_comment`           | Create a new comment in a story      | storyId,<br>content                                                     | Details of the created comment                                   |
| `tb_get_projects`             | Get the list of projects             |                                                                         | List of projects that the user has access to                     |
| `tb_search_stories`           | Search stories by keyword or labels and status | projectId, <br>query (optional), <br>labels (optional) , <br> status (optional) | List of stories matching the search criteria                     |
| `tb_update_story_estimate`    | Update estimate of a story                     | storyId, <br>estimate                                                           | Update the estimate of a story.                                  |
| `tb_get_project_members`      | Get the list of project members                | projectId                                                                       | List the members of a project                                    |
| `tb_update_story_owners`      | Assign/Remove owners from a story              | storyId, <br>addOwnerIds, <br>removeOwnerIds                                    | Add or Remove owners for a story                                 |
| `tb_add_story_label`          | Add a label to a story                         | storyId, <br>label                                                              | Label Id, Name and Count                                         |
| `tb_remove_story_label`       | Remove an existing label from a story          | storyId, <br>label                                                              | Label Id, Name and Count                                         |

**NOTE**:
If you don't see all the tools listed above, please ensure that you are using the latest docker image of the MCP server.



# Installations

## Claude Applications

You need either one of the following:

### a. Claude Desktop
<hr>
Prerequisites
Claude Desktop installed (latest version)
Tracker Boot API Key (https://trackerboot.com/settings/api)
For local setup: Docker installed and running

#### Configuration File Location
- macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`
- Windows: `%APPDATA%\Claude\claude_desktop_config.json`
- Linux: `~/.config/Claude/claude_desktop_config.json`

#### Local Server Setup (Docker)
Add this codeblock to your `claude_desktop_config.json` (**replace `<YOUR_TRACKER_BOOT_API_KEY>` with your own key**) and restart Claude Desktop:
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
        "public.ecr.aws/tracker-boot/mcp-server:latest"
      ],
      "env": {
        "TRACKER_BOOT_API_KEY": "<YOUR_TRACKER_BOOT_API_KEY>"
      }
    }
  }
}


```

### b. Claude Code CLI
<hr>

#### Prerequisites
- Claude Code CLI installed
- Tracker Boot API Key (https://trackerboot.com/settings/api)
- For local setup: Docker installed and running
- Open Claude Code inside the directory for your project (recommended for best experience and clear scope of configuration)

#### Local Server Setup (Docker)
1. Run the following command in the Claude Code CLI (**replace `<YOUR_TRACKER_BOOT_API_KEY>` with your own key**):
```shell
claude mcp add tracker-boot -e TRACKER_BOOT_API_KEY=<YOUR_TRACKER_BOOT_API_KEY> -- docker run -i --rm -e TRACKER_BOOT_API_KEY public.ecr.aws/tracker-boot/mcp-server:latest
```
2. Restart Claude Code
3. Run `claude mcp list` to see the Tracker Boot server is configured from the terminal

<br>

## Github Copilot Coding Agent
- Ensure that Copilot Coding Agent is enabled for your repository
- Add the following to Copilot's MCP Configuration in Repo Settings > Copilot > Coding Agent > MCP configuration
```json
{
  "mcpServers": {
    "tracker-boot-mcp-server": {
      "type": "local",
      "tools": ["tb_get_story","tb_get_current_iteration","tb_get_story_tasks","tb_create_comment","tb_get_story_comments","tb_create_task","tb_create_story","tb_update_task","tb_update_story_title","tb_update_story_description","tb_update_story_status","tb_get_projects", "tb_batch_create_tasks", "tb_search_stories"],
      "command": "docker",
      "args": [
        "run",
        "-i",
        "-e", "TRACKER_BOOT_API_KEY",
        "public.ecr.aws/tracker-boot/mcp-server:latest"
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

## Devin
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
|                   | public.ecr.aws/tracker-boot/mcp-server:latest                      |

* Click `Save Changes`
