# datapulse-action

Convert PDFs to clean, structured **Markdown** inside GitHub Actions — powered by
[DataPulse](https://datapulse-hazel.vercel.app), the pay-per-job bulk engine.

No server, no Adobe license, no 300-page churn by hand. Every document costs
**$0.05/page, capped at $1.50** — billed to your prepaid DataPulse wallet.
New accounts start with **$2.00 free credit** (≈ 40 pages converted for free).

## Usage

1. Sign up at [datapulse-hazel.vercel.app](https://datapulse-hazel.vercel.app) and verify your email.
2. Mint an API key (copy-paste, fill in your email and password):

   ```bash
   curl -s -X POST "https://datapulse-hazel.vercel.app/api/keys" \
     -H "Authorization: Bearer $(curl -s -X POST 'https://datapulse-hazel.vercel.app/api/auth/login' \
       -H 'Content-Type: application/json' \
       -d '{"email":"you@example.com","password":"YOUR_PASSWORD"}' | jq -r .session.access_token)"
   ```

   The response contains your `apiKey` — a `dpk.<id>.<signature>` string. It
   stays valid until you delete your account, so treat it like a password.
3. Add it as a repository secret named `DPULSE_API_KEY`.
4. Use the action:

```yaml
steps:
  - uses: actions/checkout@v4
  - name: Convert docs to Markdown
    uses: Bolu-Tife-dev/datapulse-action@main
    with:
      api-key: ${{ secrets.DPULSE_API_KEY }}
      files: "docs/manual.pdf specs/*.pdf"
      output-dir: markdown
```

Outputs land in `markdown/` as clean `.md` files — running heads, page numbers
and layout artifacts stripped, headings/lists/paragraphs restructured. Feed
them to Notion, Obsidian, an LLM pipeline, or your docs site.

## What counts as "clean"?

- Headers/footers repeated on most pages are dropped
- Standalone page numbers are removed
- Hyphenated line-breaks are rejoined
- ALL-CAPS / numbered / isolated lines become headings
- Bullet and numbered lists are rebuilt

## Notes

- Max 500 pages per document, 15 MB per file.
- Insufficient wallet balance returns HTTP 402 — top up from the dashboard.
- API keys remain valid until the account is deleted; treat them as secrets.
- HTTP `429` means rate limit — retry after a minute.

## Why pay-per-job?

Because "free" bulk converters either watermark your output, cap at 2 pages,
or want your whole document corpus in someone's cloud forever. DataPulse
bills $0.05/page with a $1.50 flat cap, and nothing is stored after the
conversion completes.

MIT licensed. The engine itself is hosted — see [DataPulse](https://datapulse-hazel.vercel.app).
