This document is intended to be a guide on how to approach planning small to large project here at YCharts.

### 1. Gather project documents

An essential step before planning any project is to ensure you have all the information you need. Below is a list of artifacts to ensure you have:

> NOTE: Each project will be different in terms of what information is needed; for example, if a project doesn't have a frontend component to it, then there will most likely not be any design documents. There may also be documents that aren't listed here that are created for a specific project, so reach out to the product team before proceeding past this step to make sure there are not any other documents you should be aware of.

**Documents:**
  - PRD (Project Requirement Document): This is a document that our product team should put together that explains the desired changes or behavior of the feature we are building. This is the first document that will have been created by the product team and is often the roughest. [Example PRD](https://docs.google.com/document/d/1cw480rNxq1m_YKYJll1DCF6zrEdioNs455H2OJUDKCQ/edit)
  - Design Documents: These are the actual designs that are put together by the design team and are hosted on Zeplin. [Example Design Docs](https://app.zeplin.io/project/5c4a461075d24d013c6bee67?seid=5f2c3f13cf695b87cc25c5f1)
  - CSS Mockup: This is the actual CSS mockup and markup that is done by the CSS team and is hosted on ycharts.netlify.app [Example Mockup](https://ycharts-pdf-disclosure.netlify.app/)

<hr>

### 2. Formulate questions and decisions

Once all the documents and artifacts of the projects have been gathered the next step is to start formulating questions, concerns, and discussion points that need to be clarified before any further work can take place. The goal of this step is to uncover as many big decisions before the project starts so that the estimate is as correct as it can be, the spec is accurate. Once you have formulated all the clarifying questions, discussion points, or roadblocks, you need to create a document and setup time with the product team and technical leaders to discuss them.

Below is a list of general questions to ask. It is not exhaustive but should give you a general sense of what you should considere in this phase of project planning.

**Considerations:**

1. Are there other projects being actively worked on or in the pipeline that should be considered?
    - Take stock of what is happening, this may require asking leaders too. Determine if there are projects that may effect the work and determine if there needs to be any ordering or if they can happen in parallel.
1. Is there refactoring work that needs to take place before doing this work?
    - If we are adding or changing functionality, we should consider whether the place we are technically is one that will work in the long term. If it is not, we should consider what work needs to be done to achieve that.
1. When looking at the PRD are there interactions that may take place that have not been considered?
    - Our product team knows our product but they don't know every technical interaction. It is our job to consider if there are interactions that "may" be possible and make sure we present them to our product team.
1. Does this work require permissions?
1. When looking at the Design Document and CSS mock up, is it possible, do we have all the data needed? Can we create visualizations that look like that?
    - Make sure that the designs are feasible. It is possible there are data points that may not apply for all securities. Make sure all the charts that are designed are possible as they are shown.
1. Are there storage implications we should consider if we are importing more data in the project?
1. Are there data pipeline concerns?

**Communicating with Product:**

1. Once you have identified all the questions you have or discussion points, you need to formulate them in a written google document. You should present the questions with any examples that would be helpful so that discussions can be better guided.
2. Schedule a meeting with the product team and any technical leaders that should be involved. Share the document before the meeting so everyone has a chance to view it and digest it.
3. Go through your written document and take notes on the decisions that are made in the meeting.
4. Ensure the PRD has been updated with any changes or additions in behaviour.

<hr>

### 3. Refine the project

After meeting with the product team and technical leaders, there should have been decisions made and questions answered. That doesn't mean we are done though. It is crucial that before you go any farther that you iterate on Part 2. With the answers and decisions revisit those same considerations and make sure there are not more things that should be clarified. If there are, repeat part 2.

<hr>

### 4. Breakdown Sections and Estimate

Once the project has been refined and clarified as best as it can. The next step is breakdown the project into logical parts so you can attempt a rough estimate with unit being "weeks". The term logical parts is vague on purpose since there is no one size fits all way to separate general steps in all projects. At the end of this step we should have a rough idea of the "parts" of the projects, their estimate in weeks and whether the work is sequential or parralel.

Below is a list of ways to create these logical separations. These are not mutually exclusive and can be combine.

**Logical Separations:**

1. Frontend work vs Backend work.
    - If a project consists of a front aspect and backend this is a good place to create a divide. How much work is on the frontend and how much is on the backend? Is there a contract that two engineers can work off of?
1. By security type.
    - If the project, for example importing "X" data point for Funds, Stocks and Indices requires a similar body of work but split across security types, breaking things down by security type is a good place.

**Output:**
1. An estimate in weeks or months (depending on how large the project is)
1. A written document of logical parts of the project and rough idea if parts of it are parallelize or sequential.

<hr>

### 5. Create Technical Spec Document

Before an engineer can start implementing or working on a project there needs to be an formalized spec document. The goal of the document is to syntehsize all the work that has been done up until thise point in a concise manner so whoever is implementing the project has context.

[Example](https://gist.github.com/KFoxder/eeefb1f02a0fe86545142e8cebfa9759)

The document should aim to answer the following questions:

1. Goal: What is the project attempting to achieve?
2. Reason: Why are we attempting to achieve this?
3. Plan: How are we planning on achieve this goal?

The spec document should contain at least 2 parts. It should contain an "Overview" section that should answer the Goal and Reason for the project and it should contain a "Work Plan" that should explain how it will be achieved.

**Overview:**
The overview should be written in paragraph form and it may include any reference materials, examples, or images the help explain the goal and reason of the project.

**Work Plan:**
The work plan is the meat of the document. It should contain all the information needed for the implementing engineer to create tickets, execute the tickets and complete the project. The process of creating the work plan is one of the most time consuming parts of planning a project, but with experience and time becomes quicker. The goal of creating the work plan is to develop the plan and more percise estimate for the project. As the planning engineer it is your responsibility to focus more on the arch of the project rather than the code and tests that needs to be written. That may sound too abstract, but a better example is the difference between thinking "in this section we need to get these 5 data points for each security" vs. "gather the securities and group them by their security type to make a more performant call to get the 5 data points we need". Leave the implementation details to the implementing engineer. As the planning engineer it is your job to concern yourself with the project's order and direction.

1. It should be broken up into sections where each section represents a ticket and accompanying pull request.
1. Each section should contain 5 piece of information: Title, Points, Description, Implementation Notes and Acceptance Criteria.
    - **Title:** The title of the ticket / pull request
    - **Points:** How many points is this body of work. 1 being 1/3rd of a day, 2 being 1/2-2/3 a day and 3 being a full day.
    - **Description:** One or more sentences describing what the ticket is about.
    - **Implementation Notes:** These are notes that guide the implementing engineer on how to achieve what you are asking for. It shouldn't be a step by step instructions but should point out hard to find or easy to miss parts of the work. It may include similar Examples or places in our codebase that could be helpful to reference. It may also include things to look out for or be catious of ("Make sure the migration isn't too long." or "Make sure there is no impact on the time it takes to import price data both locally and on staging after it is merged")
    - **Acceptance Criteria:** This is arguably the most important part of the section. It should describe exactly what you would expect after the work is successfully completed. It allows the implementing engineer to understand if their work has been successful or not. It also forces the planning engineer to verbalize what they will be looking to review when reviewing the code for behaviour. Acceptance criteria may be behaviour related ("We should see a button appear here that when clicked does Y") but can also be code related as well ("If we call `some_object.get_first_point()` it should return the first point" or "There should be full coverage of these 3 methods").
1. When breaking up each section consider the order of work.
    - Does A need to happen before B?
    - If A gets merged into develop with B is that ok?
    - Are there performance ramifications to look out for?
    - What tests need to be written to prove this logic works?

**Output:**
1. A formalized document that lays out the Goal, Reason and Plan for the project so an engineer can breakdown into tickets. [Example](https://gist.github.com/KFoxder/eeefb1f02a0fe86545142e8cebfa9759
1. By adding the points up for each section the project should be given an estimate in days / weeks.
1. The spec should be reviewed by an engineering leader.

<hr>

### 6. Kickoff Project

Before the implementing engineer starts on the Spec Document, the planning engineer should setup a meeting to review the spec document. Before the meeting occurs, the planning engineer should share the document with the implementing engineer so both parties have a chance to review it.

**Meeting Goals:**
1. Implementing engineer should come prepared with any questions they have about the Goal, Reason or Plan for the project and take a look at the overall estimate and deadline make sure it aligns with their expectations.
1. Planning engineer should walk through verbally the different sections and do their best to answer any questions the implementing engineer has for the project.
1. The estimate and deadline should be communicated and agreed upon.


