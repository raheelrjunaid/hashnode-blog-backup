---
title: "Fixing Vulnerabilities with Speed"
seoDescription: "In this article, I walk through how I managed to bump several major, critical, and vulnerable dependencies with speed, resulting in a 234-file pull request."
datePublished: Tue Aug 08 2023 15:20:26 GMT+0000 (Coordinated Universal Time)
cuid: cll2g7e8o000209l8hqvo9ydq
slug: fixing-vulnerabilities-with-speed
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1691468339843/075bccd8-5e24-470b-bbb3-743a81f92377.jpeg
tags: productivity, automation, funny, vulnerability, dependency-management

---

> No, not that kind of speed.

I'm not a fan of introductions or TL; DRs because they give you a summary of what you're going to learn, but you can summarize the key takeaways with AI these days. Anyway, you read the title.

---

## Background

We had a dependency so outdated, it had a critical vulnerability. I was tasked with upgrading it. Sounds small and simple right?

> "It was not."
> 
> — Raheel Danger Junaid: Understatement of the year

Here was my:

### Plan of Action

1. Bump all the dependencies first and break most/all of the repository
    
2. Fix the code
    
3. Fix the tests (because they will fail: as they do)
    
4. Document the changes made
    
5. Make a blog post
    
6. Sit in my Costco chair and sip a cuppa lukewarm Exclusive Blend 7-Eleven coffee
    

The only apparent

### Flaw in The Plan

was the last step, considering that a 12oz cup of exclusive blend costs $1.75 CAD (as of whenever this article came out), but a Dr. Pepper Zero Sugar bottle from Dollarama costs the same amount. They are both:

1. Calorie-free 🫃
    
2. Caffeinated ☕
    
3. Convenient to obtain ⛽
    
4. Low in Cost 🤑
    
5. Taste Good 😛
    

Ultimately struck by indecision like Hamlet: I bought both. I didn't mix them though; that's going too far. As per the POA, I bumped the package versions and started

## Fixing the Code

Which first required fixing any

### Type Errors

When running

```bash
npm run typecheck
```

I was greeted with about 70 type errors. There were 10 issues to resolve for the first directory, four of which already had documented `sed` patches (I'll show an example of one later), so I had 6 errors to actually fix.

$$10-4=6$$

<div data-node-type="callout">
<div data-node-type="callout-emoji">🗒</div>
<div data-node-type="callout-text">Side note: I only focused on type <strong>errors</strong> instead of warnings as those could be code-gardened later.</div>
</div>

Each type error was viewed in isolation for analysis on repitition. For each error, I asked: what could easily be substituted/replaced, removed, and reordered? Manual work was reduced in many areas by delegating trivial tasks to scripts.

These scripts were never made by me as actually fixing the type errors was more important than learning and soon forgetting bash (which has happened before). Rather ChatGPT was used. Here's an example prompt I fed it:

```plaintext
Create a script in any language that will perform a batch change on all files in the current directory recursively. Handle and report any errors in each step.

Here are the steps you must perform:
1. Create a backup copy of the current directory, naming it: {directory_name}-backup
2. Parse all files that import "package" from "library". For example:
  - import { x, package, y } from "library"
  - import package, { x, y } from "library"
  - import { package } from "library"
3. Remove the package import from the detected line, or delete the line entirely if that is the only import.
4. If any operation fails, rename the backup directory to the name of the current directory and delete the current directory
```

<div data-node-type="callout">
<div data-node-type="callout-emoji">🤔</div>
<div data-node-type="callout-text">I would've <code>git reset</code> instead of using the backup directory, but many times, I had other changes I wanted to keep</div>
</div>

Everytime, it chose to use JavaScript, and I'd rather not use TS or JS for anything (and I mean **anything**). Converting it to bash was easy and revising the prompt until I got something that ran error-free was simple enough.

Once I thought I was done with the current directory, I moved on to the next. Many changes weren't repeated often enough to warrant a script. Others could be done in one command like:

```bash
find . -type f -exec sed -i '' 's/ObjectID/ObjectId/g' {} \;
```

In layman's terms: look for all files in the current `./` directory and execute a `sed` substitution, replacing all instances of `ObjectID` with `ObjectId`.

#### Why didn't you view the CHANGELOG.md for each package?

I used my LSP to show the documented differences in new function signatures and supported class methods. This was crucial so I didn't waste keystrokes and brain cells digging up each package's release notes on Github.

### Lint Errors

There was only one error which was surprising. It was another find and replace operation, so it was pretty simple. Why did I include this section?

## Fixing the Tests

I had to bump the `jest` version, and there were obscure changes to variable scoping within tests that were not documented nor discussed online. Peer programming with a coworker fix the issue.

Otherwise, it was the same fixes as the lint errors. If you forgot:

> "It was another find and replace operation, so it was pretty simple."
> 
> — Raheel Danger Junaid: 10 Seconds Ago

## Things I Would Do Differently

Nothing huge comes to mind considering how quickly the PR was approved and merged. I'd probably document repeated changes while fixing them next time so I don't have to remember them when opening the 234 file PR for review.

## The Final Result

Everything worked and nothing broke! Tha— that's it; on to the next ticket now.