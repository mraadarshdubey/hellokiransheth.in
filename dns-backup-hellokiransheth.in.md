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

## Fix applied on 3 Aug 2026

`www` had been pointed at `mraadarshdubey.github.io`, which GitHub was not serving —
`www.hellokiransheth.in` returned no response at all. Restored to `hellokiransheth.in`.

## Restore

If anything breaks, re-enter the table above in the DNS editor, or point the
nameservers back to `ns1.dns-parking.com` / `ns2.dns-parking.com`.
