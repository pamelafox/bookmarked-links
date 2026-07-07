# Using Agents and MCP Servers

This guide covers tips for using available agents and MCP servers when working on this repository.

## X (Twitter) MCP Server

The X MCP server is valuable for discovering and verifying links from tweets.

### When to use it

- **Finding links from tweet threads**: When an issue references X tweets containing links, use the X API to extract the actual URLs rather than guessing.
- **Verifying tweet content**: Get authoritative data directly from the tweet source.
- **Resolving shortened links**: Twitter uses t.co shortlinks; the X API returns the tweet text, and you can detect redirects to find final URLs.

### How to use it

#### Step 1: Get the tweet IDs from the issue

Look for X/Twitter URLs in issue descriptions. Extract the status ID from the URL:
- URL format: `https://x.com/username/status/TWEET_ID`
- Example: `https://x.com/pamelafox/status/2074273436319584454` → tweet ID is `2074273436319584454`

#### Step 2: Fetch tweet data using X-twitter-getPostsById

Use the `X-twitter-getPostsById` tool with:
- `ids`: Array of tweet IDs
- `tweet.fields`: Include `["text"]` to get the tweet content

```
Call X-twitter-getPostsById with ids: ["2074273436319584454"]
Response will include tweet.data[0].text containing the tweet content
```

#### Step 3: Extract t.co links from tweet text

Parse the tweet text to find t.co shortlinks (format: `https://t.co/XXXXXXX`).

#### Step 4: Resolve t.co links to final URLs

Use `web_fetch` on the t.co link. The tool will detect HTTP 301 redirects and report the final URL:
```
web_fetch on https://t.co/abc123 → reports redirect to https://www.youtube.com/watch?v=XXXXX
```

### Example: Issue #4 Video Bookmarks

Issue #4 referenced two X tweets with video links:
1. Fetched tweets using X API → found t.co links in tweet text
2. Resolved t.co links → discovered YouTube URLs
3. Updated README.md with real video titles and correct links

**Result**: Accurate bookmarks sourced from authoritative tweet data instead of hallucinated URLs.

### Tips

- Always verify links are real before adding them to the repository
- Use X API to get authoritative data rather than copying URLs from screenshots/descriptions
- Remember that t.co links require following redirects to find the actual target
- Document which tweets the links came from in commit messages (e.g., "Fixes #4" with actual X tweet IDs)
