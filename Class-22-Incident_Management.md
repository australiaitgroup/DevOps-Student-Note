
# Class-22-Incident_Management

## Agenda

1. Introduction to Incident
2. Incident Stages
3. Good/bad incident Management
4. Getting ready for on-call

## Introduction to Incident
What is an incident?
Incident is an event that causes or is going to cause disruption to or a reduction in
the quality of service, which requires an emergency response.

Is it an Incident?
Free disk space of a web server drops from 10GB to 10MB within 5 minutes
Autoscaling function of a production cluster is broken.
Not scaling
Over-scaling
Jenkins CI/CD pipeline is broken
Weird UI issues after a release


## Incident Stages
1. Detect
2. Respond
3. Recover
4. Learn
5. Improve

## Incident Detection
Incident can be detected by
- customers (Jira Service Desk, Service Now, Zendesk)
- employees
- monitoring system
- Metrics Monitoring
- Error & Exception Monitoring
- Application Performance Monitoring
- Uptime & Synthetic Monitoring
- We want to know before customers do.

## Incident Response
Proper response takes preparation and training
1. Acknowledge the alert (within 15 minutes)
2. Gather missing information, fill in the gap
3. Validate the incident (is this an actual incident?)
4. Setup a communication channel
5. Gather related persons

## Gather information

1. What is the incident behaviour?
2. What time did it occur? is it a new behaviour?
3. Error message or screenshot?
4. What environment? How many customers affected?
5. Is it an internal system? Any vendor involved?
6. Twitter or any other public discussion?


## Bug or Low priority Incident?

Bug usually doesn’t require urgent mitigation. It usually fixes in a sprint as a high
priority task.
Low priority incident usually requires urgent mitigation in the working hours. It
cannot be planned into a Sprint.


## Incident comms

Internal comms:
- E-mail
- Slack
- ServiceNow thread
External Comms:
- Status Page


## Internal Comms Example
Regular updates during the incident.
Status update, resolution progress, next steps

## External Comms Example
Dropbox
Facebook

## Incident Recovery
Trouble shooting is learnable
Trouble shooting skill
Knowledge of the system


Problem Report
Expected behavior
Actual behavior
How to reproduce


Triage
Severity? All-hands-on-deck?
Stop bleeding
divert traffic
drop traffic (data corrupt)
degrade system


Diagnose
Simplify and reduce
Ask "what," "where," and "why"
What touched it last
Specific diagnoses

Simplify and reduce
Inject test data, check output
Dividing and conquer
Linear
Bisection

Ask What, Where and Why

What it is doing?
Why doing that?
Where resource is used?
A cluster is having high latency.
Why? Servers are using all the cpu.
Where? Profiling server: sorting logs to disk
Where in the log sorting logic? Evaluating a regular expression
Solution: Re-write the regular expression logic

What touched it last
Specific diagnoses
Build specific tools to diagnose a system

Test and Treat
Having list of possible causes
To find out which caused the problem
An ideal test should have mutually exclusive alternatives
High possibility first
Misleading test
Tests have side effect
Tests are suggestive

Cure
We can only find probable causal factor
System are complex
Cannot reproduce in live production
Fix the problem
Postmortem


## Incident Analysis: Learning from Failure

postmortem
If we don’t learn from incident, it will happen again and again
a written record of an incident
its impact
the actions taken to mitigate or resolve it
the root cause(s)
the follow-up actions to prevent the incident from recurring

postmortem, when to write
It costs time and effort, when to write
User visible downtime
Data loss
On-call engineer invention (rollback…)
Long resolution time
Monitoring failure, issue from user

.
5-Why example
1. Why did the user complain he couldn’t use the “Send email” feature of our application?
Because there was a bug in the latest release
2. Why was there a bug in the latest release?
Because we didn’t test this scenario
3. Why didn’t we test this scenario?
Because we only tested in depth the features developed in the current sprint. We didn’t do a regression
test on all the other features of the application.
4. Why didn’t you test all the other features of the application?
Because "Send email" was a feature that was created way before and not within the current sprint, so
its impractical to test all the features for every release.
5. Why do you think its impractical to test all the features?
Because our application is so vast that performing regression testing on every single
feature manually would require an excessive amount of time and would delay the process


Formal review and publication
www.jiangren.com.au
• Was key incident data collected for posterity?
• Are the impact assessments complete?
• Was the root cause sufficiently deep?
• Is the action plan appropriate and are resulting bug fixes
at appropriate priority?
• Did we share the outcome with relevant stakeholders?


No blame

• Assume everybody is good
• Fix the system not the people


## Incident Management

Story of an unmanaged incident

  What has gone wrong in this incident?
● Sharp Focus on the Technical Problem
● Poor Communication
● Freelancing

Element of Incident Management

● Separation of Responsibilities
• Incident Command
• Operational Work
• Communication
• Planning (longer term)
● A Recognized Command Post (war room, IRC)
● Live Incident State Document
● https://sre.google/sre-book/incident-document/
● Clear, Live handoff
● You take over it.
● Make it clear to everyone


Story of a managed incident

Again, Mary is a on-call engineer…
1, 2 datacenter down
Sabrina take command
Sabrina send email
“Users not impacted”, Sabrina write to a live incident document.
3 down, Sabrina update the document, VP noticed
Loop in Joseph, Robin volunteer to help
Sabrina: you must inform Mary of any actions
Mary wanted to revert, Robin tells her, that won’t work
5pm Sabrina tries to find replacement, brief phone meeting, hand off at 6pm
Mary come next morning, college mitigated the issue, start postmorte


Incident Mgmt Best Practices
www.jiangren.com.au
Prioritize.
Stop the bleeding, then find root cause
Prepare.
Develop procedure before incident happen
Trust.
Give full autonomy within the assigned role to all incident
participants.
Introspect.
When panic, ask for help
Consider alternatives.
Is it right, shall I do differently
Practice.
Use the process routinely so it becomes second nature.
Change it around.
Take a different role so everybody can practice


## Getting Ready for On-Call

Life as a on-call engineer

● Take care of outages and operations that affect teams
● Available to perform operations on production system within minutes
● Acknowledge page and triage problem
● Lower priority and non-production system events can be handled during
business hours
Ref: a day as an on-call engineer https://dzone.com/articles/a-day-on-call-in-a-devopsteam


Balance in Quality and Quantity

● Day/Night shift
● number of engineers on-call
● Primary/Secondary
● Incident Priority (non-prod and lower priorities)

Compensation

● Time-in-lieu
● Cash
● Proportion of overall salary
● Time based compensation

Best Practices

● Clear escalation policy
● Well-defined incident management procedure
● A blameless postmortem culture
● Identify re-curring issue and priotise work


## HOT incident management
Jira Service Desk
ServiceNow
Zendesk
Rootly

## SlackOps

• Connect your alert system with your chat
• Set intelligent thresholds for your alerts
• Set up a separate room for each major incident actions into the chat
• Invite multipleteams
• Save chat transcript


Homework

• Who Destroyed Three Mile Island?
• https://www.youtube.com/watch?v=1xQeXOz0Ncs
• Rootly
• https://www.youtube.com/watch?v=H_dhYcV6Z3g






















## useful links:

- five whys: <https://www.mindtools.com/pages/article/newTMC_5W.htm https://www.mindtools.com/pages/article/newTMC_5W.htm>

- Feature Flags: <https://launchdarkly.com/blog/what-are-feature-flags https://launchdarkly.com/blog/what-are-feature-flags>

- Chrome Runtime Performance get started <https://developer.chrome.com/docs/devtools/evaluate-performance/>
