---
name: desearch
description: Use Desearch MCP tools for AI and web research, page extraction, X data, and finding people or companies with public contacts.
---

# Desearch

Choose among 31 Desearch MCP tools for public-source research, page extraction, X data, People Search and Company Search.

## When to Use This Skill

- Find source-backed answers with AI search or web search.
- Read public pages as text, markdown or HTML.
- Retrieve X posts, replies, retweeters and trends.
- Find people on X, LinkedIn or TikTok, discover companies, or enrich known domains.

## What This Skill Does

1. **Research**: Finds web sources and AI summaries, then extracts supporting pages.
2. **X data**: Retrieves posts and related public data without posting or changing accounts.
3. **People Search**: Finds matching profiles with scores, evidence and public contacts.
4. **Company Search**: Finds matching companies or enriches supplied domains with public contacts and leaders.
5. **Task management**: Checks existing results, follows background progress and reports costs.

## How to Use

### Basic Usage

Have a person create an API key in the [Desearch console](https://console.desearch.ai/api-keys). Configure the MCP client with streamable HTTP transport, endpoint `https://mcp.desearch.ai/mcp`, and header `Authorization: Bearer <DESEARCH_API_KEY>`. Substitute the key privately through the client's secret configuration; never put key values in prompts, output or committed files.

Usage is billed to Desearch account credits. See [pricing](https://desearch.ai/pricing) and the [MCP documentation](https://www.desearch.ai/docs/guide/sdk/mcp). An optional local alternative is `npx -y desearch-mcp-server@0.6.0` with environment variable `DESEARCH_API_KEY`; the [source](https://github.com/Desearch-ai/mcp-desearch) is MIT licensed.

```text
Find public research on urban heat mitigation, extract useful pages,
and summarize the evidence with source links.
```

Search, page and X tools send read requests and do not post or change accounts. Use only registered tools. Skip external tools for local-file tasks or when the needed text is already supplied.

### Advanced Usage

```text
Find 10 companies in Germany building water monitoring sensors with public
email contacts. Explain the maximum reservation and ask me to confirm before
starting a billed task. Include evidence, leaders and the final cost.
```

All 31 tools and their key arguments follow. Arguments marked required must be supplied; other arguments are optional.

- **AI Search** (`ai-search`): Calls Desearch AI search. Arguments: `prompt` (required). `tools` is optional and defaults to `web` and `twitter`. Older labels such as `Web Search` are accepted and rewritten to the short id. Optional: `date_filter`, `start_date`, `end_date` (UTC), `result_type` (`ONLY_LINKS` or `LINKS_WITH_FINAL_SUMMARY`), `include_domains`, `exclude_domains`, `model` (`NOVA` or `ORBIT`, default `NOVA`).
- **Search** (`search`): Web search that returns `results` with `id`, `title`, and `url`. Argument: `query` (required). Use this when a client asks for a search tool.
- **Fetch Page** (`fetch`): Read one public page as text. Argument: `id` (required, an `http` or `https` URL, usually a `search` result URL).
- **Web Search** (`web-search`): Web search. Returns titles, links, and snippets. Arguments: `query` (required), `start` (optional offset: 0, 10, 20, ...).
- **Web Links Search** (`web-links-search`): Link search. Arguments: `prompt` (required), `tools` (optional, only `web`, default `["web"]`), `count` (optional, 10 to 200). `Web Search` is accepted and rewritten to `web`. Other sources are rejected. This tool does not accept X.
- **Extract** (`extract`): Read one public URL as text, markdown or HTML. Arguments: `url` (required), `format` (optional, `text`, `markdown` or `html`), `js` (optional), `wait` (optional milliseconds), `full_page` (optional, `text` only: every visible section of the page instead of its main content). Errors carry an `error_code`: `js_required` (retry with `js: true`), `blocked` (the site refused or showed a bot check), `page_not_found`.
- **Web Crawl** (`web-crawl`): Same arguments as Extract, on the legacy `/web/crawl` route. The SDK marks `webCrawl` deprecated in favor of `extract`. This tool stays so that route remains reachable.
- **X Search** (`x-search`): Search posts on X. Arguments: `query` (required), `count` (optional, default 20). Sort stays Top. Optional filters: `user`, `start_date`, `end_date` (YYYY-MM-DD), `lang`, `verified`, `blue_verified`, `is_quote`, `is_video`, `is_image`, `min_retweets`, `min_replies`, `min_likes`.
- **X Links Search** (`x-links-search`): Search for X post links. Arguments: `prompt` (required), `count` (optional, 10 to 200).
- **X Posts by URLs** (`x-posts-by-urls`): Posts for a list of post URLs. Argument: `urls` (required).
- **X Post by ID** (`x-post-by-id`): One post by ID. Argument: `id` (required).
- **X Posts by User** (`x-posts-by-user`): Posts by one user. Arguments: `user` (required), `query` (optional), `count` (optional, 1 to 100).
- **X User Posts** (`x-user-posts`): Posts from a username. Arguments: `username` (required), `cursor` (optional, from a previous response).
- **X User Replies** (`x-user-replies`): Posts and replies by one user. Arguments: `user` (required), `count` (optional, 1 to 100), `query` (optional).
- **X Post Replies** (`x-post-replies`): Replies to one post. Arguments: `post_id` (required), `count` (optional, 1 to 100), `query` (optional).
- **X Post Retweeters** (`x-post-retweeters`): Users who retweeted a post. Arguments: `id` (required), `cursor` (optional).
- **X Trends** (`x-trends`): Trends for a location WOEID. Arguments: `woeid` (required), `count` (optional, 30 to 100).
- **People Search** (`people-search`): Start a billed background search for people on `x`, `linkedin`, or `tiktok` who match a plain-language description. Arguments: `platform` and `query` (required), `count`, `min_score`, `exclude`, `idempotency_key`, and platform filters. Follow the confirmation and task workflow below.
- **People Search Status** (`people-search-status`): Status, progress, cost, and a page of people for one task. Arguments: `task_id` (required), `cursor`, `limit`, `include_people`.
- **Pause People Search** (`people-search-pause`): Pause a task. Argument: `task_id` (required).
- **Resume People Search** (`people-search-resume`): Resume a paused task with no new charge. Argument: `task_id` (required).
- **Cancel People Search** (`people-search-cancel`): Stop a task for good; only people already delivered are billed. Argument: `task_id` (required). Cancel cannot be undone.
- **List People Search Tasks** (`people-search-tasks`): Your searches, newest first. Arguments: `platform`, `status`, `limit`, `cursor`, all optional.
- **List People Found** (`people-profiles`): Everyone your searches found on one platform, with filters. Free; it does not start a search. Arguments: `platform` (required) and optional filters.
- **Company Search** (`company-search`): Start a billed background task that finds companies on the web matching a plain-language description, with each one's public emails, phones, social profiles, and leaders. Arguments: `query` (required), `count`, `min_score`, `exclude`, `countries`, `require`, `roles`, `infer_emails`, `verify_emails`, `metadata`, `idempotency_key`. Follow the confirmation and task workflow below.
- **Company Enrich** (`company-enrich`): Start a billed background task that collects the same data for domains you already have, one company per domain, with no fit score. Arguments: `domains` (required, 1 to 200), `roles`, `infer_emails`, `verify_emails`, `metadata`, `idempotency_key`. Follow the confirmation and task workflow below.
- **Company Search Status** (`company-search-status`): Status, progress, cost, and a page of companies for one Company Search or Company Enrich task. Arguments: `task_id` (required), `cursor`, `limit`, `include_companies`.
- **Pause Company Search** (`company-search-pause`): Pause a company task. Argument: `task_id` (required).
- **Resume Company Search** (`company-search-resume`): Resume a paused company task with no new charge. Argument: `task_id` (required).
- **Cancel Company Search** (`company-search-cancel`): Stop a company task for good; only companies already delivered are billed. Argument: `task_id` (required). Cancel cannot be undone.
- **List Company Search Tasks** (`company-search-tasks`): Your Company Search and Company Enrich tasks, newest first. Arguments: `type` (`search` or `enrich`), `status`, `limit`, `cursor`, `metadata`, all optional.


People Search details:

- `query`: 3 to 1000 characters; `count`: 5 to 200, default 25; `min_score`: 0 to 100; `exclude`: handles to omit.
- Send platform filters as top-level arguments. X accepts `min_followers`, `max_followers`, `verified_only`, `countries`, `keywords`, `exclude_keywords`. LinkedIn accepts the same except `verified_only`. TikTok accepts `min_followers`, `max_followers`, `require_contact`, `keywords`, `exclude_keywords`.
- `people-profiles` accepts `q`, `sort` (`score`, `recent`, `audience`), `limit` (1 to 200), `cursor`, `has` (contact or profile link types), `min_audience`, `max_audience`, `min_score`, `location`, `verified` (X and TikTok only).
- People status accepts `limit` 1 to 100, default 25; `include_people: false` reads only status and cost. Task lists accept `status` lists and `limit` 1 to 100.

Company details:

- Company Search `query`: 3 to 1000 characters; `count`: 5 to 200, default 25; `min_score`: 0 to 100; `exclude`: up to 1000 domains or URLs; `countries`: up to 20 country names or ISO codes.
- Top-level `require` accepts `email`, `phone`, `linkedin`, `leader`, `leader_email`. Companies missing any required field are not delivered or billed.
- Company Enrich accepts 1 to 200 domains or URLs, normalizes hosts and removes duplicates. It returns no fit score.
- Both starters accept `roles` (`ceo`, `founder`, `cto`, `cfo`, `coo`, `cmo`, `president`, `owner`, `managing_director`; default the first four), `infer_emails` and `verify_emails` (both default true), `metadata` (up to 10 labels with string values up to 200 characters), and `idempotency_key`.
- Company status accepts `limit` 1 to 100, default 10; `include_companies: false` reads only status and cost. Task listing accepts `status` lists, `limit` 1 to 100 and up to three exact-match `metadata` labels.

Background task workflow:

1. Check existing results with `people-profiles` or the relevant task list before starting more work.
2. **Confirm with the user before starting `people-search`, `company-search` or `company-enrich`.** State the tool, platform where applicable, count or domains, and maximum reservation using current pricing. Wait for confirmation before calling the starter.
3. Start once with a new `idempotency_key`; reuse it on retries after failures or timeouts to avoid duplicate billed tasks. Keep `task_id` and `reserved_cost_usd`.
4. Poll the matching status tool every 15 to 30 seconds, follow its `next` hint, and pass `next_cursor` as `cursor` for further pages. People and company task IDs are not interchangeable.
5. Finish when `done` is true and `has_more` is false. For companies, reread without `cursor` and paginate again to collect final enriched versions. Report `final_cost_usd`, `completion_reason`, `coverage_limited` and warnings when present.

Status, results, task control and task lists are free. Resume works only on paused tasks and adds no new charge. Pause allows the current batch to finish; a task paused longer than seven days is cancelled. **Confirm before cancellation because cancel cannot be undone**; delivered results remain billable. Results remain available for 30 days after completion. The MCP tools do not export files.

## Example

**User**: "Find public research on urban heat mitigation and summarize two useful pages with links."

**Output**:

```text
Call web-search with query="urban heat mitigation research".
Call extract for two selected result URLs with format="markdown".
Return each page's title, source URL, supported findings and limitations.
```

This illustrates the workflow and output structure, rather than a live search result.

## Tips

- Use People Search for lists of people, X search for posts, and Company Enrich for known domains.
- For `extract`, retry `js_required` with `js: true`; report `blocked` or `page_not_found` rather than inventing content.
- Preserve evidence and contact source URLs. Label inferred emails as guesses and include their verification status.
- Missing fields mean unavailable data, not zero or proof that a contact does not exist.
- Surface incomplete enrichment, unverified criteria and task warnings.
- Tools read public data; they do not access logged-in pages, contact people or companies, or change account settings.

## Common Use Cases

- Topic research with AI summaries and supporting web links.
- Public page extraction for comparison or analysis.
- X post, reply and trend research.
- Finding creators or professionals on X, LinkedIn or TikTok.
- Company discovery with required contacts and evidence of fit.
- Enriching known domains and reviewing existing background results.
