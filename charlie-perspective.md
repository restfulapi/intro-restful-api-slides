<br /><br />
<br /><br />

###Charlie's Perspective Talk
<br /><br />

Perspective: A mental view or outlook







###Are we being innovative?

<br /><br />

- What have we done to change the game?
- Does Higher Ed look anything like it did 20 years ago?  Why?
- Is Ellucian leading?

<br /><br /><br />

How can we be more innovative?






### "Laurel Resting" Anti-Pattern

- Was selected for inclusion in [Antipatterns](http://www.amazon.com/Antipatterns-Identification-Refactoring-Management-Engineering/dp/0849329949) book but pulled after company review (to avoid traceability using my name to company)

- Good News:
 - We are **no longer** laurel resting
- Bad News:
 - Our clients are still laurel resting
 - We are not being disruptive, innovative






###Being More Innovative

<br />
We need solutions that are disruptive and redefine Higher Ed

<br />

How?

- "Just say no" & "Opinionated software"
- "New Jersey Method" (aka 'worse is better')
- Internet-Scale Architecture
- Maturity
- Disrupt, not evolve
- More Recommendations







### "Just say no" & "Opinionated software"

<br /><br />

Our customers are not innovative - so why do we follow them?
_(This doesn't mean not to listen & understand!)_

<br />

Our solutions should be opinionated - they should do something one way, and do it simply
(or should we not lead?)

<br />

Start-ups tend to have

- a 'narrow focus'
- practice YAGNI






### "New Jersey Method" (aka 'worse is better')

MIT Style (aka 'the right thing') is our current model

<br />

"Simplicity is the most important consideration in a design"<br />
_Simplicity of implementation is more important than interface_

<br />

It is slightly better to be simple than 'correct'

<br />

It is better to drop those parts of the design that deal with less common circumstances than to introduce either complexity or inconsistency

<br />

Completeness can be sacrificed in favor of any other quality






###Internet-Scale Architecture

<br />

'Client applications' and scalable, RESTful services

<br />

Example Technologies

- HTML 5 & 'modern browsers', REST
- 'MEAN' Stack
 - MongoDB, Express.js, Angular.js, Node.js
- Vert.x
- NoSQL (Mongo, Redis)
- Big Data & 'intelligence'
 - Hadoop, Pig, Lucene, recommendations, categorization...
- AWS, Azure






### Maturity

<br />

Late 1990s: Developers -> XP practices but mgmt not agile
Now: Mgmt -> Scrum/Kanban, developers less disciplined

<br />

- We still have teams that are not test focused
 - well over 1/2 of all code written should be tests
- Developers don't practice DRY, don't refactor
- Some don't even use Git, some only use IDEs
- Some still use Windows outside of .Net projects
- We debate 'easy' questions

<br />

If it is not an open source / start-up best practice, it is **WRONG**. Period.<br />






### Disrupt, not evolve

<br />

- Why do Universities think student data is theirs?

- Why can't students authenticate via Facebook?

- Why can't they 'like' something in Banner?

- Why isn't transfer articulation instant?

- Why can't a student build a schedule, taking courses from several universities?

- Why \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ ?






### Recommendations

- If what you are doing does not **directly** contribute to the release of source code -- **STOP DOING IT** <br />
 - Exception: Allocate time for developers to learn and use new technologies

 - Adopt GitHub Enterprise in-house for all projects except Banner 8
   - Encourage developers to fork any repo, issue pull requests

 - Assess development of 'CI as a Service' ala Travis CI

 - Open Source as many 'core architecture' framework components as possible




### Recommendations  continued... (2)

- Mandate Behavior Driven Development (BDD)
 - Automate QA testing

- Move focus away from legacy ERP "evolution"
 - apply new technologies on greenfield projects
 - innovate vs. evolve

- Require all project repos to include markdown documentation
- Limit formal documentation / models to those that will be maintained




### Recommendations continued... (3)

- Stop silly debates
 - again, if it's not an open source / start-up best practice, it's **wrong**
 - any project (except Banner 8) not using Git by now should probably be cancelled
 - any project with less than 50% of code residing in tests should probably be cancelled
 - any project without active CI should probably be cancelled
