# WJH Floor Guide - UI Improvement Recommendations

## Priority 1: Critical Accessibility & Usability Issues

### 1.1 Focus Indicators Missing
**Issue**: Interactive elements lack visible focus states for keyboard navigation
**Location**: Buttons, dropdowns, accordion triggers
**Fix**: 
```css
button:focus-visible,
.dd:focus-visible,
.ahd:focus-visible {
    outline: 3px solid #0b57d0;
    outline-offset: 2px;
}
```
**Impact**: WCAG 2.1 compliance, keyboard users can't see where they are

### 1.2 Color Contrast Issues
**Issue**: Several text/background combinations fail WCAG AA standards
- `.ddl` (gray text #4a5263 on navy background) - only 3.5:1 ratio
- `.chip` backgrounds with smaller text
- `.hsrc` link color may not meet contrast requirements

**Fix**:
```css
.ddl { color: #cfdcf0; } /* Increase contrast on navy background */
.chip { font-size: 15px; } /* Increase from 14px or darken color */
```

### 1.3 Missing ARIA Labels
**Issue**: Many interactive elements lack proper ARIA labels
- Dropdown buttons don't have `aria-label` describing what they filter
- Accordion sections missing `aria-expanded` states
- Icon-only buttons (#srBtn) need labels

**Fix**: Add to HTML:
```html
<button class="dd" aria-label="Filter by year" aria-expanded="false">
<button id="srBtn" aria-label="Open search">
```

## Priority 2: Mobile & Responsive Improvements

### 2.1 Touch Target Sizes
**Issue**: Some buttons are below the 44x44px minimum touch target
- `.qlrow button` at 36px height
- `.chip` elements at ~28px height when interactive

**Fix**:
```css
.qlrow button {
    min-height: 44px; /* Increase from 36px */
}
```

### 2.2 Horizontal Scroll Indicators
**Issue**: `.qlrow` has horizontal scrolling but no visual indicator
**Fix**:
```css
.qlrow::after {
    content: '→';
    position: absolute;
    right: 0;
    background: linear-gradient(to right, transparent, #002C5F);
    padding: 0 8px;
}
.qlrow::-webkit-scrollbar {
    height: 2px; /* Show thin scrollbar */
}
.qlrow::-webkit-scrollbar-thumb {
    background: rgba(255,255,255,0.3);
    border-radius: 10px;
}
```

### 2.3 Sticky Header Overlap
**Issue**: `#bar` sticky header may cover content when scrolling to anchors
**Fix**:
```css
:target {
    scroll-margin-top: 120px; /* Height of sticky headers */
}
```

## Priority 3: Visual Hierarchy & Consistency

### 3.1 Inconsistent Spacing
**Issue**: Card margins vary (12px, 8px, 0 depending on context)
**Fix**: Establish consistent spacing scale:
```css
:root {
    --space-xs: 4px;
    --space-sm: 8px;
    --space-md: 12px;
    --space-lg: 16px;
    --space-xl: 24px;
}
```

### 3.2 Button State Feedback
**Issue**: Buttons lack hover states, only have active/pressed states
**Fix**:
```css
button:hover:not(:disabled) {
    transform: translateY(-1px);
    box-shadow: 0 2px 8px rgba(0, 44, 95, 0.15);
    transition: all 0.2s cubic-bezier(0.22, 1, 0.36, 1);
}
.dd:hover:not([aria-expanded="true"]) {
    background: #f4f6f9;
}
```

### 3.3 Loading States Missing
**Issue**: No loading indicators when content is being fetched/filtered
**Fix**: Add loading skeleton:
```css
.loading {
    background: linear-gradient(90deg, #f4f6f9 25%, #ffffff 50%, #f4f6f9 75%);
    background-size: 200% 100%;
    animation: loading 1.5s ease-in-out infinite;
}
@keyframes loading {
    0% { background-position: 200% 0; }
    100% { background-position: -200% 0; }
}
```

## Priority 4: Enhanced User Experience

### 4.1 Dark Mode Support
**Issue**: Hardcoded `color-scheme: light only` prevents dark mode
**Recommendation**: Add dark mode support
```css
@media (prefers-color-scheme: dark) {
    :root {
        --navy: #4a90e2;
        --ink: #e8eaed;
        --bg: #1a1f29;
        --card: #2d3139;
    }
}
```

### 4.2 Smooth Scroll
**Issue**: No smooth scrolling between sections
**Fix**:
```css
html {
    scroll-behavior: smooth;
}
```

### 4.3 Empty States
**Issue**: No visual feedback when filters return no results
**Fix**: Add empty state messaging:
```css
.empty-state {
    text-align: center;
    padding: 48px 16px;
    color: #4a5263;
}
.empty-state svg {
    width: 64px;
    height: 64px;
    opacity: 0.3;
}
```

### 4.4 Transition Improvements
**Issue**: `.abody` accordion uses grid transition which can be janky
**Fix**:
```css
.abody {
    max-height: 0;
    overflow: hidden;
    transition: max-height 0.3s cubic-bezier(0.22, 1, 0.36, 1);
}
.acc.open .abody {
    max-height: 2000px; /* Adjust based on content */
}
```

### 4.5 Badge/Chip Improvements
**Issue**: `.chip` elements have inconsistent sizing and don't stack well on mobile
**Fix**:
```css
.chip {
    white-space: nowrap;
    display: inline-flex;
    align-items: center;
    gap: 4px;
    line-height: 1.2;
}
```

## Priority 5: Performance & Polish

### 5.1 Reduce Shadow Complexity
**Issue**: Multiple box-shadows stack causing paint performance issues
**Recommendation**: Use `will-change` sparingly on animated elements:
```css
.dd[aria-expanded="true"],
.ddp.on {
    will-change: transform, opacity;
}
```

### 5.2 Input States
**Issue**: Search input `#srQ` and `#invQ` lack focus states
**Fix**:
```css
#srQ:focus,
#invQ:focus,
.qcur input:focus {
    outline: none;
    border-color: #0b57d0;
    box-shadow: 0 0 0 3px rgba(11, 87, 208, 0.1);
}
```

### 5.3 Disabled State Consistency
**Issue**: `.qb:disabled` only has opacity, should also show cursor change
**Fix**:
```css
button:disabled {
    cursor: not-allowed;
    opacity: 0.4;
}
```

## Implementation Priority Order

1. **Immediate (Accessibility)**:
   - Focus indicators (1.1)
   - Color contrast (1.2)
   - ARIA labels (1.3)
   - Touch targets (2.1)

2. **Short-term (Usability)**:
   - Hover states (3.2)
   - Input focus states (5.2)
   - Smooth scroll (4.2)
   - Sticky header fix (2.3)

3. **Medium-term (Enhancement)**:
   - Loading states (3.3)
   - Empty states (4.3)
   - Scroll indicators (2.2)
   - Spacing consistency (3.1)

4. **Long-term (Nice-to-have)**:
   - Dark mode (4.1)
   - Transition improvements (4.4)
   - Performance optimization (5.1)

## Testing Recommendations

- Run Lighthouse accessibility audit
- Test with keyboard-only navigation
- Test with screen readers (NVDA, JAWS, VoiceOver)
- Test on devices with different screen sizes
- Test with reduced motion preferences
- Verify WCAG 2.1 AA compliance
