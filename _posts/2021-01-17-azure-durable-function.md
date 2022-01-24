---
layout: post
title: "Azure Durable Function"
subtitle: "Stateful serverless functions"
date: 2022-01-23 01:23:55 -4000
background: '/img/posts/samuel-sianipar-4TNd3hsW3PM-unsplash.jpg'
---

<h3>Functions</h3>
<p>Traditional functions have below traits:
<ul>
    <li>
        Re-usable
    </li>
    <li>
        Responsible for single things and thus short-lived
    </li>
    <li>
        Stateless: doesn't hold any persistent state <br />
        and don't rely on the state
    </li>
    <li>
        Event-driven
    </li>
    <li>
        Scalable: when the usage spikes it will automatically cover it
    </li>
</ul>
<p>As good as it sounds, there are couple of disadvantages to how traditional functions works.</p>
<ul>
    <li>
        <b>Managing sequence</b>: Changing events are hard to do in normal serverless functions. Therefore, handling error is difficult. e.g. What happens if step 3 fails. How do we know which step we are in and how can we handle that error.
    </li>
    <li>
        <b>Fanning out and fanning in</b>: How to handle the next situation after the fan out and fan in... as in how to pull parallelsied computes into asingle one after it becomes distributed.
    </li>
    <li>
        <b>External event</b>: What if we are waiting for external events to finish. We can use durable function to make it wait. 
    </li>
    <li>
        <b>Long-running events</b>: If function requires to watch for an event for a long time, traditional function won't work.
    </li>
    <li>
        <b>Long-running http-based async/apis</b>: We can't open the connection for a long time. It's important to know when the response is ready and in order to do this, we need to track the state.
    </li>
    <li>
        <b>Human interaction</b>: In case when we need an approval or input from human, how do we co-relate this between events.
    </li>
</ul>

<p>With durable function, we can overcome these disadvantages by maintaining local state and orchestrating multiple functions using orchestrator.</p>

<p>A tiny example of what it can do. We can effectively paralelise the work. If we are generating a report, for each order, I can call the shipping api but this each call to the shipping API would take some time </p>

<h3>Durable functions</h3>
<p>Now let's have a look at the components of the durable functions!</p>
<b>Starter function</b>
<p>Starts orchestration or triggers the process (e.g. report, create an order) and sends the message to the orchestrator. Often a http endpoint.</p>

<b>Orchestrator function</b>
<p>This function coordinates activities and is the heart of the durable function. It also does status management.</p>

<b>Activity function</b>
<p>This is the function that will actually perform the work.</p>

<h3>How it all works</h3>
<p>Firstly, starter function gets the request and calls the orchestrator function. Then, execution history tracks the history in the history table. e.g. <code>1. Orchestrator started, 2. Execution started.</code></p>
<p>Instead of calling activity function directly, it goes to execution history log and asks, 'hey, did we peform this activity function?'. If not, orchestrator function starts the activity function. This also gets recorded in the history table and then, the orchestration <b>completes</b>. Our activity function wakes up and then looks at the history table and finds that it has to run the function. After activity function finishes running, it updates the execution history. e.g. <code>Task completed</code></p>

<h3>Replayability</h3>
<p>One of the cool thing about durable function is that it remembers the state. When the orchestration function gets called again the second time, it asks the execution history, 'Have I run the activity function?' and the execution history will say 'Yes' and return the output from the first call.</p>

<h3>Orchestrator Constraints</h3>
<p>Replayability means that the orchestrator code must be deterministic. What that means is that the orchestrator function will be replayed multiple times but it must produce the same result each time.</p>

<p>For example, if you use <code>Guid.NewGuid</code> everytime the orchestrator function runs, it's going to generate differrent result each time. Use something like <code>DurableOrchestrationContext.CurrentUtcTime</code></p>

<p>You can also use activity function in order to keep orchestrator function deterministic. e.g. Do I/O in activity function.</p>

<p>One last thing, don't write infinite loop! As we've learnt from above, , history logs will record everything and the inifite loops will cause memory to run out. Use <code>DurableOrchestrationContext.ContinueAsNew</code> to clean all the logs.</p>

<br />
<small>Photo by <a href="https://unsplash.com/@samthewam24?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Samuel Sianipar</a> on <a href="https://unsplash.com/s/photos/typing?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a></small>