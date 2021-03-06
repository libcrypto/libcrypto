---
title: "Workflow"
date: 2019-12-27T08:10:08+04:00
draft: false
toc: false
images:
tags: [bugbounty,pentest]
---


#### Intro
Been interested for a while now in bug bounty programs. But going through the immense amount of data available online made me realize that these data must be put in order first. My trials so far were pouring more hay onto the haystack. I can't find any needles.

And while there are some publicly available work flows from some amazing people. Like [capt-meelo](https://github.com/capt-meelo/LazyRecon/blob/master/workflow.png), and [NahamSec](https://docs.google.com/presentation/d/1xgvEScGZ_ukNY0rmfKz1JN0sn-CgZY_rTp2B_SZvijk/edit#slide=id.g4052c4692d_0_264). I thought I need to build my own.

Yeah, I can hear some thinking *"Not again, another work flow and another recon automation tool"*
,and, I immediately remembered the infamous XKCD standards post <p align="center">
[<img src="https://imgs.xkcd.com/comics/standards.png" style="width:30%;">](https://imgs.xkcd.com/comics/standards.png)
</p>

But, I want to go through more than this actually. I don't want to hit you in the face with a tool or a diagram. **I want to walk you and me through the thought process.** I want to study existing tools and try to make something better. Sounds good?

Moreover, Most of the workflows stop at the recon phase. I will try to go beyond that.


If you're still reading, I assume you're interested. Let's dig in.

---

#### Characteristics of a workflow

This workflow should help us in gathering information and organizing the data presented. We don't want to get lost - especially with these large targets - or overwhelmed by the amount of data. A workflow usually is backed up by some kind of tools/scripts for automation and efficiency. 

On a high level, a work flow should be:

  - Extensive, to capture all types of info that can be amassed ;)
  - Modular, to be able to plug in new tools, or process data in a new, different way that emerged
  - Easy, so the majority can contribute. Not everyone knows go or python
  - Time efficient, to run things in parallel whenever possible
  - Resumable, in case of interruption
 

I think these are good starting points. Thinking the tooling or scripts through, I the below sounds the best approach IMHO:

  - Chaining bash aliases or functions. They have to be well commented, with predictable outputted file names
  - Maybe usage of `parallel` command when possible for efficiency
  - Outputs in parsable `.txt` format, whenever possible

---

#### Goal of a workflow
<p align="center">
  <img style="width:50%;" src="https://i.kym-cdn.com/photos/images/original/001/142/233/897.gif"">
</p>

I went through many public posts and ended up with a feeling similar to the image above. Now what?

An extensive recon process - which is indeed needed - that gives you a gigantic haystack. I was left with a feeling I should be looking for an exposed tomcat admin panel, or something similar. Of course, I was wrong. 

Don't get me wrong, the writeups are awesome, almost all of them. But when combined, I was left with that attitude. It might be just me. So I decided to clear up the goals a little bit.


What is an expected goal of a workflow?

First, what are the steps of a workflow (IMHO)?

  - **Recon**: As in identifying subdomains, IPs, ports, technologies. Basically, building up the attack surface. The methods and tools around this step seems constantly evolving.
  - **Content discovery**: As in brute forcing dirs, parameters, end points and APIs.
  - **Visual inspection**: Might help in getting a feel of technologies used. In some cases you might get lucky just with this step.
  - **Hone in**: Here we need to start interacting on interesting findings from above steps.
    - **Manual interaction**: We still need to look into details ourselves, get a feel of the code in hand. Regardless of how far automation goes.
    - **Client controls**: What controls are there on user input on the client side?
    - **Access controls**: Checking for IDORs and similar issues
    - **Session management**: For example, are cookies predictable?
    - **User input parsing**: XSS, SQLi, LFI, RFI and all these fun stuff
    - **Vuln scanning**: Any publicly available vulnerabilities not fixed on the target's infrastructure? 


How should we decide where to focus our investigation or hone in? 
Consider answering the below questions to decide

  - Does the code interact with the server's filesystem, (file uploads, LFI)?
  - Could the code be pulling data from a backend system? (stored XSS, SQLi)
  - Could this same code/functionality have a privileged or admin version? (IDORs, privesc) 
  

With all that. We go back to our question "What is an expected goal of a workflow?"

A workflow is your routine, your thought process or mind map, a reference to go back to if you get lost. Backed up by tools for automation and efficiency. Otherwise, it is so easy to get lost.

Now I will go try to design my workflow. Will update this post when am done.

---

After some research and lots of distractions (work +life) I came up with this workflow
<p align="center">
  <a href="/images/bugbounty_wokflow.png">
    <img border="0" alt="workflow" src="/images/bugbounty_wokflow.png" width="80%" height="80%">
  </a>
</p>


This workflow is an outcome of studying the awesome work of [0xpatrik](https://0xpatrik.com/subdomain-enumeration-2019/),[phspade](https://github.com/phspade/Automated-Scanner) and diving into the amazing script (Chomp-Scan) by [SolomonSklash](https://github.com/SolomonSklash/chomp-scan).


You'll notice the following in the shared workflow:

  - Parallelization of independent actions. That, of course, might increase CPU/memory usage in some situations.
  - I tried to illustrate what each box is actually trying to do. Instead of a diagram full of tools names.
  - There are still some missing poking points, like POST/GET parameters for example.   


Now, to explain some of the design decisions:

  - The DNS routine at the beginning might seem awkward, but in my testing, it provided balance between efficiency and thoroughness.
    - The usage of `massdns` for a second time after `goaltdns` to ensure the permutations of subdomains are actualy resolvable. Kudos to 0xpatrik on that.
    - In my testing I came across occasions where private/internal IPs were reported back. Those IPs need to be filtered out of the results or at least saved in a separate output file. 
  - `masscan` is the initial port scanner used as it is way faster than `nmap`.
    - `nmap` will be used on the ports discovered by `masscan` to check on protocols, type of service running, and discover versions.

As I mentioned, I find the work put into [chomp-scan](https://github.com/SolomonSklash/chomp-scan) is impressive. So, I think I might use it as a starting point. Yet, I think it needs some work. And, I think I might be able contribute that to the project, open source FTW.

On that note, I will end this blog post. And, will start working on modifications of `chomp-scan`.
If you have any comments or additions to the suggest workflow I will assume you know how to contact me ;).