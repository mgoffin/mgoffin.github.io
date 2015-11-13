---
title: Determining the Right CND Tool for a Job
author: Michael Goffin
layout: post
permalink: /2015/07/24/determining-the-right-cnd-tool-for-a-job/
tweet_this_url:
  - http://is.gd/KuN4W0
categories:
  - Chopshop
  - CND
  - Cyber
  - TACT
tags:
  - Chopshop
  - CND
  - Cyber
  - TACT
  - Tools
---
Throughout the day a SOC team uses dozens of tools to complete tasks in a few minutes that would normally take much longer. Tools improve productivity and efficiency. But with the immensity of tasks to be performed in a timely fashion, it is sometimes difficult to figure out what tool to use and how it fits into the continuous cycle of monitoring and incident response.

**Tools as differentiators**

A tool that can automate a manual task can save hundreds of hours a year, allowing us to focus on non-trivial tasks. A tool that can monitor and alert takes defenders from staring at a screen waiting for something to happen to allowing us to look at ways to refine our sensors and detection methodologies. Tools are the backbone of cyber security operations.

One thing I often get asked is what tool would I recommend for solving a specific problem regarding monitoring and incident response. A great example would be: “What tool should I use to collect network traffic?” I find this type of question a good opening toward finding the best tool. It shows that a problem area has been identified as either a gap, where a tool can fill a need, or as an inefficiency or ineffective use of an existing tool. From this, our conversation can then explore the larger issues the organization faces, and sometimes, it turns out that they don’t need a tool for network traffic but a better tool for PCAP carving or tap management.

However, what I find in many cases is that the “big picture” thinking of cyber operations isn&#8217;t addressed in the decision-making process for acquiring tools. I can usually tell this is the case when someone asks me my opinion about a specific tool. Such a conversation soon reveals that they haven’t thought through what other processes could be affected by acquiring and using the tool. What seemed like an obvious choice at-first becomes, after our exchange, a not so clear-cut decision. 

I’ve spent time reviewing the general tasks performed on a regular basis in active threat-based operations believing that if I could provide an outline to anyone asking a question about tools, it might allow him or her to quickly identify which task or tasks were associated with their problem and how any changes to them might alter other tasks. This would allow them to make a more informed decision about the tools they were looking to implement. 

To help facilitate such an impact assessment, but more importantly to provide a “big picture” view of incident response tasks for which tools could be a differentiator, I created a ring diagram that generalizes the tasks and groups them by the M&R life cycle. See Figure 1: Task Analysis for Cyber Threats (TACT) diagram.

<a href="http://mgoffin.github.io/images/tact.png" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://mgoffin.github.io/images/tact.png', '']);" target="_blank"><img src="http://mgoffin.github.io/images/tact_small.png" alt="Figure 1: Task Analysis for Cyber Threats (TACT) diagram" /></a>  
**Figure 1: Task Analysis for Cyber Threats (TACT) diagram**

**Differentiate the tasks to find the tools **

The TACT Diagram has four sections that represent monitoring and response activities. Each group of tasks are usually performed for that given phase of M&R (i.e., Pre-incident, Incident Response, Digital Forensics, Post-Mortem).; however, the tasks are loosely ordered and can be performed out-of-order, in serial, in parallel, in conjunction with other tasks, including task from other phases. The order is entirely dependent on the evolving situations we, as cyber defenders, find ourselves in. 

Each task in the diagram is a point within which simplicity or complexity of execution is indeterminate: sometimes there are no tools to assist in performing the task, and other times, one or more tools are necessary to complete the work. The TACT diagram can help you address the gamut of tasking your cyber operations undertakes, so that you can then determine what tools you currently have, and find gaps in coverage, but also to identify efficiency problems as you interrelate tasks around the ring.

**Assessing the tools **

When it comes to finding the right tool for a task, approach it like you have a job opening and the tool is applying to fill the position. Just like employment, a tool opening comes about because:

  * A tool has left the job (e.g., no longer supported or patched) or was let go for performance reasons.
  * You&#8217;ve identified a gap in tool coverage or output and need to find one or more tools to fill that gap.

Before you start “interviewing” for tools, determine your needs by listing the tools you currently leverage for each task, making note of any inefficiencies. From my experience, inefficiencies usually arise as tools overlap in functionality and tasks. For example, if you have several tools that help with building an incident timeline, how does your SOC team determine which one to use? Valuable time can be lost in this type of decision-making, or worse, if your analysts have to spend time populating each tool with incident data, thus slowing down the overall resolution time of your incident. 

If you’ve identified any tool inefficiencies, try to resolve them by:

  * Trimming your toolset to a single tool for that specific task if it makes sense;
  * Allowing analysts to use a tool they are most comfortable with to complete a task if it makes them more efficient and productive;
  * Developing glue-code so analysts will only have to interact with one tool that can automatically push data to other tools and/or tasks;
  * Identifying tool priority so that the most important tools are used first to produce results, then when the situation is less pressing, use the secondary tools on the same data set.

Next, consider the complexity of a task. For these, it may be necessary to have several tools. To determine if that may be the case, break down the task into sub-tasks to expose the underlying needs. For example, the task “Collection of Raw Data&#8221; may require tools for collecting border traffic, network node logs, suspect emails, rather than a single tool. If this is the case, then it becomes critical that you then list interdependencies that might require glue code or that have other data requirements among them.

This methodology review of tasks in your SOC’s M&R should produce a nice list of needs with which to begin the tool “interviews”: requirements, interfaces, expected outcome and performance, etc. Armed with your tasks and needs, reviewing any externally developed tool, whether it’s COTS or freeware, will help you determine if the right tool is available or needs to be built in-house. Also, just as you would when interviewing someone for a job, contact other organizations for references about their experiences using the tool, which will give you a better idea about if it has performed as expected. And be sure the analysts who will be using the tool are a part of its acquisition or creation. Many tools fail because the people who are suppose to use them can’t or won’t.

**An example: the “answer” is tool replacement **

Let&#8217;s take a look at an example using the &#8220;Isolate and Decode Network Traffic&#8221; Task. Several years ago some fellow CND-ers and I concluded that our solution (i.e., one-off scripts) for decoding network traffic had become too unwieldy. Code was being duplicated, re-implemented, and scattered across host systems. We identified this Task as an inefficient cog in our overall forensics processes. For a solution, we first determined our big-picture requirements: (1) allow us to develop a decoder in a centralized location for which everyone on the SOC team had access; (2) our process of developing a decoder needed to be quick, simple, with commonly duplicated code abstracted; (3) not cause us to use a different development language because all of our existing decoders had been written in Python; (4) allow us to create modular decoders to allow &#8220;plug and play;&#8221; and (5) enable us to develop shareable modules. Armed with these requirements, we “interviewed” a number of closed and open source tools but found none that fit well with our requirements. For our optimal solution we concluded we had to develop it in-house: the result was that our developers and analysts created what became <a href="https://github.com/MITRECND/chopshop" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://github.com/MITRECND/chopshop', 'Chopshop']);" target="_blank">Chopshop</a>.

**Conclusion**

Investing time to assess your existing workflows, procedures, and tools using the TACT diagram as a beginning outline might help you identify what gaps, inefficiencies in workflows and procedures, and ways to streamline your responsiveness to an Incident.

As I discuss Task Analysis with other groups I may refine this diagram to reflect the changes in the Cyber-landscape. If you have any input, I&#8217;d love to hear your feedback!
