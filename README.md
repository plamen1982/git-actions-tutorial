# [setup-with-node-js](https://github.com/marketplace/actions/setup-node-js-for-use-with-actions)

# git-actions-tutorial

[Video](https://www.youtube.com/watch?v=E1OunoCyuhY)

- new features in Github in 2019
  - Free private repos
    - free for all users
  - Package Registry
    - Share packages for private and public repos
    - Feed for every repo
  - Dependabot
    - Automatic security remediation pull requests, if you have vulnerable dependencies - free for all developers
  - Desktop 2.0
    - Support rebaseing and stashing and a lots of other great features
  - Pull Panda
    - Helps you and your team to work productively together, stay on top of pull requests, reduce code review time
  - Sponsors
    - Financially support the developers that you follow in GitHub
- Github actions
  - Build in CI/CD deployment
  - Fully integrated in github
  - Respond to any Github event
    - Trigger workflows on any github event, in github is registrated
      - New contributor
      - New Issue
      - New Dependency
      - Run on any event there is a webhook that you can listen, you can trigger workflows with Github actions
  - Community-powered workflows
    - Any workflow is sharable
      - You can make it public
      - You can fork it, edit it, build on top of it and make it yours
  - Sny platform, any language, any cloud
    - Github is agnostic, any language, any platform
- Since GitHub actions are build in
  - No need to manually configure and setup CI/CD
  - You do not need to setup webhooks
  - You do not need to buy hardware
  - Example: Get some instances keep them up to date do security patches in your images, spool down idle machines, none of that is needed... just drop one file in your repo and is working

## Tutorial to start about Actions

- Go to Actions in github
  - You can see on top Node.js (CI/CD for node.js) and Node.js Package(For registering packages)
  - But all the lanuages are support just click load more
  - Github actions are lot more than CI/CD
    - It really can connect all of the different tools and services that you use and it can respond on any event on GitHub
      - It can be creating a new issue
      - It can be a comment
      - Someone new comes to your repository
      - Joints a member
    - You can also automate
      - creating a new contributor
      - you can automatically label issues based on files that are changed in the repository or label a pull
      - You can autmatically clase stale issues
- Setup Node.js with Actions

  - Click on Setup with Node.js
    - Automatically is created YAML workflow file directly in your repository, this is a simple CI template and on the right side you can see a samples with most developers want to do and also there is link with more details about workflows in Github Actions
    - Scroll down to do a matrix build, you can build across **Mac, Windows and Linux**
    - Click Start Commit
      - The from the radio menu choose Create a **new branch** for this commit and start a pull request.Learn more aboult pull request
      - Then click propose new file
      - Then click Create pull request
      - Then you can notice immediately CI is runned
        - Then click on Details link agains every build you can find this link
          - You can see live streaming logs build directly into the experience
          - You can see in the log there is color coding, emoji support as well
          - You can search in logs in the top right corner of the logs console
          - You can click on the ...(triple dots menu) and choose from the three options: Show timestamps, Download log archive, View raw logs
            - Show timestamps it will show timestamps on every single line
  - In your .github/workflow in your main directory of the project you can add different multiple work flows
    - ci.yml
    - greetigs.yml
    - stale.yml
  - If you have larger repository
    - you can do filters on specific paths, so we can have multiple different builds kicked off for that same repository, if I had different apps in it, as well

- ci.yml Example: this below is the content in yaml format

  - name: CI
  - on:
    - pull/request
      - branches
        - master
    - push
      - branches
        - -master
  - jobs:
    - build:
      - strategy:
        - matrix:
          - os:
            - -macOS-latest
            - -ubuntu-latest
            - -windows-latest
          - node-version:
            - -8
            - -10
            - -12
          - runs-on: \${{matrix.os}}
          - steps:
            - uses: actions/checkout@master
            - uses: actions/setup-node@master
              - with:
                - version: \${{matrix.node-version}}
              - run: npm ci
              - run: npm test
          - publish-npm:
            - needs: build
            - runs-on: ubuntu-latest
            - steps:
              - uses: actions/checkout@master
              - uses: actions/setup-node@master
                - with
                  - version: 12
                  - registry-url: https://registry.npmjs.org
                - name: Update package version
                  - run: |
                    - git config user.name "Actions User"
                    - git config user.email noreply@github.com
                    - npm version 1.0.\$(date +%s)
                  - run: npm publish
                    - env:
                      - NODE_AUTH_TOKEN: \${{secrets.npm_token}}
            - publish-github-package-registry:
            - needs: build
            - runs-on: ubuntu-latest
            - steps:
              - uses: actions/checkout@master
              - uses: actions/setup-node@master
                - with
                  - version: 12
                  - registry-url: https://registry.npmjs.org
                  - scope: '@pied-piper-inc'
                - name: Update package version
                  - run: |
                    - git config user.name "Actions User"
                    - git config user.email noreply@github.com
                    - npm version 1.0.\$(date +%s)
                  - run: npm publish
                    - env:
                      - NODE_AUTH_TOKEN: \${{secrets.npm_token}}
  - In the example above
    - **on** - is the trigger event that will start the workflow, and this is work with all events in the github repo, you can also triggers Jenkins or anythings else here. You can check the list of events at [this link here](https://help.github.com/en/actions/automating-your-workflow-with-github-actions/events-that-trigger-workflows#webhook-events). You can imagine to editing on Wiki(that is in github), uploading a package to Github Package Registry, doing a deployment, a comment on a issue, whenever a label is created, any of these events can automate
      - branches
        - -master **-** means that will filter this event only for master. Basically **-** in front of anything means filtering
        - you can filter on file paths, branches and many more things
    - **jobs** - this section from the example is to see all the jobs that are runned, matrix strategy is ruuning right here
      - Here we do little bit more... it's not just building across three different operating systems it's also building multiple different versions of Node as well and this not have to be node it could be GO or any other runtime - this is 9(3 operating systems and 3 node version) total of 9 jobs at the same time kicked off, speed up your testing and getting a much broader set of testing.
        - little down of **jobs** we have **steps** that are running inside this job
        - one of thing you will notice that there are **uses** command in the example so this let you reference an action that lives in another repository and one of the awesome things about this is you don't have to go install an action or deal with any of that or install a GitHub App just to be able to leverage another action. All you have to do is put JavaScript file and that is an action
          - Let's check that kind of actoin we looging at **actions/setup-node@master** - this one is living in actions org in GitHub in **setup-node** repository and the version that we picking is just a branch(in this case **@master**) it can be a branch or a tag
            - Now let's go to this repositry at github.com/actions/setup-node [github.com/actions/setup-node](github.com/actions/setup-node)
              - You can see one more time actions are just a repository
                - We can go to the source of this in folder **src**
                  - then just open setup-node.js and you can see this is just a Javascript file, setting a repository
          - **with** pass a parameters for the actions in **uses**
          - **run** npm ci - is to run a scripts and any tool that have ci GitHub actions support it
    - **needs** - says whenever build complete go ahead and take these(**publish-npm and publish-github-package-registry**) off in parallel
    - **credentials** GitHub has a built in secret store to make it easy to go ahead and deploy to any package registry or to any cloud
      - and you can notice the **env** NODE_AUTH_TOKEN : {{secrets.npm_token}} - **{{secrets.npm_token}}** this is how we reference our secret store
        - The location of secret store is any reposity -> Go to **Setting** in your repo -> then in the site menu **Secrets** and you can see **npm_toke**(for the demo example it can be your own one) the tokens right there,
  - Conclusion:
    - You don't have to worry setting up compute or deploying a server
    - You just go ahead and check a JavaScript file into a repo
    - And anyone in the entire GitHub community can go ahead and leverage that.
      - So GitHub is running that for us in the GitHub Cloud

- Tutorial from **Yarn** and how they use GitHub Action with great example: **Mael Nison - work for yarn**
  - Build workflow for **yarn**
    - Set up job
    - Run actions/checkout@master
    - Use Node.js 10.x
    - User Yarn 1.17.2
    - Check that the Yarn files don't change on new installs
    - Check that the cache doesn't contain obsolete packages
    - Check that the cache files are consistent with their remote sources
    - Check for linting errors
    - CHeck for unmet constraints
    - Complete job
  - Two problems that yarn solved with Github Actions - time of video 19min:19seconds [example with issues](https://github.com/yarnpkg/berry/issues/342) - It comes out they spent more time to figuring out if this is an issue, rather building the project
    - Problems with checking if issue is real issue and if it is reproducible solved with Action when issue is triggered
    - If they want to release new version of the project, then the bot just check where there are opened issues are still valid or not
- You can use **yarn** workflow because these are just Javascript files

- CI/CD for web services: Another core scenario
  - Building a container
  - Testing the container
  - Then deploying it in multiple different clouds

### Web services example

- name Build and Deploy
- on:
  - pull_requirest:
  - push:
    - branches:
      - -master
- jobs:

  - build

    - name: Build
    - runs-on: ununtu-latest
    - steps:
    - -uses: actions/checkout@master

    - -name: Push the image to GPR
    - run:
      - docker login docker.pkg.github.com -u publisher -p "\${GITHUB_PACKAGE_REGISTRY_TOKEN}"
      - docker push docker.pkg.github.com/pied-piper-inc/container-service/app-services:latest
    - env:
      - GITHUB_PACKAGE_REGISTRY_TOKEN: \${{ secrets.GITHUB_PACKAGE_REGISTRY_TOKEN }}

  - deploy_to_aws:

    - name: Deploy on AWD
    - runs-on: ununtu-latest
    - needs: build
    - steps:

      - -name: Deploy to AWS Elastic Container Service
      - -uses: adhereware/aws-ecs@master
      - env:
        - AWS_DEFAULT_OUTPUT: json
        - AWS_DEFAULT_REGION: us-east-1
        - AWS_ECS_CLUSTER: appservices
        - AWS_ECS_TASK_DEFINITION: appservices
        - AWS_ACCESS_KEY_ID: AKIAV5FUZIES5RXGHBXTK
        - AWS_SECRET_ACCESS_KEY: \${{ secrets.AWS_SECRET_ACCESS_KEY }}
        - CONTAINER_IMAGE_NAME: docker.pkg.github.com/pied-piper-inc/container-service/app-services:latest

    - deploy_to_azure:
      - name: Deploy on AWD
      - runs-on: ununtu-latest
      - needs: build
      - steps:
        - -uses: azure/actions/login@master
        - with:
          - creds: \${{ secrets.AZURE_CREDENTIALS }}


        -  -uses: azure/appservice-actions/wabapp-container@master
        - with:
          - app-name: 'actionsdemo'
          - images: 'docker.pkg.github.com/pied-piper-inc/container-service/app-service:latest'

- deploy_to_google_cloud:
  - name: Deploy to Google Cloud
  - runs-on: ununtu-latest
  - needs: build
  - steps:
    - -name: Deploy to Google Kumernetes Engine
    - -uses: tractionware/google-gke@master
    - env:
      - GCLOUD_ZONE: us-central1-a
      - GCLOUD_KUBE_SERVICE_NAME: appservices
      - GCLOUD_KUBE_CONTAINER_NAME: app-services-google
      - GCLOUD_PROJECT: latest-music-215819
      - GCLOUD_KUBERNETES_CLUSTER: appservices-cluster
      - GCLOUD_KEY_FILE: \${{ secrets.GCLOUD_KEY_FILE }}
      - CONTAINER_IMAGE_NAME: docker.pkg.github.com/pied-piper-inc/container-service/app-services:latest

### Now when we make a pull request we will se in the pull request area in github. All these actions done with console with live updating

    - you will notice that alse there is a cold folding(imitate structure of tree), with colorizing, so you can really focus on what is matters when reading the logs. This color lines is what we write exactly in our files, so you can see how you wrote that and how GitHub interpret that
    - Another cool feater is that on warnings, when you hover in the build console in github in pull request section is that you can copy the error or warning there is a popup that you can press and just copy that warning link, and when the page is refresh this is already highlighted

### Launchdarkly example:

- on: push
- name: SupportService Workflow
- jobs:
  - launchDarklyCodeReferences:
    - name: LaunchDarkly Code Refrences
    - runs-on: ubuntu-lastes
    - steps:
      - -name: Checkout repository
      - uses: actions/checkout@master
      - -name: Prepare repository
      - run: git checkout "\${GITHUB_REF:11}"
      - -name: Update LaunchDarkly Code References
      - uses: ./
      - env:
        - GITHUB_TOKEN: \${{ secrets.GITHUB_TOKEN }}
        - LD_ACCESS_TOKEN: \${{ secrets.LD_ACCESS_TOKEN }}
        - LD_PROJE_KEY: support-service

## Summary:

- Git Actions can run in a container or on a virtual machine with support of Linux, MacOS, Windows
- Actions support matrix builds natively, so you can run parallel jobs across operating system version, run time versions or any variable you want to use to multiple your workflows
- Actions have build-in support for real-time feedback in the form of streaming logs, that are searchable, support colors, emojis and are deep linkable that you can use to help pinpoint a log line and send that to a friend or a colleague
- Every repo on GitHub now comes with secure secret store where you can store your secrets update them easily
- Since actions are on GitHub they are easy to write easy to share, you can build on top of someone else Action
- Free for public repositories
- For private repos - pay as you go: check price list at minute **30:28**
- Later this year(2019) you can run on your own machine for free on Linux, MacOS, Windows, Raspberry Pi

### List of open-source projects that use Git Actions you can go to these repos and look at their workflows

- scipy/scipy
- imagemagick/imagemagick
- yarnpkg/berry
- twbs/bootstrap
- ruby/ruby
- junit-team/junit5
- numpy/numpy
