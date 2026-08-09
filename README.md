# Valence Runtime

Production-ready container images for [Elsa Workflows](https://github.com/elsa-workflows/elsa-core) —
built, hardened and patched by the maintainer of Elsa.

Elsa Workflows is open source and always will be. Valence Runtime is the assembled, production-ready
distribution of it: pre-built images, a committed security patch cadence, and — on paid tiers —
direct access to the person who wrote the engine.

📖 **[Documentation Wiki](https://github.com/valence-works/runtime/wiki)** ·
🐛 **[Report an issue](https://github.com/valence-works/runtime/issues)** ·
💬 **[Discussions](https://github.com/valence-works/runtime/discussions)**

> ### ⚠️ Pre-launch — read this first
>
> Valence Runtime is not generally available yet. Right now:
>
> - **Community images are published as `valenceworks/elsa-pro-*` on Docker Hub.** They will be
>   **renamed to `valenceworks/elsa-ce-*`**. If you are pulling `elsa-pro-*` today, you are using
>   what will become the Community edition — a migration notice will be published here before
>   anything changes.
> - **Paid images on `ghcr.io/valence-works/*` do not exist yet.** Commands below that reference
>   GHCR will not work until launch.
> - **Elsa 3.8 is still in preview.** These images track `3.8.0-preview.*`.
> - Hardening (non-root, minimal base, CVE gating, SBOM, signing) is **in progress**, not done.
>   Do not treat current images as hardened.
>
> This repository is documentation and issue tracking only. There is no source here.

---

## Editions

| | **Community** | **Runtime** | **Runtime Priority** | **Maintainer Access** |
|---|---|---|---|---|
| Price / year | Free | **€1,500** | **€4,500** | **€25,000** |
| Availability | Open | Open | Open | **3 slots** |
| Registry | Public | GHCR, private | GHCR, private | GHCR, private |
| Hardened, signed, SBOM | — | ✅ | ✅ | ✅ |
| Security patch cadence | — | ✅ | ✅ | ✅ |
| Immutable version tags | — | ✅ | ✅ | ✅ |
| Bug reports triaged | Public queue | 5 business days | 2 business days | 1 business day |
| Backports to your pinned minor | — | — | ✅ | ✅ |
| Direct channel to the maintainer | — | — | — | ✅ |

**Triage is committed; a fix is not.** Every report gets a written answer within your tier's window —
accepted with a target release, needs information, won't fix with a reason, or already fixed.

Prices are per year, EUR, excluding VAT. **Subscriptions are not open yet** — Valence Runtime is in
Early Preview; get in touch to discuss a tier or request access. Existing subscribers keep their rate
through at least their following renewal if prices change.

Full details, inclusions and exclusions:
**[Editions & Support](https://github.com/valence-works/runtime/wiki/Editions-and-Support)**

---

## Which image do I want?

| Image | Elsa API | Studio UI | Use when |
|---|:---:|:---:|---|
| `elsa-*-server` | ✅ | — | Backend only; Studio deployed separately or not at all |
| `elsa-*-studio` | — | ✅ | Studio UI pointing at an existing Elsa API |
| `elsa-*-combined` | ✅ | ✅ | Everything in one container, one origin — simplest option |

Replace `*` with `ce` (Community) or `pro` (paid). More detail:
**[Choosing an Image](https://github.com/valence-works/runtime/wiki/Choosing-an-Image)**

---

## Quick start

The fastest path to a running Elsa with a designer UI — one container, one port.

```bash
docker run -d --name elsa-pro -p 8080:8080 \
  -e CShells__Shells__Default__Features__DefaultAdminUser__AdminUsername=admin \
  -e CShells__Shells__Default__Features__DefaultAdminUser__AdminPassword='ChangeThisPassword123!' \
  -e CShells__Shells__Default__Features__Identity__SigningKey="$(openssl rand -base64 32)" \
  valenceworks/elsa-pro-combined:latest
```

Then open **http://localhost:8080** and sign in with the username and password you just set.

| What | Where |
|---|---|
| Studio UI | `http://localhost:8080` |
| Elsa API | `http://localhost:8080/elsa/api` |
| Health (readiness) | `http://localhost:8080/health` |
| Liveness | `http://localhost:8080/alive` |

Out of the box this uses SQLite inside the container, so **data is lost when the container is
removed**. That is fine for a first look and wrong for anything else — see
[Persistence & Databases](https://github.com/valence-works/runtime/wiki/Persistence-and-Databases).

> The image name above is the current Community image. After the rename it becomes
> `valenceworks/elsa-ce-combined`.

### Separate server and Studio

```bash
docker network create elsa

docker run -d --name elsa-server --network elsa -p 8080:8080 \
  -e CShells__Shells__Default__Features__DefaultAdminUser__AdminUsername=admin \
  -e CShells__Shells__Default__Features__DefaultAdminUser__AdminPassword='ChangeThisPassword123!' \
  -e CShells__Shells__Default__Features__Identity__SigningKey="$(openssl rand -base64 32)" \
  -e Elsa__Cors__AllowedOrigins__0=http://localhost:8081 \
  valenceworks/elsa-pro-server:latest

docker run -d --name elsa-studio --network elsa -p 8081:8080 \
  -e Studio__HostingModel=WebAssembly \
  -e Studio__Client__Backend__Url=http://localhost:8080/elsa/api \
  valenceworks/elsa-pro-studio:latest
```

Studio is on **http://localhost:8081**.

⚠️ In the default WebAssembly mode the **browser** calls the API directly, so
`Studio__Client__Backend__Url` must be reachable from your browser — not from inside the Docker
network — and the server needs a matching CORS origin. This is the single most common setup mistake;
[Running the Images](https://github.com/valence-works/runtime/wiki/Running-the-Images) explains both
hosting models.

---

## Documentation

| Page | What's in it |
|---|---|
| [Getting Started](https://github.com/valence-works/runtime/wiki/Getting-Started) | First run, in more detail than above |
| [Choosing an Image](https://github.com/valence-works/runtime/wiki/Choosing-an-Image) | Server vs Studio vs Combined, hosting models |
| [Pulling Images](https://github.com/valence-works/runtime/wiki/Pulling-Images) | Registries, private registry authentication, tags and pinning |
| [Running the Images](https://github.com/valence-works/runtime/wiki/Running-the-Images) | Docker run, Compose, Kubernetes notes, health probes |
| [Configuration](https://github.com/valence-works/runtime/wiki/Configuration) | Precedence, `/config/config.json`, environment variable reference |
| [Persistence & Databases](https://github.com/valence-works/runtime/wiki/Persistence-and-Databases) | SQLite, PostgreSQL, other providers, volumes |
| [Extending with Nuplane](https://github.com/valence-works/runtime/wiki/Extending-with-Nuplane) | Adding database providers, message buses, schedulers at runtime |
| [Production Checklist](https://github.com/valence-works/runtime/wiki/Production-Checklist) | What to change before going live |
| [Troubleshooting](https://github.com/valence-works/runtime/wiki/Troubleshooting) | Common failures and how to diagnose them |
| [Editions & Support](https://github.com/valence-works/runtime/wiki/Editions-and-Support) | Tiers, what's included, where to get help |

---

## Getting help

The right place depends on what the problem is — and if you're on a paid tier, you can always use
your private channel for anything.

| Your problem is | Go to |
|---|---|
| An Elsa **engine** bug or feature request | [elsa-workflows/elsa-core](https://github.com/elsa-workflows/elsa-core/issues) — that's where fixes land |
| An **image, packaging or deployment** problem | [Issues on this repo](https://github.com/valence-works/runtime/issues) |
| A question, or "how do I…" | [Discussions](https://github.com/valence-works/runtime/discussions) |
| Billing, licensing, subscriptions | _Commercial contact — pending_ |
| A suspected **security vulnerability** | _Security contact — pending_. Please do not use public trackers |

Paid subscribers: use your private intake channel — it's the only one carrying a response
commitment. Public channels here are best-effort.

---

## Licensing

- **Elsa Workflows** — the engine — is open source under the
  [MIT License](https://github.com/elsa-workflows/elsa-core/blob/main/LICENSE). Unaffected by
  anything here.
- **Community images** are free to use. _(Terms to be published.)_
- **Paid images** are licensed under the Valence Works Commercial License and require a current
  subscription. _(Link pending.)_

---

<sub>Valence Runtime is a product of Valence Works. Elsa Workflows is an open source project by the same
maintainer and remains freely available.</sub>
