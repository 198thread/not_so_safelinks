### Schema

`&data=` has this:

| Position | Value in Sample | Exact Purpose & Mechanism |
| - | - | - |
| 0 | `04` | Schema Version: Tells Microsoft's edge servers which parsing algorithm to use (Versions 01...05 exist, late 2026). |
| 1 | `01` | Recipient Type Flag: Indicates an internal M365 user/mailbox context. |
| 2 | `<User Email>` | Recipient **mailbox address** / User Principal Name (UPN) |
| 3 | `<32 Hex Chars>` | Tenant ID: 128-bit GUID, no hyphens |
| 4 | `<32 Hex Chars>` | Mailbox ID OR User Object ID OR Exchange Directory ID: 128-bit GUID, no hyphens. |
| 5 | `0` | Action / Threat State Flag: Internal routing flag (e.g., whether the link was clicked pre- or post-delivery). |
| 6 | `0` | Isolation Level: Signals whether rendering requires browser isolation or basic proxying. |
| 7 | `<18-Digit Timestamp>` | [.Net Datetime Ticks](https://learn.microsoft.com/en-us/dotnet/api/system.datetime.ticks?view=net-10.0)  |
| 8 | `unknown` | Threat Verdict: Default placeholder field populated when no prior bad-reputation verdict exists at wrap-time. |
| 9 | `<Base64>` | **Client Fingerprint:** (Optional 'EmptyMapi for delivery agents) Version, Platform, Application Name, Wrapping Type (e.g. `Mailflow\|{"V":"0.0.0000","P":"Win32","AN":"Mail","WT":2}` for Version 0, Platform Windows, Mail app, Client-side render).|
| 10 | `1000` | Routing Flag / Policy Bitmask: Internal policy enforcement state applied by the Exchange Transport Rule engine. |

### Recipient Type Flag
Enum:
- 01 = Internal Org Mailbox
- 02 = [External Usertype](https://learn.microsoft.com/en-us/entra/external-id/user-properties) Guest/Member
- 03 = Shared/Equipment Mailbox
- 04 = External Recipient/Outbound
- 05 = Distribution Group/List Context

### Action / Threat State Flag
Enum:
- 0 = Pre-delivery (default)
- 1 = Threat Identified/Modified (updated by Exchange Transport Rule)

### Threat Verdict
Enum:
- unknown (default, meaning every click = link check against AZ Intel DB)
- clean/safe
- malware
- phishing
- spam
- suspicious
- custom/blocked

### .Net Datetime Ticks

*Each tick is 100-nanosecond intervals since January 1, 0001 AD (00:00:00 UTC)*

Conversion is 

FromUnixTimestamp((<sample> - 621_355_968_000_000_000) / 10_000_000)

It's 18 digits, you're better off using [a online converter](https://www.epochconverter.com/dotnet)

### Wrapping Type
Enum:
- 0 = Pre-delivery scanning
- 1 = Time-of-click rewriting
- 2 = Client-side rendering
- 3 = O365 Integration, 4 = Teams

### NB
`&data=` is followed with `&sdata=` and `&reserved=\d`.

`&sdata=` is url integrity via HMAC-256 of everything in the URL except `&sdata=`, but including `&reserved=\d`

`&reserved=\d` takes a bit mask for flags. Possibly underused.

