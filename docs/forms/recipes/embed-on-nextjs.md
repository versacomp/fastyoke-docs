---
title: Embed a form on a Next.js page
summary: Drop the embed snippet onto any page; works in App Router and Pages Router.
order: 6
recipe: true
---

# Embed a form on a Next.js page

**When you'd use this — your marketing site is Next.js and you want the form to render directly on `/contact` (or any other page) without managing an iframe yourself.**

The FastYoke embed is a single `<script>` tag. It is not a React component, so there's no npm package to install and no SSR/CSR boundary to think about. The same snippet works in App Router (`app/contact/page.tsx`) and Pages Router (`pages/contact.tsx`).

## 1. Copy the snippet from the Embed panel

Open the form in the admin shell, switch to the **Embed** tab, pick a mode (start with `inline`), and click **Save embed**. The panel shows a copyable snippet of the form:

```html
<script async src="https://app.fastyoke.io/embed.js" data-embed="em_AbCd1234567890EfGhIjKl"></script>
```

The `em_…` value is the embed ID assigned when you saved.

## 2. Paste it into your Next.js page

JSX accepts a raw `<script>` tag inline — you do not need `next/script` for this, and you do not need to escape anything. This is the most common stumbling block: people reach for `next/script` and end up debugging hydration mismatches that don't exist.

```tsx
export default function ContactPage() {
  return (
    <main>
      <h1>Contact us</h1>
      <script
        async
        src="https://app.fastyoke.io/embed.js"
        data-embed="em_AbCd1234567890EfGhIjKl"
      />
    </main>
  );
}
```

For `inline` mode the form renders where the tag sits. For `modal`, `slide`, or `bubble` modes the placement of the tag doesn't matter — the chrome is a fixed overlay. Make sure your production domain is on the **Settings → Embed domains** allowlist before launch, otherwise visitors see the unavailable placeholder.

## See also

- [Share with an invite or embed](/docs/forms/tutorials/101-share-invite-and-embed)
- [Embed reference](/docs/forms/embed)
