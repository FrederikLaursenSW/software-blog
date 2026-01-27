# Fast Development Flow When Working with CI/CD

 If you've ever worked with CI for creating pipeline test jobs, you have probably tried the following workflow:

1. Writing some configuration and script code in the .yaml files
2. Committing the changes and waiting for the pipeline to run the job, to see if the changes worked as expected.

This is the fundamental flow that works fine and can't be avoided in many cases.

But if you're unlucky you have probably also experienced this: You need to make changes to a CI job. The job contains anything from 50-300 lines in the script section of the job. Just pure bash written directly in the yaml file.

Let's say your luck is even worse and this CI job is placed in the very end of the pipeline. You are now looking at a typical 30-minute workflow cycle to validate your changes. Imagine what this will cost you, when a bug shows up and your only friend is printing values in the terminal, since you can't run a debugger in your pipeline.

You might be able to disable the rest of the pipeline and only run that single job, but such configuration must be removed again, before merging to main.

Your simple feature change takes an extreme amount of time due to this "validating in the pipeline" workflow.

## Solution

Move the script logic from the yaml file into a separate script that you can run locally.

This will ensure that you can iterate fast and avoid the wait time from pushing and running the pipeline.

### Example: Before and After

**Before** - Script embedded in `.gitlab-ci.yml`:
```yaml
deploy_job:
  stage: deploy
  script:
    - echo "Starting deployment..."
    - apt-get update && apt-get install -y jq curl
    - export VERSION=$(cat version.txt)
    - export BUILD_ID=$(date +%s)
    - |
      if [ "$CI_COMMIT_BRANCH" == "main" ]; then
        ENVIRONMENT="production"
        REPLICAS=3
      else
        ENVIRONMENT="staging"
        REPLICAS=1
      fi
    - echo "Deploying to $ENVIRONMENT with $REPLICAS replicas"
    - curl -X POST "https://api.example.com/deploy" \
        -H "Authorization: Bearer $DEPLOY_TOKEN" \
        -d "{\"version\":\"$VERSION\",\"env\":\"$ENVIRONMENT\",\"replicas\":$REPLICAS}"
    - curl "https://api.example.com/status/$BUILD_ID" | jq '.status'
```

**After** - Clean YAML with separate script:
```yaml
deploy_job:
  stage: deploy
  script:
    - python scripts/deploy.py
```

Now you can test locally: `python scripts/deploy.py` with appropriate environment variables set.

Now most things can simply be done with bash, but I wouldn't recommend this approach for complex logic. When the logic becomes complicated, it's valuable to have a real test framework that allows you to write unit tests of the CI logic.

I personally prefer Python with pytest for this task. 

## Dependencies

Now what about dependencies? Because now you have to run things locally. Well you're probably already running your jobs inside docker containers in the pipeline. So to make it easy for you and your co-workers, you can simply make your script check if it's running inside a docker container and if not, then it will prompt you and ask if you wish to run the script inside the container. This, in my opinion, solves all our issues with library dependencies, since new developers can get instant access to the right docker container, without having to search the company github.

Now a last thing you might need is .env variables and secrets. This I haven't solved completely and am very open to suggestions.

So far, a .env-template file that shows the variables needed and a link to where you can obtain the needed values is the best we've got.


And there you have it, a workflow that ensures rapid development and usability.

## Trade-offs

### Benefits
1. **Iteration time drops from 30 minutes to 30 seconds** - Test changes locally without waiting for pipeline execution
2. **Use real debugging tools** - Set breakpoints, inspect variables, and step through code instead of print debugging
3. **Unit tests become practical** - Test edge cases and error handling with proper test frameworks
4. **Code is reusable** - Share logic across multiple CI jobs without duplication
5. **Better code review** - Focus on actual logic changes instead of YAML syntax and formatting

### Costs
1. **Two layers to maintain** - Both YAML configuration and separate script files need to be kept in sync
2. **Documentation and onboarding overhead** - New developers need to learn both the CI system and where/how local scripts work
3. **Team discipline required** - Risk of reverting to inline scripts without clear guidelines on when to abstract
4. **Local environment may diverge** - "Works on my machine" issues if local setup doesn't match CI exactly
5. **Environment-specific bugs still need CI** - Network configs, permissions, and cloud resources can't be replicated locally

## Discussion - It Depends:

So what is left up to discussion? Well should everything be abstracted into a separate Python script? 

Well, maybe. If so we have to separate the pre-script, script and after-script. Using Python this is probably best done in 3 separate functions. So your yaml file calls `python <scriptname> prescript` for the prescript and `python <scriptname> script` for the main script part.

But remember we only really have to do this if we want to test things locally. If the logic in the prescript and after script is small, it might not be worth the effort. Or maybe we want to enforce this pattern to ensure that the yaml doesn't start with 4-5 lines of logic that doesn't need testing and end up with 20-30 lines of pure bash script over time. It's hard to ensure that the next developer will take responsibility and refactor the logic into abstract script files.

## Conclusion

The key to fast CI/CD development is minimizing the feedback loop between making a change and validating it works. By extracting complex pipeline logic into standalone scripts that can be run and tested locally, you transform a 30-minute cycle into a 30-second one. This approach isn't without trade-offs—you'll maintain more files and need discipline to keep things consistent—but for any CI job with substantial logic, the productivity gains far outweigh the costs.

Start small: identify your most painful CI job, extract it to a script, add a few tests, and experience the difference. Your future self (and your team) will thank you.

Looking forward to hearing your opinion.