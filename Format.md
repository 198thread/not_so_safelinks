### Schema Version 01 — 6 Fields

| Pos | Values | Status |
| - | - | - |
| 0 | `01` | Schema Version |
| 1 | `01` | ? - Fixed. |
| 2 | `@ddress` / empty | Recipient. Locks fields 4 (Org Tenant ID) |
| 3 | `<32-hex>` | Message ID |
| 4 | `<32-hex>` | Org Tenant ID |
| 5 | `0` / `1` | ? |

---

### Schema Version 02 — 8 Fields

| Pos | Values | Status |
| - | - | - |
| 0 | `02` | Schema Version |
| 1 | `01` | ? - Fixed. |
| 2 | `@ddress` / empty | Recipient. Locks fields 4 (Org Tenant ID) |
| 3 | `<32-hex>` | Message ID |
| 4 | `<32-hex>` | Org Tenant ID |
| 5 | `0` / `1` | ? |
| 6 | `0` / `1` | ? |
| 7 | `<18-digit timestamp>` | .Net Ticks. [.NET DateTime.Ticks](https://learn.microsoft.com/en-us/dotnet/api/system.datetime.ticks). Converter: [epochconverter.com/dotnet](https://www.epochconverter.com/dotnet) |

---

### Schema Version 04 — 11 Fields

| Pos | Values | Status |
| - | - | - |
| 0 | `04` | Schema Version |
| 1 | `01` | ? - Fixed. |
| 2 | `@ddress` / empty | Recipient. Locks fields 4 (Org Tenant ID), 8 & 9 |
| 3 | `<32-hex>` | Message ID |
| 4 | `<32-hex>` | Org Tenant ID |
| 5 | `0` / `1` | ? |
| 6 | `0` / `1` | ? |
| 7 | `<18-digit timestamp>` | .Net Ticks. [.NET DateTime.Ticks](https://learn.microsoft.com/en-us/dotnet/api/system.datetime.ticks). Converter: [epochconverter.com/dotnet](https://www.epochconverter.com/dotnet) |
| 8 | `Unknown` | Possibly Defender Threat Indicator?. No `Good` here. |
| 9 | `<base64>` | `Mailflow\|{V,P,AN,WT:2}`. No `EmptyMapi` here |
| 10 | `0` `1000` `2000` `3000` `4000` `5000` `7000` | ? - Multiples of 1000. No 6000. Possibly bit-mask |

---

### Schema Version 05 — 11 Fields (14 raw, last 3 empty)

Not observed in every region checked.

| Pos | Values | Status |
| - | - | - |
| 0 | `05` | Schema Version |
| 1 | `01` / `02` | Possibly internal/external marker from [Entra B2B UserType](https://learn.microsoft.com/en-us/entra/external-id/user-properties): Member / Guest |
| 2 | `@ddress` / empty | Recipient. Locks fields 4 (Org Tenant ID) & 8 |
| 3 | `<32-hex>` | Message ID |
| 4 | `<32-hex>` | Org Tenant ID |
| 5 | `0` / `1` | ? |
| 6 | `0` / `1` | ? |
| 7 | `<18-digit timestamp>` | .Net Ticks. [.NET DateTime.Ticks](https://learn.microsoft.com/en-us/dotnet/api/system.datetime.ticks). Converter: [epochconverter.com/dotnet](https://www.epochconverter.com/dotnet) |
| 8 | `Unknown` `Good` | ? - `Good` pairs with `WAC`/WT:4 always. Rare.  |
| 9 | `<base64>` | `Mailflow\|{V,P,AN,WT:2}` or `WAC\|{V,P,AN,WT:4}`. `EmptyMapi` present implies field 1 = `02`. Reverse not true. |
| 10 | `0` `1` `1000` `2000` `3000` `4000` `6000` `7000` `20000` `40000` `41000` `60000` `62000` `80000` | ? - `1` only on WT:4 rows. Possibly bit-mask. |

---

### Placeholder Tenant: `84df9e7fe9f640afb435aaaaaaaaaaaa`

| Check | Result |
| - | - |
| Consumer mailbox marker (Hotmail/Outlook/MSN/Live) | Yes |
| Real Entra tenant | No |
| Forces field 5 = `1` | Yes |
| Recipient email blank | Mostly yes |
| One sender spamming | No |
| Tied to `EmptyMapi` / agent clients | No |
| Node bytes = deliberate sentinel (`aa`×6, not random) | Yes |
| Encodes region / routing | No |
| Matches Microsoft [Multi-Geo / PreferredDataLocation](https://learn.microsoft.com/en-us/microsoft-365/enterprise/microsoft-365-multi-geo) model | Yes — routing is a directory lookup, not GUID-encoded |

---

### Message ID Integrity

| Check | Result |
| - | - |
| RFC4122/RFC9562 compliance | No |
| Spans multiple ticks values per ID | Yes |

Possibly using same generator as [GUID/UUID for Office](https://learn.microsoft.com/en-us/openspecs/office_file_formats/ms-onestore/ba2ccfe9-f8d9-4a32-ad9c-3b0b6e1037d6)

---

### Field 5 vs Field 6

| | Field 5 | Field 6 |
| - | - | - |
| Dynamic despite same Message ID | No | Yes |
| Meaning | ? | ? |
