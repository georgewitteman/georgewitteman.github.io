# Agent guidance

This repo is a handful of small static tools. Keep it that way.

## Goal: zero maintenance, timeless

The site must keep working for years with nobody touching it. That means
no updates, no security patches, and no new vulnerabilities appearing while
it sits unmaintained. Every change should be judged against that.

## Rules

- **Static files only.** Plain `.html` files with inline CSS and JS, plus
  data files. They must work when served from any static host (GitHub Pages,
  S3, a local folder, etc.).
- **No build step.** No bundlers, transpilers, package managers, or
  `package.json`. What's in the repo is exactly what gets served.
- **No dependencies.** No npm packages, CDN scripts, web fonts, or frameworks.
  Anything that can go away, break, or need patching does not belong here.
- **Only use Baseline "widely available" web features.** Check MDN's
  Baseline badge; "newly available" is not enough, since older devices that
  never update won't have it. The only exception is `Intl.DurationFormat`
  in `dns/`, which is checked for first; where it's missing, the readable
  durations are left out. Don't use features that are experimental,
  non-standard, or deprecated (or likely to be). Never use `var`; use
  `const`/`let`.
- **Use as few external services as possible.** Each one is something that
  can disappear. If a page needs one, make it fail gracefully.
- **Never put untrusted data into HTML.** Write anything from user input,
  APIs, or data files with `textContent`, never `innerHTML`,
  `insertAdjacentHTML`, `document.write`, or `eval`. This is the site's main
  defense against script injection, so headers or CSP aren't needed.
- **Keep it minimal.** Small diffs, few files, no abstractions or features
  nobody asked for. Use relative links so the site works under any path.
  Keep styling minimal too: browser defaults plus a few lines of CSS.
- **Every page stands alone.** No page loads, frames, or calls code from
  another page. Each tool's data files are the only exception.
- **No tests or test pages.** Check changes by hand in a browser instead.

## Code style

Optimize for readability over brevity.

- Always use `{}` for `if`, loops, etc., even for one-line bodies.
- Never put `return`, `continue`, or `throw` on the same line as its `if`.
- Use descriptive names (`address`, `prefixLength`), not `s`, `p`, `n`.
  `i` is fine for a plain index loop.
- Prefer early returns and `async`/`await` over nesting and `.then` chains.
- Group code into commented sections and explain anything non-obvious.
- HTML: two-space indent, `<head>` and `<body>` indented inside `<html>`,
  no self-closing slash on void elements (`<meta>`, not `<meta />`), and
  every form control has a label (`aria-label` is fine).
- Workflows: no third-party actions (not even `actions/checkout`), just
  shell commands, so there's no action version to keep updated.

## Keeping this file current

Update this file as part of your change, without being asked, whenever:

- the user states a preference or rule that should apply to future work, or
- a change makes something here wrong (layout, files, workflows).

Keep it short and easy to scan:

- Edit or replace existing lines rather than appending. Remove anything
  outdated, redundant, or obvious from the code itself.
- One idea per bullet, no history or changelogs. Put it in the right section.
- This is the only guidance file. `CLAUDE.md` and `GEMINI.md` just import it,
  so don't add content to them.

## Layout

- `index.html`: home page that links to each tool.
- `favicon.ico` and `apple-touch-icon.png`: the site icon, a white pixel G
  on red. Browsers find `favicon.ico` at the site root by themselves, so
  pages don't link to it. Only the home page links the iOS icon, since
  that's the page that gets bookmarked.
- Each tool lives in its own folder as `<tool>/index.html`, with its data
  files beside it, so URLs are `/<tool>/` on any host.
- `icloud-private-relay/`: checks whether an IP is an iCloud Private Relay
  address and shows the relay's location.
- `icloud-private-relay/ranges.csv`: Apple's egress ranges, saved as-is
  (`cidr,country,region,city,postal`, CSV that may contain quoted fields),
  refreshed daily by `.github/workflows/update-ranges.yml`, which rejects a
  download that doesn't look like the usual list. If that workflow ever stops
  running or fails, the site still works, just with older data.
- `icloud-private-relay/ranges-updated.txt`: the date (`YYYY-MM-DD`) the
  workflow last refreshed `ranges.csv`, rewritten when the list changes and
  at least weekly. The page shows it and warns once it's over 30 days old.
  Not `Last-Modified`, which GitHub Pages sets to the last deploy.
- `clean-link/`: strips tracking and clutter from pasted links. Per-site
  rules (Amazon, eBay, Gmail, Google, YouTube, X, Spotify, Instagram,
  Notion, LinkedIn, Reddit, Linear, Figma) come first, then a generic
  tracking-parameter list runs on every link. `#<link>` prefills the link,
  encoded or not; it's in the `#` part so it never reaches the server. The
  page doesn't write links into the address, so cleaned links stay out of
  browser history.
- `dns/`: looks up a name's records (or an IP's PTR) with both Google and
  Cloudflare DNS-over-HTTPS JSON APIs and shows where they differ. `?q=`
  prefills and runs a lookup (short, since it's typed by hand). Each lookup
  of a new name (not typing) pushes a history entry, except from an empty
  page, which is replaced; the name is also put in the page title. A
  SERVFAIL is retried with `cd=1` (validation off); if that answers, it's
  shown as a DNSSEC failure. IP WHOIS links go to `bgp.he.net`.
