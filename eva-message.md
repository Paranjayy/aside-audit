# Draft message to Eva (@xyz3va)

## Option A: DM / Signal / Twitter DM

> hey eva, did a filesystem audit of Aside browser (the YC chromium fork) — found some stuff worth looking at deeper. wrote it up at https://site-sandy-pi-22.vercel.app
>
> tldr: both extensions have <all_urls> content script injection (including file://), the agent manager has debugger + tabCapture + cookies permissions, the daemon runs on localhost:21420 with an empty auth token in the bundled extension, and the recovery key entropy gets sent to their server.
>
> the permission model is wild for a 5-person team. most actionable ones are the daemon auth (#5), web_accessible_resources (#4), and the tabCapture-without-consent custom permission (#3). full breakdown in the site.
>
> also their recovery key is just standard BIP39 12 words (132 bits entropy) — the algorithm isn't novel. and PostHog analytics in a password manager lmao
>
> if you wanna dig into the actual binaries or do a deeper audit the extension source is in ~/Library/Application Support/Aside/ — background.js is 1MB of minified code and the extensions are "hidden" from chrome://extensions but fully on disk.
>
> no rush, just thought you'd find it interesting given your security work. lmk if you want the raw files or anything

## Option B: Twitter/X thread

> aside browser (YC chromium fork) security audit — filesystem inspection of v1.26.721.1635
>
> thread 🧵

> 1/ both extensions inject content scripts into EVERY page including file:// URLs. password manager runs at document_start in all frames. any XSS = full page compromise on every site you visit.

> 2/ agent manager has debugger, tabCapture, cookies, history, bookmarks, browsingData permissions + a custom "capture-tab-without-userinteraction" permission. this is a 5-person startup with full browser takeover.

> 3/ aside daemon runs on localhost:21420 with VITE_ASIDE_DAEMON_AUTH_TOKEN: "" — empty. any local process can connect and interact with it.

> 4/ password manager exposes iframe.html, fido2-page-script.js, autofill-page-script.js to <all_urls> via web_accessible_resources. any website can load these.

> 5/ recovery key is standard BIP39 12 words (132 bits entropy) — algorithm isn't novel. worse: recoveryEntropyB64 is sent to their server. if their backend is compromised, all recovery keys are too.

> 6/ PostHog analytics in a password manager. both extensions initialize _posthogChunkIds. for a product handling passwords, credit cards, and passkeys, shipping telemetry to a third party is a choice.

> full writeup with code references: https://site-sandy-pi-22.vercel.app
>
> not trying to dunk — aside has genuinely cool design. but the permission model is extremely aggressive for what it is. could this work as a browser extension in other browsers? technically yes, but then they'd have to confront how much of their "product" is just fork-level permissions.

## Option C: Responsible disclosure to Aside

> hi aside team,
>
> i did a filesystem audit of aside browser v1.26.721.1635 on macos and wanted to share some findings. not trying to be adversarial — the product has genuinely cool design — but some of the permission architecture raised flags.
>
> high priority:
> - daemon auth token is empty in bundled extension (VITE_ASIDE_DAEMON_AUTH_TOKEN: ""), localhost:21420 accessible to any local process
> - agent manager requests debugger + tabCapture + custom capture-tab-without-userinteraction permission
> - both extensions inject content scripts into <all_urls> including file://
>
> medium:
> - password manager exposes web_accessible_resources to <all_urls>
> - PostHog analytics initialized in password manager extension
>
> the recovery key entropy being sent server-side (recoveryEntropyB64) is worth documenting as a trust boundary.
>
> full writeup: https://site-sandy-pi-22.vercel.app
>
> happy to discuss further or provide raw files. thanks for building this — hope the feedback is useful.
