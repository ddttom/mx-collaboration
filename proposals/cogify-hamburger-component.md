---
title: "Cogify Hamburger Component Proposal"
description: "Add mobile hamburger menu and componentize hero/nav for cogify templates and Salva demo"
author: Tom Cranstoun
created: 2026-02-22
modified: 2026-02-22
status: Ready for Implementation
version: "1.0"
---

# Cogify Hamburger Component Proposal

**Author:** Tom Cranstoun
**Status:** Ready for Implementation
**Date:** 2026-02-22

## Background

Interview session identified improvements needed for the cogify workflow and Salva demo:

1. **Mobile hamburger menu** — Salva demo lacks proper mobile navigation
2. **Component extraction** — Hero and nav sections need componentization for reusability
3. **EDS alignment** — allaboutv2 has existing header/hero blocks that should inform standalone cog patterns

## Problem

Current Salva demo (`packages/allaboutv2/mx/demo/salva/`) has:

- Inline styles and scripts (not componentized)
- No mobile hamburger menu
- Hero and nav tightly coupled to page structure
- Different pattern from EDS blocks in allaboutv2

Cogify templates (`hub-content/mx-reference-implementations/_templates/`) similarly lack:

- Mobile-friendly navigation
- Reusable component patterns
- Guidance on EDS vs standalone approaches

## Proposed Solution

### Dual Approach Strategy

| Context | Approach |
|---------|----------|
| **EDS sites** (allaboutv2) | Use existing `blocks/header/` and `blocks/hero/` with `decorate()` pattern |
| **Standalone cogs** (Salva demo, cogify templates) | Self-contained hamburger/nav patterns adapted from EDS |

### Hamburger Menu Specification

**Behavior:** Slide-in drawer from right
**Breakpoint:** 900px (match EDS header.js)
**Features:**

- Hamburger icon visible on mobile
- Drawer slides in with smooth animation (300ms)
- Body scroll lock when open
- Escape key closes drawer
- Aria-expanded accessibility
- Works with n-language selector (not limited to 2 languages)
- Overlay click closes drawer

### Component Structure (Standalone Cogs)

```html
<!-- Nav container with hamburger -->
<nav id="nav" aria-expanded="false">
  <div class="nav-hamburger">
    <button type="button" aria-controls="nav" aria-label="Open navigation">
      <span class="nav-hamburger-icon"></span>
    </button>
  </div>
  <div class="nav-sections">
    <!-- Navigation items -->
  </div>
</nav>
```

```javascript
// Adapted from EDS header.js
const isDesktop = window.matchMedia('(min-width: 900px)');

function toggleMenu(nav, navSections, forceExpanded = null) {
  const expanded = forceExpanded !== null ? !forceExpanded : nav.getAttribute('aria-expanded') === 'true';
  const button = nav.querySelector('.nav-hamburger button');
  document.body.style.overflowY = (expanded || isDesktop.matches) ? '' : 'hidden';
  nav.setAttribute('aria-expanded', expanded ? 'false' : 'true');
  button.setAttribute('aria-label', expanded ? 'Open navigation' : 'Close navigation');
}

function closeOnEscape(e) {
  if (e.code === 'Escape') {
    const nav = document.getElementById('nav');
    const navSections = nav.querySelector('.nav-sections');
    toggleMenu(nav, navSections);
    nav.querySelector('button').focus();
  }
}
```

### CSS (Slide-in Drawer)

```css
/* Mobile hamburger */
.nav-hamburger {
  display: none;
}

@media (max-width: 899px) {
  .nav-hamburger {
    display: block;
    position: fixed;
    top: 1rem;
    right: 1rem;
    z-index: 1000;
  }

  .nav-sections {
    position: fixed;
    top: 0;
    right: -100%;
    width: 80%;
    max-width: 300px;
    height: 100vh;
    background: var(--background-color);
    transition: right 0.3s ease;
    z-index: 999;
    padding: 4rem 1rem 1rem;
    overflow-y: auto;
  }

  nav[aria-expanded="true"] .nav-sections {
    right: 0;
  }

  /* Overlay */
  nav[aria-expanded="true"]::before {
    content: '';
    position: fixed;
    inset: 0;
    background: rgba(0, 0, 0, 0.5);
    z-index: 998;
  }
}
```

## Files to Modify

| File | Changes |
|------|---------|
| `packages/allaboutv2/mx/demo/salva/es/index.cog.html` | Add hamburger component |
| `packages/allaboutv2/mx/demo/salva/en/index.cog.html` | Add hamburger component |
| `packages/allaboutv2/mx/demo/salva/es/index.cog.css` | Add drawer styles |
| `packages/allaboutv2/mx/demo/salva/en/index.cog.css` | Add drawer styles |
| `hub-content/mx-reference-implementations/_templates/single-language-business-template.cog.html` | Add hamburger pattern |
| `hub-content/mx-canon/MX-Cog-Registry/cogs/cogify-this.cog.md` | Document EDS vs standalone approach |

## Constraints

- **No JS frameworks** — Vanilla JavaScript only
- **Preserve visual design** — Existing styles unchanged
- **Update templates** — Changes propagate to cogify templates
- **N-language support** — Language selector supports 2+ languages, not binary toggle
- **EDS alignment** — Patterns adapted from allaboutv2/blocks/header/

## Risks and Downsides

1. **Maintenance overhead** — Two patterns (EDS blocks vs standalone cogs) to maintain
2. **Drift risk** — Standalone patterns may diverge from EDS over time
3. **Testing complexity** — Must test mobile nav with language toggle

**Mitigations:**

- Document the relationship between EDS blocks and cog patterns
- Consider extracting shared utilities when patterns stabilize
- Add mobile + bilingual test cases

## Alternatives Considered

1. **Convert Salva to use EDS blocks directly** — Rejected because Salva demo needs to work as standalone `.cog.html` without EDS infrastructure
2. **Web Components** — Rejected due to complexity for standalone cogs and desire for vanilla JS
3. **Separate nav file includes** — Rejected to keep cogs self-contained

## Open Questions — RESOLVED

| Question | Decision | Rationale |
|----------|----------|-----------|
| 1. Hamburger icon style | **CSS-only** | Match EDS pattern — 3 lines via ::before/::after |
| 2. Drawer slide direction | **Right** | Drawer slides from same side as hamburger button |
| 3. Language toggle placement | **In drawer (mobile), menu (desktop)** | Responsive placement |
| 4. Shared utility file | **No — inline per file** | Keep cogs self-contained |
| 5. Overlay click behavior | **Yes — closes drawer** | Standard mobile UX |
| 6. N-language support | **Required** | Language selector must support 2+ languages, not binary toggle |

## Success Criteria

- [ ] Hamburger icon visible on mobile (< 900px), CSS-only
- [ ] Drawer slides in from **right** smoothly (300ms)
- [ ] Escape key closes drawer
- [ ] **Overlay click** closes drawer
- [ ] Body scroll locked when drawer open
- [ ] Language selector in drawer (mobile), menu (desktop)
- [ ] **N-language support** (not limited to 2)
- [ ] WCAG 2.1 AA accessibility maintained
- [ ] Salva demo updated (es + en versions)
- [ ] Cogify templates updated (parallel implementation)
- [ ] cogify-this.cog.md documents dual approach

## Next Steps

1. Review and approve this proposal
2. Enter plan mode for detailed implementation
3. Implement on Salva demo first
4. Propagate to cogify templates
5. Update documentation

---

*Proposal generated from interview session 2026-02-22*
*Open questions resolved via follow-up interview 2026-02-22*
