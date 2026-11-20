---
name: reddit-research
description: "This skill should be used when the user wants to search Reddit or read Reddit threads from their agent, or asks to \"search Reddit for <topic>\", \"read this Reddit thread\", \"read the comments on <reddit link>\", \"what are people on Reddit saying about <product>\", \"find Reddit threads where people ask for <thing>\", or \"what does r/<subreddit> think of <topic>\". Runs on the superagnt MCP server's Reddit tools: Reddit search, full comment threads, subreddit feeds and user history. Read only: it never posts, comments, votes or sends a message."
version: 0.1.0
---

# Reddit research

Find the Reddit threads on a topic, read them in full, and bring back what
people actually say, each point linked to the thread it came from.

It only reads. It never posts, comments, votes or sends a message.

**Tools:** `data_reddit_discover`, `data_reddit_posts`, `data_reddit_subreddits`
and `data_reddit_users`, from the superagnt MCP server
(`https://mcp.superagnt.com/mcp`). If none of them is listed, the server isn't
connected yet: the setup guide is https://www.superagnt.com/install.

## Step 1: what to look for

Ask once, in one message, for what's missing:

- the topic, product or question;
- the subreddits to stay in, if any;
- how far back: this week, this month, this year or all time.

A thread link alone needs no questions: go to Step 3 with it.

## Step 2: find the threads

- **Across Reddit:** `data_reddit_discover` with `action` set to
  `search_posts`, `query` set to the topic and `sort` set to `top`, with
  `time` set to the window (`week`, `month`, `year`, `all`). Add `subreddit`
  to stay in one community. `time` only works with `sort` set to `top`; for
  the newest threads, use `sort` set to `new` and no `time`.
- **Inside one subreddit:** `data_reddit_subreddits` with `action` set to
  `top_posts` and `time` set to the window, or `posts` with `sort` set to
  `new`, `hot` or `rising`. To find the right subreddits first, use
  `data_reddit_discover` with `action` set to `search_subreddits`.
- Pass the `cursor` from a result to read its next page.

Keep the threads that are about the topic and have comments. Rank them by
comments, then score, and take the top five unless the person asked for more.

## Step 3: read each thread in full

For each thread, call `data_reddit_posts` with `action` set to `comments`,
`post_url` set to the thread's full link and `response_format` set to `raw`.
The raw format returns the reply tree as Reddit serves it, nested, so the
answer can follow a conversation down its replies rather than only the
top-level comments. Reddit leaves out some deep or collapsed replies, so a
long thread can come back with fewer comments than its count. The compact
format (no `response_format`) lists the top-level comments only.

For a thread with many comments, `comments_sorted` with `sort` set to `top`
puts the most upvoted first; `qa` follows an AMA's questions and answers.

## Step 4: who is talking (when it matters)

When the person wants to know who is behind a post, or whether to reply to
it, read the author with `data_reddit_users`: `profile` and `stats` for the
account, `comments` with `sort` set to `new` for what they said lately.

## Step 5: the answer

Write what people say, not what the threads are about:

- each point in the commenters' own words, quoted short, with the thread's
  link and the comment's score;
- requests, complaints and the products people name, grouped, with how many
  threads each came up in;
- disagreements shown as both sides.

Say how many threads and comments the answer read, and the window it
covered. Never invent a quote, a score or a thread; when a call fails or
returns nothing, say so.
