---
title: "Is GitHub Down Today?"
date: 2021-08-11T00:57:37+08:00
draft: false
tags: [GitHub]
categories: [Git]
---

On 2021-08-10 23:30 CST, I observed that I cannot push to my GitHub repo even
with `--force`. After checking the [GitHub status site](https://www.githubstatus.com/), it seems that the GitHub API is down[^1].

It is not the first time that GitHub is down. [There](https://www.infoq.com/news/2020/03/github-february-incidents/) [has](https://devclass.com/2020/03/02/github-outages-its-the-database-stupid/) [been](https://techmonitor.ai/techonology/software/github-outage-impacts-millions-of-developers-issue-found-fix-coming) [several](https://www.theregister.com/2020/07/13/github_takes_some_downtime_availability/) failure reports last year (2020), due to MySQL and other issues.

At this moment, I have realized how much I and other developers around world
are relying on this free service.

# References

+ The official site for GitHub status: https://www.githubstatus.com/
+ Has GitHub been down more since its acquisition by Microsoft?: https://news.ycombinator.com/item?id=23676199
+ https://statusgator.com/blog/2020/06/04/has-github-been-down-more-since-its-acquisition-by-microsoft/
+ Post-mortems: https://github.com/danluu/post-mortems

[^1]: On 2021-08-10 23:57 CST, GitHub is back to its normal state. Git-push works fine now.
