# Pair Programming

Pair programming is very central to how we transfer knowledge between engineers at Andela. Several arguments have been made for and against it around the web, but this is how we have been using it here so far.


### How To

#### In Apprenticeship: Cognitive Apprenticeship Model

We apply the principles of cognitive apprenticeship through pair programming. [See more details here](https://github.com/andela/learning/blob/Apprenticeship-Advancement/Apprenticeships/Pair%20Programming.md).

#### At Engineering

We do not formally pair program to the extreme but we use it for the following scenarios:

- Senior engineers unblocking juniors or engineers unblocking each other.
- During the onboarding phase for new engineers.

### Pair Programming Styles

**Driver-Navigator**
The driver is at the keyboard, and takes a tactical view of the problem. The navigator observes and takes a strategic view of the problem, offering suggestions and asking questions. The roles may switch back and forth as needed, depending on how the work flows and how the interaction between the two people works

Useful for general development and for knowledge/skill transfer. When used for knowledge/skills transfer, the junior member takes the driver role and the senior member takes the navigator role

**Ping-Pong**
One member of the pair writes a failing unit test. The second member writes production code to make the test pass, and then writes the next unit test. The first member writes production code to make that test pass, and then writes the next unit test. The pair continues in the same way to solve the problem.

The ping-pong style is usually applied when two peers are working on a problem together.

**Silent Running**
Typically as a variation on the ping-pong style, the developers do not speak aloud, but communicate only through the test code and production code they write.

Silent running is sometimes used as a way to alleviate the monotony of a long pairing session.

<img width="416" alt="screen shot 2016-11-10 at 1 12 44 pm" src="https://cloud.githubusercontent.com/assets/13568167/20172985/5d4ea93c-a748-11e6-8aa5-cc1b7f6311ac.png">

### Pair Programming Anti-Patterns
**Superman:** He refuses to be impaired by a slow partner. He will hog the keyboard and save the world all on his own.

**Absent-Minded:** He’s distracted, sleepy, or preoccupied with other things besides the task at hand.

**The Back-Seat Driver:** As navigator, he breaks the driver’s train of thought with a non-stop stream of trivial comments.

**The King of Shortcuts:** As navigator, he mentions every keyboard shortcut the driver fails to use, but doesn’t make practical or meaningful observations.

**The Anti-Mentor:** As navigator, this senior developer leaves his junior partner without guidance.

**Fearful Freddie:** He refuses to refactor code he didn’t personally write.

**The Defactorator:** He reverses refactorings others have done to make the code consistent with his personal preferences.

**The Soloist:** He works solo as much as possible, and has a large repertoire of excuses not to pair.
