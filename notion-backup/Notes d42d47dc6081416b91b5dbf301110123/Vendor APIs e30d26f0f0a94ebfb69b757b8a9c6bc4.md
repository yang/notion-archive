# Vendor APIs

# Dropbox API

Dropbox is migrating away from legacy Paper API to a consolidated API ([source](https://www.dropbox.com/lp/developers/reference/paper-migration-guide)). Unfortunately the new API has no ability to export Paper docs as markdown, only HTML! ([source](https://www.dropboxforum.com/t5/Dropbox-API-Support-Feedback/Does-Paper-still-support-export-as-Markdown/td-p/393500))

Also, experimentally, I’ve found that:

- Accounts are either in old paper.dropbox.com or new dropbox.com
- Former can use Paper API, latter can use new API only
- New API cannot access old paper.dropbox.com docs (not found)

Unfortunate because this was a nice easy CMS, not just for authoring docs, but also easily embedding images, videos, and inter-linking.