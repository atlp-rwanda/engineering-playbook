## What is Technical Debt

Technical debt can sometimes be seen as extra complexities owed during a project that may cause
feature problem down the line.
Academically, Technical debt is a concept in programming that reflects the extra development
work that arises when code that is easy to implement in the short run is used instead of
applying the best overall solution. [Techopedia](https://www.techopedia.com/definition/27913/technical-debt)

### Cyclomatic amd Cognitive Complexity

Cylocmatic complexity is a quantitative way to measure the number of linear path a piece of code is likely to run through. This complexities are usually pointer on when the code should be optimised or another approach taken entirely. Cognitive complexity on the other hand starts from the precedents set by Cyclomatic Complexity, but
uses human judgment to assess how structures should be counted, and to decide what
should be added to the model as a whole. This [tool](https://marketplace.visualstudio.com/items?itemName=kisstkondoros.vscode-codemetrics) can be used to calculate cyclomatic complexities on visual studio code during development
[Cognitive Complexity](https://www.sonarsource.com/docs/CognitiveComplexity.pdf)

### Number of Acceptable Lines (functions, classes and modules)

In order to ensure readability is key during code writing, its important that we do not overload a function, class or module with too many code lines. Below are industry average. This can be tuned per project

- lines of code in a function should not be between 20 - 35 (upper bound)
- lines of code in class and module should be between 250 - 300 (upper bound)
  Check out `rule of 30`

### Testing

Testing often time can help inform the quality of a product and its codebase as their is a good correlation between technical debt and testing. This can be underlined with `the more difficult your code base is to test the more your debt`.

## Common causes of technical debt

- Poor planning: It is important to layout the project needs properly before starting a sprint. This is to avoid issues that may later lead to debt.
- Needless complexity: When solving an engineering problem it is important to keep your solutions simple. Needlessly complex code normally gets refactored.
- Tightly-coupled components: where functions are not modular, the software is not flexible enough to adapt to changes in business needs.
- Distractions: During a sprint do not work on more than you can manage. Keep track of task via PT and always attend to them were ever possible.
- Business pressures and Deadlines: There are situations were time constraints and business needs pressure developers into delivering a solution that may require refactoring.
- Lack of knowledge: when the developer simply doesn't know how to write elegant code.

## Continous Refactoring

In Andela, we can keep track of technical debt with the following

- PT Board: When ever a developer identifies potential bugs or areas for
  refactoring, it is important to raise it with the team and keep track of the item.
  In andela we do this using the `icebox` section of pivotal tracker/Trello with a `tech-debt` tag.

- Code Climate: Code climate keeps track of issues accrued during a project. This issues can be looked into by members of the team when they have free time.

- PR Review: Issues/debt with a feature can be easily caught during this process. Though continous deployment checks exist they might not be smart enough to catch issues with semantics; thus PR reviews. Its also worth nothing that reviewers shouldn't be limited to PRs in their team as they review other teams code to ensure the conform to the language syntax best practise and andela's engineering playbook conventions.
