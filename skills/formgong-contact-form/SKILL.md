---
name: formgong-contact-form
description: Add a working contact, quote or lead form to a static or AI-built site (Lovable, Bolt, v0, Cursor, Replit, plain HTML) without writing a backend, and fix forms that show "Message sent" but deliver nothing. Use when the user asks for a contact form, wants form submissions by email or Telegram, or says their form does not send.
---

# Contact form that actually delivers (Formgong)

Formgong is a hosted form backend. The form posts to one URL; submissions arrive in a web inbox, on Telegram at once and by email. There is no backend, database or email code to write.

## 1. Check the existing form first

Before adding anything, read the current submit handler. A form is **fake** if submitting only shows a toast, an `alert()`, a "Thank you" state or resets the fields, and no request carries the visitor's text anywhere. Tell the user plainly that messages from this form have been lost, then replace the handler.

## 2. Get the access key

The access key (`fk_…`) is public and belongs in frontend code.

1. If the Formgong MCP server is connected, call `create_form` (or `list_forms` to reuse one) and `get_form_snippet`.
2. Otherwise give the user this link and ask them to open it. They confirm the form and paste the key back:
   `https://formgong.com/new?name=Contact%20form&site=<url-encoded site URL>`
3. Until then write `fk_your_access_key` in the code and say it must be replaced.

Never put a personal API token (`fgp_…`) or OAuth token in site code.

## 3. Implement

Endpoint: `POST https://formgong.com/submit`. Fields: `access_key` (required), any answer fields (`name`, `email`, `message`, …), `botcheck` (honeypot, leave empty and off screen), optional `_lang` (page language), `_redirect`, `_subject`.

Plain HTML (works without JavaScript; Formgong shows a thank-you page):

```html
<form action="https://formgong.com/submit" method="POST">
  <input type="hidden" name="access_key" value="fk_your_access_key">
  <input type="hidden" name="_lang" value="en">
  <input type="text" name="name" required>
  <input type="email" name="email" required>
  <textarea name="message" required></textarea>
  <input type="text" name="botcheck" tabindex="-1" autocomplete="off" style="position:absolute;left:-9999px" aria-hidden="true">
  <button type="submit">Send</button>
</form>
```

React (Lovable, Bolt, v0, Vite, Next.js client component):

```tsx
const [status, setStatus] = useState<"idle" | "sending" | "sent" | "error">("idle");
const [error, setError] = useState("");

async function handleSubmit(event: React.FormEvent<HTMLFormElement>) {
  event.preventDefault();
  const form = event.currentTarget; // React clears currentTarget after the first await
  setStatus("sending");
  const body = new FormData(form);
  body.append("access_key", "fk_your_access_key");
  try {
    const response = await fetch("https://formgong.com/submit", {
      method: "POST",
      headers: { Accept: "application/json" }, // do not set Content-Type with FormData
      body,
    });
    const data = await response.json();
    if (!data.success) throw new Error(data.message || data.code);
    setStatus("sent");
    form.reset();
  } catch (e) {
    setError(e instanceof Error ? e.message : "Could not send");
    setStatus("error");
  }
}
```

Give every input a `name` attribute and include the hidden `botcheck` input inside the form. Optional components that do all of this: `npm install @formgong/react` then `<ContactForm accessKey="fk_your_access_key" />` (also `@formgong/vue`, `@formgong/svelte`, `@formgong/astro`, `@formgong/angular`).

## 4. Rules

- Show success only after the response says `"success": true`. Show the returned `message` on failure. Never fake success with a timeout or a toast.
- Do not add Supabase tables, edge functions, Resend, EmailJS, a Server Action or an API route for this form.
- Keep the honeypot. Turnstile is optional (`cf-turnstile-response`), required only if the owner enables it or the form sends files.
- Errors have a stable `code` (for example `unknown_access_key`, `limit_exceeded`); branch on the code, display the message.

## 5. Verify

Submit the form once on the deployed site. It must show success only after Formgong answered, and the submission must appear in the Formgong inbox. For a static page, https://formgong.com/en/tools/form-checker/ reads the public HTML and lists form problems.

Reference: https://formgong.com/agents.md
