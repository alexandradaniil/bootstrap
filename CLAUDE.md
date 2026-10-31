# bootstrap — front door & recovery runbook for alexandradaniil.com

**You are a Claude session. If you are reading this, start here.** This is the authoritative, current
operating runbook for the project. It is public and contains **no secrets**.

## What this project is

A static website for **Alexandra Daniil** (Αλεξάνδρα Δανιήλ), live at **https://alexandradaniil.com**,
hosted free on **Cloudflare Workers**, versioned on **GitHub**, and built and run entirely through
**Claude Code on the Web**. The owner is non-technical and runs the site by asking Claude in plain
language; each session starts fresh, so **the repositories are the only memory**. (The full design
rationale lives in the project primer, held by the operator; this file is the operating summary.)

## Who you talk to, and in what language

- **Owner — Alexandra Daniil.** Preferred language is **Greek** — **all owner-facing text and
  conversation is in Greek.** She also reads English (moderate–advanced); use it only where it is
  clearly better for a specific thing. Her plain-language guide is **`getting-started.el.md`** here.
- **Operator — Thanos** set the project up and then stepped away. Developer-facing material (this file,
  the technical logs, code comments) is in **English**.

## The four repositories (all on GitHub account `alexandradaniil`)

| Repo | Visibility | What it holds |
|---|---|---|
| `bootstrap` | **public** | this front door + the owner's Greek guide. No secrets. |
| `website` | private | the live site. `site/` **is** the served tree. **Push to `main` = deploy.** |
| `documentation` | private | the project memory: `history.md`, `operations.md`, `lessons.md`, `owner-journal.md`. |
| `resources` | private | high-resolution originals the served images are derived from. |

Attach the repos you need to your session and clone them. **Read `documentation/` to catch up** — it
is append-only and written for a cold reader. Read **`lessons.md` first** (environment gotchas:
sandbox, DNS-over-HTTPS, dashboard drift).

## How publishing works

- Hosting is **Cloudflare Workers static assets**; **Workers Builds** redeploys on every push to
  `website` `main`. No terminal, no Wrangler or Cloudflare credentials on anyone's machine.
- **Content** edits (text, image swap, a new FAQ answer) may merge **straight to `main`**
  (owner-approved, primer §14.5). **Code / design / structure** go via a **draft** (a pull request);
  the **merge is the deploy** and is the owner's call — merge it yourself when told "publish" / "make
  it live". (The owner's own word for a pull request is **"draft"**, never "PR". Opening a draft is
  safe and changes nothing live.)
- **No change is done until it is logged** in `documentation`: a `history.md` line always; prose in
  `operations.md` if notable; a **Greek** line in `owner-journal.md` if it's owner-facing.
- **Dashboard-side config** (DNS, managed headers/redirects) is **not in any repo** — audit by fetching
  the live URL, and note any dashboard change in `documentation` (primer §2).

## Standing practices (learned on this project)

- **Cold start:** when you have no memory and had to re-learn the project from these repos,
  **re-familiarise with the *current* Cloudflare dashboard and GitHub UIs** before giving any UI steps
  — they drift from the official docs. Prefer **`wrangler.jsonc` config** (which Claude can change via a
  draft) over dashboard clicks.
- **DNS checks:** `dig`/`nslookup` aren't installed — use DNS-over-HTTPS
  (`curl -sS -H 'accept: application/dns-json' "https://cloudflare-dns.com/dns-query?name=HOST&type=TYPE"`).
- The session is **sandboxed to attached repos**; use GitHub **REST** (`gh api repos/{owner}/{repo}/...`),
  not GraphQL; it **cannot create repos**. Details in `lessons.md`.

## Credentials — the recovery ticket

Ordinary content work needs **no secret** — the session reaches GitHub through the owner's sign-in.
For **cold recovery** or driving the Cloudflare/GitHub APIs directly, the owner holds **one encrypted
recovery ticket** (a file + a passphrase, in her password manager — **not** in any repo). When a
session needs credentials, she provides the ticket and types the passphrase; decrypt the payload with:

```
openssl enc -d -aes-256-cbc -pbkdf2 -iter 600000 -base64
```

Load the two keys (`CLOUDFLARE_API_TOKEN`, `GITHUB_TOKEN`) into the environment; **never print, echo,
or commit them.** The Cloudflare token is the account-wide vault key; any other secret the site ever
needs lives in Cloudflare and is reached with it. Nothing is ever rotated. Where the ticket and this
runbook disagree, **trust this runbook** (it may be newer).

> **Status:** the ticket is created at the final handoff to the owner. If it does not exist yet,
> ordinary work still proceeds — only cold recovery / direct API access needs it.

## Current status  *(keep this section current — it is the first thing a cold session reads)*

- **Infrastructure complete:** four repos + project memory; push-to-deploy pipeline; custom domain
  **live with TLS**.
- **Bilingual skeleton is live** (unindexed): **Greek at the root, English under `/en/`**, Bio is the
  front page. 8 sections — Bio, Kyra Ithasimigi, Dance Improvisation, CEM Method, Educational
  Programmes, Documentaries / Video Dance, Academic Research, Contact. `/bio` and `/en/bio` 301 to the
  Bio root. **CEM Method** renders via the `.cem` component (light mode: warm gold / green / orange letters
  `#a87c00 / #34a152 / #d8602a` on a near-white `#f6f6f6` chip; dark mode via `prefers-color-scheme`:
  vivid `#ffff00 / #29b95c / #ff7474` on a `#333` chip). Section pages are "coming soon" stubs awaiting real content.
- **Still not indexed** — `website/site/robots.txt` is `Disallow: /`.
- **Pre-launch workflow (owner's call):** while unindexed with no visitors, commit content/structure
  **straight to `main`** (no drafts). **At go-live, switch back to draft-then-publish** (§11, since
  `main` is then public) and run the launch checklist in `website/CLAUDE.md` (robots → permissive,
  `workers_dev: false`, verify live).
- **Next:** fill in the real page content with the owner, gradually.

## Where things are

- **Live:** https://alexandradaniil.com   ·   preview: https://website.alexandradaniil.workers.dev
- **Repos:** https://github.com/alexandradaniil/ → `bootstrap`, `website`, `documentation`, `resources`
