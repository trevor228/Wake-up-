# Wake Up — Claude Code implementation instructions

## Objective
Build a complete Windows 10/11 utility named **Wake Up**, developed on the user's gaming desktop and installed on a **different, spare Windows laptop** that stays at home. The spare laptop is still a normal laptop: a one-click ON/OFF tray switch makes it an optional remote Wake-on-LAN bridge, and switching OFF restores normal laptop behavior.

**Do not stop at a plan or mockup. Implement, test, build, package, and document the application.**

## Confirmed facts
- The gaming desktop is connected by Ethernet to a SandyNet home router.
- Local Wake-on-LAN from an iPhone has **already been proven to work** after enabling LAN wake in the WoL app.
- Gaming PC can be in S3 sleep and should stay asleep when not in use.
- The spare laptop runs Windows (version not yet established) and is a normal everyday laptop.
- Development happens on the **gaming desktop**, not on the spare laptop.
- The user wants **no paid subscription, no new hardware, and no monthly cloud hosting**.
- The user wants to wake their desktop from an iPhone over cellular, then connect with Parsec/Moonlight.

## Privacy & configuration
This GitHub repository is PUBLIC. **Never commit actual MAC addresses, internal IPs, phone numbers, device credentials, Tailscale auth keys, tokens, or personal information.** Provide only placeholders in examples; let the user enter the Ethernet MAC and the home LAN broadcast address in the installed app. Keep sensitive settings only in local AppData and gitignore them.

## Windows application requirements
1. Use .NET (WinForms/WPF or another justified light Windows-native approach) to produce a polished but simple system tray app, named **Wake Up**. Make the UI easy for a nontechnical user.
2. Tray menu and compact settings window should show **Remote Wake Mode ON/OFF**, **Wake Gaming PC** (local test button), Tailscale readiness, simple diagnostics, recent wake requests, and copy private wake URL when configured.
3. **Default OFF** on installation, fresh launches, and after unexpected crashes unless the user explicitly opts into auto-enable. Optional "Launch at sign-in" is separate from "Automatically enable mode".
4. When ON and connected to AC, keep Windows **system** awake but let the screen turn off; preserve normal use of the spare laptop and minimize CPU/memory. If the laptop is unplugged, warn the user and respect their battery-saver choice.
5. Windows lid closure is special: keeping the system awake via SetThreadExecutionState alone does NOT prevent lid-close sleep. Offer a clear **opt-in** option to temporarily change the *AC lid-close action* to Do Nothing when ON, only after backing up the user's original power settings. Restore original AC setting when OFF, on normal exit and at next launch if an abnormal termination left recovery pending. Do not modify DC/battery settings by default. Handle permissions safely. Do not alter development machine power settings while building/testing.
6. Use correct Win32 execution state or power request API, on/off thread-safe and idempotent. Release on OFF and exit. Protect against rapid toggle races, crashes, double instances, and orphan processes.
7. Provide an obvious **Restore normal laptop sleep settings** recovery button. Install/uninstall must clean up settings and other managed resources.
8. Provide signed/not-signed release documentation honestly. Make an actual portable, self-contained Windows executable (and optional installer) that does NOT need .NET SDK, Python, Claude Code, developer tools, or files from the development PC on the target laptop.

## Wake-on-LAN logic
- User-configurable Ethernet MAC, local IPv4 directed broadcast address, and UDP port default 9.
- Validate MAC/IP and only ever send the standard WoL magic packet (FF x 6 + MAC x 16) to configured destination. No arbitrary host, script, remote-shell, or system-control execution.
- State "Wake packet sent", NOT "PC is awake", unless a separate verifiable status check confirms.
- Support configuring and testing local Wake from the tray app before testing remote.
- Provide clear troubleshooting for subnet broadcast mismatches, guest Wi-Fi isolation, PC Hibernate vs Sleep, adapter power settings, and destination PC being unavailable.

## Remote iPhone access
- Build tiny, mobile-friendly local page with large **Wake Gaming PC** button and status indicator. UI must be read-only on GET; waking only on POST.
- HTTP listener must bind to **127.0.0.1 only** with safe defaults and explicit start/stop tied to Remote Wake Mode.
- Use **Tailscale Serve**, *not* Tailscale Funnel, so the page is available only to devices on the same private tailnet. Do NOT open router ports or use public relays/dashboards. The app should guide setup, check Tailscale state, and avoid overwriting other Serve rules.
- Browser endpoint must be protected against CSRF and request replay, throttle wake requests, and reject requests while OFF. Never treat the presence of a Tailscale client alone as proof that this is private; guide user to verify Serve is private and secure.
- Phone should use Tailscale on iPhone and a bookmarked tailnet-only HTTPS URL, usable over cellular. If laptop sleeps, is shut down, or is taken away from home, show the wake service as unavailable; never imply it works in that case.
- No commercial cloud backend or paid services.

## Implementation and test expectations
- Create a maintainable solution with source, build scripts, unit tests, .gitignore, a brief README, and a sample configuration **with placeholders only**.
- Test magic packet bytes, MAC parsing, IP configuration, off-state refusal, repeated toggles, concurrency, web request validation, throttling/CSRF protections, and sleep-setting restore/recovery logic via mocks.
- Implement useful logging without secrets or sensitive network details.
- Include a testable no-side-effect simulation mode on the development PC. **Do not change the gaming desktop's power plan or sleep behavior during development.**
- Publish portable self-contained executable (prefer win-x64 and document any ARM compatibility); create a separate install/release folder ready to copy to the spare laptop; no development SDK on spare laptop.
- Ensure clean uninstall procedure and feature toggle restores previous sleep/lid-close settings. Include smoke test list.

## First-user setup / acceptance
1. Build on gaming PC with Claude Code. Run automated tests and package app.
2. Copy installer or portable release to spare laptop, launch, and add the PC's Ethernet MAC and LAN broadcast address locally.
3. Install/configure free Tailscale on spare laptop and iPhone. Set up **private Serve** (never Funnel).
4. Test local wake with desktop sleeping, then test from iPhone using cellular data.
5. Confirm ON laptop AC awake with display off; OFF immediately restores ordinary laptop sleep and lid behavior.
6. Show the final release path, test results, installation steps, known limitations and any actions still required from the user.

## References
- https://tailscale.com/docs/features/tailscale-serve
- https://tailscale.com/docs/reference/tailscale-cli/serve
- https://learn.microsoft.com/en-us/windows/win32/api/winbase/nf-winbase-setthreadexecutionstate

## Important
The user wants an app they can use, not only design notes. Build real working software, with separate development/testing and target laptop deployment. Do not claim wake over cellular works until physically verified on the home network.