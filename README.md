# georgewitteman.github.io

Small static tools. Plain HTML files with no build step and no dependencies.
Deploy by serving the folder from any static host.

- `icloud-private-relay/`: checks whether your IP is an iCloud Private Relay
  address and, if it is, shows the relay's location. Its `ranges.csv` holds
  Apple's egress IP ranges, refreshed daily by a GitHub Action.

To run locally: `python3 -m http.server`, then open http://localhost:8000.

See [AGENTS.md](AGENTS.md) for contribution rules.
