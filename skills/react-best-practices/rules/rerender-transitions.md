---
title: Use Transitions for Non-Urgent Updates
impact: MEDIUM
impactDescription: maintains UI responsiveness
tags: rerender, transitions, startTransition, performance
---

## Use Transitions for Non-Urgent Updates

Mark non-urgent state updates that trigger expensive renders as transitions, so urgent updates (typing, clicks) can interrupt them and the UI stays responsive.

**Incorrect (expensive tab render blocks further interaction):**

```tsx
function Tabs() {
  const [tab, setTab] = useState('overview')

  return (
    <>
      <TabBar selected={tab} onSelect={setTab} />
      <TabPanel tab={tab} /> {/* slow to render */}
    </>
  )
}
```

**Correct (tab render is interruptible):**

```tsx
import { startTransition, useState } from 'react'

function Tabs() {
  const [tab, setTab] = useState('overview')

  const selectTab = (next: string) => {
    startTransition(() => setTab(next))
  }

  return (
    <>
      <TabBar selected={tab} onSelect={selectTab} />
      <TabPanel tab={tab} /> {/* slow to render */}
    </>
  )
}
```

A transition still re-renders; it only lowers the priority. For high-frequency values that don't need to drive rendering (scroll position, pointer coordinates), use a ref instead (see [Use useRef for Transient Values](./rerender-use-ref-transient-values.md)).
