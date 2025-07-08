# PR Reviews

**Last update:** 2025-07-09

My goal with this post is to describe how I review pull requests, and what can you (or, well, the PR requestor) do to make my life easier. I have written this for my team, but the advice is generic enough.

## What do I review

I mainly work with Go these days. You can expect me to review 400 lines in less than an hour, but how long isn't really linear; longer PRs may take hours or days depending on what I'm reviewing. Long PRs also bring more risk of continuous back-and-forth.

So what am I looking at? Let's start, I expect the below in the PR description,

1. What are you trying to do?
2. Why?
3. Should you?
4. Do you have any references or sources that validate your intention?

If I agree with the goal, then I can start working through the code. I'll try to answer the below.

5. Are you actually doing what you say you're trying to do?
6. Risk of the changes.
7. Does it fit within the current project's architecture? If not, does it really need to break it? Why?
8. Is is tested?
9. Does it follow code conventions within the company, project or the team?

Let's assume there are no concerns with the above. What can you do to make a PR to be easier to review?

## Recommendations: code quality

Let's talk about the code itself first.

### Go to the point

What are you trying to do? Focus on that.

I'll talk about splitting code later on, but it's easier if the PR has an specific goal. I'll be open-minded with small things here and there, but for mixes of concerns, it's better to separate them into different PRs.

### Risk

How thorough I'm going to be depends on the risk of something going wrong. If it's inconsequential, I'll be more open-minded about it on the assumption you'll verify whether things work. Otherwise I'll spend more time validating it. The less risk the PR brings, the easier it's to review it.

### Encapsulation

In a codebase we aggregate functionality into abstractions, components, layers, and other type of encapsulations to reduce the context needed to keep in mind when looking at a piece of code.

Ideally have clearly-defined layers, with limits on what and how far they go, which makes it easier to understand. Mixing concerns between layers just adds overhead when reviewing, and potentially get a complain from me.

### Expectations

If someone uses or reads your code, they'll have expectations on how it behaves.

Different people may have different expectation but nonetheless, there is value on having a profile of someone looking at the code in mind, and what can be done so that it fits expectations of those reading, or document where it doesn't and why. This also signals where comments are the most useful.

### Assumptions

On the other side of the coin, if you use someone else's code, you'll have assumptions on how it behaves.

I'll try to double check whether those assumptions fit: what do they do, how they behave in case of failures, etc. Usually just looking deeper into docs or the code is sufficient, but when it isn't, it can be worth to provide evidence (tests of some kind, etc.) to ease the process.

### Automated testing

The whole point of tests is to give a sign of confidence to people that the code behaves how it's supposed to. If the code doesn't behave as desired, the test _needs_ to break.

I'll try to correlate what the code does and what the test is validating. Depending on how skeptical I am, I'll break the code or debug the test to validate it. But you can do that before it reaches me.

Special note about bug fixes: if you're fixing something, please have an unit test reproducing the scenario.

### Manual testing

Run your code, either via unit tests or some other kind, and/or manually testing the functionality it touches. Also please confirm that your code _actually_ runs. It wastes effort trying to review code that has never been executed by anything.

### Generated code

It's a pattern to have generated code in Go. The projects I work on usually have linters validating the generated code is up-to-date and untampered, so there's less need to look into it too much. That said, they may give a sanity check of whether the changes done were appropriate. There's value in trying to confirm the changes in generated code make sense with the changes in the PR.

### Quantity

Code is a liability, so having less code can be ideally better. Writing a lot of code for the sake of it is not something to be proud of. Keep that in mind when deciding in the implementation.

### Test conciseness

Related to quantity, having a lot of tests is difficult to read, and harder to keep in mind on what they do. Techniques such as table tests can help to simplify tests for example.

I also personally try to avoid superfluous tests. For example, if layers are well defined, layers and their individual concerns can be tested individually (i.e., unit tests.) Concerns from downstream layers may not be needed to tested at upstream layers. Likewise, functional tests on upstream layers may avoid the need to be very thorough in downstream components.

### AI-generated code

I've been asked to review AI-generated code more often recently, my two bits of advice here:
* Just because AI allows you to generate a lot of code doesn't mean you should.
* You need to review the generated code, and on my experience you need to be a better reviewer than if you were reviewing your or some other human-written code.

## Recommendations: splitting PRs

Say we're now satisfied with the code we want to implement, but it's a large PR, what can we do?

### Bottom-up components

One way to split PRs is to split them into layers, and implement the most downstream ones, and go upstream step by step.

This assumes you are able to identify the appropriate layers upfront, although you won't be able to confirm all components work against reality until you've finished the work. Because of that I'm not a fan of this approach.

### Top-down components

You can go the other direction, implementing upstream layers first, and having placeholders for pending functionality. Then following up with implementations for them.

It's worth to consider how to validate it works when the whole functionality isn't there yet.

### When can it be validated?

If you have code that has to do something, and that something can be verified, it can work as a stopping point to know when you can stop for a given PR, and start with the next one.

### Can you explain everything the PR does in single paragraph?

If not, where are you digressing? Are those considerations, functionalities, edge cases, etc., be moved out?

This is more abstract. The goal here is to streamline the purpose of the PR, so that you can then "go to the point."

### Be intentional to have small PRs

Don't excuse yourself for having large PRs. I mean having the mindset of "I must have an small PR otherwise nobody will look at it."

Here's the thing, to review a large PR I may need to _split_ the code so I can review functionalities independently (sounds familiar?). Basically, if you won't split the PR, you are delegating the reviewer to do it for you.

Which brings to my final point.

### Review your own code

If you're looking at your own code, what would you like the code to be so that it's easier for you to review? That would also be helpful for the reviewer.