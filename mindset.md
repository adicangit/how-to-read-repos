# Mindset: How to Think When Reading Code

Before you learn any technique, you need to fix how you *think* about reading code.

Most of the struggle isn't technical — it's psychological.

---

## Confusion Is Normal

When you open an unfamiliar repository and feel lost, your brain tells you:

> "I should understand this. Why don't I understand this? I must be bad at this."

Here's the truth: **confusion is the default state**.

Even senior engineers with 20 years of experience feel confused when opening a new codebase. The difference is they've learned to expect it, work through it, and not panic.

**You are not supposed to understand everything immediately.**

---

## The 80% You Don't Need

Here's a secret that took me years to learn:

> In any codebase, roughly 80% of the code is irrelevant to what you're trying to do.

That 80% includes:
- Configuration files you'll never touch
- Edge cases for features you don't care about
- Legacy code that's barely used
- Abstractions that exist for reasons lost to time
- Tests for functionality you're not working on

Your job is to find the **20% that matters** for your current goal.

Reading code is not about completeness. It's about **relevance**.

---

## How Senior Engineers Actually Read Code

Junior developers: "I need to understand this whole codebase before I can contribute."

Senior developers: "What specific thing am I trying to understand or change?"

The difference:

| Junior Approach | Senior Approach |
|-----------------|-----------------|
| Read files sequentially | Start with a question |
| Try to understand everything | Ignore most things intentionally |
| Feel bad about not knowing | Comfortable with uncertainty |
| Read for hours | Time-box to 30-45 minutes |
| Avoid asking questions | Ask questions early |

Senior engineers don't understand more. They **ignore more strategically**.

---

## You're Allowed to Not Know

Let's be explicit about this:

- You don't need to understand the build system
- You don't need to understand every abstraction
- You don't need to understand why they made certain choices
- You don't need to understand the test infrastructure
- You don't need to understand code that doesn't touch your feature

You need to understand **one path through the code** that's relevant to your task.

That's it.

---

## The "Good Enough" Threshold

When reading code, you're not looking for complete understanding.

You're looking for **enough understanding to take the next step**.

That might mean:
- Enough to make a small change
- Enough to ask a specific question
- Enough to know where to look next
- Enough to know what to Google

"Good enough" is the goal. Perfection is the enemy.

---

## Fighting Imposter Syndrome

When you feel like everyone else "just gets it" and you don't:

1. **They don't.** They struggled too. They just did it earlier or in private.

2. **Context matters.** Someone who's worked on a codebase for 2 years will navigate it faster. That's not talent — that's familiarity.

3. **Every expert was once confused.** The person who wrote this code was confused while writing it. I guarantee it.

4. **Confusion is information.** Feeling lost tells you something: the code might be complex, poorly documented, or you might need to ask for help. All useful data.

---

## The Mindset Checklist

Before diving into any repository, remind yourself:

- [ ] I'm allowed to be confused
- [ ] I don't need to understand everything
- [ ] I'm looking for one path, not the whole map
- [ ] I will time-box this (30-45 minutes max)
- [ ] I will ask for help if stuck
- [ ] "Good enough" is the goal

---

## Practical Self-Talk

When you catch yourself spiraling, try these:

Instead of: *"Why don't I understand this?"*
Try: *"What specific part is confusing me?"*

Instead of: *"I should know this by now."*
Try: *"What's the one thing I need to understand next?"*

Instead of: *"Everyone else finds this easy."*
Try: *"Everyone finds unfamiliar code hard. That's normal."*

Instead of: *"I've been looking at this for an hour and I'm lost."*
Try: *"Time to take a break and ask someone a specific question."*

---

## When to Stop Reading

This is the hardest part. Here's when to stop:

1. **You can answer your original question.** Done.

2. **You've been reading for 45 minutes without progress.** Take a break or ask for help.

3. **You understand enough to make your change.** Ship it. You'll learn more in code review.

4. **You've identified the right file/function.** You don't need to understand *how* it works to know *that* it's the right place.

5. **You realize you're reading out of anxiety, not necessity.** Stop. You're procrastinating disguised as "being thorough."

---

## Key Takeaways

1. Confusion is normal and expected
2. You need to understand ~20%, not 100%
3. Senior developers ignore more, not less
4. "Good enough" is the goal
5. Time-box your exploration
6. Ask for help early

---

## Next Step

Now that your mindset is calibrated, learn the actual process in [`navigation.md`](navigation.md).
