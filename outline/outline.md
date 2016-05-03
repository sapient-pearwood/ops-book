The ops perspective of building a good app
==========================================

The things you have to do and consider when building and maintaining an app.
While code is an important part it doesn't stop there.

# Developer centric

The big thing about logging and monitoring and how to do it well for an app is
that it's always unique. You can't prescribe one solution for every app.

But there's basics, the things you'll always need. And you'll realize what you
need after a problem has occurred. By following the steps below you'll be able
to preempt some of those realizations.

This document will outline the smallest effort to make an app debuggable in
production. What you'll need to provide from the start to fix issues as you
notice them.

## Informative git commits

- Explain why you're doing a commit. Not what. I know you changed `foo` to
  `true`, but not why. When you're debugging issues from production and find the
  breaking commit you need to understand why.

## Logging

- Log all the things
  - How will this log message help me in the future?
    - Does it contain all the information I need?
  - Various log levels so you can toggle it on when you're seeing problems
  - Cloud tracing / correlation IDs for a request
  - Structured log messages
    - Just outputting log lines isn't enough. If you've to search through the
      logs you'll need to have a structure. Ex:
       `"Renewal successful. customer_id=1234 renewal_period=month"`
  - The way to structure ties to what log search tool gets used.
  - Log incoming data
  - Log outgoing requests/data
    - Log responses
  - Aggregate logs into a centralized system
    - Splunk
    - ELK
  - Ties in with auditability

## Continuous Delivery/Deployment

- Delivery = someone has to click a button to actually deploy
- Deployment = Automatic delivery
- Feature toggles are great, but has to be short lived
- Try to delineate so that there's as few integration points as possible
- Use a system that has pipelines at its core. Concourse or GoCD are great
  examples. Avoid Jenkins as the plague.
- Automation/integration between many parts
- Blue/green vs canary
  - How to do them with clear strategies

## Monitoring

- What services?
- What is up?
- What is connecting where?
- Turbine
- Hardware
- OS
- Service
- Integrations
- Alerting and granularity
  - How will the alert help you fix the problem?
- Monitor the same resource in multiple ways
  - For example disk space is running out but there's no warning for that, but
    there's something that catches that you're unable to write to disk
- Can you figure out _why_ your app was CPU spiking after the fact?
  - SAR and similar

## Centralize configuration
- Cloud config with all its goodness
## Databases
- Is your backup strategy good enough to handle migration issues?
- Migrations happens outside or despite deployments?
- Can you reverse a migration?
  - When is a migration irreversible?
- Are you storing data in such a way that you can deal with data loss?
  - event sourcing, and similar...

## Can you reverse a deployment?
- Be able to roll back to an old version of an app
  - Datastore rollback


## Caching
- Cache headers
- Redis/something else
- CDN
  - How do you invalidate something on the CDN?
- Minify assets
  - artifact sourcemaps for debugging (in case you have a problem with versions)



# Ops centric

These are the things that you'll need to deal with to have a complete infrastructure. Disaster recovery, security, and all of those things.

## Are your watches synchronized?

Do you take time from the internet? Is it from a local time server?
Is that server trustworthy or does it behave irrationally?

Time is in many systems important and taking care of knowing who controls the
time server is important. And how far off it is from a reliable time source. In
a distributed system it's important to know that records can be corralated.

Having monitoring on whether the time is being set throughout the day can be a
saver.

In one project I was on the NTP servers were run by the hosting provider, and
because network policies we weren't allowed to change, and one of those servers
were constantly getting out of sync. Leading to sudden jumps for VMs on that
server and our logs being hard to follow.

Other problems that can occurr is that your physical hardware is out of sync
with NTP and has a setting to sync all VMs inside it with the hardware clock.
Which can lead to sudden jumps and times that are out of sync.

There's lots more to cover in this subject, a quick dump.

## Security

- How do you deal with a security breach?
  - App in "maintenance" mode?
- Remote access?
  - When, how, etc?
- Hardening
  - CIS benchmarks
  - Immutable VMs
  - Project atomic /  Ubuntu snappy style atomically upgradable base OS

### Pen testing

- Not the ball point type


## Load testing
- Do you have a plan?
- Sustained load, when will you break?
- Soak tests

## Backups
- Have 'em or face the wrath of Khaaaaaaan
- Test disaster recovery scenarios

## Debugging
- Does your OS image contain the utilities that are necessary to debug things live?
  - strace, etc...

## Failure mode planning
  - Datacenter blows up
  - Security breach
  - What if X blows up in Y datacenters?
    - What if you end up with only one running app instance?
  - What if for each step of the stack
    - What if X dies for X in
      [web server, app server, database, disk where data store stores data, machine, switch, datacenter]

## Auditability
- Do you have enough information in your logs to figure out _why_ something
  happened? Or do you need to store it in your app? Is it easily
  queryable/searchable?

## CF Deploy vs traditional deploy

### Pets vs Cattle

- Servers that have cute names vs servers that are transient (by default if on PCF)

### Deployment opinions

- Containers
- OS package managers
- VMs
- PAAS
- Pipelines

## Group priorities

A similar thing to the app continuum:

- If the most important thing for you is:
  - Data security
  - Uptime
- If your domain is really complex then:
- Etc...

Where one end of the spectrum is Apache with cgi-bin scripts written in C, and
the other is a box with the app installed but no power cord. Clearly you'd want
to somewhere between these extremes, and towards which end is up to the
priorities of the project.
