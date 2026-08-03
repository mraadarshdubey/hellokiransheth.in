# DNS backup — hellokiransheth.in

Captured from Hostinger hPanel (nameservers `ns1/ns2.dns-parking.com`) before any
Cloudflare migration. **Every row below must exist in Cloudflare before the
nameservers are switched**, otherwise the website and/or email will break.

## Records

| Type  | Name                       | Priority | Content                                                    | TTL   |
|-------|----------------------------|----------|------------------------------------------------------------|-------|
| A     | @                          |          | 147.93.17.102                                              | 1800  |
| AAAA  | @                          |          | 2a02:4780:11:1933:0:2c51:85ed:2                            | 1800  |
| ALIAS | @                          |          | ai.hellokiransheth.in.cdn.hstgr.net                        | 300   |
| MX    | @                          | 5        | mx1.hostinger.com                                          | 14400 |
| MX    | @                          | 10       | mx2.hostinger.com                                          | 14400 |
| TXT   | @                          |          | `v=spf1 include:_spf.mail.hostinger.com ~all`              | 14400 |
| TXT   | @                          |          | `facebook-domain-verification=5qgj0o3ne3jcm5epeehbtzrvtun39c` | 14400 |
| TXT   | _dmarc                     |          | `v=DMARC1; p=none`                                         | 3600  |
| CNAME | www                        |          | hellokiransheth.in                                         | 300   |
| CNAME | gmcbootcampyt              |          | mraadarshdubey.github.io                                   | 14400 |
| CNAME | autodiscover               |          | autodiscover.mail.hostinger.com                            | 300   |
| CNAME | autoconfig                 |          | autoconfig.mail.hostinger.com                              | 300   |
| CNAME | hostingermail-a._domainkey |          | hostingermail-a.dkim.mail.hostinger.com                    | 300   |
| CNAME | hostingermail-b._domainkey |          | hostingermail-b.dkim.mail.hostinger.com                    | 300   |
| CNAME | hostingermail-c._domainkey |          | hostingermail-c.dkim.mail.hostinger.com                    | 300   |
| A     | ftp                        |          | 147.93.17.102                                              | 1800  |
| A     | 5daychallenge              |          | 147.93.17.102                                              | 1800  |
| AAAA  | 5daychallenge              |          | 2a02:4780:11:1933:0:2c51:85ed:2                            | 1800  |

## What each group does — do not drop any of it

- **Email (critical):** the two `MX` rows, the SPF `TXT`, `_dmarc`, and the three
  `hostingermail-*._domainkey` DKIM CNAMEs. Losing any of these silently breaks
  sending or receiving mail on the domain.
- **Main website:** `A @` → `147.93.17.102` (Hostinger LiteSpeed) plus the `AAAA`.
- **`ALIAS @` → `ai.hellokiransheth.in.cdn.hstgr.net`** — Hostinger-specific ALIAS
  record pointing at their CDN. Cloudflare has no ALIAS type; the equivalent is a
  root `CNAME` with CNAME-flattening. The plain `A @` record is what actually
  answers today, so confirm which one the live site depends on before relying on it.
- **Other hosts:** `www`, `ftp`, and the `5daychallenge` landing page.
- **`gmcbootcampyt`** → GitHub Pages. This is the bootcamp page and is the only
  record added for this project.

## Changes made on 3 Aug 2026

1. **Removed four stray GitHub Pages `A` records** (`185.199.108–111.153`) that had been
   added alongside the Hostinger `A` record. With five apex `A` records the domain was
   round-robining — roughly four in five visitors were being sent to GitHub Pages instead
   of the real site.
2. **Repaired `www`.** It pointed at `mraadarshdubey.github.io`, which GitHub was not
   serving, so `www.hellokiransheth.in` returned nothing at all. Restored to
   `hellokiransheth.in`.
3. **Added `gmcbootcampyt` CNAME** → `mraadarshdubey.github.io` for the bootcamp page.
4. **Moved DNS to Cloudflare.** Nameservers are now `ivan.ns.cloudflare.com` /
   `izabella.ns.cloudflare.com`. Cloudflare's automatic scan missed three records
   (`gmcbootcampyt`, and `5daychallenge` A + AAAA) — these were re-added by hand from the
   table above. Every record in that table was then verified present in Cloudflare.

## Cloudflare settings

- Proxied (orange): apex `A`/`AAAA` and `www` only.
- DNS-only (grey): **all mail records** (MX, SPF, DMARC, the three DKIM CNAMEs,
  `autodiscover`, `autoconfig`), plus `ftp`, `ai`, `5daychallenge` and `gmcbootcampyt`.
  Proxying a DKIM or autodiscover record breaks mail, so these must stay grey.
- SSL/TLS mode: **Full**. Always Use HTTPS: on.

## Where /gmcbootcampyt/ is actually served from

`hellokiransheth.in/gmcbootcampyt/` is served **directly by the Hostinger origin** — the
file lives on the web host, and the response comes back from LiteSpeed. A Cloudflare
Worker was built to proxy that path through to GitHub Pages, but once the file appeared
on the origin the Worker became redundant, so **its routes were deleted** to avoid two
competing copies of the page. The Worker script `gmcbootcamp-proxy` still exists in the
Cloudflare account, unused, if that approach is ever wanted again.

**To update the page, replace the file on Hostinger.** A `git push` alone will not change
what this URL serves — that only updates the GitHub copy behind
`gmcbootcampyt.hellokiransheth.in`.

## Restore

- To undo Cloudflare entirely: set the nameservers back to `ns1.dns-parking.com` /
  `ns2.dns-parking.com` in hPanel. The original Hostinger zone is still intact there.
- To rebuild the zone from scratch anywhere: re-enter the table above.
