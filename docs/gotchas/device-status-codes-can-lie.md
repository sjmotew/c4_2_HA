# Device status codes can lie

**TL;DR:** Don't assume a device's protocol uses HTTP/TCP status codes the way the web does — a `401` may mean "malformed command," not "wrong credentials" — verify against the device's own spec before you chase the wrong problem.

## The situation

You're integrating a device over its IP control protocol — a matrix switch, an AVR, a display. It speaks something that *looks* like HTTP, and it returns familiar-looking status codes. So when a control command comes back with a code you recognize, you interpret it the way you would on the web: `401` means authentication failed, `404` means wrong endpoint, and so on. You start debugging from that interpretation.

## What bit me

A control command to a device returned `401`. The natural reading — wrong password — sent debugging straight down the credentials path: re-check the token, re-pair, re-enter the secret. None of it helped, because in *that device's* protocol `401` didn't mean "unauthorized" at all. It meant the command itself was illegal or malformed. The real fix was the command's format, not its authentication. Reading a borrowed status code with its borrowed meaning could have burned days.

## The general rule

**A device's protocol owns the meaning of its status codes — not the web's conventions.** Vendors routinely repurpose numeric codes to mean whatever their protocol needs. The number looking familiar tells you nothing reliable; only the device's own documentation does.

This holds because pattern-matching is seductive when you're debugging: a code you've seen a thousand times hands you an instant hypothesis, and a wrong instant hypothesis is worse than no hypothesis at all — it sends you confidently in the wrong direction. The most expensive debugging sessions are the ones where you were *sure* you knew what the error meant.

## How to apply it

- **Read the device's protocol spec for the codes you get** before acting on what they "obviously" mean. If the spec is silent, treat your interpretation as a guess, not a fact.
- **Capture the device's own working traffic.** The fastest way to learn the exact, accepted command format is to watch the device's own web UI or app talk to it, then copy that format.
- **Prove control with a physical, end-to-end change** — and under rapid repeats. A request that returns "success," or a read-only query that works, is not proof the write path works (see [prove the riskiest integration first](prove-the-riskiest-integration-first.md)). Have someone in the room confirm the zone actually switched.
- **Separate "the command was rejected" from "the command was wrong."** When a control call fails, question the command format as readily as the credentials.

## How to verify

- For every status code your integration acts on, you can cite where the device's own spec (or its observed traffic) defines that meaning — not the web's.
- A control command produces a confirmed physical change in the room, repeatedly, not just a success response.
- When something fails, your first move is to compare your command against the device's own captured traffic, not to assume you know which layer is at fault.
