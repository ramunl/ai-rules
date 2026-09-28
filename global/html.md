# HTML Coding Rules

Rules for web pages: HTML, CSS, and the JavaScript inside them, including
Telegram Mini Apps. Several come from real bugs in the dashboard; those are
marked *(learned)*.

## 1. Structure

- Keep a small page in **one self-contained file** (HTML + CSS + JS), with no build step, until it genuinely needs one.
- Start with `<meta charset="utf-8">`, set `<html lang="…">`, and use `<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">`.
- Use semantic elements: `<button>` for actions, `<a href>` for navigation, `<main>`, `<section>`, headings in order. Never make a `<div>` or `<span>` clickable.
- One job per function; if describing it needs "and", split it.

## 2. Security

- Put dynamic data into the page only with `textContent` / `createElement`. Never `innerHTML`, `outerHTML`, `insertAdjacentHTML`, or `document.write` with data:
  ```js
  // Good
  cell.textContent = task.branch;
  // Never: a branch name or todo text could contain markup
  cell.innerHTML = task.branch;
  ```
- Never put tokens, API keys, or other secrets in HTML or JS. Everything sent to the browser is public.
- The page is not a security boundary: every data endpoint authenticates the request on the server.
- Load third-party scripts only from their official origin, and only the ones actually needed.

## 3. CSS

- Colors come from CSS custom properties with fallbacks; support both light and dark themes.
- Mobile first: relative units and flexbox/grid, no fixed pixel widths for layout; respect `env(safe-area-inset-*)`.
- Class names in `kebab-case`, describing role (`.problem-list`), not look (`.red-text`).
- No `style="…"` attributes and no `!important`.
- Never convey meaning by color alone: a status dot always has text next to it.

## 4. JavaScript

- `const` by default, `let` only when reassigned, never `var`. Always `===` / `!==`.
- Names: `camelCase` for variables and functions, `PascalCase` for classes, `UPPER_SNAKE_CASE` for constants. Booleans read as questions: `isLoading`, `hasData`.
- Log why work was skipped instead of skipping silently.
- Keep page-wide state in a few named variables declared together near the top.

## 5. Network and async

- Every `fetch` has a timeout (`AbortController`) and a visible error. A page must never stay on "Loading…" silently. *(learned)*
- When the user navigates, cancel the previous request, and ignore responses for a page the user has left. The new page always starts its own request. *(learned)*
- Check `response.ok` and show the server's error message; never assume success.
- Show uncaught errors in the UI by listening to `error` and `unhandledrejection`, not only in the console. *(learned)*
- Poll only with idempotent GETs, and skip a tick while a request is still in flight.
- Request live data with `cache: "no-store"`.

## 6. Navigation (single-page)

- Every route immediately shows a loading state and the correct title, so a stale header never lingers over an empty body. *(learned)*
- Going forward uses `history.pushState`; going back uses `history.back()`. History must not grow on a round trip. *(learned)*

## 7. Telegram Mini Apps

- Load `https://telegram.org/js/telegram-web-app.js` before any other script, then call `ready()` and `expand()`.
- Route with paths (`/pm`), never the URL fragment: Telegram puts its launch data in `#…`. *(learned)*
- Send `initData` with every request (`Authorization: tma <initData>`) and verify its signature on the server. Never trust `initDataUnsafe`.
- Use the native `BackButton` on every screen except the root; don't draw a custom back arrow.
- Use theme variables (`--tg-theme-*`) with fallbacks; open external links with `Telegram.WebApp.openLink`.

## 8. Testing

- Any page with logic has browser-level tests (jsdom + `node --test`) that run in CI.
- Tests simulate a slow server and a request that never answers, and cover navigation races. *(learned)*
- A test for a bug must fail on the old code before it passes on the fix.

## 9. Accessibility

- Everything interactive works with a keyboard and shows a visible focus state.
- Keep text contrast readable in both themes.
