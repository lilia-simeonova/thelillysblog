---
layout: post
title:  "How to manage Software Project"
date:   2016-06-01 09:00:13
categories: project-management
---

Starting a project from scratch could be very overwhelming. Especially if you are beginner in the project management and  you are not sure from where to start or how to connect all the pieces.


Would it be nice to have a complete guide on how to manage a project with a real-life example? From the initial phases of creating a business case, collecting the requirements, to the executions and all the way to closing.

## The experiment

Having all that said  I decided  to create a series of blog posts where I will introduce a Software Project and plan it step by step, including all the necessary parts and relevant tutorials for it.

For being more specific and closer to the real world I will plan and build an open source software project. Sounds interesting, huh?

<img src="{{ site.baseurl }}/images/cartoon-admitting-mistakes.jpg" height="400" style="float:left; margin-right:30px">


## The product

Some time ago I did a small research about the estimation practices in the project management.

While I was doing that I didn’t found any useful web application which provides tools for performing Monte Carlo Analysis and basic estimations like PERT or 3-points.

So I thought it would be awesome if we have a simple tool, which can provide this functionality – simple, easy and user friendly.

<br><br><br>


## The best of both worlds

I will begin my experiment following the Watefall model - create the project charter, collect the requirements, create Work Breakdown Structure (WBS), define and estimate the activities.

Meanwhile, according to the plan, I will write some code and create the application myself :)

The main goal here is to apply all the theory I have learned from the PMBOK, practice my PM skills and create a valuable product at the end.

Feel free to share your thoughts about my experiment or give advises, using the following twitter hashtag #onlineestimations or comment here :)

### So, let’s start!

In this article I will create a project charter and identify the stakeholders, as part of the initiating phase.

The Project Charter

Creating a project charter is one of the most essential parts of the project planning. At first it may looks unecessary, however it can help:

* the stakeholders to have an overview of the project’s main goals
* you clear your mind over the project
* finding the weak spots

Here you can find great tutorial on how to write your project charter (free template in included).

Back to our project: in the next few paragraphs I will fill out all the components, needed for creating a project charter.

| **Business Case Statement** |
|Currently in the market it is hard to find a free system, which allows the project managers to create estimations of the tasks in their projects and compare different approaches.|


The system we are aiming to create should give the end user different options for their time or costs estimations, using the most popular and useful techniques. It would also allow them to perform Monte Carlo analysis, which can predict many different possible outcomes, based on statistical formulas.

| **Problem Statement** |
|1.	Turn the complex statistical formulas in programming algorithm.|
|2.	Create the system simple and user-friendly, easy to navigate from user's perspective, but containing all the required functionality.|


| **Goal Statement** |
|1. To have a tool, which can use different estimation techniques and can show the difference in the end results.|
|2. To have a graphical representation of the results.|

| **Project Scope** |
| Deliverables:  The system should have options for using a PERT and 3-Points estimations, should allow to perform a Monte Carlo analysis, should show a graphical representation of the results, should compare the different techniques and export the end results.|
|Assumptions:  The system shouldn't have a very complex architecture.|
|Constraints:  One or two months for development and one resource.|
|Out of Scope:  Only one type of Monte Carlo should be implemented. |

| **High-Level Project Risks** |
|One of the major risks that we can find is the transformation of the statistical Monte Carlo formulas to working algorithm.|

| **Stakeholders & Affected Business Areas** |
|The project will be open sourced, so every project manager could benefit from it and easily calculate the project estimations.|

|**Core Team Members**|
|As this is experiment, only me :) I can use some external consultants though, so every volunteer is more than welcomed.|

Our next step will be to create a stakeholders list. In our case this is only an experiment and we don’t have any different sides involved, so we should kind of skip this part.


