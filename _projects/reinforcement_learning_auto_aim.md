---
layout: page
title: Reinforcement Learning for Auto Aim
description: 2026 - Early development
card_meta: "Fall 2026 · Early development"
card_description: "Exploring reinforcement learning for robotic target selection and shot timing."
importance: 2
category: work-in-progress
related_publications: false
---

I am beginning a reinforcement learning project with CU Robotics to improve how our robot chooses an enemy armor plate and decides when to fire.

We plan to build a MuJoCo simulation of a stationary shooter and a rotating target, then train a policy using observations comparable to our real tracking pipeline. For the first milestone, the policy will choose a plate and request a shot while our existing ballistics solver and gimbal controller handle the low-level aiming. We will compare its hit rate and ammunition efficiency with our current solver before expanding the environment.
