# reddit-mcp

Yes, Claude can search Reddit and read the threads. Connect this Reddit MCP server and Claude, ChatGPT or Cursor searches Reddit and can read full comment threads.

This repo holds the client config for superagnt's hosted MCP server and a skill that uses its Reddit tools. The server is hosted: point your client at the URL below and sign in. There's no Reddit API key to get and nothing to run on your machine.

## Install

**Server URL:** `https://mcp.superagnt.com/mcp` (Streamable HTTP, OAuth). Your client opens superagnt's consent screen in your browser, and approving it creates your superagnt account if you don't have one. There's no API key to copy. The setup guide for every client, with a line you can paste into your agent: https://www.superagnt.com/install

**Claude** (claude.ai, the desktop app and Cowork): Customize, then Connectors, then Add custom connector. Name it superagnt, paste the server URL, add it and approve. One connector covers Claude on the web, the desktop app, mobile, Cowork and Claude Code signed in with your Claude account. Free Claude plans can add one custom connector.

**ChatGPT:** In ChatGPT on the web, go to Plugins, select the plus button, then Add custom MCP server. Name it superagnt, enter the server URL under Connection, and set Authentication to OAuth. Tick "I understand and want to continue", then select Create as a plugin. Install the plugin, then start a new chat, type @ and select superagnt. ChatGPT takes custom MCP servers on the web only, and OpenAI lists them for Business, Enterprise/Edu and Pro plans. Pro accounts get read and fetch actions only, which covers every Reddit tool here.

**Cursor:** put [`config/cursor.mcp.json`](config/cursor.mcp.json) in your project as `.cursor/mcp.json`, then click Login for superagnt under Settings, then MCP.

**Claude Code:**

```bash
claude mcp add --scope user --transport http superagnt https://mcp.superagnt.com/mcp
claude mcp login superagnt
```

**Codex:**

```bash
codex mcp add superagnt --url https://mcp.superagnt.com/mcp
codex mcp login superagnt
```

**Other clients**, each file in its client's own shape:

| Client | Config | Where it goes |
|---|---|---|
| Claude Code, one project | [`config/claude-code.mcp.json`](config/claude-code.mcp.json) | `.mcp.json` in the project |
| VS Code | [`config/vscode.mcp.json`](config/vscode.mcp.json) | `.vscode/mcp.json` (the key is `servers`, not `mcpServers`) |
| Gemini CLI | [`config/gemini-cli.settings.json`](config/gemini-cli.settings.json) | `~/.gemini/settings.json` (the key is `httpUrl`), then `/mcp auth superagnt` |
| Windsurf | [`config/windsurf.mcp_config.json`](config/windsurf.mcp_config.json) | `~/.codeium/windsurf/mcp_config.json` |
| Zed | [`config/zed.settings.json`](config/zed.settings.json) | `~/.config/zed/settings.json` |
| Codex, by file | [`config/codex.config.toml`](config/codex.config.toml) | `~/.codex/config.toml` |

**The skill:** `npx skills add superagnt/reddit-mcp` installs [reddit-research](skills/reddit-research/SKILL.md) into any agent that reads SKILL.md files, such as Claude Code, Codex and Cursor. It finds the threads on a topic, reads them in full and brings back what people say, each point linked to its thread.

A new superagnt workspace starts with the Reddit tools on.

## What each tool does

| Tool | Actions | What it does |
|---|---|---|
| `data_reddit_discover` | `search_posts`, `search_users`, `search_subreddits`, `popular_posts`, `best_posts`, `rising_posts`, `top_posts`, `popular_subreddits`, `new_subreddits` | Searches posts, users and subreddits. Post search sorts by relevance, hot, top, new or comments, with a time window for top. Also reads Reddit's popular, best, rising and top feeds. |
| `data_reddit_posts` | `details`, `comments`, `comments_sorted`, `duplicates` | Reads one post and its comments, sorted by top, new, controversial or Q&A. With `response_format` set to `raw`, the comments come back nested, as the reply tree Reddit serves. |
| `data_reddit_subreddits` | `info`, `rules`, `moderators`, `similar`, `posts`, `top_posts`, `controversial_posts`, `comments` | Reads a subreddit: its new, hot and rising posts, top and controversial posts, comments, rules, moderators and similar subreddits. |
| `data_reddit_users` | `profile`, `stats`, `overview`, `posts`, `top_posts`, `comments`, `top_comments`, `post_rank` | Reads a user's profile, stats, posts and comments, and how their posts rank in a subreddit. |

Every tool only reads. Nothing here posts, comments, votes or sends a message. Each call uses credits from your superagnt workspace: https://www.superagnt.com/pricing

## Prompts to try

- "Find this month's top posts in r/SaaS about onboarding emails, then read the comments on the busiest one."
- "Search Reddit for people asking for an alternative to <product>, and list each thread with what the person wants."
- "Read every reply on this thread and sum up the arguments on each side: <thread link>"
- "What does r/marketing say about <topic> this year? Quote the top comments and link each one."
- "Before I reply, check who posted this: <username>'s profile, stats and recent comments."

A timed run of the first kind of prompt, with every call shown: https://www.superagnt.com/claude/reddit

## About superagnt

superagnt is a marketing plugin for Claude and ChatGPT that helps businesses find customers through outbound, SEO and ads. Reddit is one of its data sources, beside X, LinkedIn, TikTok, YouTube, the Meta Ad Library, Google rankings and keywords, and a database that keeps what each run finds. More: https://www.superagnt.com

## License

This repo's config and skill are MIT licensed. See [LICENSE](LICENSE). The server is superagnt's hosted service.
