# Wake Up

A lightweight remote Wake-on-LAN controller for a spare Windows laptop, to wake a sleeping gaming desktop from an iPhone over a **private Tailscale connection**.

> **Status:** Build specification only. No working executable has been produced or tested yet.

Read [CLAUDE.md](./CLAUDE.md) for the complete implementation, security, packaging, and QA requirements.

## Intended workflow
- Build on gaming desktop with Claude Code.
- Package as a self-contained Windows app.
- Copy the app to the spare laptop, which still works as a regular laptop.
- Toggle **Remote Wake Mode ON** to keep laptop awake on AC (display off) and serve a private iPhone wake button.
- Toggle **OFF** to stop wake service and restore normal laptop sleep/lid-close behavior.
- Install free Tailscale on the spare laptop and iPhone; use Tailscale **Serve**, never Funnel or public port forwarding.

## Security
This repository is **public**. Do not commit real device MAC/IP addresses, login details, authorization tokens, tailnet information, passwords or personal files. Configure sensitive settings locally after installation.

## Development
Claude: implement rather than merely drafting a plan. Test in a simulation/safe mode on the build desktop. Do not modify desktop power settings during development. See [CLAUDE.md](./CLAUDE.md).
