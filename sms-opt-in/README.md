# SMS opt-in screenshots

Public-facing screenshots referenced by `/sms-opt-in` (content at
`web/content/sms-opt-in.md`, route at `web/pages/sms-opt-in.vue`).

These exist because AWS SNS / carrier registry reviewers need a
publicly accessible URL that shows how a user opts in to FastYoke
SMS. The opt-in flow lives behind authentication, so screenshots
substitute for the live screens.

## Files expected here

| Filename | What it shows |
|---|---|
| `01-navigation.png` | The user menu / settings nav, with the path to **Account → Security → Two-Factor Authentication** visible |
| `02-consent-screen.png` | SMS selected as the 2FA method, with the full seven-element consent disclosure visible **above** the phone-number field |
| `03-send-code.png` | Phone number entered, "Send verification code" button highlighted |
| `04-verify-code.png` | Verification-code entry field with "Verify" button |
| `05-confirmation.png` | Post-enrollment confirmation showing SMS 2FA active, STOP/HELP reminder, and the remove-number control |

## Capture guidance

- PNG, ~1200–1600 px wide, light theme.
- Crop to the relevant panel — no surrounding browser chrome
  needed.
- Redact any real email address, tenant name, or phone number
  by overlaying a placeholder (e.g. `jane@example.com`,
  `+1 555 0100`).
- Make sure all seven disclosure elements are legibly visible
  in `02-consent-screen.png`. That's the one the reviewer
  will scrutinize.
