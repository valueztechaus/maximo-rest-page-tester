# Maximo REST Page Tester

Valueztech branded, browser-based tester for IBM Maximo REST pagination.

This project is a static HTML solution. It can be downloaded, hosted on any basic static host, or run locally without a build step.

## What It Does

- Calls a Maximo REST API GET URL from the browser.
- Shows HTTP status, elapsed time, detected record count, response JSON, current URL, next URL, and request history.
- Supports common Maximo auth header patterns, with browser guidance for CORS-sensitive headers.
- Supports two pagination strategies:
  - Use Maximo's returned `responseInfo.nextPage.href`.
  - Generate sequential `pageno=2`, `pageno=3`, and so on.
- Detects the common stable paging error `BMXAA9510E` and explains what it means.

## Quick Start

Open `index.html` from a web server, then paste a Maximo GET URL.

Example Maximo URL with API key as a query parameter:

```text
https://<domain-name-mas-url>/maximo/api/os/<OSNAME>?lean=1&oslc.pageSize=100&pageno=1&apikey=YOUR_API_KEY
```

Use the Maximo REST API context:

```text
https://<domain-name-mas-url>/maximo/api/os/<OSNAME>
```

Do not use the older OSLC context for this tester:

```text
https://<domain-name-mas-url>/maximo/oslc/os/<OSNAME>
```

For browser-based use, pass the API key as `?apikey=...` in the URL and keep **Auth header mode** set to `None`.

Do not use the `apikey` request header unless your Maximo/IHS/Liberty/gateway CORS configuration explicitly includes:

```text
Access-Control-Allow-Headers: apikey
```

Many Maximo environments allow API keys as URL query parameters but do not allow the custom `apikey` header from browser JavaScript.

## Cheapest Hosting Option: Localhost

On Windows, run the page from the folder where the project is saved.

If you see `>>>`, you are inside Python, not PowerShell. Exit Python first:

```text
exit()
```

or press `Ctrl + Z`, then `Enter`.

Then run:

```powershell
cd C:\Users\your-user\Downloads\MaximoRestAPITester
py -m http.server 8765
```

Open:

```text
http://localhost:8765/
```

This changes the browser origin from `null` to:

```text
http://localhost:8765
```

Maximo still needs to allow that origin. A local server fixes the `file://` origin problem, but it does not bypass CORS.

Update the Maximo system property `mxe.oslc.aclalloworigin` to include the local server URL:

```text
http://localhost:8765
```

If you host the tester somewhere else, add that hosted origin instead.

## Free Static Hosting Options

Users can choose any static host. Common free options:

- GitHub Pages
- Cloudflare Pages
- Netlify
- Vercel
- Localhost with `py -m http.server`

For hosted deployments, Maximo must allow the deployed origin, for example:

```text
https://your-github-user.github.io
https://your-project.pages.dev
http://localhost:8080
http://localhost:8765
```

## CORS Guidance

If the browser blocks the request before Maximo returns JSON, it is usually CORS, network, SSO, certificate, or authentication related.

For direct browser calls, ask the Maximo environment owner to allow the tester origin:

```text
mxe.oslc.aclalloworigin=http://localhost:8765
```

Use the actual origin where the tester is hosted. For example, if you run the local server on port `8765`, Maximo must allow:

```text
http://localhost:8765
```

If you want to use API keys from this browser tester, pass them in the URL:

```text
?apikey=YOUR_API_KEY
```

If you instead send the API key as a request header, Maximo must also allow that header:

```text
Access-Control-Allow-Headers: apikey,accept,content-type
```

If CORS cannot be changed, use:

```text
HTML tester -> controlled local/server proxy -> Maximo
```

Do not expose API keys through public proxies.

## Stable Paging Error: BMXAA9510E

If Maximo returns:

```text
BMXAA9510E - The stable page resource was not found because the reload of a stable page was attempted or the idle expiry time has expired.
```

the browser successfully reached Maximo. This is not a CORS error.

It usually means a returned `nextPage.href` stable paging reference is stale, expired, reused after refresh, or used out of sequence.

To test stable paging:

1. Call the original first-page URL.
2. Click `Next Page` immediately.
3. Do not refresh the browser.
4. Do not paste an old `nextPage.href`.
5. Continue sequentially from the latest response only.

If you only need repeatable page-by-page API behaviour, switch the tester to generated `pageno` mode.

## Security Notes

- The tester does not save API keys or tokens.
- Do not commit real API keys into GitHub issues, screenshots, or sample URLs.
- Rotate any key that was pasted into a public channel.
- Prefer short-lived, test-environment credentials.

## Files

```text
index.html
assets/logos/
README.md
```

No package install or build command is required.

## Valueztech

Valueztech Pty Ltd · ABN 81 677 255 300 · www.valueztech.com · Melbourne, Australia
