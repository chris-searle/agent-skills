## Commands available to verify code in this repository:

- build: `mvn clean package -DskipTests`
- lint: `npm run lint`
- run single unit / component test: `mvn surefire:test -Dtest=<TestClassName>#<testMethodName>`
- Run full unit / component test suite: `mvn surefire:test`
- run full integration / e2e test suite: `./scripts/run-integration-tests.sh`

## Full verification workflow on work completion or checkpoints during larger work:

When work is ready to complete or when a checkpoint is reached during larger work, run the following commands in sequence to verify your code changes:
build -> lint -> run full unit / component test suite -> run full integration / e2e test suite

Do not substitute alternative test runners, linters, or build tools, or shortcuts when one of these covers the goal.
