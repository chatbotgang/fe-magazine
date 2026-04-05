# Magazine Design Reference

Reference document for `/magazine-edit`. Contains design standards, validation scripts, and patterns learned from session retrospectives.

## WCAG Color Contrast Verification

### Requirements

| Level | Normal text (<18pt) | Large text (≥18pt or ≥14pt bold) |
|-------|--------------------|---------------------------------|
| AA (minimum) | 4.5:1 | 3.0:1 |

### Python Verification Script

Run this before finalizing any color pair on dark backgrounds:

```python
import sys
def lum(h):
    h=h.lstrip('#'); r,g,b=[int(h[i:i+2],16)/255 for i in(0,2,4)]
    f=lambda c:c/12.92 if c<=0.04045 else((c+.055)/1.055)**2.4
    return 0.2126*f(r)+0.7152*f(g)+0.0722*f(b)
def contrast(a,b):
    l1,l2=lum(a),lum(b); mx,mn=max(l1,l2),min(l1,l2)
    return round((mx+.05)/(mn+.05),2)
# Usage: python3 -c "..." '#ffffff' '#0f0d2e'
fg,bg=sys.argv[1],sys.argv[2]
r=contrast(fg,bg)
print(f'{fg} on {bg}: {r}:1 {"PASS" if r>=4.5 else "FAIL (need 4.5:1)"}')
```

### Known Borderline Colors (large text ONLY)

- `#e63946` on `#faf9f6` → 3.96:1 (large text only)
- `#2ea043` on `#f6f8fa` → 3.17:1 (large text only)
- `#7c3aed` on `#0d1b2a` → 3.05:1 (large text only)

## Whitespace Management

### Dense Pages — Bottom Whitespace

| Whitespace | Strategy |
|------------|----------|
| < 15mm | Acceptable. Use `<div style="flex:1"></div>` spacer if desired |
| 15–35mm | Add decorative rule or ornament |
| > 35mm | Content problem — add more text, increase font size, or merge pages |

### Flex Column Pattern (recommended for all dense pages)

```css
.page.dense {
  display: flex;
  flex-direction: column;
  padding: 14mm 18mm;
}
/* Content grids should NOT stretch cards */
.grid-2, .grid-3 {
  align-content: start; /* prevents card stretching */
}
```

### Overflow Detection Script

Add to HTML `<script>` during development — shows red overlay on overflowing pages:

```javascript
if (window.matchMedia('not print').matches) {
  document.querySelectorAll('.page').forEach((p, i) => {
    if (p.scrollHeight > p.clientHeight + 2) {
      p.style.outline = '3px solid red';
      const l = document.createElement('div');
      l.style.cssText = 'position:absolute;top:0;left:0;background:red;color:white;font:9px monospace;padding:2px 4px;z-index:9999';
      l.textContent = `OVERFLOW +${p.scrollHeight - p.clientHeight}px (p${i+1})`;
      p.style.position = 'relative'; p.appendChild(l);
    }
  });
}
```

## CSS Selector Gotchas

### Same-Element vs Descendant Selectors

```css
/* WRONG: descendant selector — won't match <div class="page s-eco dense"> */
.s-eco .dense { padding: 14mm 18mm; }

/* RIGHT: same-element compound selector */
.s-eco.dense { padding: 14mm 18mm; }
```

Always verify CSS selectors match the actual HTML class structure.

## Verification Workflow

### Pre-Flight Checklist (before declaring "done")

1. **Export PDF** and read every page via `Read` tool — browser preview lies
2. **WCAG contrast**: run Python script on all text-on-dark-background color pairs
3. **Overflow**: check no content is clipped at page bottom edges
4. **Whitespace**: no page has > 35mm unused bottom space
5. **Selector audit**: grep for descendant selectors that should be compound (e.g., `.topic .dense` → `.topic.dense`)
6. **Font loading**: verify custom fonts render (not fallback to system fonts)
7. **Within-topic consistency**: pages belonging to the same topic use a coherent visual language
8. **Names**: all person names verified from Slack/GitHub, never fabricated
