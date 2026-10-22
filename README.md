# Referral Hub — holding page

The page visitors see while **Referral Hub** is unavailable. PayPlan's Business
Continuity Plan is active following the PHP cyber security advisory; this is
where traffic for Referral Hub is pointed in the meantime.

One static HTML file. No build, no framework, no dependency on any
PayPlan service — it has to render when other things do not.

## One file, under 4KB

`index.html` is the whole site: the CSS is inline and there is no stylesheet to
fetch. It is **3136 bytes**, inside the 4096-byte limit for a WAF custom
response body, so the same file can be served by Pages and pasted into the WAF.

**That budget is the constraint on every edit.** Check it before pushing:

```sh
wc -c index.html     # must stay under 4096
```

Things that were cut to fit, and should not come back without finding the bytes
elsewhere: the favicon, the code comments (which is why this file carries the
explanation instead), and the separate stylesheet. What was kept: the Figtree
webfont, because without it the page stops looking like PayPlan.

Brand values are inline literals rather than custom properties — each is used
once or twice, and the indirection cost more than it saved. They come from
`packages/design-tokens/v6.css` in the website repo: cobalt-600 `#2200cc`,
cobalt-900 `#0f0054`, sand-50 `#faf7f0`, sand-200 `#ece3d8`, sand-600
`#6b6257`, sand-900 `#231f1a`, gold-500 `#c9922f`.

## Changing the message

Everything editable is in `index.html`, between the `STATUS MESSAGE`
comments. Edit, commit to `main`, and the deploy runs itself; the page is live
within a minute or two. Nothing else in the file needs touching.

Keep it short. Someone reading this is trying to find out what to do instead,
and anything past the first screen is a paragraph they will not reach.

### Outstanding

The contact details are **placeholders**. The Creditor Suite notice directs
people to Creditor Relations on 0845 606 6624; the equivalent for referral
partners has not been supplied, so the page says `PLACEHOLDER` rather than
guessing at a number or inbox. Search `index.html` for `PLACEHOLDER` —
there are five — and replace them before the domain is pointed here.

## Deployment

GitHub Actions publishes to GitHub Pages on every push to `main`, and
**Actions → Deploy → Run workflow** republishes without a commit.

One-time repository setup: **Settings → Pages → Build and deployment →
Source: GitHub Actions**. Until that is set the workflow runs and the final
step fails.

### Pointing a domain at it

Add a `CNAME` file at the repository root holding the hostname on its own
line — `creditor-suite.payplan.com`, for example — then set that hostname's
DNS to the Pages target and enter it under **Settings → Pages → Custom
domain**. There is deliberately no `CNAME` committed: one naming a domain
that is not pointed here yet takes the default `github.io` address away and
leaves no working URL at all.

## What is deliberately absent

- **No analytics, tags or cookie banner.** Nothing to consent to, so no
  interstitial between a worried visitor and the message.
- **`noindex, nofollow`.** A holding page must not be left ranking in place
  of the real service once it is back.
- **No auto-refresh.** A page that reloads under someone reading it is worse
  than one they refresh themselves.
- **Brand tokens copied, not imported.** The palette and type scale come from
  `packages/design-tokens/v6.css` in the website repo. Nothing keeps the two
  in step; if the brand changes, change it here too.

## When the service is back

Point the domain back at the real service. Leave this repository as it is —
the next continuity event wants it ready, not rebuilt.
