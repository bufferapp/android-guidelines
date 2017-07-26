# Pull Request Guidelines
---------------------------

The following guidelines state some general approaches we take when it comes to Pull Request for the Android project 🙂

## Pull Request Content

When submitting a Pull Request, there are some general concepts which a Pull Request should follow.

### Focus

A Pull Request should be focused. If implementing a feature, the PR should contain a single feature. If fixing a bug, the PR should fix a bug - rather than bundling 50 bug fixes into a single PR.

This approach of Focus helps us to maintain the quality of the project, makes it easier for us to check PRs and also avoids changing too much at once. If we need to roll back, or find a specific commit where something changed (for whatever reason), then a focused Pull Request is easier to source. 

### Maintainability

When it comes to testing, we avoid the attitude of "adding it later" or "not having time" as product quality is of importance in the project 🙂

Any Pull Request submitted that contains testable code must have corresponding Unit and User Interface tests. Pull Requests will not be merged if they don’t have tests for both of these, this is not only to ensure that what you’ve built is working both behind the scenes and visually but also to give us the security that these things are less likely to be broken by future changes 🤜🤛 

### Consistency

The content of the PR should be consistent with the architecture of our application, this means that any code that has been committed must follow the way in which the project is built.

For example, now we have a cache module you shouldn’t be injecting an instance of a database helper inside of an activity. Or if you’re making a network request, this should go through a Use Case an the rest of the modular flow within the architecture. See the Architecture diagram in the Architecture guidelines page for help with this.

## Pull Request Etiquette

There are some general guidelines you can follow to ensure a great experience when it comes to Pull Requests:

- When it comes to reviewing a Pull Request, any comments you leave should promote discussion. We avoid pointing out that something is wrong, rather ask why that approach was taken and have a conversation about it. This makes the PR experience much better and keeps a good relationship between developers 🙂

- We don't commit commented-out code for whatever reason. Rather than commenting out code, delete it as it's probably been commented out for a reason

- Only open a PR when the code is ready. We try to avoid Work-in-Progress PRs as often the branch can end up sitting in the PR queue for some time

- If you can remember to, try running the code style checks (PMD, checkstyle and findbugs), Unit Tests and UI tests (if possible) before opening the PR. This helps to avoid hopping back and forth between branches and waiting for CI to complete again after a previous fail. This also helps to adhere to the above guideline

- When merging a PR remember to delete the branch, we don't like a build up of old branches on Github!

## Squashing Commits

When merging a Pull Request commits must be squashed. This can be done either from the command line or from within GitHub itself. 

Why? Squashing commits keeps out commit history cleaner, more navigable and easier to rollback in any circumstance that we may ever need to 🙂

## Pull Request Merging

In-order for a Pull Request to be merged the following criteria must be met:

- PMD must complete successfully
- FindBug must complete successfully
- CheckStyle must complete successfully
- Unit tests must run and pass
- Functional tests must run and pass
- Committed code must have corresponding tests in terms of both Unit and Functional tests. This will soon be checked by code coverage tools.

If any of the above fail then they must be fixed before the PR is merged into master. This helps to keep our codebase clean, functional and error free. Master should never be broken 😄

## Pull Request Titles

Titles in our Pull Requests use specific formatting so that we can automatically generate change logs for releases. 

When opening a Pull request, the title should follow the below format:

- If fixing a bug:

[FIXES] Updates Pull-to-Refresh not refreshing content


- If adding an improvement to the app:

[IMPROVES] Added DiffUtil so that the way we handle Updates is more efficient


- If implementing a feature:

[FEATURE] Implemented Instagram Grid Preview for Business customers


- If doing something that isn’t directly related to product development:

[NONE] Adding script to push our code coverage results to sonarqube 

## Pull Request Descriptions


All Pull Request should contain a description, these description should outline:


- If the PR is fixing a bug, then the description should state the problem the Pull Request resolves. This description should also contain a link to the corresponding trello card / github issue.


- If the PR is for a feature, then a visual representation of the implementation (this will be in the form of an image / video / GIF) should be contained in the description.


- If the PR is for some form of improvement or refactoring, the description should state why this has been done, followed by any other information which you may deem useful

Overall, PR description don’t need to be long - just provide enough detail to avoid needing to ask further questions on the submitted PR.


