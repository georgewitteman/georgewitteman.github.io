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
- **Older browsers must work.** Write ES6 (ES2015) JavaScript. Use
  `const`/`let` (never `var`), arrow functions, template literals, `Promise`,
  and `fetch`. Nothing newer: no `async`/`await`, `BigInt`, `?.`/`??`,
  optional catch binding, or trailing commas in calls. Stick to widely
  supported CSS too.
- **Use as few external services as possible.** Each one is something that
  can disappear. If a page needs one, make it fail gracefully.
- **Keep it minimal.** Small diffs, few files, no abstractions or features
  nobody asked for. Use relative links so the site works under any path.

## Layout

- `index.html`: home page that links to each tool.
- `check.html`: checks whether an IP is an iCloud Private Relay address and
  shows the relay's location.
- `ranges.csv`: Apple's egress ranges (`cidr,country,region,city`), refreshed
  daily by `.github/workflows/update-ranges.yml`. If that workflow ever
  stops running, the site still works, just with older data.
