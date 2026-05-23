---
title: Formatting
summary: Currency, percent, date, and time formatting reference. Native Intl + a tiny dayjs-token parser. Storage stays canonical; locale + timezone applied at display only.
---

# Formatting

LCAP renders numbers and timestamps via the browser's native
`Intl` APIs plus a small dayjs-compatible token parser — no
heavy date library in the SDK base bundle. Storage stays
canonical (plain JSON numbers, ISO-8601 UTC strings); display
formatting layers on top through the annotation's
`ui_config_json` keys.

This page documents the formatting behavior, the dayjs token
vocabulary, and the locale resolution chain.

## Locale resolution

The resolver picks a locale via:

1. Caller-supplied `locale` prop (rare — host apps rarely set
   this).
2. `navigator.language` (the browser's negotiated locale).
3. `'en-US'` fallback for SSR / Node test environments.

A planned follow-on will wire the tenant's
`BrandingConfig.locale` as the highest-priority source.

## Currency

`@ui/component = "currency"` plus `@ui/currency_code` (default
`USD`):

| Annotation | Stored value | Display (`en-US`) |
|---|---|---|
| `{ "@ui/component": "currency" }` | `1234.56` | `$1,234.56` |
| `{ "@ui/component": "currency", "@ui/currency_code": "EUR" }` | `1234.56` | `€1,234.56` |
| `{ "@ui/component": "currency", "@ui/currency_code": "JPY" }` | `1234` | `¥1,234` |

CLDR drives the per-currency intrinsic decimal places — JPY
zero, USD/EUR two, BHD three — so `JPY` renders without a
decimal portion automatically.

| Key | Effect |
|---|---|
| `@ui/decimal_places` | Override CLDR. `decimal_places: 4` on USD → `$1,234.5600`. |
| `@ui/use_separators` | `false` removes the thousands separator: `$1234.56`. |

## Percent

`@ui/component = "percent"`. Storage stays decimal — the
display layer multiplies by 100:

| Stored value | Display |
|---|---|
| `0.07` | `7%` |
| `0.07` (with `@ui/decimal_places: 2`) | `7.00%` |
| `1.5` (with explicit `min: 0, max: 5`) | `150%` |

The shipped zod refinement defaults percent fields
to `[0, 1]` storage range. Annotations with explicit `min` /
`max` override that — useful for "growth %" cases that exceed
1.0.

## Timestamps

Storage is **always ISO-8601 UTC**. Display formatting reads
two annotation knobs:

- `@ui/date_format` — dayjs-compatible token template.
- `@ui/timezone` — IANA tz (e.g. `"America/Los_Angeles"`).

Without `@ui/date_format`, the resolver falls back to the
locale's short date — `04/25/2026` for `en-US`. `@ui/include_time`
appends `12:00 PM` when set.

### Token vocabulary

The parser supports the common dayjs / date-fns subset.
Unrecognized tokens pass through as literal characters.

| Token | Example output (en-US) | Intl source |
|---|---|---|
| `YYYY` / `yyyy` | `2026` | `year: 'numeric'` |
| `YY` / `yy` | `26` | `year: '2-digit'` |
| `MMMM` | `April` | `month: 'long'` |
| `MMM` | `Apr` | `month: 'short'` |
| `MM` | `04` | `month: '2-digit'` |
| `M` | `4` | `month: 'numeric'` |
| `DD` / `dd` | `25` | `day: '2-digit'` |
| `D` / `d` | `25` | `day: 'numeric'` |
| `HH` | `14` | `hour: '2-digit', hour12: false` |
| `H` | `14` | `hour: 'numeric', hour12: false` |
| `hh` | `02` | `hour: '2-digit', hour12: true` |
| `h` | `2` | `hour: 'numeric', hour12: true` |
| `mm` | `30` | `minute: '2-digit'` |
| `ss` | `45` | `second: '2-digit'` |
| `a` | `pm` | `dayPeriod` (lowercased) |
| `A` | `PM` | `dayPeriod` (uppercased) |

Literal characters in the format string (commas, dashes,
slashes, the `T` separator, etc.) pass through verbatim. So
`"yyyy-MM-dd HH:mm"` renders as `2026-04-25 19:30`.

### Timezone behavior

`@ui/timezone` is applied to **every token** in the format
template. Cross-day boundaries shift correctly:

```
Stored:        2026-04-26T02:30:00Z
@ui/timezone:  America/Los_Angeles
@ui/date_format: yyyy-MM-dd HH:mm
Displays:      2026-04-25 19:30
```

(02:30 UTC on the 26th is 19:30 PT on the 25th.)

## Numbers (uncomponented)

`@ui/component = "number"` with no further keys:

| Annotation | Stored | Display |
|---|---|---|
| (none) | `1234567.89` | `1,234,567.89` |
| `{ "@ui/use_separators": false }` | `1234567.89` | `1234567.89` |
| `{ "@ui/decimal_places": 0 }` | `1234.56` | `1,235` |

The zod refinement honors `@ui/decimal_places` + `@ui/min` +
`@ui/max` automatically.

## Boolean

`mode="display"` renders booleans as `Yes` / `No`. Future
locale-aware variants (oui/non, sí/no) would attach a
`@ui/true_label` / `@ui/false_label` pair if the need
materializes.

## File reference

`mode="display"` for `field_type = "file_ref"` renders
`filename (size)` with rounded-KB. The Page Designer Card
block (rolling out) wraps `<SmartField />` with
the existing `<FilePayloadView />` so image previews + download
links work inline.

## Cross-references

- [UI config reference](/docs/lcap/ui-config) — the full
  `@ui/*` key catalog.
- [Field types](/docs/lcap/field-types) — type → default
  component mapping.
- [SDK reference](/docs/sdk/reference) — `<SmartField />`
  props and the `entityAnnotationToZod` helper.
