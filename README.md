# ik-fiesta-sample-webapp

A **sample website** for a Fiesta Online private server — account registration,
login, character/leaderboard views, and a cash shop — talking to
[ik-fiesta-api](https://github.com/IkaronClaude/ik-fiesta-api) over HTTP. It's a
small static SPA (vanilla HTML/JS/CSS, no build step) served by a tiny ASP.NET
host that also terminates TLS.

It's deliberately a **starting point**, not a finished product: clone it, restyle
it, or rip out `wwwroot/` and drop in your own React/Vue/Svelte build — the host
serves whatever static files are there. Split out of
[ik-fiesta-collab](https://github.com/IkaronClaude/ik-fiesta-collab).

> Assembly/namespace: `Fiesta.Webapp` (`Fiesta.Webapp.dll`). Repo + image:
> `ik-fiesta-sample-webapp`.

## How it works

- The host (`src/Fiesta.Webapp/Program.cs`) serves `wwwroot/` with SPA fallback
  routing (`UseDefaultFiles` + `MapFallbackToFile("index.html")`).
- It exposes **`GET /config.json`** → `{ "apiUrl": "<API_URL>" }`. The SPA fetches
  this on load to discover the API base URL, so the same static bundle works
  across environments without rebuilding.
- TLS: Let's Encrypt (ACME) if `LETSENCRYPT_DOMAIN` is set, else a manual PFX via
  `HTTPS_CERT_PATH`, else plain HTTP (dev default).

## Configuration (env)

| Key | Purpose |
| --- | --- |
| `API_URL` | Base URL of `ik-fiesta-api`, returned by `/config.json` for the SPA. |
| `ASPNETCORE_URLS` | Bind addresses (image defaults to `http://+:8080`). |
| `LETSENCRYPT_DOMAIN` / `LETSENCRYPT_EMAIL` / `LETSENCRYPT_CERT_DIR` | Auto TLS via ACME. |
| `HTTPS_CERT_PATH` / `HTTPS_CERT_PASSWORD` | Manual PFX cert instead of ACME. |

## Build & run

```bash
dotnet build ik-fiesta-sample-webapp.slnx -c Release
API_URL='http://localhost:5000' dotnet run --project src/Fiesta.Webapp
```

## Docker

```bash
docker build -t ik-fiesta-sample-webapp .                      # Linux
docker build -t ik-fiesta-sample-webapp -f Dockerfile.windows .   # Windows
docker run -p 8080:8080 -e API_URL=http://your-api:5000 ik-fiesta-sample-webapp
```

CI publishes `ghcr.io/<owner>/ik-fiesta-sample-webapp:latest` on pushes to `main`.

## License

[Apache License 2.0](LICENSE).
