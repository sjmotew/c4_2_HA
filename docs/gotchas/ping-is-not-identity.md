# Ping is not identity

**TL;DR:** Something answering at an address is not proof it's your device — identify by the service it speaks, not by whether it replies, and remember that randomized client MACs quietly break address reservations.

## The situation

You write down a device's address once, early, when you are moving fast and everything is new. From then on, that address is the device as far as every script, every note, and every troubleshooting session is concerned. When you want to know whether the device is up, you ping it. It replies. You move on.

A home network has dozens of things on it, most of which answer ICMP and none of which say what they are.

## What bit me

A wall tablet's address was documented wrong for months. The address in the notes belonged to an unrelated appliance on the same network — a pool controller — which answered pings reliably. That is precisely why the error survived: every check "confirmed" the tablet was up. The tablet was up too, at a different address, so nothing ever contradicted the note badly enough to force a re-check.

Underneath that was a second problem. The tablet used a randomized per-network MAC, so the address reservation keyed to its hardware MAC never applied. It drifted whenever the lease turned over. And on a separate occasion a device was declared dead and scheduled for replacement when it had simply drifted to a new address — the hardware was fine.

The last piece cost an extra hour on its own: after the tablet was moved to a correct, reserved address, the integration that talked to it kept failing, because that integration had cached *both* the old address and the old randomized MAC in its own stored configuration. It failed silently and looked exactly like dead hardware.

## The general rule

**Reachability is not identity.** ICMP proves that some network stack exists at an address. It says nothing about which device it belongs to, and any address you did not verify by its service is a guess that will eventually be wrong.

The second half of the rule is narrower but bites just as hard: **a reservation keyed to a randomized MAC is not a reservation.** Modern client operating systems default to per-network MAC randomization for privacy, which is a good default and quietly incompatible with static assignment. The address will hold until it doesn't.

## How to apply it

- **Identify by the service, not the reply.** Probe the device's control port, its API, its management interface — something only that device speaks. A successful connection on its own protocol is identity; an ICMP echo is not.
- **Disable per-network MAC randomization on any device you intend to reserve an address for**, and confirm the reservation is keyed to the hardware MAC.
- **When a device looks dead, check for address drift before checking for hardware failure.** Scan for its service, not for its old address.
- **When you move a device's address, update every place that stores it.** Integrations commonly cache address *and* MAC together, and a half-updated entry fails without an error.
- **Never let a documented address outlive its verification.** Re-confirm it the same way you confirmed it originally.

## How to verify

- For every device in your notes, you can state the service probe that confirms it — not just its address.
- The reservation list and the devices' hardware MACs match, with randomization off on each reserved client.
- A device that has been power-cycled and rejoined the network still answers on its expected address, on its own protocol.
