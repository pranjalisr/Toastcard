# 🍞 Toastcard

A beautifully animated, stateful **Toast notification component** built with **Next.js 15**, **Framer Motion**, and **Tailwind CSS**. Inspired by linear-style save confirmations — pill-shaped, dark, and silky smooth.

---

## ✨ Demo

The Toast cycles through three states with spring-animated transitions:

| State | Appearance | Behaviour |
|---|---|---|
| `initial` | ℹ️ "Unsaved changes" + Reset & Save buttons | Default state — prompts user to act |
| `loading` | 🔄 iOS spinner + "Saving…" | Shown while async save is in progress |
| `success` | ✅ "Changes Saved" | Confirmed — auto-resets after 2s |

```
[ℹ️ Unsaved changes]  [Reset]  [Save]
         ↓ (Save clicked)
[🔄 Saving…]
         ↓ (1.5s later)
[✅ Changes Saved]
         ↓ (2s later, auto-resets)
[ℹ️ Unsaved changes]  [Reset]  [Save]
```

---

## 🗂️ Project Structure

```
Toastcard/
├── app/                    # Next.js App Router entry
├── components/             # Shared UI components (shadcn/ui)
├── hooks/                  # Custom React hooks
├── lib/                    # Utility helpers (cn, etc.)
├── public/                 # Static assets
├── styles/                 # Global CSS
│
├── toast.tsx               # ⭐ Core Toast component
├── spinner.tsx             # iOS-style blade spinner
├── spinner.css             # Spinner keyframe animation
├── demo.tsx                # Interactive demo page
│
├── components.json         # shadcn/ui config
├── tailwind.config.ts      # Tailwind configuration
├── next.config.mjs         # Next.js configuration
├── tsconfig.json           # TypeScript config
└── package.json            # Dependencies & scripts
```

---

## 🧱 Tech Stack

| Layer | Library / Tool | Version |
|---|---|---|
| Framework | Next.js | 15.2.4 |
| UI Library | React | 19 |
| Language | TypeScript | ^5 |
| Styling | Tailwind CSS | ^3.4 |
| Animations | Framer Motion | latest |
| Component Primitives | Radix UI | (full suite) |
| Component System | shadcn/ui | via `components.json` |
| Icons | Lucide React | ^0.454 |
| Font | Geist | ^1.3.1 |
| Toasts (alt) | Sonner | ^1.7.1 |
| Package Manager | pnpm / npm | — |

---

## ⚙️ How It Works

### `toast.tsx` — The Core Component

The `<Toast>` component accepts a single `state` prop and two optional callbacks:

```tsx
interface ToastProps {
  state: 'initial' | 'loading' | 'success'
  onReset?: () => void
  onSave?: () => void
}
```

**Three rendered states:**

**`initial`** — Shows an inline SVG info icon, the label "Unsaved changes", and two action buttons:
- **Reset** — ghost button, subtle `hover:bg-white/[0.08]` hover
- **Save** — purple gradient pill (`from-[#7c5aff] to-[#6c47ff]`), with hover/active colour shifts

**`loading`** — Replaces the content with an `<IOSpinner />` and the label "Saving"

**`success`** — Shows a white-bordered check icon pill and "Changes Saved"

**Animation architecture:**

The pill container uses a Framer Motion `motion.div` with a spring-driven `width: 'auto'` animation — the whole pill smoothly grows and shrinks as content changes.

State transitions use `AnimatePresence` with `mode="wait"`, fading content in/out at zero duration to prevent blending, while the width springs fluidly.

The Reset/Save button group has its own `AnimatePresence` block — it slides in from `width: 0` with `opacity: 0` when entering the `initial` state, and collapses back out otherwise.

```tsx
const springConfig = {
  type: "spring",
  stiffness: 500,
  damping: 30,
  mass: 1
}
```

**Pill styling** — a single `commonClasses` string captures the dark glass look:
- Background: `bg-[#131316]`
- Six layered box shadows (depth, inner highlight, top highlight)
- `rounded-[99px]` for a perfect pill shape
- `inset` shadow for a subtle 1px inner glow and top highlight

---

### `spinner.tsx` — iOS-style Blade Spinner

```tsx
export function IOSpinner() {
  return (
    <div className="ispinner">
      {/* 12 blades */}
      <div className="ispinner-blade"></div>
      ...
    </div>
  )
}
```

The `<IOSpinner />` renders 12 blade `div`s in a circular layout, each animated via CSS keyframes in `spinner.css` with staggered `animation-delay` values — mimicking the classic iOS activity indicator.

---

### `demo.tsx` — Interactive Demo

The demo wires up state management and shows the full save-flow lifecycle:

```tsx
const [state, setState] = useState<'initial' | 'loading' | 'success'>('initial')

const handleSave = () => {
  setState('loading')
  setTimeout(() => {
    setState('success')
    setTimeout(() => setState('initial'), 2000)
  }, 1500)
}
```

It renders on a full-screen dark (`bg-[#09090B]`) background with the Toast centred — perfect for showcasing the component in isolation.

---

## 🚀 Getting Started

### Prerequisites

- Node.js 18+
- pnpm (recommended) or npm

### Installation

```bash
# Clone the repo
git clone https://github.com/pranjalisr/Toastcard.git
cd Toastcard

# Install dependencies (pnpm recommended — pnpm-lock.yaml is present)
pnpm install
# or
npm install
```

### Development

```bash
pnpm dev
# or
npm run dev
```

Open [http://localhost:3000](http://localhost:3000) to see the demo.

### Build for Production

```bash
pnpm build
pnpm start
```

---

## 🧩 Using the Toast Component

Copy `toast.tsx`, `spinner.tsx`, and `spinner.css` into your own project, then wire up state:

```tsx
'use client'
import { useState } from 'react'
import Toast from '@/toast'

export default function MyPage() {
  const [toastState, setToastState] = useState<'initial' | 'loading' | 'success'>('initial')

  const handleSave = async () => {
    setToastState('loading')
    await saveData()             // your async save logic
    setToastState('success')
    setTimeout(() => setToastState('initial'), 2000)
  }

  const handleReset = () => {
    setToastState('initial')
  }

  return (
    <div className="fixed bottom-6 left-1/2 -translate-x-1/2">
      <Toast
        state={toastState}
        onSave={handleSave}
        onReset={handleReset}
      />
    </div>
  )
}
```

### Props

| Prop | Type | Default | Description |
|---|---|---|---|
| `state` | `'initial' \| 'loading' \| 'success'` | `'initial'` | Controls which state the toast renders |
| `onSave` | `() => void` | `undefined` | Called when the Save button is clicked |
| `onReset` | `() => void` | `undefined` | Called when the Reset button is clicked |

---

## 🎨 Customisation

### Colours

The Save button uses a purple gradient — update these Tailwind arbitrary values in `toast.tsx` to match your brand:

```tsx
// Save button — change these gradient colours
className="... bg-gradient-to-b from-[#7c5aff] to-[#6c47ff] ..."

// Hover state
hover:from-[#8f71ff] hover:to-[#7c5aff]

// Active state
active:from-[#6c47ff] active:to-[#5835ff]
```

### Animation Speed

Tune the spring in `springConfig`:

```tsx
const springConfig = {
  type: "spring",
  stiffness: 500,   // higher = snappier
  damping: 30,      // higher = less bounce
  mass: 1
}
```

### Labels

Change the text directly in `toast.tsx`:

```tsx
// initial state
"Unsaved changes"

// loading state
"Saving"

// success state
"Changes Saved"
```

### Positioning

The component renders as an `inline-flex` pill — wrap it in a `fixed` container to anchor it:

```tsx
// Bottom-center (common pattern)
<div className="fixed bottom-6 left-1/2 -translate-x-1/2 z-50">
  <Toast state={state} onSave={handleSave} onReset={handleReset} />
</div>
```

---

## 📦 Key Dependencies

```json
{
  "framer-motion": "latest",
  "lucide-react": "^0.454.0",
  "next": "15.2.4",
  "react": "^19",
  "tailwindcss": "^3.4.17",
  "geist": "^1.3.1"
}
```

The full Radix UI suite and shadcn/ui are also included for the broader component system in `components/`.

---

## 🗺️ Roadmap

- [ ] Configurable auto-dismiss timeout via props
- [ ] Support for `error` state (red variant)
- [ ] Configurable position (top/bottom, left/right/center)
- [ ] Custom icon slot
- [ ] Accessibility: ARIA live region for screen readers
- [ ] npm package export


---

## 📄 License

Open-source under the [MIT License](LICENSE).
