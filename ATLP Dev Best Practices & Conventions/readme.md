### Table of Content

---

1. [Branch Naming](#branch-naming)
2. [Commit Message](#commit-message)
   1. [Message Header](#message-header)
   2. [Message Body](#message-body)
   3. [Message Footer](#message-footer)
   4. [Message Example](#message-example)
3. [Pull Request](#pull-request)
   1. [PR Title](#pr-title)
   2. [PR Description Template](#pr-description-template)
   3. [PR Example](#pr-example)
   4. [PR Etiquette](#pr-etiquette)
4. [Pivotal Tracker/Trello Story](#pivotal-tracker/trello-story)
   1. [Feature Story](#feature)
   2. [Bug Story](#bug)
   3. [Chore Story](#chore)
5. [Repo Readme](#repo-readme)

### Branch Naming

---

Branches created should be named using the following format:

```
{story type}-{story summary}-{pivotal tracker/trello id}
```

`story type` - Indicates the context of the branch and should be one of:

- ft == Feature
- bg == Bug
- ch == Chore

`story summary` - Short 2-3 words summary about what the branch contains

`pivotal tracker/Trello story id` - The Id of the pivotal tracker/Trello story associated with the commit.

**Example**

```
ft-resources-rest-endpoints-0408
```

### Commit Message

---

A commit message consists of a **header**, a **body** and a **footer**, separated by a blank line.

Any line of the commit message cannot be longer than **100 characters!** This allows the message to be easier to read on github as well as in various git tools.

```
<type>(<scope>): <subject>
<BLANK LINE>
<body>
<BLANK LINE>
<footer>
```

These rules are adopted from [the AngularJS commit convention](https://docs.google.com/document/d/1QrDFcIiPjSLDn3EL15IJygNPiHORgU1_OOAqWjiDU5Y/).

#### Message Header

---

The message header is a single line that contains succinct description of the change containing a **type**, an optional **scope** and a **subject**.

#####`<type>`
This describes the kind of change that this commit is providing.

- feat (feature)
- fix (bug fix)
- docs (documentation)
- style (formatting, missing semi colons, …)
- refactor
- test (when adding missing tests)
- chore (maintain)

#####`<scope>`
Scope can be anything specifying place of the commit change. For example **events**, **kafka**, **userModel**, **authorization**, **authentication**, **loginPage**, etc...

#####`<subject>`
This is a very short description of the change.

- use imperative, present tense: “change” not “changed” nor “changes”
- don't capitalize first letter
- no dot (.) at the end

#### Message Body

---

- just as in `subject` use imperative, present tense: “change” not “changed” nor “changes”
- includes motivation for the change and contrasts with previous behavior

http://365git.tumblr.com/post/3308646748/writing-git-commit-messages

http://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html

#### Message Footer

---

Finished, fixed or delivered stories should be listed on a separate line in the footer prefixed with "Finishes", "Fixes" , or "Delivers" keyword like this:

```
[(Finishes|Fixes|Delivers) #STORY_ID]
```

#### Message Example

---

```
feat(kafka): implement exactly once delivery

- ensure every event published to kafka is delivered exactly once
- implement error handling for failed delivery

[Delivers #130635935]
```

### Pull Request

---

#### PR Title

---

The PR title should be named using the following format:

```
#[STORY_ID] [Story description]
```

#### PR Description Template

---

The description of the PR should contain the following headings and corresponding content in Markdown format.

```md
#### What does this PR do?

#### Description of Task to be completed?

#### How should this be manually tested?

#### Any background context you want to provide?

#### What are the relevant pivotal tracker/Trello stories?

#### Screenshots (if appropriate)

#### Questions:
```

#### PR Example

---

![](https://github.com/andela/bestpractices/raw/master/img/git-naming.png)

#### PR Etiquette

---

It is our belief that PR reviews should not negatively impact a team's ability to deliver features.
PRs that take too much time to get reviewed can hinder on a team's progress. As such, we practice the following behaviours when raising PRs:

- When I raise a PR, I specifically assign a developer and my TTL as reviewer
- When I raise a PR, I notify the reviewer(s) on Slack in a public channel
- The reviewer(s) can re-assign the PR to someone else (e.g. TTL re-assigning to a Senior Engineer)
- The reviewer(s) has a **8 hours SLA** to review the PR
- If SLA (Service-Level Agreement) is not met, I can merge the unreviewed PR if and only if:
  - All the PR checks are passing (CI, CodeClimate, Test Coverage)
  - I communicate in #technology Slack channel that I am merging an unreviewed PR
  - I immediately take ownership of fixing any issues that arise from merging the PR

### Pivotal Tracker/Trello Story

---

#### Feature

---

A Feature story should contain the following information

**Title:** one line describing the story

**Description:** a detailed description of the story. The description should include acceptance criteria

Example

```
Title:
As a Director of Success (DoS), I should be able to initiate a "Share with Fellows" action for engagements with "Completed" needs assessments to Fellows who are not externally placed.

Description:
* As a DoS, I should be able to select and share engagements with "Completed" Needs Assessment status
* After sharing an engagement, it shows up in the open engagement view
```

**Note:** Every story title should include the word ‘should’ as opposed to ‘can’. e.g. It’s unclear whether the story “User can delete comment” is a feature or a bug. “User should be able to delete comment” or “User should not be able to delete comment” are much clearer: the former is a feature, the latter a bug.

#### Bug

---

A bug story should contain the following information:

**Title:** A short description of the bug.

**Description:** What is currently happening? What should be happening?

**Steps To Reproduce:** Outline the steps to reproduce/show the bug.

**Resources:** Include screenshots and other assets to help explain/show the bug.

Example

```
Title:
Unwanted spaces should not appear between widgets.

Description:
The number of widgets showing per row is inconsistent.
When users view their dashboard, each row should contain 2 widgets per row.

Steps To Reproduce:
- Login to Skilltree dashboard.
- Some rows display unwanted spaces in between the widgets.

Resources:
Attach a screenshot of the error caused by the bug if applicable.
```

#### Chore

---

A chore story should include the following information:

**Title:** A short description of what needs to be done.

**Description:** Why is it needed? Does it help the team go faster or is it a dependency that could cause problems in the codebase if it’s not done?

**Acceptance Criteria:** These should include conditions that must be met for the chore to be accepted.

Example

```
Title:
Setup CI for user management service

Description:
Stable CI setup will make it easy to integrate our code while ensuring that all defined tests are passing per integration.

Acceptance Criteria:
Working CI integration with circle CI.
```

### Repo Readme

---

There's a lot we can do to encourage code reuse, sharing and onboarding of new team members. For instance, Every github repo should fill in the short description and website link at the top. Also, a github repository needs a proper Readme to best help out our teammates.

The belief here is that a repo should have a few defining traits to make it easy for anyone to understand it's purpose, set it up, run it's tests, and work towards contributing to it.

Each Repo's readme should follow this basic structure:

```
## Repo Name [codeclimate badge] [testing badge]

1 sentence tag line

# Description

3-5 sentences describing the code and what it's purpose is

## Documentation

List of endpoints exposed by the service

## Setup

Step by step instructions on how to get the code setup locally. This may include:

### Dependencies

List of libraries, tools, etc needed (e.g. yarn, node.js, python, etc)

### Getting Started

List of steps to get started (e.g. clone repo, submodule, .env file, etc)

### Run The Service

List of steps to run the service (e.g. docker commands)

### Microservices

List out the microservices if any that this repo uses

## Testing

Step by step instructions on how to run the tests so that the developer can be sure they've set up the code correctly

## Contribute

Any instructions needed to help others contribute to this repository

## Deployment

Step by step instructions so that the developer can understand how code gets updated
```
