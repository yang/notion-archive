# General development

# POSIX

- fork() doesn’t work for multithreaded processes ([source](https://stackoverflow.com/questions/1235516/fork-in-multi-threaded-program))

# Regular expression alternatives

- Rulex [https://news.ycombinator.com/item?id=31690878](https://news.ycombinator.com/item?id=31690878)
- Melody [https://news.ycombinator.com/item?id=30358554](https://news.ycombinator.com/item?id=30358554)
- Pomsky [https://pomsky-lang.org/](https://pomsky-lang.org/)
- VerbalExpressions [https://github.com/VerbalExpressions/JSVerbalExpressions](https://github.com/VerbalExpressions/JSVerbalExpressions)
- Kleenexp [https://github.com/SonOfLilit/kleenexp](https://github.com/SonOfLilit/kleenexp)
- EggExp [https://www.oilshell.org/release/latest/doc/eggex.html](https://www.oilshell.org/release/latest/doc/eggex.html)
- Compose Regxp [https://github.com/compose-regexp/compose-regexp.js](https://github.com/compose-regexp/compose-regexp.js)
- List [https://github.com/oilshell/oil/wiki/Alternative-Regex-Syntax](https://github.com/oilshell/oil/wiki/Alternative-Regex-Syntax)

# Mac

- Does not have python but seems to either come with python3 or python3 came with Xcode Command Line Tools.
- Don’t need to install all of Xcode for Macports. Just install homebrew first which implicitly pulls Xcode Command LIne Tools.
- System python pip installs to ~/Library/Python/3.8 by default. You can blow this away easily.
- Homebrew can install normal apps, which is its killer feature. For things that Macports also has, Brew has tighter system integration but is limited to single user.
- Macports: use -b for faster binary installs.
- Use pipx for python apps. `sudo port install -b pipx`

# Node libraries

## Exit handling

- node-cleanup: works but unmaintained, doesn’t allow uninstalling single one / stacking them
- Really want something that bundles together both `finally` and `node-cleanup`

## Execution

- execa: mostly nice, but annoying that it doesn’t have a sane shell by default

# Node tooling

- CPU
    - perf profiling: `--perf-basic-prof` or `--perf-basic-prof-functions-only` (less output so less overhead), then use with [FlameGraph](https://github.com/brendangregg/FlameGraph)
- Memory
    - Used to default to 1.5 gigabytes, now up to over 2gb. [https://stackoverflow.com/questions/72415924/has-the-default-memory-limit-of-node-js-changed](https://stackoverflow.com/questions/72415924/has-the-default-memory-limit-of-node-js-changed)
    - `gcore` to take core dump, plus mdb’s `::findjsobjects`
        - `gencore` forks then dumps, so doesn’t pause your process, built for Node: [https://github.com/RuntimeTools/gencore](https://github.com/RuntimeTools/gencore)
    - [https://www.npmjs.com/package/heapdump](https://www.npmjs.com/package/heapdump) - can be loaded in V8
- Misc
    - Coredump on crash: `--abort-on-uncaught-exception`
        - lldb’s llnode can be used, but below talk says it’s not as mature
        - Solaris mdb_v8 works; has commands:
            
            ![image.png](General%20development%20f9d969d11f0d4cbb8f0bd1def913fc60/image.png)
            
    - General diagnostic report: [https://nodejs.org/api/report.html](https://nodejs.org/api/report.html)
    - Start a repl:
- More resources
    - General guide to tooling: [https://github.com/naugtur/node-diagnostics-howtos](https://github.com/naugtur/node-diagnostics-howtos)
    - Great talk on a couple of tools: [https://www.youtube.com/watch?v=O1YP8QP9gLA&list=WL&index=1](https://www.youtube.com/watch?v=O1YP8QP9gLA&list=WL&index=1) ([slides](https://www.slideshare.net/yunongx/debugging-node-in-prod))

# asdf-vm

- No prebuilt binaries for at least Python
- direnv is supposed to help with reshimming
    
    ```jsx
    sudo apt-get install git curl
    
    git clone https://github.com/asdf-vm/asdf.git ~/.asdf --branch v0.8.1
    
    cat >> ~/.bashrc << EOF
    . $HOME/.asdf/asdf.sh
    . $HOME/.asdf/completions/asdf.bash
    EOF
    
    sudo apt-get install dirmngr gpg curl gawk
    
    asdf plugin add nodejs https://github.com/asdf-vm/asdf-nodejs.git
    
    asdf list all nodejs 14
    
    asdf install nodejs 14.17.5
    
    asdf global nodejs 14.17.5
    ```
    

# Python development

- pipx: install Python “end-user programs” in their own virtualenvs. Nice and easy.
    - pipx install cookiecutter poetry
- Scaffolding:
    - Use hypermodern Python:
        
        ```jsx
        cookiecutter gh:cjolowicz/cookiecutter-hypermodern-python --checkout=2021.3.14
        # Then
        poetry install -D nox nox-poetry
        cat > poetry.toml
        [virtualenvs]
        create = true
        in-project = true
        ^D
        ```
        
    - The above still has [roadmap work ahead](https://github.com/cjolowicz/cookiecutter-hypermodern-python/issues/450)
    - PyScaffold: doesn’t include poetry, github actions, etc. uses putup to keep things up to date

# GitHub Actions

## Self-hosted runners

- No clean way to support autoscaling runners yet. (Unlike Jenkins.) [https://040code.github.io/2020/05/25/scaling-selfhosted-action-runners](https://040code.github.io/2020/05/25/scaling-selfhosted-action-runners)
- If using Gerrit, need to set up that integration first.
- One runner runs one job at a time. Can run multiple runners.
- You can filter using labels. This is the only way to achieve scheduling.
- Kubernetes controller: [https://github.com/summerwind/actions-runner-controller](https://github.com/summerwind/actions-runner-controller)

# Ubuntu

- `dpkg-reconfigure -plow unattended-upgrades`: turn on auto security upgrades

# Docker

- Concepts
    - Dockerfiles are instructions to build images.
    - Images: a “snapshot” or “template.” Images can also be snapshots of a running container after manual interaction, but this approach risks reproducibility.
    - Container: the “process” / running instance. Images can be instantiated as containers.  Each has its own data.
    - Dockerfiles are a sequence of instructions, each of which results in a layer, which gets cached.
    - Dockerfiles cannot mount volumes like at runtime.  If you want to reuse cached results across builds, see e.g. [issue](https://github.com/yarnpkg/yarn/issues/749), [gist](https://gist.github.com/armand1m/b8061bcc9e8e9a5c1303854290c7d61e)
    - Network modes: bridge (default), host (no containment), others
        - Easiest but messiest way for container to connect to host is with host network mode.
        - Otherwise, container can connect to host.docker.internal
    - Port forwarding: `-p external:internal`
- Commands
    - `docker images`: list images incl. built and downloaded
    - `docker ps -a`: list all containers (incl. stopped)
    - `docker exec -u root -it jenkins bash`: log in to running Dockerfile
    - `docker build -t plasmic/jenkins:lts .`: from Dockerfile dir
    - `docker image tag f5ccaaaa513d plasmic/jenkins:lts`: rename a built image
    - `docker rmi IMAGE IMAGE`: remove an image
    - `docker rm CONTAINER`: remove a container
    - `docker rm $(docker ps -a -f status=exited -q)`: remove stopped containers
    - `docker rename OLD NEW`: rename a container
    - `docker run -it --entrypoint /bin/bash ubuntu:latest``: run a container based on the ubuntu:latest image, installing if needed
        - `-d`: run in daemon mode
        - `-v /var/run/docker.sock:/var/run/docker.sock`: volume
        - `--entrypoint`: overwrite the entrypoint
        - `--name NAME`: name this container
        - `-it`: interactive tty
        - `-p 8085:8080`: external/internal ports
        - `--mount type=bind,source=/var/jenkins_home,target=/var/jenkins_home`
        - `--restart=unless-stopped`
    - [Nice cheatsheet](https://www.digitalocean.com/community/tutorials/how-to-remove-docker-images-containers-and-volumes)
- Misc
    - [Change ports on running container](https://stackoverflow.com/questions/19335444/how-do-i-assign-a-port-mapping-to-an-existing-docker-container)
    - Volumes slow on MacOS: [docs](https://docs.docker.com/docker-for-mac/osxfs-caching/), [issue](https://stackoverflow.com/questions/57578110/yarn-install-linking-dependencies-is-slow-every-time-using-docker-compose)
    - tar may fail in containers; try bsdtar ([issue](https://github.com/coreos/bugs/issues/1095#issuecomment-292664145))
- Issues
    - yarn linking dependencies extremely slow (without any volumes) ([issue](https://github.com/yarnpkg/yarn/issues/1496), [SO](https://stackoverflow.com/questions/57578110/yarn-install-linking-dependencies-is-slow-every-time-using-docker-compose))

## DinD: Docker-in-Docker

- Needed if you are running CI/CD (Jenkins, GitHub Actions, etc.) inside Docker/Kubernetes
- `docker run --privileged -d docker:dind`
- Obsolete: [https://github.com/jpetazzo/dind](https://github.com/jpetazzo/dind)
- Cloud services do not support DinD which requires privilege, e.g. Fargate ([source](https://about.gitlab.com/blog/2020/07/31/aws-fargate-codebuild-build-containers-gitlab-runner/)), AppEngine ([source](https://github.blog/2020-08-04-github-actions-self-hosted-runners-on-google-cloud/)), etc.

## Tools

- gitpod: instantly spin up a Theia-powered dev container for any github repo, competes with GitHub Codespaces, but has a free tier (50h/mo)

# Jenkins

- Polling SCM every minute can cause a lot of jobs to start, as long as each job takes longer than a minute to run. Better to rely on explicit hooks.
- One job calling another job will occupy two executors. It's thus easy to encounter a deadlock. Two such jobs running can each call into a job that in turn is blocked forever waiting for an executor.
- EC2 plugin: auto-starts if queue overflows, stops after 30m idle

## Jenkinsfile (pipeline as code)

- Great overview: [https://www.youtube.com/watch?v=7KCS70sCoK0&list=WL&index=1](https://www.youtube.com/watch?v=7KCS70sCoK0&list=WL&index=1)
- Syntax:
    - `environment` (directly use as variables)
    - parameters: accessed by `params`
    - `when` to conditionally run; can use with `expression`
    - `post` with `always`, `success`, `failure`, etc.
    - Import external groovy scripts with `load`; has access to globals like `params` etc.
    - Use credentials
- Stages, steps can be run in `parallel` ([source](https://www.jenkins.io/blog/2017/09/25/declarative-1/))

# React Native

- As of 2019-10-19, the general state of React Native docs and resources is tricky / easy to get wrong.
- Expo is supposed to be a nice way to get started.  However:
    - It only works with certain versions of React Native.  You must wait for them to upgrade their SDK to fit newer RN versions (currently at 0.59 but want 0.61 for Fast Refresh).
    - It is its own native app layer, which you cannot modify, so you are stuck with whatever native modules it comes with/exposes.
- Follow the Getting Started docs carefully on exposing the right env vars for Android emulation; you get little hints when things fail.
- The [Getting Started](https://facebook.github.io/react-native/docs/getting-started) says to `npm install -g react-native-cli` and [other resources](https://facebook.github.io/react-native/blog/2018/05/07/using-typescript-with-react-native) say to then run `react-native init MyTSProject` `--``template typescript` but this will fail/create some nonsense project dir.  Some newer docs suggest `--template react-native-template-typescript`  but that will cause an error. You will then learn that react-native-cli is legacy and to use @react-native-community/cli but if you install that globally (rather than run npx) you will get something wrong. The [TS docs](https://facebook.github.io/react-native/docs/typescript) are right, if you just notice that you should run with npx: `npx react-native init MyTSProject --template react-native-template-typescript@6.2.0`.
- react-native-web is just something you can alias as react-native and use it from a webpack project build (has nothing else to do with the actual react-native infrastructure).

# Google Apps Script

- See [+Google Sheets as a Platform](https://paper.dropbox.com/doc/Google-Sheets-as-a-Platform-iBozVhqzxdNiczvAYeoCH)
- Issue found by Chung:
    
    > Man, when I open Yang's spreadsheet as app-domino@cascal.com, then open the sidebar, we ask GScriptUtil who I am.  GScriptUtil uses Session.getActiveUser(), which thinks I am chungwu@cascal.com instead of app-domino@cascal.com :open_mouth:  [https://developers.google.com/apps-script/reference/base/session#getActiveUser()](https://developers.google.com/apps-script/reference/base/session#getActiveUser())
    > 

# OAuth

## Vocab

- Client ID == consumer key

## Tools

**Python libraries**

- Also: oauthlib, requests-oauth
- google-auth: has flow for simple “installed” apps (incl. console apps).  See gremind2todoist for example.
    
    ```jsx
    flow = InstalledAppFlow.from_client_secrets_file('client.json', SCOPES)
    creds: Credentials = flow.run_local_server(port=3423)
    creds.token # This is the access token
    ```
    
- authlib: works with Flask apps
    
    ```jsx
    oauth = OAuth(app)
    oauth.register(
        name='google',
        client_id=config['GOOGLE_CONSUMER_KEY'],
        client_secret=get_google_oauth_secret(),
        base_url='https://www.googleapis.com/oauth2/v1/',
        access_token_url='https://oauth2.googleapis.com/token',
        access_token_method='POST',
        authorize_url='https://accounts.google.com/o/oauth2/auth',
        authorize_params={
            'scope': ' '.join(GOOGLE_OAUTH_SCOPES),
            'access_type': 'offline',
            'prompt': 'consent'
        },
    )
    oauth.register(
        name='salesforce',
        client_id=config['SF_CONSUMER_KEY'],
        client_secret=get_sf_oauth_secret(),
        base_url='https://www.googleapis.com/oauth2/v1/',
        access_token_url='https://login.salesforce.com/services/oauth2/token',
        access_token_method='POST',
        authorize_url='https://login.salesforce.com/services/oauth2/authorize',
        authorize_params={
            'scope': ' '.join(['id', 'email', 'profile', 'api', 'refresh_token']),
        },
    )
    
    oauth.salesforce.authorize_redirect(redirect_callback_url)
    
    def callback_handler():
      token = oauth.google.authorize_access_token()
    ```
    
- google-api-python-client’s google.oauth2
    
    ```jsx
    return Credentials(
        token=token['access_token'],
        refresh_token=token['refresh_token'],
        id_token=token['id_token'],
        token_uri='https://oauth2.googleapis.com/token',
        client_id=client_id,
        client_secret=client_secret,
        scopes=token['scope'].split()
    )
    
    return Credentials(
        token=token['access_token'],
        refresh_token=token['refresh_token'],
        token_uri='https://login.salesforce.com/services/oauth2/token',
        client_id=client_id,
        client_secret=client_secret,
        scopes=token['scope'].split()
    )
    
    # Then, client-specific code for Google:
    http = RetryHttp(creds)
    build(service_name, version, http=http, **kwargs)
    
    # Client-specific code for Salesforce:
    session = AuthorizedSession(
      credentials=creds, refresh_status_codes=(http.client.UNAUTHORIZED, http.client.FORBIDDEN)
    )
    Salesforce(
      session_id=creds.token, instance_url=instance_url, session=session, version='44.0'
    )
    ```
    

## Example flow:

- Register app with Todoist, and specify a redirect callback URL like http://localhost:3423
    - This gives you a CLIENT_ID and CLIENT_SECRET
- Direct user’s browser to [https://todoist.com/oauth/authorize?client_id=CLIENT_ID&scope=SCOPES&state=SECRETSTATE](https://todoist.com/oauth/authorize?client_id=CLIENT_ID&scope=SCOPES&state=SECRETSTATE)
    - SECRETSTATE is a transient secret for just this particular auth request, echo’d back during the callback redirect
- Once user authorizes, they are directed to http://localhost:3423/?state=SECRETSTATE&code=SECRETCODE (where SECRET is what it was before)
- Exchange the SECRETCODE authorization code for an access token:
    
    ```jsx
    curl "https://todoist.com/oauth/access_token" \
        -d "client_id=CLIENT_ID" \
        -d "client_secret=CLIENT_SECRET" \
        -d "code=SECRETCODE" \
        -d "redirect_uri=https://example.com"
    ```
    

# IntelliJ/Jetbrains/Webstorm/etc.

- Searching for e.g. `foo()` will match even `commonModule.foo()`
- Can’t parameterize a statement block
- Doesn’t seem to work on TSX substitutions

# MyPy

Experience as of 2019-02-06:

- Oddly seems to be happy with types whose names match?
- Mismatch between what PyCharm reports and what MyPy reports.  They have different implementations.   PyCharm also won’t heed the mypy.ini configurations, such as stricter checking.
- No algebraic data types / discriminated unions that support exhaustive branch matching, but you can do [https://github.com/python/mypy/issues/5818](https://github.com/python/mypy/issues/5818)
- Recursive types [coming soon](https://github.com/python/mypy/issues/731), currently e.g. `JSON = Union[Dict[str, "JSON"], List["JSON"], str, int, float, bool, None]` (from that issue page) gives you “error: Recursive types not fully supported yet, nested types replaced with "Any"”
- You should enable the flags set by —strict, or else you are missing out.
    - `check_untyped_defs` is important for type checking the lines of code within a function
- Not strict about a lot of things - many things slip by the type checker.

# Parsing

## Experience with parser tools

- Packrattle: GLL parser
    - Easily explodes in complexity, hard to determine why
    - Couldn’t fully understand/debug the parser in general—possibly immature/buggy
    - Returns just one parse tree
- Nearley: Earley parser
    - Returns all possible parse trees, which can be powerful but is often not what you want / too slow.  Wish there were a way to eagerly prefer certain trees over others.
    - Was considering using this for writing patterns like `A*AB`, which most parsers are greedy about and won’t match against “AAB”.  Problem is, I was only looking for these patterns within input files to replace occurrences with something else—if there’s no such pattern, I still want to parse the input fully (as a different parse tree), hence Earley parser produces 2 parse trees for each occurrence, one with `A*AB`  recognized and one without.  Instead, turned to simpler parsers like PEG and wrote `([^A]|A[^B])*AB`.
    - Has some tooling, which is nice
- PEGjs: PEG packrat parser
    - Favorite tool so far
    - Simple and predictable
    - Fast lexerless parsing
    - Precedence in alts is handy
    - Has some tooling, which is nice

# SQL

## Batch updates

You cannot reference the outer `table` in the subquery.:

```
UPDATE target
SET …
FROM (
  SELECT ...
  FROM inner
  WHERE inner.foo = target.foo
) t

```

You must join with a batch query that limits the subquery:

```
UPDATE target
SET ...
FROM (
  SELECT ...
  FROM inner
  WHERE inner.foo = target.foo
) t

```

# Online schema migrations

Most general requirements:

- Zero downtime
- Complex migrations
- Multiple versions of code live in prod (across different app servers)
- Ability to roll back

Approach:

- Write to both old & new tables (can be multiple)
- Replicate old tables to new tables (to backfill—can do this offline)
- Switch reads from old to new tables
- Remove old tables

More specific approach to prevent code complexity and promoting safety:

- Writes to old tables should be auto replicated by “DB layer” as writes to new tables (rather than have application layer write to both everywhere)
    - DB layer can still live in the application, or it could live in something like gh-ost
- Replicate old tables to new tables (to backfill—can do this offline)
- Start reading from both old and new tables and compare results for safety
- Write to new tables, this time replicating to the old tables. This means changing/refactoring all the app code. Most complexity / app changes here.
- If comparisons are still safe, stop reading from both and read from just new.
- Remove old tables

More specific approach for ease, with less safety:

- Writes to old tables should be auto replicated by “DB layer” as writes to new tables
- Replicate old tables to new tables
- Once done, switch to reads and writes together from old to new tables, replicating to old tables
- Remove old tables

[Stripe migration](https://stripe.com/blog/online-migrations)

## gh-ost

- Replicates old to new shadow table asynchronously
    - Uses binary log shipping rather than triggers (like FB’s and PT’s tools do)
- When ready, ‘cut-over’ to new table
- App is supposed to eithre:
    - be able to handle old and new versions of the code, or
    - you ensure app versions are strictly before/after the cut-over