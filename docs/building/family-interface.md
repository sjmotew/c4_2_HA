# The interface everyone else uses

You will control this system from a laptop, with a terminal open, on a network you understand, having just deployed the thing you are about to test. Nobody else in the house will.

For everyone else, the wall tablet **is** the system. Not a convenience layer over it — the whole product. And it fails in ways your laptop never will, because it is always on, never refreshed, mounted where nobody can plug in a keyboard, and used by people who have no model of what's underneath it. Treat it as a deliverable with its own requirements, not as a dashboard you happen to have opened on a spare device.

## It must survive a cold boot with zero touches

The requirement is exact: power returns, and without anyone touching it, the tablet reaches the dashboard. Not "reaches the login screen." Not "reaches the home screen where you tap the icon." The dashboard.

The single most common blocker is the device lock screen. Any PIN or pattern halts the operating system before the kiosk browser ever launches, and the tablet sits on a lock screen until someone walks past and notices. Removing the lock screen is what makes unattended boot work, and it is a real security tradeoff worth stating plainly: a wall-mounted tablet with no lock screen is a control surface anyone in the house — or in the house as a guest — can use. For a device whose entire job is to be tapped by anyone standing in front of it, that is usually the right trade. Make it deliberately.

The rest of the chain has to hold too: the kiosk app set to launch on boot, the session already authenticated so no login prompt appears, and a stable address so the dashboard it loads still exists. Test it the honest way — pull the power, walk away, come back.

## Kiosk mode and the way out

Lock the browser to one page, and **know the recovery gesture before you need it.**

Kiosk browsers deliberately make some settings unreachable remotely — that's the point of a kiosk. The consequence is that the exit path is on-device only: a specific gesture, then a PIN, then the settings menu. If you have not written that down somewhere other than the tablet, you will eventually be standing in front of a locked device trying to remember which edge to swipe from.

Two practical notes. Set the exit PIN to something other than the default, since the default is documented publicly. And keep the recovery procedure in the same place as the rest of your runbook, not in your head.

## Burn-in and the always-on display

An always-lit panel showing a mostly-static dashboard will burn in. This is not a maybe.

A screensaver is not optional, and the useful version of it is harder than "go dark after ten minutes." A wall panel that goes black is a wall panel people assume is broken, so they stop looking at it. The screensaver has to be **legible from across the room, in the light that room actually has** — which means it earns its place by showing something worth glancing at: what's playing, where, the time, whatever the household actually wants at a distance. Design it as a second, low-information view of the system rather than as an absence of one.

Move the content around, dim rather than blank where you can, and check the panel for retention after a few months rather than assuming you got it right.

## Power and charging

A wall tablet that charges continuously degrades its battery until it swells; one that doesn't charge dies at some point when nobody is watching. Neither default is safe.

- **Alert on low battery.** It is the failure that looks exactly like a dead tablet.
- **Consider a charge-aware display profile** — brightness and screensaver behavior that differ depending on whether it's charging — so the device isn't running at full brightness on battery.
- **Expose battery state on the dashboard you look at**, not just in the device's own settings, so you notice it before somebody else does.

## The silent no-op

The tablet's signature failure is a tap that does nothing at all. No error on screen, no log entry, no visible difference from a person who didn't press anything.

The usual cause is session age. The tablet has held the same page open for days, spanning several deploys, and the reload that took effect on the server invalidated what the page is holding. The tap registers on the glass and goes nowhere. **Check whether the action ever fired before you debug the action** — if the last-triggered timestamp never moved, the problem is the client, not your automation. The full treatment is in [deploys invalidate open clients](../gotchas/deploys-invalidate-open-clients.md). The operational fix is to make refreshing the wall tablet the last step of every deploy, and to consider a scheduled refresh so session age is bounded whether or not you remember.

## Design for the person who didn't build it

Everything above is about the device. This is about the screen.

- **No scrolling to reach a common control.** If turning off the music requires a scroll, it requires a decision, and people who are unsure stop tapping.
- **No ambiguity about what's currently playing, and where.** Those are two separate facts and the interface has to show both — a room that never joined the group must not read as playing. See [architecture](architecture.md) on why now-playing describes the engine, not the room.
- **Every action reversible by the same person who triggered it.** If someone can start it, they can stop it, from the same screen, without knowing what a group is.
- **Say when something didn't work.** Skipped rooms belong on this screen; see [reliability](reliability.md).
- **Room-per-screen beats one dense wall of controls.** The person standing in the kitchen wants the kitchen.

The test is not whether you can operate it. It's whether someone can operate it while you are out of the house and unreachable.

## See also

- [Stage 5 — Operate & harden](../framework/05-operate-and-harden.md) — where the family-facing surface becomes a deliverable.
- [Reliability](reliability.md) — visible degradation, which this screen renders.
- [Gotcha: deploys invalidate open clients](../gotchas/deploys-invalidate-open-clients.md).
- [Gotcha: ping is not identity](../gotchas/ping-is-not-identity.md) — the address-drift problem this device is unusually prone to.
