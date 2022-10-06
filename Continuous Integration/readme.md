The basics are:

- We have unit tests that each developer runs on their own machine.
- When they commit their code to git, the tests are run again, "integrated" with code from other developers.

This helps ensure there's nothing specific to the developer's machine making the tests pass.
The code in version control needs to run cleanly in production later so before the code is allowed to be deployed to production,
it is run on a CI server or service.

When a build fails, we investigate the root cause and how to "fix the build."
When we write the fix and commit to version control again, we'll get a "passing build" alert via email. Click the alert and we see the passing build.

Green is good.

For continuous integration, we use [GitHub Actions](https://docs.github.com/en/actions).

CI test runs are triggered when we push to Github and can be seen as part of the status checks of pull requests.
We also use [CodeClimate](https://codeclimate.com/) to help us follow our style guide and keep review comments focused on improving the code quality.
