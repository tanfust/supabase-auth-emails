# Supabase Email Templates Reference

## Table of Contents

1. [Auth Templates (Action Required)](#auth-templates)
2. [Notification Templates (Informational)](#notification-templates)
3. [Complete Go Template Variables](#complete-go-template-variables)

---

## Auth Templates

These templates contain a CTA (button or link) that the user must act on. Configured via `[auth.email.template.<type>]` in config.toml.

### 1. confirmation — Confirm Sign Up

- **Purpose**: Sent after a user signs up to verify their email
- **CTA**: Button linking to `{{ .ConfirmationURL }}`
- **Variables**: `{{ .ConfirmationURL }}`, `{{ .Email }}`, `{{ .SiteURL }}`
- **Subject example**: "Confirm your email"

### 2. invite — Invite User

- **Purpose**: Sent when an admin invites a new user
- **CTA**: Button linking to `{{ .ConfirmationURL }}`
- **Variables**: `{{ .ConfirmationURL }}`, `{{ .Email }}`, `{{ .SiteURL }}`
- **Subject example**: "You've been invited"

### 3. magic_link — Magic Link

- **Purpose**: Passwordless sign-in link
- **CTA**: Button linking to `{{ .ConfirmationURL }}`
- **Variables**: `{{ .ConfirmationURL }}`, `{{ .Email }}`, `{{ .SiteURL }}`
- **Subject example**: "Your sign-in link"

### 4. email_change — Change Email Address

- **Purpose**: Verify new email address after changing it
- **CTA**: Button linking to `{{ .ConfirmationURL }}`
- **Variables**: `{{ .ConfirmationURL }}`, `{{ .Email }}`, `{{ .NewEmail }}`, `{{ .SiteURL }}`
- **Subject example**: "Confirm email change"

### 5. recovery — Reset Password

- **Purpose**: Password reset link
- **CTA**: Button linking to `{{ .ConfirmationURL }}`
- **Variables**: `{{ .ConfirmationURL }}`, `{{ .Email }}`, `{{ .SiteURL }}`
- **Subject example**: "Reset your password"

### Reauthentication (not configurable via config.toml)

Supabase sends a reauthentication OTP email internally when a user attempts a sensitive action. There is no `[auth.email.template.reauthentication]` key in config.toml — this email cannot be customized through the template system. You can still create a React Email component for it for preview/documentation purposes.

- **CTA**: Displays OTP code `{{ .Token }}`
- **Variables**: `{{ .Token }}`, `{{ .Email }}`

---

## Notification Templates

These are informational security alerts — no CTA needed. Configured via `[auth.email.notification.<type>]` in config.toml. All require `enabled = true`.

### 6. password_changed

- **Variables**: `{{ .Email }}`
- **Subject example**: "Your password has been changed"

### 7. email_changed

- **Variables**: `{{ .Email }}`, `{{ .OldEmail }}`
- **Subject example**: "Your email address has been changed"

### 8. phone_changed

- **Variables**: `{{ .Email }}`, `{{ .Phone }}`, `{{ .OldPhone }}`
- **Subject example**: "Your phone number has been changed"

### 9. identity_linked

- **Variables**: `{{ .Email }}`, `{{ .Provider }}`
- **Subject example**: "A new identity has been linked"

### 10. identity_unlinked

- **Variables**: `{{ .Email }}`, `{{ .Provider }}`
- **Subject example**: "An identity has been unlinked"

### 11. mfa_factor_enrolled

- **Variables**: `{{ .Email }}`, `{{ .FactorType }}`
- **Subject example**: "A new MFA method has been added"

### 12. mfa_factor_unenrolled

- **Variables**: `{{ .Email }}`, `{{ .FactorType }}`
- **Subject example**: "An MFA method has been removed"

---

## Complete Go Template Variables

All variables available in Supabase email templates:

| Variable | Type | Description | Available In |
| --- | --- | --- | --- |
| `{{ .ConfirmationURL }}` | URL | Full confirmation/action URL | confirmation, invite, magic_link, email_change, recovery |
| `{{ .Token }}` | String | 6-digit OTP code | reauthentication (internal only) |
| `{{ .SiteURL }}` | URL | Application base URL | All templates |
| `{{ .Email }}` | String | User's email address | All templates |
| `{{ .NewEmail }}` | String | New email after change | email_change |
| `{{ .OldEmail }}` | String | Previous email address | email_changed notification |
| `{{ .Phone }}` | String | New phone number | phone_changed notification |
| `{{ .OldPhone }}` | String | Previous phone number | phone_changed notification |
| `{{ .Provider }}` | String | Identity provider name (e.g., "google", "github") | identity_linked, identity_unlinked notifications |
| `{{ .FactorType }}` | String | MFA factor type (e.g., "totp") | mfa_factor_enrolled, mfa_factor_unenrolled notifications |

