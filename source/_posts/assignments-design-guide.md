---
title: "Assignments Design Guide - Candidate Perspective"
date: 2020-12-18 00:00:00
tags:
---

I've been writing assignments lately due to interviews. I am happy to see the software industry turning assessment approach from doing Leetcode (or whiteboarding) to a way that is more closely to how most engineers handle day-to-day works. However, the recent experience with take-home assignments is painful to me, mostly because of how the take-home assignment process is designed. I often got rejected replies like `not fulfilling requirements correctly` or `we don't like your architecture`... etc. Got rejected without solid clues makes people really confused and frustrated.

Here are some thoughts that I think could improve the process:

# Explicitly Declare What Tools Can be Used and What Tools Cannot
Regarding the use of libs/frameworks, you might be thinking of using A is enough, but your candidate is thinking of B. The effort is bound to be different, and thus the time/effort estimation will not be on the same standard.

By explicitly saying what lib/frameworks can be used and what cannot, will help both sides to narrow down the scope, eliminate the grey zone, and thus clear potential misunderstands.

If you want to give your candidate the freedom of choosing libs/frameworks, please soften the time limitation. Your candidates might spend extra time evaluating tools and learning usage.

# Make Specification VERY Detailed
Explicitly define requirements like what browsers should be supported and what not be, what platform the assignment should be deployed to, what it is going to do when a user clicks the button...etc. Things like these will help define the boundary of the assignment.

Ideally, the mock-up design should be provided, but when it is provided, you have to name what will be taken into account in and what will not be.

# No Bonus Requirements
The bonus section is a lie. There are only things that you will assess and things you won't. Yes means yes, no means no. Grey zone are not allowed here.

If you are going to assess criteria like tests, architecture, coding styles, be specific what you expect to see, otherwise, everyone has their own opinions on these parts and will possibly consume lots of candidates' time if their standard is beyond your expectation.

# Do it Yourself before Sending to Your Candidate
When you have done designing an assignment, please do it yourself like an interviewee. Ask engineers who match the mentioned experience in the job description to do the assignment. The point is, you have to estimate how much time you will really spend on finishing the assignment.

If you spend x hours, multiply it by 2 or 3. Your candidate might not have the same skillset as you do, they will require extra time to do some survey in order to finish the assignment.

# Arrange a Retrospective Meeting after Done Submitting the Assignment
This stage tries to simulate doing a PR code review. Human errors and have different opinions on things. Give the candidate a chance to justify him/herself, asking how to fix an overlooked bug in the assignment, asking why and what's the reason for a specific approach. It should be a discussion like how you do PR.

---

These are all the caveats that I could conclude by now.

Ideally, the candidate should be provided with a sandbox project, and the candidate will be asked to add a feature to this project. After done adding the feature, the interviewer reviews the PR and gives some feedback.

The whole thing is aiming to simulate the onboarding life of first 1 ~ 3 months before the engineer could take harder tasks.

I really hope companies could follow this guide while designing assignments. A crappy defined assignment is like a foot gun. You shoot in yourself, wasting tons of time on reviewing assignments that you think is shabby, complaining why a capable engineer is so hard to find while overlooking the fact you might assess in a wrong way. It's a no-win situation, both you and the candidate waste tons of time on each other.

A good assessment should eliminate the grey zone as possible. For the god sake, please stop playing the game of "I have opinions, but I am not going to tell you. Guess what I want."