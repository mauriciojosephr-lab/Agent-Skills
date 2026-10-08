---
title: Defer Non-Critical Third-Party Libraries
impact: MEDIUM
impactDescription: loads after hydration
tags: bundle, third-party, analytics, defer
---

## Defer Non-Critical Third-Party Libraries

Analytics, logging, and error tracking don't block user interaction. Load them after hydration.

**Incorrect (blocks initial bundle):**

```tsx
import { Analytics } from '@/lib/analytics'

export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        {children}
        <Analytics />
      </body>
    </html>
  )
}
```

**Correct (loads after hydration):**

```tsx
// app/deferred-analytics.tsx
'use client'

import dynamic from 'next/dynamic'

const Analytics = dynamic(
  () => import('@/lib/analytics').then(m => m.Analytics),
  { ssr: false }
)

export function DeferredAnalytics() {
  return <Analytics />
}

// app/layout.tsx
import { DeferredAnalytics } from './deferred-analytics'

export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        {children}
        <DeferredAnalytics />
      </body>
    </html>
  )
}
```

In the App Router, `ssr: false` is only allowed inside a Client Component, so the dynamic import lives in a small `'use client'` wrapper rather than in the layout itself.
