---
title: "How to Avoid Problems with the StranglerApplication Pattern"
date: "2012-10-29"
categories: 
  - "agile"
  - "technology"
tags: 
  - "agile"
  - "strangler-pattern"
  - "stranglerapplication"
---

Recently I noticed a [question](http://stackoverflow.com/a/13002712/33886) on StackOverflow that seemed to indicate that a project had encountered problems with the [Strangler Application pattern](http://martinfowler.com/bliki/StranglerApplication.html). The basic premise behind the strangler is really great: instead of trying to do a "big bang" style all at once replacement of a legacy system, you instead build a strangler application by making vertical slices down the layers, replacing each existing feature one by one until the original system can be decommissioned. It works well, in theory and in practice - probably one of the best things about it is that it greatly reduces technology risk and helps a team focus on replacing the most important features. It reduces risk in that if the features being created to replace the old system aren't an improvement, that people should be able to continue to use the old system

### What could go wrong?

- Organizations may find that it can become difficult to keep long term infrastructure refresh projects funded in the wake of new projects that promise new features, competitive advantage, revenue potential, or increase marketshare – especially for organizations that are focused on a product or service. While this problem is an easy scapegoat, it’s typically not the only problem.
- When the system being replaced is overly connected with the system replacing it, this can cause the technical difficulty to increase dramatically – it might be OK to have them share one specific integration point, but more than that is trouble. When the team becomes distracted by the technology challenges they’ve created for themselves, they’re not longer working effectively and the project timelines spiral out of control.
- This might seem obvious; but its worth pointing out that Technology teams that are horizontally organized may have an especially hard time with the StranglerApplication Pattern, as the vertical slices required to finish the work require integration with several departments or teams to complete, and each group has its own release cycles, goals, and organizational pressures.
- If there is a replacement of key technical or management staff during the project, sometimes the new people that inherit the project find that they don’t like the new system any better than the one it replaces – leading them to start potentially a third system.
- Sometimes you might succeed partially, and create a new version of a system -but when it comes time to go back and re-write lower importance systems to start using the new system, people are too busy making cool new things with the new system. Now you have two systems. Or, if you’ve already done this once before, you’ll have three.

All of these trouble points are manageable of course. It can be helpful to re-arrange teams, keep clean separation between systems, pay special attention to project direction, and set realistic goals around replacing the existing system.

_This is a repost of an [answer I provided](http://stackoverflow.com/a/13002712/33886) on StackOverflow_
