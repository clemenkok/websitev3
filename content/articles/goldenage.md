---
title: Golden Age of Software Development
type: page
description: How is it like to develop software in early 2025?
topic: career
---

The goal of this is to elucidate to non-technical readers and technical readers a snapshot into AI-assisted development and the road ahead. This essay was NOT written with AI, and is a latent representation of my (limited) experience.  

> TL;DR: Software development has become much faster with AI. At its current growth rate, there is some possibility of displacement. But making products are more fun than ever.  

#### On AI Anxiety

Language Models have achieved staggering jumps in performance both in benchmarks and our lived realities.  

GPT-3.5 was barely able to pass coding assessments. GPT-4 would get it right sometimes, but require human tuning and intervention. GPT-4o would get it right most of the time without intervention. GPT-o1 would solve the problems GPT-4o's edge cases.  

I would get code that would not compile on 3.5 and 4. It would require some adjustment, but was still largely not that useful. On 4o, I could drag a screenshot of a user interface designed in Figma and obtain workable React and SwiftUI that compiles.  

This set up a very tight feedback loop for software engineering and led to a major jump in efficiency. It would still fail edge cases, particularly for more niche development efforts that use less popular open source software (e.g. developing for a less well-known CMS). o1 would solve these edge cases to a surprising degree.  

On the early days of Advent of Code, we saw the leaderboard being dominated by submissions that were clearly AI generated, with the latest ones in a matter of seconds [0].  What use is a human then? Are we just there to define requirements, prompt and refine? Will you ever need to write a line of code?  

> Hacker News: "I will for sure miss the days when writing code, or coder is still a real business." [1]

I related to the anxiety here. GPT could finish some of my assignments flawlessly.  

Do we need as many coders? Let's think about it. If you consider the amount of software/products that the world needs, $S$,  as a linearly growing function, then I think not. But if you consider this function to be non-linear, then yes, we will need more engineers.  

I define this function by technical possibilities (what is achievable with today's science and technology) and problems (what kinds of problems are we facing).  

$$
S = T \star P
$$

This equation includes feedback and is highly coupled. Software solves problems but could make them higher too (think: building explainability software on top of AI).  

T goes up with time but some fields are showing signs of plateaunation (physics), while others are exponentially growing (computing, biotechnology).  

P oscillates. Globalisation, economic and scientific development have solved many of humanity's low-level problems. But the difficult problems remain - e.g. climate change and cancer - and even if science finds a way, political factors could deter them from being solved easily. I don't think these problems are unsolvable. New problems could also emerge from developments in T - think AI safety and explainability.  

I view S as increasing.  

Our software needs are not bounded. It's about identifying new products and developing them so as to be useful to people, businesses and government. With a Cambrian Explosion in products (both startups and BigCo), there will be more to be done (and thus more engineers are needed).  

If you are appraised by the number and quality of pull-requests,  I would worry about job security. Businesses will likely need less code monkeying - a startup could work with 1 full-stack engineer instead of 3 engineers for frontend, backend and ML.  

> For non-SaaS startups, you still need engineers from other disciplines to be in the team.  Unfortunately, the pool of engineers who did both computer science and X engineering is anecdotally, not large! For SaaS startups, having UX/Design is still extremely important, and best if not coupled with software engineering for rapid iteration.

 I'm still happy my workflow requires me to be in the IDE, even though I write less code. Someone's definitely working on an IDE for agents.  

#### Development becomes Supervision

In 2021, I was learning programming by watching tutorials on YouTube and copying code from the Internet. I felt creative when defining what code to write (i.e., what I need from this function).  

The workflow then was:  

`[requirements] -> [solution architecture e.g. serverless vs monolithic, and then mapping out what objects and data structures we need] -> [code: copy from StackOverflow, YouTube, GitHub, Documentation] -> [quality assurance: testing] -> [deployment on cloud] -> [marketing and continuous iteration on requirements]`  

Now, transformers have enabled us to prompt the requirements or interface we want, obtaining a satisfying result more than 50% of the time. I am adopting the following process for coding.  

`[prompt the UI/property of what this function does] -> [develop a good approach in ChatGPT/Copilot that the AI and I agree upon] -> [copy and paste into the IDE] -> [see the results; if unsatisfactory, re-prompt. if satisfactory, to the next requirement]`  

 Unsatisfactory results arise usually due to incomplete context such as missing out on a niche/old/new library not within an LLMs training dataset (this could be disrupted by OpenAI's new feature, Deep Research, released today, and is sometimes alleviated by web search).  
 
 One example is when working with PyVis, I screenshotted the current UI and specified the feature I wanted to develop. I was unable to get the desired output. I went back to thinking of a way to approach the problem and commented my approach before alt-tabbing. You can argue it would have been faster if I did this initially. This is still way faster than how work was done in 2021.  

Being able to give my entire codebase in GitHub Copilot aids with the context problem. Claude 3.5-Sonnet seems to give much more focused results. o1's reasoning is great but it's output must be constrained by the user, less it 'spasms' and over-generates code that I was not looking for. I also do not appreciate getting rate-limited so quickly, hence needing to revert to ChatGPT sometimes.   

Moving forward, I would like to see AI evaluate its own output. Humans must be able to oversee and intervene. AI doesn't have the context of our mental space, and sometimes hallucinates. I had hoped to see this with Anthropic's Computer Use, but from our experience in ICHack 2025 [2], it still in beta mode and difficult to use.

> I haven't tried Operator, but I am hopeful that it is better; surely OpenAI staff have tried Computer Use.

#### On Agents

Some startups are working on agents that will take over the entire software development pipeline, from Development, to QA, to Data Science. But present models (I have not tried o3) still get stuck on edge cases and must know when to give up and seek human intervention.   

There are financial reasons why humans should not trust AI's output blindly. For example, AI hallucination cost one startup $10K USD [3].   

It is likely that AI will continue to develop at its present pace, given new scaling laws from test-time compute promising gains that have not been eked out. It could be possible to have an agent focus on QA, and approve this at a human-level, before letting the development agent iterate and achieve the stated requirements whilst being rigorously examined by the QA agent with human supervision.   

> Will test-time compute lead us to AGI? My response to that is, what do you define as AGI? It should be possible for LLMs to conduct their own research and generate interesting, novel results via gains in reasoning and agents (Operator/Computer Use), which enable learning from trial-and-error. It was argued previously that LLMs are terrible extrapolators but great interpolators, and my hunch is on test-time compute to make gains in this area. Several problems still remain unsolved: Continual Learning (weights remain static), power-efficient hardware, alignment and AI incapability to do its own 'wet-lab' research (humanoids).  

DevOps teams are already maintain platforms for enterprise users to incorporate LLMs into their daily workflow. Teams are building Retrieval-Augmented Generation into their enterprise AI stack. Open-source tools such as AnythingLLM has made this a turnkey process.   

We could see more agent deployments in 2025. Startups such as Browser Use will have a major impact into realising the potential of agents, enabling them to be deployed in enterprise environments without an enterprise ChatGPT subscription. DevOps teams should prepare to integrate such tooling for users, while startups working on agent frameworks should aspire to make implementation turnkey.  

Interpretability and deciding the limits of supervision will decide the success of agent implementations.  

#### Meta-Discussion

##### Should you still study Computer Science?

Yes, but do a double major in engineering, business, humanities or a science (includes mathematics). Interpolate between these disciplines and it'll make you a more useful engineer who can not just code, but contribute new perspectives. This leads to research/startup ideas and greater odds of taking them to fruition.   

Be a hedgehog, not a fox.  

> Does this mean you don't need deep expertise in $discipline? Unless you are a rare talent, it is better to hedge first, then stochastic-gradient-descent your way into a market niche later on.

For individuals interested in hardware (EE/MechE), it would be sensible to go into robotics - it seems that the present trajectory of AI is bounded by it's ability to impact the real world. Power systems seem to also be another good track.  

> Yes, AI can build and produce (order?) its own hardware, and orchestrate them via API calls. But on its own without human intervention? Not until we see the first, commercially-viable humanoids, but this doesn't seem too far away.  
 
##### How should we appraise engineers?

Individuals should be appraised on personal interactions, problem-solving, ability to innovate (a sense for product-market fit), and passion. Referrals matter more than ever.    

Online Assessments have become perfunctory. Assessments should be made live (screen shared or in-person). Whiteboard interviews still matter. Algorithms and Data Structures still matter, as engineers must scrutinise code generated by AI, or intervene when necessary to improve upon sub-optimally generated code. It also matters for 'big picture' ideation (solution architecture, knowing what works well and what doesn't - this comes from experience).    

More importantly, examine how developers use AI. Do they use it blindly by copy and pasting, or have a rigorous, thought-through process?   

Lastly, project-based assessments which incorporate elements of Product Market Fit, Solution Architecture and design trade-offs should be the way to go (such as taking on original research and then performing oral examinations).   

If you would like to comment or discuss, please direct it to clemen (dot) kok (at) gmail (dot) com.  

[0] https://www.unicornds.org/advent-of-code-in-the-advent-of-ai-an-analysis-on-yearly-solve-times/
[1]  https://news.ycombinator.com/item?id=42473321
[2] https://www.youtube.com/watch?v=wCr_Gc6hmFo&t=2s
[3] https://asim.bearblog.dev/how-a-single-chatgpt-mistake-cost-us-10000/