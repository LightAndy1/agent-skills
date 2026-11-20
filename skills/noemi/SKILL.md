---
name: noemi
description: Use when the user invokes /noemi, asks to remove em dashes or dashes, or writes, edits, or reviews user-facing copy in frontend UI or backend responses, emails, and notifications.
---

# noemi (`/noemi`)

Remove em dashes (`—`), en dashes (`–`), and visible dashes (`-`) from user-facing text. Replace them naturally or reword so the copy never feels forced.

When invoked via `/noemi`, apply this to all user-facing text you write or edit.

## Scope

- **Check:** Frontend UI (headings, labels, banners, tooltips, placeholders, page titles, empty states) and backend user-facing strings (API messages, validation errors, emails, notifications).
- **Keep untouched:** Non-user-visible tokens (identifiers, JSON keys, error codes, CSS classes, URLs, CLI flags, ISO dates, negative numbers).

## Replacement Options

Pick what fits the sentence best instead of mechanical find-and-replace:

- **Period (`.`)**: Split two full thoughts instead of creating a comma splice.
- **Colon (`:`)**: Introduce a reason, detail, or subtitle.
- **Comma (`,`) or parentheses `()`**: Wrap a short aside or secondary detail.
- **Pipe (`|`) or separate element**: Separate page titles, badges, or metadata.
- **Reword**: Rewrite stiff clauses, ranges, or hyphenated phrases into plain words (`1-2 days` → `1 to 2 days`, `step-by-step` → `guided`).

## Examples

- `"Billing — Acme Cloud"` → `"Billing | Acme Cloud"`
- `"No invoices yet — you're all caught up."` → `"No invoices yet. You're all caught up."`
- `"Payment failed — card declined. Update billing — or contact support."` → `"Payment failed: card declined. Update your billing info, or contact support."`
- `"Reply to this email—we're happy to help."` → `"Reply to this email and we'll be happy to help."`
