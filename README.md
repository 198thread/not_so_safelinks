# not_so_safelinks
Informal learning notes on the [MS Safelinks](https://learn.microsoft.com/en-us/defender-office-365/safe-links-about)

Auto functions to wrap links, commonly with Tenant ID, Entra ID and Client fingerprint *and even email.* . 

Internally, it feeds:
- the Unified Audit Log (`ThreatIntelligenceUrl`)
- Sentinel/Defender (`UrlClickEvents`)

MS Graph exposes it too (`threatIntelligenceUrlClickData`). 

Unsure of event transmission hierarchy. Either:
- there's a switch somewhere in the duplicated feeds
- there's no deduplication and MS is happily turning  1 event into 3 events * 0.0087c).

### Trigger
Unsure for now. Can't _expect_ MS to have tightened everything, if legacy support is required. It's a start-up, give them some time.

According to [MS](https://learn.microsoft.com/en-us/defender-office-365/safe-links-about) `Safe Links no longer wraps URLs pointing to SharePoint or OneDrive sites...`

**Posit** that if domain of page is not in default denylist (e.g. *.sharepoint.com, *.office etc.), then link gets wrapped.

Commonly from Outlook and Office365 client-facing public outputs. 

Might be more sources, update later with DNS (RFC 1035) compliance vs. AZ Routing/Edge servers. 

**Suspect** that same issues with CDN 4 primary URL formats:
1. External signed
2. Internal auth'd
3. Edge node
4. Physical location

...since we're seeing a version of the Edge node form. 

### In the wild
If public url has 'aspx' or feels like a MS product, grep the html.

When safelinks are copy-pasted into other applications, e.g. Outlook [CTRL-V> Teams

---

### Grep
Link format is `https://<region><node>.safelinks.protection.outlook.com/?url=...`

Link format can also be `https://usgov\d\d.safelinks.protection.office365.us`.

Unsure for Vainet `https://chn\d\d.safelinks.protection.partnet.outlook.cn` but may be without `partnet`.

### Free JS

This should manually catch everything in `data=` that `decodeURI` can miss, especially if multilayer-decoding.

```
a.href.toString()
  .replaceAll('%3A', ':')
  .replaceAll('%2F','/')
  .replaceAll('%3D','=')
  .replaceAll('%40','@')
  .replaceAll('%7C','|')
  .replaceAll('&amp','&')
  .replaceAll('&;','&')
  .replace(/^.*&data=/,'')
  .replace(/&(sdata|reserved).*/,'')
```

oneliner:
`a.href.toString().replaceAll('%3A', ':').replaceAll('%2F','/').replaceAll('%3D','=').replaceAll('%40','@').replaceAll('%7C','|').replaceAll('&amp','&').replaceAll('&;','&').replace(/^.*&data=/,'').replace(/&(sdata|reserved).*/,'')`

The Client Fingerprint is not useful, but just in case..

1. decode into a `const decodedUrl` from the above one-liner
2. `atob(decodedURL.split('|').find(element => element.slice(-1) == '=')))} catch {}`
