Logging and monitoring
======================

Logging is about helping you figure out what has happened in your system. When
something went wrong you need to be able to find out what happened, when, and
that should give you the why.

This document will give you an idea of what a:
- Good log message is
  - How you structure a message to provide useful information
  - Will you be able to recreate the scenario with this and other messages in
    the same flow?
- What to log and stages where it's important to log
- What to log when (log levels)
- How to enable/disable logging in various parts of your app (top level, package
  level)
- How to structure a message for searchability
- What correlation IDs are and why they're important (cloud tracing)
- How logging will help you with auditability

The most important thing to take away though is that this is unique to your app.
There is no one-size fits all. But following these guidelines will get you
thinking the right way. Not leaving you hat in hand explaining why you don't
know why your app blew up in production.

## Where to log

- Whenever you receive data from outside of your app
  - HTTP Request
  - MQ Message
  - A response to a call that you made
    - E.g. you made a request to the OTP service and it returned something
- Whenever you send data from your app
  - HTTP Requests
  - MQ Message
- When doing manipulation of data

## What to not log

- Health check endpoints
  - May come from a LB or similar to see if the site is up, will come relatively
    often and provides little to no value in being seen in the logs.

# Example scenario

[Marvin] is a chat bot that sits between Slack and other apps that want to be
available through Slack. This works by the other apps configuring Marvin to
listen for commands for them. When Marvin detects these commands he'll then
forward a parsed message to the app. This means that you could integrate with
Marvin with little to no code at all.

The problem is that Marvin doesn't have any logging enabled. So unless you can
get the exact commands sent you'll never be able to reproduce an error
condition. Making debugging that much harder.

[Marvin]: https://github.com/pivotal-sg/spring-guides
