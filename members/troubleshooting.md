---
author: "Tom Cranstoun"
created: "2026-01-24"
description: "Common issues and solutions for MX implementations"
purpose: "member-support"
tags: ['machine-experience', 'members', 'resources', 'support', 'mx-gathering']
---

# Troubleshooting Guide

Common issues and solutions for implementing Machine Experience (MX) patterns.

## Quick Diagnosis

**Symptom:** [Describe your issue]
**Check:** [Relevant section below]

## Common Issues

### llms.txt Problems

#### Issue: AI agents not finding llms.txt

**Symptoms:**

- File exists but agents report "not found"
- 404 errors in server logs

**Causes:**

- File not at site root
- Server not serving .txt files
- Incorrect file permissions

**Solutions:**

1. Verify file location: `https://yoursite.com/llms.txt`
2. Check server configuration:

```nginx
# Nginx example
location ~ \.txt$ {
    types { text/plain txt; }
}
```

1. Check file permissions: `chmod 644 llms.txt`

#### Issue: YAML frontmatter not parsing

**Symptoms:**

- Frontmatter shown as plain text
- Missing metadata in agent reads

**Causes:**

- YAML syntax errors
- Missing opening/closing delimiters
- Indentation problems

**Solutions:**

1. Verify YAML format:

```yaml
---
title: "Project Name"
author: "Name"
---
```

1. Use YAML validator: <https://www.yamllint.com/>
2. Check for tabs (use spaces only)

### Schema.org Issues

#### Issue: Schema validation errors

**Symptoms:**

- Google Rich Results Test shows errors
- Schema.org validator fails

**Causes:**

- Required properties missing
- Incorrect property types
- Malformed JSON-LD

**Solutions:**

1. Validate JSON-LD syntax
2. Check required properties for your type
3. Use Schema.org validator: <https://validator.schema.org/>

Example fix:

```html
<!-- ❌ Missing required properties -->
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Organization"
}
</script>

<!-- ✅ Includes required properties -->
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Organization",
  "name": "Company Name",
  "url": "https://example.com"
}
</script>
```

### Accessibility Problems

#### Issue: Screen reader not announcing properly

**Symptoms:**

- Content skipped or misread
- Navigation confusing
- Interactive elements not announced

**Causes:**

- Missing ARIA labels
- Incorrect heading hierarchy
- Div soup (non-semantic HTML)

**Solutions:**

1. Use semantic HTML elements
2. Add ARIA labels where needed
3. Fix heading hierarchy (H1 → H2 → H3)

Example:

```html
<!-- ❌ Non-semantic -->
<div class="button" onclick="submit()">Submit</div>

<!-- ✅ Semantic with proper attributes -->
<button type="submit">Submit</button>
```

### Web Audit Suite Issues

#### Issue: Tool failing to scan site

**Symptoms:**

- Scan stops prematurely
- Timeout errors
- Connection refused

**Causes:**

- Rate limiting triggered
- robots.txt blocking
- Network connectivity issues

**Solutions:**

1. Reduce concurrency: `npm run audit:start -- -c 3`
2. Check robots.txt allows scanning
3. Add delays between requests
4. Verify network connectivity

#### Issue: False positives in reports

**Symptoms:**

- Valid patterns flagged as errors
- Incorrect accessibility warnings

**Causes:**

- Tool configuration issues
- Edge cases not handled
- Outdated rulesets

**Solutions:**

1. Review specific warning details
2. Verify against WCAG 2.1 guidelines
3. Report false positives to community
4. Update Web Audit Suite to latest version

### Implementation Challenges

#### Issue: AI agents not understanding content

**Symptoms:**

- Agents misinterpreting purpose
- Missing key information
- Incorrect summaries

**Causes:**

- Missing metadata
- Unclear content structure
- No llms.txt guidance

**Solutions:**

1. Add comprehensive llms.txt
2. Use clear headings and structure
3. Include Schema.org markup
4. Provide context in meta descriptions

#### Issue: Changes breaking existing functionality

**Symptoms:**

- Features stop working after updates
- Visual design problems
- User complaints

**Causes:**

- Over-reliance on CSS for functionality
- JavaScript conflicts
- Accessibility improvements changing behavior

**Solutions:**

1. Test changes in staging environment
2. Use progressive enhancement
3. Maintain semantic HTML base
4. Test with multiple browsers and devices

## Browser Compatibility

### Issue: Patterns work in some browsers, not others

**Check:**

- HTML5 element support
- CSS Grid/Flexbox compatibility
- JavaScript API availability

**Solution:**

1. Use feature detection
2. Implement progressive enhancement
3. Provide fallbacks
4. Test across browsers

## Performance Issues

### Issue: Site slow after implementing patterns

**Symptoms:**

- Increased load times
- High server load
- Poor Core Web Vitals

**Causes:**

- Too much inline Schema.org data
- Unoptimized images
- Blocking resources

**Solutions:**

1. Externalize large structured data
2. Optimize images
3. Defer non-critical scripts
4. Use performance monitoring tools

## Getting More Help

### Before Asking for Help

1. Check this troubleshooting guide
2. Search GitHub Issues
3. Review relevant documentation
4. Test with minimal example

### How to Ask

Include:

- Clear problem description
- Steps to reproduce
- Expected vs actual behavior
- Environment details (browser, OS, versions)
- Code examples or screenshots

### Where to Ask

- **GitHub Discussions:** General questions
- **GitHub Issues:** Bugs and feature requests
- **Community Events:** Live troubleshooting
- **Email:** <tom.cranstoun@gmail.com>

## Contributing

Found a solution not listed here?

1. Test your solution thoroughly
2. Document steps clearly
3. Submit pull request adding to this guide

---

Still stuck? We're here to help!
