### Schema 01 — 6 Fields

| Pos | Values | Status |
| - | - | - |
| 0 | `01` | Fixed |
| 1 | `01` | Fixed |
| 2 | address / empty | Recipient. Locks field 4. Locks field 5 |
| 3 | 32-hex | Message ID. Matches [Network Message ID](https://learn.microsoft.com/en-us/powershell/module/exchangepowershell/get-messagetracev2). Not RFC4122 — variant nibble random. No time order |
| 4 | 32-hex | Tenant ID. RFC4122 confirmed. Recipient's tenant, not sender's |
| 5 | `0` `1` | Unknown. Static per message. No flip observed on same message |

---

### Schema 02 — 8 Fields

| Pos | Values | Status |
| - | - | - |
| 0 | `02` | Fixed |
| 1 | `01` | Fixed. `02` not observed here |
| 2 | address / empty | Recipient. Locks field 4. Locks field 6 |
| 3 | 32-hex | Message ID. Same as schema 01 |
| 4 | 32-hex | Tenant ID. Same as schema 01 |
| 5 | `0` `1` | Unknown. Static per message. Zero flips observed, any direction |
| 6 | `0` `1` | Unknown. Dynamic per click. Flips both directions observed |
| 7 | 18-digit | Ticks. [.NET DateTime.Ticks](https://learn.microsoft.com/en-us/dotnet/api/system.datetime.ticks). Converter: [epochconverter.com/dotnet](https://www.epochconverter.com/dotnet) |

---

### Schema 04 — 11 Fields

| Pos | Values | Status |
| - | - | - |
| 0 | `04` | Fixed |
| 1 | `01` | Fixed. `02` not observed here |
| 2 | address / empty | Recipient. Locks fields 4, 8, 9 |
| 3 | 32-hex | Message ID |
| 4 | 32-hex | Tenant ID |
| 5 | `0` `1` | Unknown |
| 6 | `0` `1` | Unknown |
| 7 | 18-digit | Ticks |
| 8 | `Unknown` | Verdict. No `Good` here. Checked against [Defender threat classification](https://learn.microsoft.com/en-us/defender-office-365/mdo-threat-classification) — no match |
| 9 | base64 | `Mailflow\|{V,P,AN,WT:2}`. No `EmptyMapi` here |
| 10 | `0` `1000` `2000` `3000` `4000` `5000` `7000` | Unknown. Multiples of 1000 |

---

### Schema 05 — 11 Fields (14 raw, last 3 empty)

Not observed in every region checked.

| Pos | Values | Status |
| - | - | - |
| 0 | `05` | Fixed |
| 1 | `01` `02` | Matches [Entra B2B UserType](https://learn.microsoft.com/en-us/entra/external-id/user-properties): Member / Guest |
| 2 | address / empty | Recipient. Locks field 4. Locks field 8 |
| 3 | 32-hex | Message ID |
| 4 | 32-hex | Tenant ID |
| 5 | `0` `1` | Unknown. Static per message |
| 6 | `0` `1` | Unknown. Dynamic per click |
| 7 | 18-digit | Ticks. Confirmed |
| 8 | `Unknown` `Good` | Verdict. `Good` pairs with `WAC`/WT:4 always. Rare |
| 9 | base64 | `Mailflow\|{V,P,AN,WT:2}` or `WAC\|{V,P,AN,WT:4}`. `EmptyMapi` present implies field 1 = `02`. Reverse not true |
| 10 | `0` `1` `1000`–`80000` | Unknown. `1` only on WT:4 rows |

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

### Tenant ID Integrity

| Check | Result |
| - | - |
| RFC4122 compliant, all schemas | Yes |
| Stable per org | Yes |
| Shared across clouds | No |
| Shared across regions (same cloud) | Yes |
| Encodes routing | No |

### Message ID Integrity

| Check | Result |
| - | - |
| RFC4122 compliant | No |
| Carries timestamp / order | No |
| Spans multiple ticks values per ID | Yes |

---

### Field 5 vs Field 6

| | Field 5 | Field 6 |
| - | - | - |
| Flips across clicks of same message | No | Yes |
| Looks per-message | Yes | No |
| Looks per-click | No | Yes |
| Meaning | Unknown | Unknown |
