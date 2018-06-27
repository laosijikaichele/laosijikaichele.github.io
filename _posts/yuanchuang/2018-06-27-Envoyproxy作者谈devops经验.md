---
layout: post
title: Envoyproxy作者谈Devops经验
category: 转载
tags: Java,Devops
description: Envoyproxy作者谈Devops经验
---

原文：https://medium.com/@mattklein123/the-human-scalability-of-devops-e36c37d3db6a

主要观点：
1.不要总想着招聘全能程序员，应该招聘某个领域的专才程序员。
2.专业的运维人员仍然是必须的，他们关注更底层的运维：网络，安全，伸缩扩展，等等。
3.开发人员也要参与日常的运维，但更侧重产品相关的运维。
4.专业运维人员嵌入到各个开发项目组，作为开发组和运维组沟通的桥梁，同时对开发人员进行运维培训：比如服务接口文档的编写，最佳运维经验，等。

What is the right SRE model?
Given the plethora of examples currently implemented in the industry, there is no right answer to this question and all models have their holes and resultant issues. I will outline what I think the sweet spot is based on my observations over the last 10 years:

Recognize that operations and reliability engineering is a discrete and hugely valuable skillset. Our rush to automate everything and the idea that software engineers are fungible is marginalizing a subset of the engineering workforce that is equally (if not more!) valuable than software engineers. An operations engineer doesn’t have to be comfortable with empty source files just the same as a software engineer doesn’t have to be comfortable debugging and firefighting during a stressful outage. Operations engineers and software engineers are partners, not interchangeable cogs.
SREs are not on-call, dashboard, and deploy monkeys. They are software engineers who focus on reliability tasks not product tasks. An ideal structure requires all engineers to perform basic operational tasks including on-call, deployments, monitoring, etc. I think this is critically important as it helps to avoid class/job stratification between reliability and software engineers and makes software engineers more directly accountable for product quality.
SREs should be embedded into product teams, while not reporting to the product team engineering manager. This allows the SREs to scrum with their team, gain mutual trust, and still have appropriate checks and balances in place such that a real conversation can take place when attempting to weigh reliability versus features.
The goal of embedded SREs is to increase the reliability of their products by implementing reliability oriented features and automation, mentoring and educating the rest of the team on operational best practices, and acting as a liaison between product teams and infrastructure teams (feedback on documentation, pain points, needed features, etc.).
A successful SRE program implemented early in the growth phase as outlined above, along with real investment in new hire and continuing education and documentation, can raise the bar of the entire engineering organization while mitigating many of the human scaling issues previously described.
