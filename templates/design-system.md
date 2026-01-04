# Design System

Baseline patterns for consistent UI. Designer proposes DS-XXX refinements when these don't fit.

---

## Typography

| Element | Classes | Usage |
|---------|---------|-------|
| Page title | `text-2xl font-semibold` | Main heading per page |
| Section title | `text-lg font-medium` | Card headers, sections |
| Body | `text-sm` | Primary content |
| Caption | `text-xs text-muted-foreground` | Secondary info, timestamps |
| Code | `font-mono text-sm` | Inline code |

---

## Spacing

| Context | Value | Example |
|---------|-------|---------|
| Page padding | `p-6` | Main content area |
| Card padding | `p-4` | Inside cards |
| Section gap | `gap-6` | Between major sections |
| Element gap | `gap-4` | Between form fields |
| Tight gap | `gap-2` | Label to input |
| Inline spacing | `gap-1` | Icon to text |

---

## Layout Patterns

### Page Layout
Standard page structure:
- Max width container with padding
- Header with breadcrumb/title
- Main content area

```tsx
<div className="max-w-6xl mx-auto p-6">
  <header className="mb-6">
    <h1 className="text-2xl font-semibold">Page Title</h1>
  </header>
  <main>
    {/* content */}
  </main>
</div>
```

### Card Grid
Responsive grid for card collections:

```tsx
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
  {items.map(item => <Card key={item.id} />)}
</div>
```

### Data Table
For tabular data:
- Use shadcn Table component
- Sortable columns where applicable
- Pagination for large datasets

### Stack Layout
Vertical list of items:

```tsx
<div className="flex flex-col gap-4">
  {items.map(item => <Item key={item.id} />)}
</div>
```

---

## Component Patterns

### Forms
- Label above input
- Error message below input
- Submit button right-aligned (or full width on mobile)
- Use shadcn Form with react-hook-form + zod

```tsx
<FormField
  control={form.control}
  name="email"
  render={({ field }) => (
    <FormItem>
      <FormLabel>Email</FormLabel>
      <FormControl>
        <Input {...field} />
      </FormControl>
      <FormMessage />
    </FormItem>
  )}
/>
```

### Buttons
| Variant | Usage | Component |
|---------|-------|-----------|
| Primary | Main action | `<Button>` |
| Secondary | Secondary action | `<Button variant="secondary">` |
| Destructive | Delete, remove | `<Button variant="destructive">` |
| Ghost | Tertiary, icon-only | `<Button variant="ghost">` |
| Link | Navigation | `<Button variant="link">` |

### Inputs
- Use shadcn Input component
- Include placeholder text
- Show validation state

### Dialogs
- Use shadcn Dialog component
- Title + description in header
- Actions in footer (Cancel left, Primary right)

### Dropdowns
- Use shadcn DropdownMenu for actions
- Use shadcn Select for form inputs
- Use shadcn Combobox for searchable selection

---

## State Patterns

### Loading States
- Skeleton for content areas: `<Skeleton className="h-4 w-[200px]" />`
- Spinner for buttons: `<Loader2 className="animate-spin" />`
- Progress for operations: `<Progress value={percent} />`

### Empty States
Centered message with optional CTA:

```tsx
<Card className="p-12 text-center">
  <div className="mx-auto w-12 h-12 text-muted-foreground mb-4">
    <InboxIcon />
  </div>
  <h3 className="text-lg font-medium mb-2">No items yet</h3>
  <p className="text-muted-foreground mb-4">Get started by creating your first item.</p>
  <Button>Create Item</Button>
</Card>
```

### Error States
| Type | Pattern |
|------|---------|
| Form errors | Inline below field (FormMessage) |
| Toast errors | `toast.error("Something went wrong")` |
| Page errors | Full page with retry button |

### Success States
- Toast for confirmations
- Redirect after create/update
- Inline success message where appropriate

---

## Scrolling

### Page Scrolling
- Main content scrolls, header fixed (if applicable)
- Smooth scroll for anchor links

### Container Scrolling
For scrollable areas within the page:

```tsx
<div className="h-[400px] overflow-y-auto">
  {/* scrollable content */}
</div>
```

---

## Interactive States

| State | Style |
|-------|-------|
| Hover | `hover:bg-muted` or component default |
| Focus | Visible focus ring (shadcn default) |
| Active | `active:scale-95` for buttons |
| Disabled | `opacity-50 cursor-not-allowed` |
| Selected | `bg-primary text-primary-foreground` |

---

## Accessibility

Required for all UI:

| Requirement | Implementation |
|-------------|----------------|
| Focus visible | Use shadcn defaults (ring on focus) |
| Color contrast | WCAG AA (4.5:1 text, 3:1 UI) |
| Keyboard navigation | All interactive elements focusable |
| Screen readers | Meaningful alt text, ARIA labels |
| Form labels | Every input has associated label |
| Error announcements | aria-live for dynamic errors |

---

## Motion & Transitions

| Element | Transition |
|---------|------------|
| Hover states | `transition-colors` (150ms) |
| Dialogs | Fade + scale (shadcn default) |
| Dropdowns | Fade (shadcn default) |
| Loading | `animate-pulse` for skeletons |
| Spinners | `animate-spin` |

---

## Dark Mode

- Use CSS variables from shadcn theme
- All colors via `text-foreground`, `bg-background`, etc.
- Never hardcode hex colors
- Test both modes for contrast
