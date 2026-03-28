# Supabase Email Templates Reference

## Table of Contents

1. [Auth Templates (Action Required)](#auth-templates)
2. [Notification Templates (Informational)](#notification-templates)
3. [Complete Go Template Variables](#complete-go-template-variables)
4. [PKCE Flow (Server-Side Auth)](#pkce-flow)

---

## Auth Templates

These templates contain a CTA (button or code) that the user must act on.

### 1. confirmation — Confirm Sign Up

- **Purpose**: Sent after a user signs up to verify their email
- **CTA**: Button linking to `{{ .ConfirmationURL }}`
- **Variables**: `{{ .ConfirmationURL }}`, `{{ .Email }}`, `{{ .SiteURL }}`, `{{ .TokenHash }}`, `{{ .RedirectTo }}`
- **Subject**: "Confirm your email"

### 2. invite — Invite User

- **Purpose**: Sent when an admin invites a new user
- **CTA**: Button linking to `{{ .ConfirmationURL }}`
- **Variables**: `{{ .ConfirmationURL }}`, `{{ .Email }}`, `{{ .SiteURL }}`, `{{ .TokenHash }}`, `{{ .RedirectTo }}`
- **Subject**: "You've been invited"

### 3. magic_link — Magic Link

- **Purpose**: Passwordless sign-in link
- **CTA**: Button linking to `{{ .ConfirmationURL }}`
- **Variables**: `{{ .ConfirmationURL }}`, `{{ .Email }}`, `{{ .SiteURL }}`, `{{ .TokenHash }}`, `{{ .RedirectTo }}`
- **Subject**: "Your sign-in link"

### 4. email_change — Change Email Address

- **Purpose**: Verify new email address after changing it
- **CTA**: Button linking to `{{ .ConfirmationURL }}`
- **Variables**: `{{ .ConfirmationURL }}`, `{{ .Email }}`, `{{ .NewEmail }}`, `{{ .SiteURL }}`, `{{ .TokenHash }}`, `{{ .RedirectTo }}`
- **Subject**: "Confirm email change"

### 5. recovery — Reset Password

- **Purpose**: Password reset link
- **CTA**: Button linking to `{{ .ConfirmationURL }}`
- **Variables**: `{{ .ConfirmationURL }}`, `{{ .Email }}`, `{{ .SiteURL }}`, `{{ .TokenHash }}`, `{{ .RedirectTo }}`
- **Subject**: "Reset your password"

### 6. reauthentication — Reauthentication

- **Purpose**: OTP code for sensitive actions (NOT a link — uses a 6-digit code)
- **CTA**: Display OTP code `{{ .Token }}`
- **Variables**: `{{ .Token }}`, `{{ .Email }}`
- **Subject**: "Security verification"
- **Note**: This is the ONLY auth template that uses a code instead of a URL

---

## Notification Templates

These are informational — no CTA needed, just a security alert with "contact support" guidance.

### 7. password_changed

- **Variables**: `{{ .Email }}`
- **Subject**: "Your password has been changed"

### 8. email_changed

- **Variables**: `{{ .Email }}`, `{{ .OldEmail }}`
- **Subject**: "Your email address has been changed"

### 9. phone_changed

- **Variables**: `{{ .Email }}`, `{{ .Phone }}`, `{{ .OldPhone }}`
- **Subject**: "Your phone number has been changed"

### 10. identity_linked

- **Variables**: `{{ .Email }}`, `{{ .Provider }}`
- **Subject**: "A new identity has been linked"

### 11. identity_unlinked

- **Variables**: `{{ .Email }}`, `{{ .Provider }}`
- **Subject**: "An identity has been unlinked"

### 12. mfa_added (mfa_factor_enrolled)

- **Variables**: `{{ .Email }}`, `{{ .FactorType }}`
- **Subject**: "A new MFA method has been added"

### 13. mfa_removed (mfa_factor_unenrolled)

- **Variables**: `{{ .Email }}`, `{{ .FactorType }}`
- **Subject**: "An MFA method has been removed"

---

## Complete Go Template Variables

All variables available in Supabase email templates:

| Variable | Type | Description | Available In |
| --- | --- | --- | --- |
| `{{ .ConfirmationURL }}` | URL | Full confirmation/action URL | confirmation, invite, magic_link, email_change, recovery |
| `{{ .Token }}` | String | 6-digit OTP code | reauthentication |
| `{{ .TokenHash }}` | String | Token hash for PKCE flows | confirmation, invite, magic_link, email_change, recovery |
| `{{ .RedirectTo }}` | URL | Post-action redirect URL | confirmation, invite, magic_link, email_change, recovery |
| `{{ .SiteURL }}` | URL | Application base URL | All templates |
| `{{ .Email }}` | String | User's email address | All templates |
| `{{ .NewEmail }}` | String | New email after change | email_change |
| `{{ .OldEmail }}` | String | Previous email address | email_changed notification |
| `{{ .Phone }}` | String | New phone number | phone_changed notification |
| `{{ .OldPhone }}` | String | Previous phone number | phone_changed notification |
| `{{ .Provider }}` | String | Identity provider name (e.g., "google", "github") | identity_linked, identity_unlinked notifications |
| `{{ .FactorType }}` | String | MFA factor type (e.g., "totp") | mfa_added, mfa_removed notifications |

---

## PKCE Flow

For server-side auth frameworks (Next.js App Router, SvelteKit, Remix), you may need to use PKCE-compatible URLs instead of `{{ .ConfirmationURL }}`. Replace the button href with a constructed URL using `{{ .TokenHash }}`:

```
{{ .SiteURL }}/auth/confirm?token_hash={{ .TokenHash }}&type=<type>&redirect_to={{ .RedirectTo }}
```

The `type` parameter changes per template:

| Template | Type Value |
| --- | --- |
| confirmation | `email` |
| invite | `invite` |
| magic_link | `magiclink` |
| email_change | `email_change` |
| recovery | `recovery` |

**Example — PKCE confirmation button:**

```tsx
<Button
  href="{{ .SiteURL }}/auth/confirm?token_hash={{ .TokenHash }}&type=email&redirect_to={{ .RedirectTo }}"
  className="box-border bg-gray-900 text-white px-6 py-3 text-sm font-medium no-underline"
>
  Confirm Email
</Button>
```

Use the PKCE approach when your app handles the token exchange server-side (e.g., Next.js `/auth/confirm` route that calls `supabase.auth.verifyOtp()`). Use `{{ .ConfirmationURL }}` when Supabase handles the redirect directly.
