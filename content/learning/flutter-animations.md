---
title: "Now learning: Flutter animations"
description: A nine-lesson path from "make a box fade in" to physics, custom painting, and designer-made animations.
date: 2026-10-22
tags:
  - flutter
  - animation
  - learning
---

I'm working through a nine-lesson path on Flutter animations, from implicit widgets up to physics, `CustomPainter`, and Rive/Lottie. Each lesson has a few core ideas, one runnable example, and three exercises that get harder. I'm going one lesson per sitting and not moving on until the exercises are done.

My [[posts/daily-tarot-onboarding|Daily Tarot onboarding screen]] already uses staggered animations, so part of the goal is to understand properly what I hacked together there.

> [!note] Rule of thumb for the whole course
> Reach for the simplest tool that works: an implicit widget first, then a `TweenAnimationBuilder`, and only then an `AnimationController`.

## 🗺️ Roadmap

- [ ] **1. Implicit animations**: animate any property change with one widget swap (`AnimatedContainer`, `AnimatedOpacity`, `AnimatedSwitcher`, `TweenAnimationBuilder`) ⬅️ *in progress*
- [ ] **2. Controllers, tweens and curves**: start, stop, reverse and repeat an animation yourself (`AnimationController`, `Tween`, `CurvedAnimation`, `TickerProviderStateMixin`)
- [ ] **3. Explicit transitions**: ready-made transition widgets driven by your own controller (`FadeTransition`, `SlideTransition`, `ScaleTransition`, `RotationTransition`)
- [ ] **4. AnimatedBuilder and AnimatedWidget**: animate anything without rebuilding the whole tree (the `child` optimisation)
- [ ] **5. Hero and page transitions**: fly a widget between screens and build custom routes (`Hero`, `PageRouteBuilder`, the `animations` package)
- [ ] **6. Staggered animations**: choreograph several parts on one timeline (`Interval`, `TweenSequence`)
- [ ] **7. Physics and gestures**: fling, snap and spring like real objects (`SpringSimulation`, `animateWith`, `GestureDetector`)
- [ ] **8. CustomPainter**: draw and animate your own shapes, paths and charts (`Canvas`, `Path`, `repaint:`)
- [ ] **9. Rive and Lottie**: drop in designer-made animations and control them from code
- [ ] **Capstone**: rebuild an animation I admire from a real app

## 🔬 Lesson 1 exercises

- [ ] **Feel the curves**: swap in `elasticOut`, `bounceOut` and `easeOutBack`, and move the box with `AnimatedAlign`
- [ ] **Like button**: heart icon swap with `AnimatedSwitcher`, plus a 1.3x pop with `AnimatedScale` and `onEnd`
- [ ] **Animated progress**: one `TweenAnimationBuilder<double>` drives both a progress ring and a counting percentage label

## 🛠️ Setup

- A scratch app (`flutter create anim_lab`), pasting each example over `lib/main.dart`, or just [DartPad](https://dartpad.dev).
- `timeDilation = 5.0;` (from `package:flutter/scheduler.dart`) at the top of `main()` to slow everything down and see what's really happening.

I'll tick things off here as I go.
