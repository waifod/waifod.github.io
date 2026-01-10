# Newsboat Configuration Improvement Plan

## Current State Analysis

### Configuration Files
- **config**: Well-structured with good defaults (auto-reload, notifications, caching)
- **bindings**: Clean vim-style navigation
- **macros**: Useful utilities (copy URL, mpv, download)
- **theme**: Simple, functional color scheme
- **urls**: 126 feeds across multiple categories, many YouTube channels without labels

### Identified Issues
1. **Feed Overload**: 126 feeds total, with ~20 unlabeled YouTube channels making it hard to prioritize
2. **Poor Organization**: Feeds mixed by topic without clear hierarchy
3. **Missing Categories**: No dedicated sections for embedded/low-level/performance content
4. **YouTube Chaos**: 20+ YouTube channels with generic "video" tags, hard to distinguish
5. **No Priority System**: All feeds treated equally, no way to focus on high-priority sources
6. **Potential Dead Feeds**: Some blogs may be inactive (need verification)

## Improvement Strategy

### Phase 1: Configuration Enhancements

#### 1.1 Config File Improvements
- Add feed-level priority system using `!` prefix for high-priority feeds
- Configure ignore-article rules to filter noise (weekly summaries, etc.)
- Add bookmark support for saving important articles
- Enable better search with `search-highlight-colors`
- Add `prepopulate-query-feeds yes` for faster query feed loading

#### 1.2 Bindings Enhancements
- Add marking shortcuts (mark all read, toggle read status)
- Add search bindings
- Add bookmark shortcuts
- Add feed reload shortcuts

#### 1.3 Macros Additions
- Add "read later" macro (save to file/service)
- Add "share" macro for interesting articles
- Add YouTube-dl macro for downloading videos

### Phase 2: Feed Reorganization

#### 2.1 New Category Structure
```
Priority Feeds (! prefix)
├── Daily Must-Read Blogs
└── Critical News Sources

Technical Content
├── Low-Level Programming & Performance
├── Embedded Development
├── Operating Systems (Linux)
├── Rust Development
├── C++ Development
├── Haskell & Functional Programming
└── General Programming

Academic & Research
├── ArXiv (CS topics)
└── Math & Theory

Rationalist Community
├── Core Blogs
└── Extended Network

Economics & Finance

Entertainment
├── Comics
└── Fiction

Video Content
├── Technical Channels (labeled by focus)
├── Educational Channels
└── Entertainment Channels

Archived/Inactive Feeds
└── Dead or rarely-updated blogs
```

#### 2.2 YouTube Channel Identification
Research and label all 20 unlabeled YouTube channels by:
- Fetching channel names from YouTube
- Categorizing by content type
- Adding descriptive tags

#### 2.3 Dead Feed Detection
Check last update dates for all feeds and move inactive ones (>6 months) to archive section

### Phase 3: New Feed Recommendations

#### 3.1 Embedded Development
- Embedded Artistry (https://embeddedartistry.com/feed/)
- Interrupt (Memfault blog) (https://interrupt.memfault.com/blog/feed.xml)
- Embedded.fm podcast (https://embedded.fm/episodes?format=rss)
- Phil's Lab (embedded YouTube channel)
- Embedded Related (https://www.embeddedrelated.com/blogs.rss)

#### 3.2 Low-Level Programming & Performance
- Easyperf (https://easyperf.net/feed.xml)
- Daniel Lemire's blog (https://lemire.me/blog/feed/)
- Brendan Gregg (http://www.brendangregg.com/blog/rss.xml)
- Agner Fog's optimization resources
- LWN.net (https://lwn.net/headlines/rss) - Linux kernel development

#### 3.3 Operating Systems (Linux)
- LWN.net (already mentioned)
- Kernel Newbies (https://kernelnewbies.org/RecentChanges?action=rss_rc)
- Linux Foundation blog
- Red Hat Developer blog (https://developers.redhat.com/blog/feed/)

#### 3.4 Systems Programming
- Julia Evans (https://jvns.ca/atom.xml)
- Cloudflare blog (https://blog.cloudflare.com/rss/)
- Netflix Tech Blog (https://netflixtechblog.com/feed)

### Phase 4: Query Feed Enhancements

#### 4.1 New Query Feeds
- "High Priority Today": Priority feeds from last 24h
- "Technical Deep Dives": Long-form technical articles
- "Performance & Optimization": Tagged performance content
- "Embedded & Low-Level": Combined embedded/systems content
- "Video Queue": Unread video content

#### 4.2 Improved Filtering
- Filter out "Weekly Summary" posts
- Filter out "This Week In" posts (unless specifically wanted)
- Filter promotional content

## Implementation Order

1. **Immediate** (Phase 1): Update config, bindings, macros
2. **Day 1** (Phase 2): Reorganize existing feeds, identify YouTube channels
3. **Day 2** (Phase 2): Check for dead feeds, create archive section
4. **Day 3** (Phase 3): Add new recommended feeds
5. **Day 4** (Phase 4): Create enhanced query feeds

## Success Metrics

- Reduced time to check important feeds (priority system)
- Clear categorization makes finding content easier
- No more missing updates from favorite blogs
- Dead feeds archived but not lost
- New feeds aligned with stated interests
- Query feeds provide useful filtered views

## Next Steps

1. Review and approve this plan
2. Proceed with implementation in phases
3. Test configuration after each phase
4. Iterate based on usage patterns
