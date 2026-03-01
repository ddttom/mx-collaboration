---
title: "REGINALD Server-Side Language Redirect"
description: "Implement Accept-Language header detection and automatic redirect at the REGINALD edge layer"
author: Tom Cranstoun
created: 2026-02-22
modified: 2026-02-22
status: Ready for Implementation
version: "1.0"
priority: high
category: reginald-features
tags: [reginald, i18n, language-detection, edge, redirect, seo]
---

# REGINALD Server-Side Language Redirect

**Author:** Tom Cranstoun
**Status:** Ready for Implementation
**Date:** 2026-02-22
**Priority:** High

## Background

The n-language cogify template system requires intelligent language routing at the server/edge level. Client-side JavaScript detection works but has limitations:

1. **Flash of wrong content** — User sees default language before JS redirects
2. **SEO impact** — Crawlers may not execute JS redirect
3. **Performance** — Extra round-trip for redirect
4. **Reliability** — Depends on JS execution

REGINALD, as the COG registry and delivery layer, is the ideal place to implement server-side language detection and redirect.

## Proposal

### Core Functionality

REGINALD will detect the user's preferred language from the `Accept-Language` HTTP header and redirect to the appropriate language version before serving content.

### Request Flow

```
User requests: https://example.com/
    ↓
REGINALD Edge (Cloudflare Worker)
    ↓
Read Accept-Language header: "es-ES,es;q=0.9,en;q=0.8"
    ↓
Parse and match against available languages
    ↓
302 Redirect → https://example.com/es/
    ↓
Serve /es/index.cog.html
```

### Language Detection Algorithm

```javascript
function detectLanguage(acceptLanguageHeader, availableLanguages, defaultLanguage) {
  // Parse Accept-Language header
  // Example: "es-MX,es;q=0.9,en-US;q=0.8,en;q=0.7"
  const preferences = acceptLanguageHeader
    .split(',')
    .map(lang => {
      const [code, qValue] = lang.trim().split(';q=');
      const normalized = code.toLowerCase();
      const base = normalized.split('-')[0]; // es-MX → es
      return {
        full: normalized,   // es-mx
        base: base,         // es
        quality: qValue ? parseFloat(qValue) : 1.0
      };
    })
    .sort((a, b) => b.quality - a.quality);

  // Cascading match: full regional → base → default
  for (const pref of preferences) {
    // Try exact regional match first (es-mx)
    if (availableLanguages.includes(pref.full)) {
      return pref.full;
    }
    // Try base language match (es)
    if (availableLanguages.includes(pref.base)) {
      return pref.base;
    }
  }

  // Fallback to default
  return defaultLanguage;
}
```

**Fallback cascade:**

1. `es-MX` header → try `/es-mx/` → try `/es/` → use default
2. No cookies or localStorage — stateless, GDPR-compliant

### Configuration

Each site registered with REGINALD declares its language configuration:

```yaml
# Site configuration in REGINALD registry
site:
  id: los-granainos
  domain: los-granainos.pages.dev

languages:
  available:
    - code: es
      name: Español
      path: /es/
    - code: en
      name: English
      path: /en/
    - code: fr
      name: Français
      path: /fr/
  default: es

redirect:
  enabled: true
  method: 302  # or 301 for permanent
  paths:
    - /         # Redirect root
    - /index.html
  exclude:
    - /assets/  # Don't redirect static assets
    - /api/     # Don't redirect API calls
```

### Edge Implementation (Cloudflare Worker)

```javascript
// REGINALD Language Redirect Worker

export default {
  async fetch(request, env) {
    const url = new URL(request.url);

    // Skip if already on a language path
    const langPathMatch = url.pathname.match(/^\/(es|en|fr)\//);
    if (langPathMatch) {
      return fetch(request); // Pass through
    }

    // Skip excluded paths
    const excludedPaths = ['/assets/', '/api/', '/.well-known/'];
    if (excludedPaths.some(p => url.pathname.startsWith(p))) {
      return fetch(request);
    }

    // Only redirect root and specific paths
    const redirectPaths = ['/', '/index.html'];
    if (!redirectPaths.includes(url.pathname)) {
      return fetch(request);
    }

    // Get site config from REGINALD registry
    const siteConfig = await getSiteConfig(url.hostname, env);
    if (!siteConfig?.languages?.redirect?.enabled) {
      return fetch(request);
    }

    // Detect language from Accept-Language header
    const acceptLang = request.headers.get('Accept-Language') || '';
    const targetLang = detectLanguage(
      acceptLang,
      siteConfig.languages.available.map(l => l.code),
      siteConfig.languages.default
    );

    // Redirect to language-specific path
    const redirectUrl = new URL(url);
    redirectUrl.pathname = `/${targetLang}${url.pathname === '/' ? '/' : url.pathname}`;

    return Response.redirect(redirectUrl.toString(), 302);
  }
};
```

### Client-Side Fallback

For sites not using REGINALD edge, provide a client-side fallback in the root `index.html`:

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>Redirecting...</title>
  <script>
    (function() {
      // Get available languages from directory structure
      // This would be injected by build process or fetched
      const available = ['es', 'en', 'fr'];
      const defaultLang = 'es';

      // Parse browser language
      const browserLangs = navigator.languages || [navigator.language];
      let targetLang = defaultLang;

      for (const lang of browserLangs) {
        const code = lang.split('-')[0].toLowerCase();
        if (available.includes(code)) {
          targetLang = code;
          break;
        }
      }

      // Redirect
      window.location.href = '/' + targetLang + '/';
    })();
  </script>
  <noscript>
    <meta http-equiv="refresh" content="0; url=/es/">
  </noscript>
</head>
<body>
  <p>Redirecting to your language...</p>
  <p><a href="/es/">Español</a> | <a href="/en/">English</a> | <a href="/fr/">Français</a></p>
</body>
</html>
```

## Integration with N-Language Templates

### Template Structure

```
/site-name/
├── assets/
│   ├── style.css
│   └── script.js
├── es/
│   └── index.cog.html
├── en/
│   └── index.cog.html
├── fr/
│   └── index.cog.html
├── index.html              # Client-side fallback redirect
└── reginald.config.yaml    # REGINALD configuration
```

### REGINALD Config File

```yaml
# reginald.config.yaml
# Placed in site root, read by REGINALD at deploy time

version: "1.0"
site:
  name: "Los Granainos"
  type: business

languages:
  available:
    - { code: es, name: Español, default: true }
    - { code: en, name: English }
  redirect:
    enabled: true
    method: 302

hreflang:
  inject: true  # REGINALD injects hreflang tags at edge
  x-default: es

caching:
  language-pages: 3600  # 1 hour
  assets: 86400         # 24 hours
```

## SEO Considerations

### hreflang Injection

REGINALD can inject hreflang tags at the edge, ensuring consistency:

```javascript
// Edge function adds hreflang to response
async function injectHreflang(response, siteConfig) {
  const html = await response.text();
  const languages = siteConfig.languages.available;
  const domain = siteConfig.domain;

  const hreflangTags = languages.map(lang =>
    `<link rel="alternate" hreflang="${lang.code}" href="https://${domain}/${lang.code}/" />`
  ).join('\n');

  // Add x-default
  const defaultLang = languages.find(l => l.default)?.code || languages[0].code;
  hreflangTags += `\n<link rel="alternate" hreflang="x-default" href="https://${domain}/${defaultLang}/" />`;

  // Inject before </head>
  const modifiedHtml = html.replace('</head>', `${hreflangTags}\n</head>`);

  return new Response(modifiedHtml, {
    headers: response.headers
  });
}
```

### Search Engine Handling

| Scenario | REGINALD Behavior |
|----------|-------------------|
| Googlebot (no Accept-Language) | Serve default language |
| Googlebot with hreflang hints | Each language URL indexed separately |
| User with Accept-Language | Redirect to matched language |
| Direct language URL access | Serve requested language (no redirect) |

## Implementation Phases

### Phase 1: Core Redirect (MVP)

- [ ] Cloudflare Worker for Accept-Language detection
- [ ] Basic redirect logic (root path only)
- [ ] REGINALD config file parsing
- [ ] Client-side fallback template

### Phase 2: hreflang Injection

- [ ] Edge injection of hreflang tags
- [ ] x-default handling
- [ ] Validation against available languages

### Phase 3: Advanced Features

- [ ] GeoIP fallback (if Accept-Language unavailable)
- [ ] Language preference cookies
- [ ] A/B testing for language defaults
- [ ] Analytics integration

## Files to Create/Modify

| File | Action | Description |
|------|--------|-------------|
| `reginald/workers/language-redirect.js` | Create | Cloudflare Worker for redirect |
| `reginald/schemas/site-config.yaml` | Create | Schema for reginald.config.yaml |
| `_templates/n-lang/index-redirect.html` | Create | Client-side fallback template |
| `_templates/n-lang/reginald.config.yaml` | Create | Example REGINALD config |
| `cogify-this.cog.md` | Update | Document REGINALD language redirect |

## Constraints

- **Cloudflare Workers** — REGINALD runs on Cloudflare edge
- **No cookies for detection** — Use Accept-Language header only (GDPR compliant)
- **Stateless** — Each request is independent
- **Performance** — Redirect must add <10ms latency

## Risks and Mitigations

| Risk | Mitigation |
|------|------------|
| Crawler confusion | Proper hreflang tags, x-default fallback |
| Redirect loops | Skip redirect if already on language path |
| Missing Accept-Language | Fallback to default language |
| Performance impact | Edge caching, minimal processing |

## Success Criteria

- [ ] Root URL redirects to correct language based on Accept-Language
- [ ] Direct language URLs (/es/, /en/) work without redirect
- [ ] hreflang tags injected correctly
- [ ] <10ms added latency for redirect
- [ ] SEO: All language versions indexed by Google
- [ ] Client-side fallback works when REGINALD unavailable

## Dependencies

- REGINALD registry infrastructure
- Cloudflare Workers deployment pipeline
- n-lang template system (this proposal)
- COG site configuration format

## Open Questions — RESOLVED

| Question | Decision | Rationale |
|----------|----------|-----------|
| Language preference cookies | **No persistence** | Always follow Accept-Language header. No cookies, no localStorage. GDPR-compliant by design. |
| Manual language switch behavior | **Always follow Accept-Language** | If user visits root after manually switching to /en/, redirect based on browser setting, not previous choice. |
| Regional variant support | **Full support** | Support /es-mx/, /es-es/, /en-gb/, /en-us/ paths when site provides them. |
| Regional fallback | **Try base, then default** | es-MX → try /es-mx/ → try /es/ → use default language. Cascading fallback. |
| Integration with REGINALD signing | Open | Language versions as separate COGs? (Deferred to signing spec) |

## Reference Implementation

The Salva demo has been updated to demonstrate the client-side fallback for this proposal:

**Location:** `packages/allaboutv2/mx/demo/salva/`

**Key files:**

| File | Purpose |
|------|---------|
| `/index.html` | Client-side Accept-Language redirect (fallback) |
| `/assets/script.js` | N-language aware JavaScript |
| `/assets/style.css` | Shared CSS for all languages |
| `/es/index.cog.html` | Spanish version |
| `/en/index.cog.html` | English version |

**Features implemented:**

- [x] Accept-Language header parsing (client-side)
- [x] Regional variant cascade (es-MX → es → default)
- [x] No cookies/localStorage (GDPR compliant)
- [x] N-language dropdown selector
- [x] Path-based routing (/es/, /en/)
- [x] hreflang tags for SEO

**Pending (REGINALD edge):**

- [ ] Cloudflare Worker redirect
- [ ] hreflang injection at edge
- [ ] REGINALD config file parsing

## Next Steps

1. Review and approve this proposal
2. Create REGINALD site config schema
3. Implement Cloudflare Worker MVP
4. Test with Salva demo (client-side reference available)
5. Document in cogify-this.cog.md ✅ (completed)
6. Deploy to REGINALD staging

---

*Proposal generated from n-language template interview session 2026-02-22*
