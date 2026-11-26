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
- **No tests or test pages.** Check each change by hand instead (below).
  `clean-link/` site rules list made-up examples (a link, then `→` what
  it becomes) in their comments; add or update them with the rule.
- **Use made-up examples.** Links, IDs, names, and emails a user pastes stay
  out of commits, code, and docs. Use invented ones like `example.com`.

## Checking a change

- Serve the repo with `python3 -m http.server` and open the changed page
  from there. Pages that `fetch` their data files don't work from `file://`.
- Try a few made-up inputs, including ones that should be left alone, and
  check that the page shows no errors.
- For `clean-link/`, run every site rule example, not just the changed one.
- Agents without a screen can do this with headless Chromium (Playwright),
  from a script kept outside the repo. Stand in for external services that
  aren't reachable from the sandbox.

## Code style

Optimize for readability over brevity.

- Always use `{}` for `if`, loops, etc., even for one-line bodies.
- Never put `return`, `continue`, or `throw` on the same line as its `if`.
- Use descriptive names (`address`, `prefixLength`), not `s`, `p`, `n`.
  `i` is fine for a plain index loop.
- Prefer early returns and `async`/`await` over nesting and `.then` chains.
- Put each comment on its own lines above the code it explains, and
  leave a blank line between unrelated blocks.
- No doc comments at the top of files. Why a file exists goes in the
  commit that adds it.
- Explain syntax and APIs a reader might look up, like a regex, `??=`, or
  `Intl.DisplayNames`, and name the one you mean. For dense notation,
  explain the notation rather than each value. Skip the obvious, and keep
  comments as short as clarity allows.
- Avoid comments that go stale: references to other files or how other
  code works, history, dates, versions, and measurements. Explaining why a
  value was chosen is fine, even if it names something else.
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
  outdated or redundant.
- Leave out anything an agent learns by reading the code, like what a page
  does. Write down decisions and gotchas the code can't show.
- One idea per bullet, no history or changelogs. Put it in the right section.
- This is the only guidance file. Claude Code reads it when there's no
  `CLAUDE.md`, so don't add one (or any other tool-specific copy).

## Layout

- Each tool lives in its own folder as `<tool>/index.html`, with its data
  files beside it, so URLs are `/<tool>/` on any host. Link new tools from
  `index.html`.
- `favicon.ico` and `apple-touch-icon.png`: browsers find `favicon.ico` at
  the site root by themselves, so pages don't link to it. Only the home page
  links the iOS icon, since that's the page that gets bookmarked.
- External services, each of which must fail gracefully: `icanhazip.com`
  and `cloudflare-dns.com` (iCloud page), the DNS page's example DoH
  servers (default `cloudflare-dns.com`; it also links to `bgp.he.net`),
  and `mask-api.icloud.com` (ranges workflow).
- `icloud-private-relay/ranges.csv`: Apple's list, saved as-is (fields may
  be quoted). The daily workflow rejects downloads that don't look like the
  usual list; if it stops, the page keeps working with older data.
- `icloud-private-relay/ranges-updated.txt`: date of the last refresh,
  rewritten when the list changes and at least weekly. The page shows it and
  warns past 30 days. Not `Last-Modified`, which GitHub Pages sets to the
  last deploy.
- `icloud-private-relay/`: the visitor's hostnames come from Cloudflare's
  DoH JSON API (`application/dns-json`), on purpose, unlike `dns/`.
- `clean-link/`: the goal is the site's own canonical link minus tracking,
  not the shortest link that works. Use a form the site itself gives out
  (address bar or share button), not one that only works by redirecting,
  like `gmail.com` for Gmail.
- `clean-link/`: a site rule that doesn't recognize a link's format leaves
  it alone instead of guessing, so when a site changes its links they stop
  being cleaned rather than break. The link to clean can be passed as
  `#<link>`, never `?`, so it doesn't reach the server. The page never
  writes links into the address, so they stay out of browser history.
- `dns/`: queries are standard DNS-over-HTTPS (RFC 8484 binary messages),
  not Google's or Cloudflare's JSON APIs, so any DoH server works. It uses
  one server at a time, chosen in the page or with `?server=`.
- `dns/`: `?q=` is short because it's typed by hand; `?server=` is spelled
  out because the page sets it. Each lookup of a new name or server pushes
  a history entry, like a search page, except from an empty page, so Back
  never lands on an empty form.
