+++
title = "Getting into big tech"
date = 2026-02-21

[taxonomies]
tags = [ "career", "learning", "resources"]

[extra]
toc = true
+++

Resources and advice I give people trying to get into large tech companies as software engineers. None of this is secret knowledge; the value is in having it organized.

<!-- more -->

## What you're preparing for

Big tech interviews (Meta, Apple, Google, Amazon, and the companies that copy their process) typically have two components: a coding interview and a behavioural interview. This post covers both. It does not cover system design, which is a separate topic and typically relevant for non-junior roles.

The coding interview tests your ability to solve algorithmic problems under time pressure. The behavioural interview tests whether you can talk about your past work without making the interviewer regret their career choices.

The coding interview dominates preparation time. Most candidates underestimate the behavioural portion, which is a mistake.

The whole process is an imperfect measure of engineering ability. But it is the process, and it's learnable. The resources below are enough to get through it.

## Who can apply

You don't need a CS degree. You don't strictly need a STEM degree either, though it helps. Some evidence that you can code is enough to get your foot in the door. A GitHub profile with personal projects helps, and the bar is lower than you think: Advent of Code solutions, a small bot, some utilities. I got in with a math background and a handful of mediocre repos, for whatever that's worth.

## Your CV

CV writing deserves its own post. A few basics:

- Keep it to one page. Two pages become acceptable with 10+ years of experience, but for most people reading this, one is the right target.
- Use a single-column layout. Applicant tracking systems struggle with multi-column formats, sidebars, and tables.
- Quantify your impact: "reduced API latency by 40%" rather than "improved performance"; "processed 2M events/day" rather than "built a data pipeline."
- Tailor it to the role. Recruiters follow the [F-shaped reading pattern](https://www.youtube.com/watch?v=veFlfYjRo1Y), so front-load the important information.
- Match the visual presentation to the company you're targeting. A large, established tech company tends toward conservative styling (black and grey, serif fonts), while a smaller startup or design-forward company gives you more room to experiment.

Write it in LaTeX or Typst, not Word or Google Docs. Structured markup is easier to maintain, produces consistent output, and gives you a source file you can hand to an LLM for editing. On that note: dump everything you can think of into an LLM, relevant or not (past jobs, projects, skills, education, hobbies, whatever), and let it help you draft and iterate. It's good at separating what's relevant from what isn't, and at finding the strongest phrasings. You'll still need to edit the output, but it's a good way to get past the blank page.

> [!WARNING]
> Make sure the final version sounds like you, not like an LLM. Recruiters and hiring managers are increasingly hostile toward what they perceive as AI-generated prose, and the reaction is not positive. The generic, overenthusiastic tone that LLMs default to ("spearheaded cross-functional initiatives to drive impactful outcomes") reads as lazy at best and dishonest at worst. Use the LLM for structure, ideas, and feedback; rewrite the language yourself.

For other tips, see [Tech Interview Handbook's resume section](https://www.techinterviewhandbook.org/resume/).

## Getting a referral

Most of these companies have internal referral systems. If a current employee refers you, your application gets more visibility and often moves faster through the pipeline. The good news: employees are generally happy to refer people. Referral bonuses are common, so it's not a one-sided favour. If you know someone at the company, ask. If you don't, LinkedIn works. A cold message explaining what role you're applying for and why has a reasonable success rate.

You don't need to know which team you're targeting; job ads are often generic, and any employee can typically refer you regardless of their own team. The worst they can do is not reply.

## Behavioural interview

The behavioural interview is where most engineers do the least preparation and then act surprised when it goes poorly.

[Tech Interview Handbook's behavioural section](https://www.techinterviewhandbook.org/behavioral-interview/) covers the format, common questions, and how to structure your answers. The standard advice applies: use the STAR method (Situation, Task, Action, Result), prepare 4-5 stories that you can adapt to different questions (past jobs, academic projects, music competitions, creative endeavours, volunteer work). Anything where you drove a result counts. Practice saying them out loud. Reading your answers silently is not the same as speaking them.

Many companies publish the principles they evaluate candidates against. Amazon has its Leadership Principles, Meta has its core values, Google has its hiring attributes. Look them up for the company you're interviewing at. Every behavioural question maps to one or more of these, and interviewers are trained to evaluate you against them. This is not subtle; they will often tell you as much.

## Coding interview

### Cheatsheets

Before diving into courses and problem sets, get yourself a couple of reference sheets. These are useful both during study and for last-minute review.

- [Tech Interview Cheat Sheet](https://github.com/TSiege/Tech-Interview-Cheat-Sheet): a summary of the data structures and algorithms you're expected to know. Think of it as the comp sci course you never took, compressed into a README.
- [Python Cheat Sheet for Leetcode](https://leetcode.com/discuss/study-guide/2122306/python-cheat-sheet-for-leetcode): if you're using Python (and you probably should for interviews, given how concise it is), this covers the standard library methods and idioms that come up repeatedly.

Neither replaces understanding the material. They're for quick lookup, not for learning from scratch.

### Algorithms and data structures

This is the core of the preparation. You need to be comfortable with the standard data structures (arrays, hash maps, trees, graphs, heaps) and the standard algorithms (sorting, searching, BFS/DFS, dynamic programming, sliding window, two pointers). The goal is pattern recognition: see a problem, identify which technique applies, implement it without fumbling.

#### Free resources

[Princeton's Algorithms course](https://algs4.cs.princeton.edu/home/) covers algorithms and data structures from the ground up. The full course is available for free: slides, videos, exercises, programming assignments, and condensed text excerpts. There are also free online courses on Coursera for [Part I](https://www.coursera.org/learn/algorithms-part1) and [Part II](https://www.coursera.org/learn/algorithms-part2). I only used the slides, but there's plenty of material if you want more depth. This is a proper CS course, not interview prep specifically, so it goes deeper than strictly necessary. That's not a bad thing.

[Tech Interview Handbook](https://www.techinterviewhandbook.org/coding-interview-study-plan/) provides a structured study plan for the coding interview. The useful part is [Grind 75](https://www.techinterviewhandbook.org/grind75/), a curated set of Leetcode problems you can adjust dynamically: specify how many weeks you have and how many hours per week, and it generates a schedule. This is the closest thing to a "just tell me what to do" resource. One caveat: the site recommends its own paid courses and materials alongside the free content. Skip those; the free plan and problem set cover everything you need.

#### Paid resources

[NeetCode](https://neetcode.io/) is a paid platform whose algorithms course is designed specifically for Leetcode-style interviews: it teaches what you need and nothing more, so you don't squander preparation time on material that won't show up. It also has courses for system design and other topics, which become relevant for more senior roles. It's pricey (currently $297 discounted, $599 full price for lifetime access), but there's an annual plan if you only need it for a round of prep, and the jobs on the other side pay well enough that removing a few weeks of friction is worth the cost. Unlike the paid courses Tech Interview Handbook recommends alongside its free content, NeetCode's material is genuinely good.

[Cracking the Coding Interview](https://www.crackingthecodinginterview.com/) by Gayle Laakmann McDowell is the canonical book for this process. I didn't use it, but many people I know did and found it useful. It covers data structures, algorithms, and the interview format in a single volume. If you prefer books over courses, this is the standard recommendation.

### Preparing

- Start early. "I'll cram for two weeks" works for some people. For most, it doesn't. Six to eight weeks of consistent practice is more reliable than two weeks of panic. Consistency matters more than volume: solving a problem or two every day goes a long way.
- Don't memorize solutions. Memorize patterns. If you understand why BFS works for shortest path in an unweighted graph, you can apply it to problems you haven't seen. If you've memorized the solution to "Number of Islands," you can solve exactly one problem.
- Looking at the solution after a genuine attempt is fine. But reading it is not the same as understanding it. Spend time tracing why the approach works, not just what it does. Then try to reproduce it yourself without looking. Come back to it a few days later and solve it again from scratch. If you can't, you learned the solution, not the pattern.
- Spaced repetition (Anki or similar) can help you internalize algorithm names, complexity classes, and core properties. Whether it's worth the time investment is less clear. It's good for the facts layer; it won't teach you when to apply what. Mentioned here for completeness, not as a strong recommendation.
- Practice talking out loud while solving problems. The coding interview is not a silent exam; you're expected to narrate your thought process as you go. If you've only ever solved problems in silence, the first time you try to think and talk simultaneously will feel unnatural. Do it enough times that it stops being a distraction. The secondary benefit: you learn to expose your reasoning clearly and naturally, without it slowing down the coding part.
- Mock interviews are probably useful. I haven't done them, so I can't say from experience. Practicing with a friend who is also prepping is the obvious move. Paid mock interview services exist, but I wouldn't bother. The value of a mock interview is the pressure of performing in front of someone, and a friend provides that for free. If you don't have a willing friend, LLMs with voice mode (ChatGPT's Advanced Voice, Gemini Live, or similar) may be a decent substitute. Tell it you want to practice a behavioural or coding interview, specify the company and role, and it will ask you questions and give feedback. It won't replicate the social pressure of a real person, but it's available at 2 AM, it doesn't get tired, and it's better than rehearsing alone in your head.

### During the interview

1. Before writing any code, make sure you understand the problem. Ask about edge cases, input constraints, and expected behaviour on empty or degenerate inputs. Interviewers expect this; jumping straight to code is a red flag.
2. Talk through your thinking. If you practiced narrating your thought process (see above), this is where it pays off. Interviewers care about your reasoning process, not just the final solution. A correct answer you can't explain is worth less than a partial solution with clear reasoning.
3. Before committing to an approach, sketch out a couple of strategies and their tradeoffs (time complexity, space, implementation difficulty). Starting with the brute force solution is fine, and often a good idea: it shows you understand the problem and gives you a baseline. But don't stop there; the interviewer will expect you to identify its weaknesses and work toward something better. Then pick your approach and explain why.
4. Once you have an approach, start coding and iterate as you go. Do not try to solve everything in your head before writing. Start coding and iterate; code on the screen is easier for the interviewer to follow, which helps them guide you, and it makes the problem clearer to you too.
5. When you're done, test your solution. Walk through it with the example input the interviewer gave you, then come up with your own cases to cover edge conditions. Some platforms let you run your code; others don't. Either way, you need to be able to trace through it mentally and reason about correctness without relying on a compiler. Minor syntax errors are forgivable when you can't run the code; logical errors cost you more.
6. Listen to your interviewer. They want you to pass; they are on your side. If they give you a hint, take it. If they seem to be nudging you away from your current approach, it's because you're missing something or your approach is wrong. Change direction. Coachability matters: an interviewer may prefer a weaker coder who listens and adapts over a stronger one who doesn't.

Good luck.
