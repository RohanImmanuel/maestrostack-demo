# MaestroStack Demo

A clone-and-run example for [**maestrostack**](https://github.com/RohanImmanuel/maestrostack) —
a config-driven CLI that packages [Maestro](https://maestro.mobile.dev) mobile UI flows and runs
them on **BrowserStack App Automate**.

This repo ships everything you need to see it work end to end:

- the real **Wikipedia sample app** (`apps/WikipediaSample.apk`),
- three ready-made Maestro flows (`smoke/`, `regression/`),
- a complete `maestrostack.yml`.

Add your BrowserStack credentials, run one command, and watch a build appear on your dashboard.

## Prerequisites

- **Node.js 18+**
- A **BrowserStack App Automate** account (for a real run). Grab your username and access key from
  <https://www.browserstack.com/accounts/profile/details>.

## Quickstart

```bash
# 1. Clone and install (pulls the maestrostack CLI as a local devDependency)
git clone https://github.com/RohanImmanuel/maestrostack-demo.git
cd maestrostack-demo
npm install

# 2. Provide your BrowserStack credentials (either way works)
export BROWSERSTACK_USERNAME=your_username
export BROWSERSTACK_ACCESS_KEY=your_access_key
#   …or: cp .env.example .env  and fill it in (.env is auto-loaded)

# 3. Sanity-check the config, app path, and devices
npx maestrostack validate

# 4. Run for real on BrowserStack
npx maestrostack run
```

`run` uploads the APK and the zipped flows, starts a Maestro build, and prints the build id and a
dashboard URL — open it to watch the flows execute on real devices.

> `npm run validate`, `npm run dry-run`, and `npm run run` are wired up as shortcuts too.

### Try it without an account

You can exercise the full packaging pipeline offline — no credentials, no API calls:

```bash
npx maestrostack run --dry-run
```

This discovers the flows, builds `.maestrostack/Flows.zip`, and prints the exact payload that
*would* be sent to BrowserStack.

## What this demo runs

The `maestrostack.yml` targets three real devices and uses `maxParallel: 2`, so maestrostack runs
the first two devices together and the third in a second batch:

| Device | OS |
| --- | --- |
| Samsung Galaxy S20 | 10.0 |
| Google Pixel 7 | 13.0 |
| Samsung Galaxy S22 | 12.0 |

Three flows execute against the Wikipedia sample app (`org.wikipedia`):

| Flow | What it checks |
| --- | --- |
| [`smoke/onboarding.yml`](smoke/onboarding.yml) | Launches the app, clears the first-run onboarding, lands on the search screen |
| [`smoke/search.yml`](smoke/search.yml) | Searches for "Maestro" and asserts a matching result appears |
| [`regression/article.yml`](regression/article.yml) | Opens the "Albert Einstein" article and scrolls through its content |

## Repo layout

`maestrostack.yml` is the entry point — it wires the app, the flows, and the target devices
together. The flows live in `smoke/` and `regression/`, split the way you'd organize a real suite,
and `apps/` holds the Wikipedia APK (committed so there's nothing to download first). The rest is
plumbing: `package.json` pins the `maestrostack` CLI and adds the `validate`/`dry-run`/`run`
scripts, and `.env.example` is the credential template you copy to `.env`.

## Customizing

- **Different app?** Drop your `.apk` into `apps/`, update `app.path` in `maestrostack.yml`, and
  point the flows' `appId` at your package.
- **Different devices?** Edit `run.devices` (any BrowserStack-supported `Device-OS` string), or
  override per run: `npx maestrostack run --device "Google Pixel 7-13.0"`.
- **Run a subset of flows?** `npx maestrostack run --execute smoke/search.yml`.

See the [maestrostack README](https://github.com/RohanImmanuel/maestrostack) for the full config
reference and command list.
