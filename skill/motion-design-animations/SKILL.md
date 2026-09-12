---
name: motion-design-animations
description: Master motion design, fluid spring animations, micro-interactions, and 3D effects for modern React and Next.js interfaces. Specializes in Motion (Framer Motion), Anime.js, Three.js WebGL canvas, Canvas Confetti, and GPU-accelerated CSS animations. Use PROACTIVELY when creating dynamic transitions, interactive UI elements, celebratory effects, or high-aesthetic design polish.
metadata:
  model: inherit
---

# 💫 Motion Design & Micro-Animations Master Skill

Mastery of fluid motion design, spring physics, interactive micro-animations, and visual storytelling for high-end web and mobile interfaces.

---

## 🎯 When to Use This Skill

- Building interactive UI components with entrance/exit transitions, gestures, or layout morphing.
- Implementing celebratory visual effects (confetti bursts, glowing success rings, order pass beams).
- Adding 3D canvas or particle backgrounds using Three.js without tanking mobile battery/frame rate.
- Orchestrating staggered list items (canteen menus, order queues, KDS order cards).
- SVG morphing, path drawing, and state change transitions with Anime.js or CSS keyframes.
- Ensuring zero layout reflows (GPU-only rendering) and full accessibility (`prefers-reduced-motion`).

---

## 📐 1. Core Principles of Cinematic Motion

### 1.1 The Golden GPU Rule (60 FPS Performance Guarantee)
**NEVER animate properties that trigger layout recalculation (reflow):**
- ❌ **Avoid:** `width`, `height`, `top`, `left`, `margin`, `padding`, `border-width`
- ✅ **Animate ONLY Composite Properties:** `transform` (`translateX`, `translateY`, `scale`, `rotate`) and `opacity`.

### 1.2 Natural Spring Physics over Linear Easing
Real physical objects have inertia, weight, and elasticity.
```typescript
// Standard Snappy Spring (Buttons, Cards, Modals)
export const SPRING_SNAPPY = {
  type: "spring",
  stiffness: 400,
  damping: 28,
  mass: 0.8
};

// Gentle Ambient Spring (Drawers, Bottom Sheets, Floating Elements)
export const SPRING_GENTLE = {
  type: "spring",
  stiffness: 180,
  damping: 24,
  mass: 1.0
};

// Bouncy Celebration Spring (Badges, OTP reveals, Status checkmarks)
export const SPRING_BOUNCE = {
  type: "spring",
  stiffness: 500,
  damping: 18,
  mass: 0.6
};
```

### 1.3 Perceived Latency & Duration Tiers
- **Micro-interactions (Hover, Press, Toggle):** `100ms - 150ms` (instant tactile feedback).
- **Component Transitions (Cards, Accordions, Dropdowns):** `200ms - 300ms`.
- **Page / Modal Transitions (Dialogs, Full screens):** `300ms - 400ms`.
- **Celebratory / Success Animations:** `600ms - 1200ms`.

---

## ⚛️ 2. Motion (Framer Motion v12) in React 19 & Next.js 15

### 2.1 Standard Import Syntax
In modern `motion@12.x`:
```tsx
'use client';

import { motion, AnimatePresence } from 'motion/react';
```

### 2.2 Interactive Button with Tactile Physics
```tsx
<motion.button
  whileHover={{ scale: 1.02, filter: "brightness(1.1)" }}
  whileTap={{ scale: 0.96, filter: "brightness(0.95)" }}
  transition={{ type: "spring", stiffness: 500, damping: 20 }}
  className="px-6 py-3 rounded-2xl bg-gradient-to-r from-[#FF6B2C] to-[#FF8A00] text-white font-semibold shadow-lg shadow-[#FF6B2C]/25"
>
  Confirm Pickup
</motion.button>
```

### 2.3 Staggered List Orchestration (Menu Cards, Order Steppers)
```tsx
const containerVariants = {
  hidden: { opacity: 0 },
  visible: {
    opacity: 1,
    transition: {
      staggerChildren: 0.06,
      delayChildren: 0.1
    }
  }
};

const itemVariants = {
  hidden: { opacity: 0, y: 16, scale: 0.97 },
  visible: {
    opacity: 1,
    y: 0,
    scale: 1,
    transition: { type: "spring", stiffness: 350, damping: 25 }
  },
  exit: { opacity: 0, y: -10, transition: { duration: 0.15 } }
};

export function AnimatedMenuGrid({ items }: { items: any[] }) {
  return (
    <motion.div
      variants={containerVariants}
      initial="hidden"
      animate="visible"
      className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4"
    >
      {items.map(item => (
        <motion.div key={item.id} variants={itemVariants} layout>
          {/* Card Content */}
        </motion.div>
      ))}
    </motion.div>
  );
}
```

### 2.4 Shared Element Transitions (`layoutId`)
Use `layoutId` for tabs, active pills, or expanding detail trays:
```tsx
{tabs.map((tab) => (
  <button
    key={tab.id}
    onClick={() => setActiveTab(tab.id)}
    className="relative px-4 py-2 text-sm font-medium transition-colors"
  >
    {activeTab === tab.id && (
      <motion.div
        layoutId="activeTabIndicator"
        className="absolute inset-0 bg-[#FF6B2C]/15 border border-[#FF6B2C]/40 rounded-xl"
        transition={{ type: "spring", stiffness: 400, damping: 30 }}
      />
    )}
    <span className={activeTab === tab.id ? "text-[#FF6B2C] font-bold" : "text-white/60"}>
      {tab.label}
    </span>
  </button>
))}
```

---

## 🎨 3. Celebratory Particle Effects (Canvas Confetti)

Trigger joyous feedback on order placement or OTP validation:
```typescript
import confetti from 'canvas-confetti';

export function triggerOrderSuccessConfetti() {
  const count = 200;
  const defaults = {
    origin: { y: 0.7 },
    colors: ['#FF6B2C', '#00D4AA', '#FFB347', '#FFFFFF']
  };

  function fire(particleRatio: number, opts: confetti.Options) {
    confetti({
      ...defaults,
      ...opts,
      particleCount: Math.floor(count * particleRatio)
    });
  }

  fire(0.25, { spread: 26, startVelocity: 55 });
  fire(0.2, { spread: 60 });
  fire(0.35, { spread: 100, decay: 0.91, scalar: 0.8 });
  fire(0.1, { spread: 120, startVelocity: 25, decay: 0.92, scalar: 1.2 });
  fire(0.1, { spread: 120, startVelocity: 45 });
}
```

---

## 🌐 4. Three.js WebGL Particle Fields & Shaders

For interactive canteen or cyber backgrounds:
```tsx
'use client';

import { useEffect, useRef } from 'react';
import * as THREE from 'three';

export function AmbientParticleField() {
  const mountRef = useRef<HTMLDivElement>(null);

  useEffect(() => {
    if (!mountRef.current) return;

    const width = mountRef.current.clientWidth;
    const height = mountRef.current.clientHeight;

    const scene = new THREE.Scene();
    const camera = new THREE.PerspectiveCamera(60, width / height, 0.1, 1000);
    camera.position.z = 40;

    const renderer = new THREE.WebGLRenderer({ alpha: true, antialias: true, powerPreference: 'low-power' });
    renderer.setSize(width, height);
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
    mountRef.current.appendChild(renderer.domElement);

    const particleCount = 120;
    const geometry = new THREE.BufferGeometry();
    const positions = new Float32Array(particleCount * 3);

    for (let i = 0; i < particleCount * 3; i += 3) {
      positions[i] = (Math.random() - 0.5) * 80;
      positions[i + 1] = (Math.random() - 0.5) * 80;
      positions[i + 2] = (Math.random() - 0.5) * 40;
    }
    geometry.setAttribute('position', new THREE.BufferAttribute(positions, 3));

    const material = new THREE.PointsMaterial({
      color: 0xFF6B2C,
      size: 1.4,
      transparent: true,
      opacity: 0.45,
      blending: THREE.AdditiveBlending
    });

    const particles = new THREE.Points(geometry, material);
    scene.add(particles);

    let frameId: number;
    const animate = () => {
      particles.rotation.y += 0.0012;
      particles.rotation.x += 0.0006;
      renderer.render(scene, camera);
      frameId = requestAnimationFrame(animate);
    };
    animate();

    return () => {
      cancelAnimationFrame(frameId);
      renderer.dispose();
      geometry.dispose();
      material.dispose();
      if (mountRef.current && renderer.domElement) {
        mountRef.current.removeChild(renderer.domElement);
      }
    };
  }, []);

  return <div ref={mountRef} className="absolute inset-0 pointer-events-none -z-10" />;
}
```

---

## ✒️ 5. SVG Path Animation (Anime.js)

For signature stroke checkmarks, loading loops, or logo draws:
```typescript
import anime from 'animejs';

export function animateSuccessCheckmark(svgSelector: string) {
  anime({
    targets: `${svgSelector} path`,
    strokeDashoffset: [anime.setDashoffset, 0],
    easing: 'easeInOutCubic',
    duration: 650,
    delay: function(el, i) { return i * 150; },
    direction: 'normal',
    loop: false
  });
}
```

---

## ♿ 6. Accessibility & Reduced Motion

Always respect users who suffer from vestibular disorders:
```tsx
import { useReducedMotion } from 'motion/react';

export function ResponsiveCard({ children }: { children: React.ReactNode }) {
  const shouldReduceMotion = useReducedMotion();

  return (
    <motion.div
      initial={{ opacity: 0, y: shouldReduceMotion ? 0 : 20 }}
      animate={{ opacity: 1, y: 0 }}
      transition={{ duration: shouldReduceMotion ? 0.05 : 0.3 }}
      className="p-4 rounded-2xl bg-[#16161E] border border-white/10"
    >
      {children}
    </motion.div>
  );
}
```

---

## 🛡️ Checklist for Every Motion Component

1. [ ] **GPU Composite Safe:** Are all animated properties restricted to `transform` and `opacity`?
2. [ ] **Natural Timing:** Does interactive feedback complete in `< 200ms`?
3. [ ] **Reduced Motion Fallback:** Is `useReducedMotion()` or `@media (prefers-reduced-motion)` handled?
4. [ ] **No Memory Leak:** Are `requestAnimationFrame`, Three.js renderers, or anime intervals cleared in cleanup?
5. [ ] **Mobile Touch Safe:** Touch targets are at least `44x44px` with visual `:active` or `whileTap` scale.
