# Contributing

This guide grows through other people's migrations. One migration produced the framework; many migrations will make it trustworthy. If you've fought a Control4 → Home Assistant battle and learned something a stranger could use, that lesson belongs here.

## Ways to contribute

- **Share a gotcha** — open an issue using the *Share a gotcha / lesson* form. Best for a single hard-won lesson you can state as a general rule.
- **Suggest a recommendation or correction** — open an issue using the *Suggest a recommendation / correction* form. Best for a better approach, an alternative, a factual fix, or a topic you'd like to see covered.
- **Start a Discussion** — for open-ended "how did you handle X?" conversations that aren't a specific gotcha or suggestion.
- **Open a pull request** — for a full lesson write-up. Match the existing structure (see below) and the bar that every page here has to clear.

## The gotcha entry convention

Full lessons in `docs/gotchas/` all follow the same shape so readers can scan for the rule and dig in only where they need to. If you're submitting a full lesson, use these sections:

- **TL;DR** — the general rule in one sentence.
- **The situation** — the generalized scenario, no specific gear or IPs.
- **What bit me** — the failure, described generally.
- **The general rule** — the reusable principle and *why* it holds.
- **How to apply it** — concrete, vendor-neutral steps a reader can follow.
- **How to verify** — how the reader confirms they got it right.

An optional credit line at the bottom is welcome — for example, `— contributed by <handle>`. Use whatever handle you'd like to be known by; there's no need to use a real name.

## Anonymization expectations (required of all contributions)

Everything published here has to work for a stranger with a different house. These are not suggestions — submissions that include private details will be sent back for edits before they're accepted:

- **No IPs, hostnames, or network topology.** Not yours, not anyone's.
- **No personal identifying information** — no real names, titles, employers, locations, or family details.
- **No exact model → input maps** or other configuration that only describes one specific install. Describe the *pattern*, not your wiring.
- **Principle-level phrasing.** Brand names are fine only as neutral illustrative examples ("an AVR such as one of the common brands"), never as part of a specific config or mapping.
- **The bar:** *would this help a stranger with a different house?* If the answer needs your specific setup to make sense, generalize it until it doesn't.

### Illustrative snippets

The Home Assistant build guide (`docs/building/`) is the one place small config snippets are allowed. They must be **illustrative, not copy-paste**:

- Use generic placeholder entities only (`media_player.kitchen`, `media_player.living_room_avr`) — never real entity IDs, device models, or input maps.
- No IPs, hostnames, or anything identifying.
- Keep them minimal — the smallest fragment that shows the pattern — and label them illustrative.

## Tone

Honest, practitioner-to-practitioner. Say what actually went wrong and what you'd do differently. No vendor bashing — the goal is to help people decide and migrate well, not to settle scores.

## License

Contributions are accepted under [CC-BY-4.0](LICENSE). By submitting, you agree your contribution can be published and adapted under that license with attribution.
