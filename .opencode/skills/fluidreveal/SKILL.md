---
name: fluidreveal
description: Apply the full FluidReveal animation system to any React + Framer Motion project.
version: "1.0.0"
author: muhidul
---

# FluidReveal — Animation System

> **OpenCode usage:** Run `/fluidreveal` or ask "add animations to this project".


# FluidReveal — Animation System

Applies a complete, production-grade animation system to any React + Framer Motion project. Every animation is GPU-composited, cleans up `willChange` after completion, and runs on scroll triggers — never on timers.

---

## Step 1 — Audit the Project

Before writing any code, read the target files:

1. Identify the **hero section** — does it have a headline, background image, subtext, CTAs?
2. Identify **content sections** — headings, body paragraphs, stat blocks, image+text cols
3. Identify **card grids** — repeating card/item patterns
4. Identify **standalone images** — featured images, mockups, screenshots
5. Check if `framer-motion` is already installed:
   ```bash
   grep -r "framer-motion" package.json
   ```
   If missing: `npm install framer-motion`

Map each element to the correct animation primitive before touching any code.

---

## Step 2 — Create `src/components/FluidReveal.jsx`

Create this file once per project. All animation primitives live here. Import from this file wherever needed.

```jsx
import { useRef, useState, Children } from 'react'
import { motion, useAnimation, useScroll, useTransform, useInView } from 'framer-motion'

// ─── Shared easing ───────────────────────────────────────────────────────────
// Expo out — fast start, soft landing. Use for all reveals.
export const ease = [0.22, 1, 0.36, 1]
// Smooth out — gentler, use for Ken Burns / parallax
export const easeSmooth = [0.16, 1, 0.3, 1]

// ─── 1. Reveal ───────────────────────────────────────────────────────────────
// Standard scroll-triggered reveal. Use on: headings, paragraphs, labels,
// metadata rows, stat rows, buttons, any block element.
// Wrap each element INDIVIDUALLY — never group multiple elements in one Reveal.
//
// Props:
//   delay    — seconds before animating (use for stagger, see Step 4)
//   distance — px to travel upward (default 36, hero entry can use 10-16)
//   className — forwarded to the motion.div wrapper
export function Reveal({ children, delay = 0, className = '', distance = 36 }) {
  const [done, setDone] = useState(false)
  return (
    <motion.div
      className={className}
      initial={{ opacity: 0, y: distance }}
      whileInView={{ opacity: 1, y: 0 }}
      viewport={{ once: true, margin: '-80px' }}
      transition={{ duration: 1.4, ease, delay }}
      style={{
        willChange: done ? 'auto' : 'transform, opacity',
        backfaceVisibility: 'hidden',
        WebkitBackfaceVisibility: 'hidden',
      }}
      onAnimationComplete={() => setDone(true)}
    >
      {children}
    </motion.div>
  )
}

// ─── 2. RevealImage ───────────────────────────────────────────────────────────
// Reveal with overflow-hidden built in — clips image during slide.
// Use on: any <img>, mockup, screenshot, illustration wrapper.
// The overflow-hidden creates a "wipe up" effect as the image rises.
export function RevealImage({ children, delay = 0, className = '' }) {
  const [done, setDone] = useState(false)
  return (
    <motion.div
      className={`overflow-hidden ${className}`}
      initial={{ opacity: 0, y: 36 }}
      whileInView={{ opacity: 1, y: 0 }}
      viewport={{ once: true, margin: '-80px' }}
      transition={{ duration: 1.4, ease, delay }}
      style={{
        willChange: done ? 'auto' : 'transform, opacity',
        backfaceVisibility: 'hidden',
        WebkitBackfaceVisibility: 'hidden',
      }}
      onAnimationComplete={() => setDone(true)}
    >
      {children}
    </motion.div>
  )
}

// ─── 3. RevealGrid / RevealGridItem ──────────────────────────────────────────
// Auto-staggers a list of children. Use on: card grids, feature lists,
// stat blocks, icon rows, team members, testimonials.
//
// Props (RevealGrid):
//   baseDelay — delay before first card
//   stagger   — delay between each card (default 0.1s)
//   className — the grid/flex classname (e.g. "grid grid-cols-3 gap-6")
function RevealGridItem({ children, delay }) {
  const [done, setDone] = useState(false)
  return (
    <motion.div
      className="h-full"
      initial={{ opacity: 0, y: 36 }}
      whileInView={{ opacity: 1, y: 0 }}
      viewport={{ once: true, margin: '-80px' }}
      transition={{ duration: 1.4, ease, delay }}
      style={{
        willChange: done ? 'auto' : 'transform, opacity',
        backfaceVisibility: 'hidden',
        WebkitBackfaceVisibility: 'hidden',
      }}
      onAnimationComplete={() => setDone(true)}
    >
      {children}
    </motion.div>
  )
}

export function RevealGrid({ children, className = '', baseDelay = 0, stagger = 0.1 }) {
  const items = Children.toArray(children)
  return (
    <div className={className}>
      {items.map((child, i) => (
        <RevealGridItem key={i} delay={baseDelay + i * stagger}>
          {child}
        </RevealGridItem>
      ))}
    </div>
  )
}

// ─── 4. SplitWords ───────────────────────────────────────────────────────────
// Per-word slide-up reveal for headlines. Each word clips behind its parent
// overflow-hidden and slides up individually, creating a cascade.
// Use on: hero h1, section display headings, large pull quotes.
// NOT for body copy — use Reveal there.
//
// Props:
//   text  — plain string (no JSX, words split on spaces)
//   as    — HTML tag: 'h1' | 'h2' | 'h3' | 'p' | 'span'
//   delay — base delay before first word
//   wordDelay — additional delay per word (default 0.08s)
export function SplitWords({ text, className = '', style = {}, delay = 0, wordDelay = 0.08, as: Tag = 'h2' }) {
  const ref = useRef(null)
  const inView = useInView(ref, { once: true, margin: '-5%' })
  const [done, setDone] = useState(false)
  const words = text.split(' ')
  return (
    <Tag ref={ref} className={className} style={style}>
      {words.map((word, i) => (
        <span key={i} className="inline-block overflow-hidden leading-[1.1]">
          <motion.span
            className="inline-block"
            initial={{ y: '105%', opacity: 0 }}
            animate={inView ? { y: '0%', opacity: 1 } : {}}
            transition={{ duration: 1.4, ease, delay: delay + i * wordDelay }}
            style={{
              willChange: done ? 'auto' : 'transform, opacity',
              backfaceVisibility: 'hidden',
              WebkitBackfaceVisibility: 'hidden',
            }}
            onAnimationComplete={i === words.length - 1 ? () => setDone(true) : undefined}
          >
            {word}
          </motion.span>
          {i < words.length - 1 ? ' ' : null}
        </span>
      ))}
    </Tag>
  )
}

// ─── 5. HeadlineWords ────────────────────────────────────────────────────────
// Like SplitWords but accepts pre-split line groups (arrays of arrays).
// Use when you need manual line breaks in the headline.
// Each word uses its flat index for consistent stagger across lines.
//
// Props:
//   lineGroups — [['Word', 'Word'], ['Word', 'Word']] (array of lines)
//   accentWords — Set of words to italicize (optional)
export function HeadlineWords({ lineGroups, className = '', style = {}, accentWords = new Set(), delay = 0.2, wordDelay = 0.12 }) {
  const allWords = lineGroups.flat()
  return (
    <h1 className={className} style={{ fontWeight: 400, margin: 0, ...style }}>
      {lineGroups.map((lineWords, li) => (
        <span key={li} className="block overflow-hidden" style={{ transform: 'translateZ(0)' }}>
          {lineWords.map((word) => {
            const i = allWords.indexOf(word)
            return (
              <motion.span
                key={word}
                className="inline-block mr-[0.2em] last:mr-0"
                style={{
                  ...(accentWords.has(word) ? { fontStyle: 'italic' } : {}),
                  willChange: 'transform, opacity',
                  backfaceVisibility: 'hidden',
                  WebkitBackfaceVisibility: 'hidden',
                }}
                initial={{ opacity: 0, y: '110%' }}
                animate={{ opacity: 1, y: '0%' }}
                transition={{ duration: 1.6, ease, delay: delay + i * wordDelay }}
              >
                {word}
              </motion.span>
            )
          })}
        </span>
      ))}
    </h1>
  )
}

// ─── 6. KenBurns ─────────────────────────────────────────────────────────────
// Slow zoom-out on image load. Use on: hero background images, featured
// full-bleed images. Combines with parallax for depth.
//
// Props:
//   src, alt — image attributes
//   className — forwarded to the <img>
//   scale    — initial scale (default 1.18 desktop / 1.12 mobile)
//   duration — animation duration seconds (default 5.5)
//   onLoad   — called when image loads (use to trigger parent reveal)
export function KenBurns({ src, alt = '', className = '', style = {}, scale = 1.18, duration = 5.5, onLoad }) {
  const controls = useAnimation()
  const hasStarted = useRef(false)

  const handleLoad = () => {
    onLoad?.()
    if (!hasStarted.current) {
      hasStarted.current = true
      controls.start({ opacity: 1, scale: 1, transition: { duration, ease: easeSmooth } })
    }
  }

  return (
    <motion.img
      src={src}
      alt={alt}
      className={className}
      style={{ willChange: 'transform, opacity', backfaceVisibility: 'hidden', WebkitBackfaceVisibility: 'hidden', ...style }}
      initial={{ opacity: 0, scale }}
      animate={controls}
      onLoad={handleLoad}
    />
  )
}

// ─── 7. ParallaxLayer ────────────────────────────────────────────────────────
// Scroll-driven translateY on a section's background image.
// Use on: hero section background, any full-bleed section image.
// Must be inside a ref'd container with overflow-hidden.
//
// Props:
//   children  — the image or background element
//   intensity — how far it travels (default '25%' desktop, '12%' mobile)
//   containerRef — ref of the scroll-tracked section
export function ParallaxLayer({ children, intensity = '25%', containerRef }) {
  const { scrollYProgress } = useScroll({
    target: containerRef,
    offset: ['start start', 'end start'],
  })
  const y = useTransform(scrollYProgress, [0, 1], ['0%', intensity])

  // Warm up MotionValues on mount to prevent cold-start jank
  useRef(() => { scrollYProgress.get(); y.get() })

  return (
    <motion.div
      style={{
        y,
        willChange: 'transform',
        backfaceVisibility: 'hidden',
        WebkitBackfaceVisibility: 'hidden',
      }}
    >
      {children}
    </motion.div>
  )
}

// ─── 8. FadeUp ───────────────────────────────────────────────────────────────
// Lighter variant of Reveal — use for hero eyebrow text, captions,
// supporting labels that should enter more subtly (less distance, faster).
export function FadeUp({ children, delay = 0, className = '', distance = 10 }) {
  const [done, setDone] = useState(false)
  return (
    <motion.div
      className={className}
      initial={{ opacity: 0, y: distance }}
      animate={{ opacity: 1, y: 0 }}
      transition={{ duration: 1.4, ease, delay }}
      style={{
        willChange: done ? 'auto' : 'transform, opacity',
        backfaceVisibility: 'hidden',
        WebkitBackfaceVisibility: 'hidden',
      }}
      onAnimationComplete={() => setDone(true)}
    >
      {children}
    </motion.div>
  )
}

// ─── 9. HoverLift ────────────────────────────────────────────────────────────
// Hover micro-interaction for cards, buttons, links.
// Lifts element slightly + adds shadow on hover, bounces on tap.
export function HoverLift({ children, className = '', lift = -3, shadow = '0 8px 24px rgba(0,0,0,0.12)' }) {
  return (
    <motion.div
      className={className}
      whileHover={{ y: lift, boxShadow: shadow }}
      whileTap={{ scale: 0.97 }}
      transition={{ duration: 0.2, ease }}
    >
      {children}
    </motion.div>
  )
}

// ─── 10. SlideTextButton ─────────────────────────────────────────────────────
// Hover: text slides out downward, secondary content slides in from below.
// Use on: primary CTAs where hover reveals an icon or alternate label.
//
// Props:
//   label   — default visible text
//   hoverContent — JSX shown on hover (icon, alternate text)
export function SlideTextButton({ label, hoverContent, className = '', ...props }) {
  const [hovered, setHovered] = useState(false)
  return (
    <motion.button
      className={`relative overflow-hidden ${className}`}
      onMouseEnter={() => setHovered(true)}
      onMouseLeave={() => setHovered(false)}
      whileTap={{ scale: 0.96 }}
      {...props}
    >
      <motion.span
        style={{ position: 'absolute', inset: 0, display: 'flex', alignItems: 'center', justifyContent: 'center' }}
        animate={hovered ? { opacity: 0, y: 8 } : { opacity: 1, y: 0 }}
        transition={{ duration: 0.3, ease }}
      >
        {label}
      </motion.span>
      <motion.span
        style={{ position: 'absolute', inset: 0, display: 'flex', alignItems: 'center', justifyContent: 'center' }}
        animate={hovered ? { opacity: 1, y: 0 } : { opacity: 0, y: 10 }}
        transition={{ duration: 0.3, ease }}
      >
        {hoverContent}
      </motion.span>
    </motion.button>
  )
}
```

---

## Step 3 — Animation Map (What Gets What)

Read the component, identify each element type, apply the matching primitive:

| Element | Primitive | Notes |
|---|---|---|
| Hero `<h1>` (page load) | `HeadlineWords` or `SplitWords` | Per-word slide-up, `animate` not `whileInView` |
| Hero eyebrow / label | `FadeUp` | delay 0.1, distance 10 |
| Hero subtext / bio | `FadeUp` | delay 1.6–1.8 |
| Hero CTA buttons | `FadeUp` | delay 1.9 |
| Hero stat bar | `FadeUp` | delay 2.2 |
| Hero background image | `KenBurns` + `ParallaxLayer` | scale 1.18 desktop, 1.12 mobile |
| Section heading (scroll) | `Reveal` | delay 0.05 |
| Section body `<p>` | `Reveal` per paragraph | stagger 0.1s between each |
| Two-col layout text | `Reveal` per row/element | left col rows: 0.06, 0.14, 0.22... |
| Data table rows | `Reveal` per row | 0.08, 0.18, 0.28, 0.38 |
| Card grid | `RevealGrid` | stagger 0.08–0.12 |
| Inline image / mockup | `RevealImage` | delay 0.1–0.2 |
| Featured full-bleed image | `RevealImage` | delay 0.05 |
| Phone screen grid | `RevealGrid` | stagger 0.06 |
| CTA card / link card | `HoverLift` | wrap the card |
| Primary button | `motion.a` / `motion.button` with `whileHover={{ y: -2 }}` | |
| Image on hover | `motion.img` with `whileHover={{ scale: 1.02 }}` | 0.6s duration |
| Text CTA with icon reveal | `SlideTextButton` | |

---

## Step 4 — Stagger Reference

Always stagger per-element. Never animate a whole section block at once.

```
Hero load sequence (FadeUp / HeadlineWords, animate not whileInView):
  Eyebrow           delay: 0.1
  Headline line 1   delay: 0.3  (first word — rest cascade from there)
  Headline line 2   delay: 0.54
  Subtext           delay: 1.6
  Buttons           delay: 1.9
  Stat bar          delay: 2.2
  Hero image        delay: 0.85 (or on image load)

Section two-col left column (metadata rows):
  Row 1             delay: 0.06
  Row 2             delay: 0.14
  Row 3             delay: 0.22
  Row 4             delay: 0.30
  Row 5             delay: 0.38

Section two-col right column (body paragraphs):
  Heading           delay: 0.08
  Para 1            delay: 0.15
  Para 2            delay: 0.25
  Para 3            delay: 0.35

Data table rows:
  Row 1             delay: 0.08
  Row 2             delay: 0.18
  Row 3             delay: 0.28
  Row 4             delay: 0.38

Card grids:
  baseDelay: 0, stagger: 0.08–0.12

Process phases (5 items):
  0.0, 0.08, 0.16, 0.24, 0.32

Learnings / numbered items:
  0.05, 0.15, 0.25
```

---

## Step 5 — Hero Pattern (Full)

For any hero section with background image + headline:

```jsx
import { useRef, useState } from 'react'
import { HeadlineWords, KenBurns, ParallaxLayer, FadeUp, ease } from './FluidReveal'

export default function HeroSection() {
  const heroRef = useRef(null)
  const [imageLoaded, setImageLoaded] = useState(false)

  return (
    <section ref={heroRef} className="relative min-h-screen overflow-hidden">

      {/* Background — parallax + Ken Burns */}
      <div
        className="absolute inset-0 z-0"
        style={{ transform: 'translateZ(0)', willChange: 'transform', contain: 'layout paint' }}
      >
        <ParallaxLayer intensity="25%" containerRef={heroRef}>
          <KenBurns
            src="/hero.webp"
            className="absolute inset-0 w-full h-full object-cover"
            scale={1.18}
            duration={5.5}
            onLoad={() => setImageLoaded(true)}
          />
        </ParallaxLayer>
      </div>

      {/* Content */}
      <div className="relative z-10 pt-36 pb-20 max-w-5xl mx-auto px-6">

        {/* Eyebrow */}
        <FadeUp delay={0.1} className="mb-3">
          <p className="text-xs font-mono uppercase tracking-widest text-neutral-500">
            Senior Product Designer
          </p>
        </FadeUp>

        {/* Headline — per-word cascade */}
        <div className="mb-8" style={{ transform: 'translateZ(0)', willChange: 'transform' }}>
          <HeadlineWords
            lineGroups={[
              ['Leading', 'high-impact', 'design'],
              ['for', 'modern', 'platforms.'],
            ]}
            accentWords={new Set(['high-impact'])}
            className="text-[38px] leading-[1.1] text-neutral-900"
            delay={0.2}
            wordDelay={0.12}
          />
        </div>

        {/* Subtext */}
        <FadeUp delay={1.6}>
          <p className="text-[14px] md:text-[16px] text-neutral-600 leading-relaxed max-w-sm mb-6">
            Bio or tagline text here.
          </p>
        </FadeUp>

        {/* CTAs */}
        <FadeUp delay={1.9}>
          <div className="flex gap-3">
            {/* buttons */}
          </div>
        </FadeUp>

      </div>
    </section>
  )
}
```

---

## Step 6 — Section Pattern (Full)

For any 2-col content section:

```jsx
import { Reveal, RevealImage } from './FluidReveal'

function ContentSection() {
  return (
    <section className="py-20">
      <div className="grid md:grid-cols-[1fr_2fr] gap-12 md:gap-20">

        {/* Left — label / metadata */}
        <div>
          <Reveal delay={0.05}>
            <h2 className="text-xs font-mono uppercase tracking-widest text-neutral-400 mb-6">
              About
            </h2>
          </Reveal>
          {/* Each metadata row individually */}
          <Reveal delay={0.06}><p className="text-sm mb-2">Role</p></Reveal>
          <Reveal delay={0.14}><p className="text-sm mb-2">Location</p></Reveal>
          <Reveal delay={0.22}><p className="text-sm">Status</p></Reveal>
        </div>

        {/* Right — body */}
        <div>
          {/* Display heading */}
          <Reveal delay={0.08}>
            <p className="font-serif text-[22px] md:text-[26px] leading-snug mb-5">
              Display intro sentence.
            </p>
          </Reveal>
          {/* Each paragraph individually */}
          <Reveal delay={0.15}>
            <p className="text-[14px] md:text-base leading-relaxed mb-4">
              First body paragraph.
            </p>
          </Reveal>
          <Reveal delay={0.25}>
            <p className="text-[14px] md:text-base leading-relaxed mb-4">
              Second body paragraph.
            </p>
          </Reveal>
          {/* Image */}
          <RevealImage delay={0.3} className="rounded-2xl mt-6">
            <img src="/image.webp" className="w-full h-auto" />
          </RevealImage>
        </div>
      </div>
    </section>
  )
}
```

---

## Step 7 — Card Grid Pattern

```jsx
import { RevealGrid } from './FluidReveal'

function CardGrid({ items }) {
  return (
    <RevealGrid
      className="grid grid-cols-1 md:grid-cols-3 gap-6"
      baseDelay={0}
      stagger={0.08}
    >
      {items.map(item => (
        <div key={item.id} className="rounded-2xl border p-6">
          <h3>{item.title}</h3>
          <p>{item.body}</p>
        </div>
      ))}
    </RevealGrid>
  )
}
```

---

## Step 8 — Performance Rules

Always follow these. Non-negotiable.

1. **`willChange: 'auto'` after done** — every primitive uses `onAnimationComplete(() => setDone(true))` and sets `willChange: done ? 'auto' : 'transform, opacity'`. Never leave `willChange` permanently on.
2. **`backfaceVisibility: 'hidden'`** — on every animated element. Promotes to GPU layer.
3. **`viewport: { once: true }`** — all `whileInView` animations trigger once only.
4. **`contain: 'layout paint'`** on parallax container — prevents the layer from triggering layout recalculations in sibling elements.
5. **Warm up MotionValues** — for parallax, call `.get()` on scroll values in a `useEffect` with empty deps to eliminate cold-start jank on first scroll.
6. **`transform: 'translateZ(0)'`** on large animated containers — forces GPU rasterization.
7. **`fetchPriority="high"` on hero images** — prevents image load blocking the Ken Burns trigger.
8. **No `useSpring` on parallax** — adds lag/drift. `useTransform` direct is smooth enough.

---

## Step 9 — Apply to Existing Project

When applying FluidReveal to an existing project:

1. Create `src/components/FluidReveal.jsx` with the full primitive set from Step 2
2. Go section by section — DO NOT refactor everything at once
3. For each section:
   a. Read the existing JSX
   b. Map each element to its primitive (Step 3 table)
   c. Wrap elements — do not change existing classNames or structure
   d. Add stagger delays per the Step 4 reference
4. For the hero specifically:
   a. Check if image is in a `<div>` — wrap in `ParallaxLayer` + `KenBurns`
   b. Replace plain `<h1>` text with `HeadlineWords` or `SplitWords`
   c. Wrap eyebrow/subtext/CTAs in `FadeUp` with the hero delay sequence
5. For each card grid — replace the wrapping div with `RevealGrid`, keep all card JSX untouched
6. For images in content sections — wrap existing `<div className="...overflow-hidden...">` in `RevealImage` (or replace the div with RevealImage + forward className)
7. Run `npm run build` to verify no errors after each section

---

## Step 10 — Checklist

- [ ] `FluidReveal.jsx` created with all 10 primitives
- [ ] Hero: `HeadlineWords` or `SplitWords` on `<h1>`
- [ ] Hero: `KenBurns` + `ParallaxLayer` on background image
- [ ] Hero: `FadeUp` sequence on eyebrow, subtext, CTAs, stats
- [ ] Sections: each element in its own `<Reveal>` with correct stagger delay
- [ ] No whole section wrapped in a single `Reveal`
- [ ] Card grids use `RevealGrid` with `stagger={0.08}`
- [ ] Content images use `RevealImage`
- [ ] `willChange` cleanup via `onAnimationComplete` on every primitive
- [ ] `backfaceVisibility: 'hidden'` on every animated element
- [ ] Parallax container has `contain: 'layout paint'`
- [ ] MotionValues warmed up in `useEffect` for parallax
- [ ] `viewport: { once: true }` on all `whileInView` animations
- [ ] Build passes clean
