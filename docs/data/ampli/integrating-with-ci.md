---
title: Validate in CI
description: Use Amplitude Data with your continuous integration (CI) workflow. 
template: guide.html
---

Amplitude Data works best when integrated into your continuous integration (CI) workflow, running continuously alongside your test suite. We integrate with all common CI providers and can be configured for custom environments.

After you've added Amplitude Data to your CI environment, Amplitude Data will verify your analytics against every build.

!!!note

    Amplitude Data checks your analytics implementation against the tracking plan version that's currently checked in. If your team made changes to your tracking plan since the last call to `ampli pull`, those changes will not cause a failure in CI.

### Step 1: Create an API token

Create an [API token](https://data.amplitude.com/settings/api-tokens) in your account or set up another account to be used for CI. Ampli uses this token for authentication when running inside CI to update your tracking plan's implementation status.

!!!warning

    Keep your token secret. Your token has global permissions on your account.

### Step 2: Configure a CI environment variable

Create an environment variable in your CI service called `ITLY_KEY` and set it to the API token you created. You'll use this environment variable to pass the token to `ampli status` when it runs inside CI.

For example, this is what the [Netlify](https://docs.netlify.com/configure-builds/environment-variables/) environment variables screen would look like.

![screen shot of the ITLY_KEY variable in Netlify](../../assets/images/data-netlify-environment-variables.png)

Read the documentation for your CI service to get step-by-step instructions:

- [Bitbucket Pipelines](https://confluence.atlassian.com/bitbucket/variables-in-pipelines-794502608.html)
- [CircleCI](https://circleci.com/docs/2.0/env-vars/)
- [GitLab CI](https://docs.gitlab.com/ee/ci/variables/)
- [Jenkins](https://jenkins.io/doc/pipeline/tour/environment/#credentials-in-the-environment)
- [Travis CI](https://docs.travis-ci.com/user/environment-variables/)

### Step 3: Prepare your project

By now, you've run `ampli pull` and `ampli status` in your project's root folder. The folder contains an `ampli.json` file with metadatata about the current state of the Itly SDK in your project. When you run `ampli status`, on your local machine or soon in CI, Ampli will verify your analytics against this file.

For non-JavaScript and non-TypeScript projects, this is all the configuration that's needed.

Optionally, for JavaScript and TypeScript projects, you may decide to install Ampli locally as a dev dependency. Installing Ampli locally in the project's node_modules folder simplifies installation and usage of Ampli for your team and CI environment. There are two steps to this.

#### Install Ampli as a dev dependency

To install Ampli locally, run `npm install @amplitude/ampli -D`.

### Step 4: Run Ampli in CI

To integrate Ampli with your CI system, modify your CI configuration to run [`ampli status`](cli.md#ampli-status) as part of the build process. We've made it easy by creating [Docker Containers](https://hub.docker.com/u/amplitudeinc) that you can use which include dependencies. Some runtimes have their own containers:

- `amplitudeinc/ampli`
- `amplitudeinc/ampli-swift`
- `amplitudeinc/ampli-dotnet`
- `amplitudeinc/ampli-all` (for convenience this contains everything but is larger in size)

The following examples are for Bitbucket Pipelines but you can use the same images in any CI system that supports containers.

=== "ampli"

    The [ampli image](https://hub.docker.com/r/amplitudeinc/ampli) can be used to verify the following SDK runtimes.

    - android-java
    - android-kotlin
    - browser-javascript
    - browser-typescript
    - ios-objc
    - jre-java
    - node-javascript
    - node-typescript
    - python (2 & 3)
    - ruby

    ```yaml
    # bitbucket-pipelines.yml
    # Runtimes:
    # android-java, android-kotlin, browser-javascript, browser-javascript,
    # ios-objc, jre-java, node-javascript, node-typescript, python, ruby
    - step:
        name: Run 'ampli status' in CI
        image: amplitudeinc/ampli
        script:
          - ampli status [-u] -t $ITLY_KEY
    ```

=== "Swift"

    The [ampli-swift image](https://hub.docker.com/r/amplitudeinc/ampli-swift) can be used to verify Swift runtimes.

    ```yaml
    # bitbucket-pipelines.yml
    # Runtimes: Swift
    - step:
        name: Run 'ampli status' in CI
        image: amplitudeinc/ampli-swift
        script:
          - ampli status [-u] -t $ITLY_KEY
    ```

=== ".NET"

    The [ampli-dotnet image](https://hub.docker.com/r/amplitudeinc/ampli-dotnet) can be used to verify .NET C#.

    ```yaml
    # bitbucket-pipelines.yml
    # Runtimes: .NET C#
    - step:
        name: Run 'ampli status' in CI
        image: amplitudeinc/ampli-dotnet
        script:
          - ampli status [-u] -t $ITLY_KEY
    ```

    </TabItem>
    <TabItem value="ruby">

    The ampli-ruby image can be used to verify Ruby.

    ```yaml
    # bitbucket-pipelines.yml
    # Runtimes: Ruby
    - step:
        name: Run 'ampli status' in CI
        image: amplitudeinc/ampli-ruby
        script:
          - ampli status [-u] -t $ITLY_KEY
    ```

You should now have Ampli running inside your CI system. Congratulations!
