# Sock Shop

Sock Shop Architecture:

![Sock Shop Architecture](https://raw.githubusercontent.com/microservices-demo/microservices-demo.github.io/40d8170161d2f81cc6524f8aa137c8e9f9131ecd/assets/Architecture.png
"Sock Shop Architecture")

Open the Jenkins X Prow:

```bash
firefox https://deck.jx.mylabs.dev &
```

Deploy the Sock Shop microservices to the `jx-production` and `jx-staging`.
Only the `front-end` microservice will be modified and handled by Jenkins X.
The rest of microservices will be installed form default Sock Shop
k8s manifests.
Modifications can be seen here: [https://github.com/ruzickap/k8s-jenkins-x/blob/master/files/complete-demo.yaml](https://github.com/ruzickap/k8s-jenkins-x/blob/master/files/complete-demo.yaml)

```bash
sed "s/namespace: sock-shop/namespace: jx-production/" files/complete-demo.yaml | kubectl apply -f -
sed "s/namespace: sock-shop/namespace: jx-staging/"    files/complete-demo.yaml | kubectl apply -f -
```

Output:

```text
deployment.extensions/carts-db created
service/carts-db created
deployment.extensions/carts created
service/carts created
deployment.extensions/catalogue-db created
service/catalogue-db created
deployment.extensions/catalogue created
service/catalogue created
deployment.extensions/orders-db created
service/orders-db created
deployment.extensions/orders created
service/orders created
deployment.extensions/payment created
service/payment created
deployment.extensions/queue-master created
service/queue-master created
deployment.extensions/rabbitmq created
service/rabbitmq created
deployment.extensions/shipping created
service/shipping created
deployment.extensions/user-db created
service/user-db created
deployment.extensions/user created
service/user created
deployment.extensions/carts-db created
service/carts-db created
deployment.extensions/carts created
service/carts created
deployment.extensions/catalogue-db created
service/catalogue-db created
deployment.extensions/catalogue created
service/catalogue created
deployment.extensions/orders-db created
service/orders-db created
deployment.extensions/orders created
service/orders created
deployment.extensions/payment created
service/payment created
deployment.extensions/queue-master created
service/queue-master created
deployment.extensions/rabbitmq created
service/rabbitmq created
deployment.extensions/shipping created
service/shipping created
deployment.extensions/user-db created
service/user-db created
deployment.extensions/user created
service/user created
```

Fork the "Sock Shop" repository `front-end`:

```bash
mkdir tmp
cd tmp
hub clone "microservices-demo/front-end"
```

Output:

```text
Cloning into 'front-end'...
remote: Enumerating objects: 1156, done.
remote: Total 1156 (delta 0), reused 0 (delta 0), pack-reused 1156
Receiving objects: 100% (1156/1156), 47.86 MiB | 8.27 MiB/s, done.
Resolving deltas: 100% (642/642), done.
```

The `jx import` command expects, that the `Dockerfile` will expose single port `8080`.
Therefore I need to change this in `front-end` Docker file to be able to work
with `jx import` smoothly:

```bash
sed -i 's/8079/8080/' front-end/Dockerfile
git -C front-end add Dockerfile
git -C front-end commit -m "Port in Dockerfile changed from 8079 to 8080"
```

Output:

```text
[master b7f3b45] Port in Dockerfile changed from 8079 to 8080
 1 file changed, 2 insertions(+), 2 deletions(-)
```

Fork the repository and push the changes there:

```bash
hub -C "front-end" fork
git -C front-end push ruzickap
rm -rf "front-end"
```

Output:

```text
Updating ruzickap
From https://github.com/microservices-demo/front-end
 * [new branch]      abuehrle-patch-2                 -> ruzickap/abuehrle-patch-2
 * [new branch]      add-localserver-option           -> ruzickap/add-localserver-option
 * [new branch]      add-weave-imgs                   -> ruzickap/add-weave-imgs
 * [new branch]      add-zipkin                       -> ruzickap/add-zipkin
 * [new branch]      add_zipkin2                      -> ruzickap/add_zipkin2
 * [new branch]      adduser                          -> ruzickap/adduser
 * [new branch]      cpu_usage                        -> ruzickap/cpu_usage
 * [new branch]      deals-integration                -> ruzickap/deals-integration
 * [new branch]      documentation/commands           -> ruzickap/documentation/commands
 * [new branch]      enhancement/brander              -> ruzickap/enhancement/brander
 * [new branch]      enhancement/opentracing-frontend -> ruzickap/enhancement/opentracing-frontend
 * [new branch]      fix-42                           -> ruzickap/fix-42
 * [new branch]      hackathon-fixed                  -> ruzickap/hackathon-fixed
 * [new branch]      master                           -> ruzickap/master
 * [new branch]      remove-metric-cardinality        -> ruzickap/remove-metric-cardinality
new remote: ruzickap
Warning: Permanently added '[ssh.github.com]:443,[192.30.253.123]:443' (RSA) to the list of known hosts.
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 4 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 332 bytes | 332.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Resolving deltas: 100% (2/2), completed with 2 local objects.
To github.com:ruzickap/front-end.git
   b6604d9..b7f3b45  master -> master
```

You can see the code in: [https://github.com/ruzickap/front-end](https://github.com/ruzickap/front-end)

Import application into Jenkins X:

```bash
jx import --git-username=ruzickap --name="front-end" --url="https://github.com/ruzickap/front-end"
cd ..
```

Output:

```text
Using Git user name: ruzickap
performing pack detection in folder /home/pruzicka/git/k8s-jenkins-x/tmp/front-end
--> Draft detected Coq (51.985930%)
--> Could not find a pack for Coq. Trying to find the next likely language match...
--> Draft detected SVG (16.817856%)
--> Could not find a pack for SVG. Trying to find the next likely language match...
--> Draft detected HTML (16.808026%)
--> Could not find a pack for HTML. Trying to find the next likely language match...
--> Draft detected JavaScript (9.282228%)
selected pack: /home/pruzicka/.jx/draft/packs/github.com/jenkins-x-buildpacks/jenkins-x-kubernetes/packs/javascript
replacing placeholders in directory /home/pruzicka/git/k8s-jenkins-x/tmp/front-end
app name: front-end, git server: github.com, org: ruzickap, Docker registry org: ruzickap
skipping directory "/home/pruzicka/git/k8s-jenkins-x/tmp/front-end/.git"
Let's ensure that we have an ECR repository for the Docker image ruzickap/front-end
Creating GitHub webhook for ruzickap/front-end for url http://hook.jx.mylabs.dev/hook

Watch pipeline activity via:    jx get activity -f front-end -w
Browse the pipeline log via:    jx get build logs ruzickap/front-end/master
You can list the pipelines via: jx get pipelines
When the pipeline is complete:  jx get applications

For more help on available commands see: https://jenkins-x.io/developing/browsing/

Note that your first pipeline may take a few minutes to start while the necessary images get downloaded!
```

Browse the pipeline log via:

```bash
jx get build logs --wait=true ruzickap/front-end/master
```

Output:

```text
The selected pipeline didn't start, let's wait a bit
Build logs for ruzickap/front-end/master #1

waiting for stage meta-pipeline : container step-credential-initializer-mxv7z to start...


Showing logs for build ruzickap/front-end/master #1 stage meta-pipeline and container step-credential-initializer-mxv7z
{"level":"warn","ts":1571326350.6521983,"logger":"fallback-logger","caller":"logging/config.go:69","msg":"Fetch GitHub commit ID from kodata failed: \"ref: refs/heads/master\" is not a valid GitHub commit ID"}
{"level":"info","ts":1571326350.6530662,"logger":"fallback-logger","caller":"creds-init/main.go:40","msg":"Credentials initialized."}

waiting for stage meta-pipeline : container step-working-dir-initializer-jvx69 to start...


Showing logs for build ruzickap/front-end/master #1 stage meta-pipeline and container step-working-dir-initializer-jvx69
{"level":"warn","ts":1571326353.7272487,"logger":"fallback-logger","caller":"logging/config.go:69","msg":"Fetch GitHub commit ID from kodata failed: open /var/run/ko/HEAD: no such file or directory"}
{"level":"info","ts":1571326353.7283375,"logger":"fallback-logger","caller":"bash/main.go:65","msg":"Successfully executed command \"mkdir -p /workspace/source\""}

waiting for stage meta-pipeline : container step-place-tools to start...


Showing logs for build ruzickap/front-end/master #1 stage meta-pipeline and container step-place-tools

waiting for stage meta-pipeline : container step-git-source-meta-ruzickap-front-end-master-hxhmf to start...


Showing logs for build ruzickap/front-end/master #1 stage meta-pipeline and container step-git-source-meta-ruzickap-front-end-master-hxhmf
{"level":"warn","ts":1571326362.0682209,"logger":"fallback-logger","caller":"logging/config.go:69","msg":"Fetch GitHub commit ID from kodata failed: \"ref: refs/heads/master\" is not a valid GitHub commit ID"}
{"level":"info","ts":1571326368.849677,"logger":"fallback-logger","caller":"git/git.go:102","msg":"Successfully cloned https://github.com/ruzickap/front-end.git @ bc24537484dfa7aed349edf20f63034e3d4f72f6 in path /workspace/source"}

waiting for stage meta-pipeline : container step-git-merge to start...


Showing logs for build ruzickap/front-end/master #1 stage meta-pipeline and container step-git-merge
Using SHAs from PULL_REFS=master:bc24537484dfa7aed349edf20f63034e3d4f72f6
WARNING: no SHAs to merge, falling back to initial cloned commit

waiting for stage meta-pipeline : container step-merge-pull-refs to start...


Showing logs for build ruzickap/front-end/master #1 stage meta-pipeline and container step-merge-pull-refs
SKIP merge-pull-refs: Nothing to merge

waiting for stage meta-pipeline : container step-create-effective-pipeline to start...


Showing logs for build ruzickap/front-end/master #1 stage meta-pipeline and container step-create-effective-pipeline
Deleting and cloning the Jenkins X versions repo
Cloning the Jenkins X versions repo https://github.com/jenkins-x/jenkins-x-versions.git with ref refs/heads/master to /builder/home/.jx/jenkins-x-versions
Effective pipeline written to jenkins-x-effective.yml

waiting for stage meta-pipeline : container step-create-tekton-crds to start...


Showing logs for build ruzickap/front-end/master #1 stage meta-pipeline and container step-create-tekton-crds
running command: echo \$(jx-release-version) > VERSION

running command: jx step tag --version \$(cat VERSION)
Updating chart version in charts/front-end/Chart.yaml to 0.3.13
Updating repository in charts/front-end/values.yaml to 822044714040.dkr.ecr.eu-central-1.amazonaws.com/ruzickap/front-end
Updating tag in charts/front-end/values.yaml to 0.3.13
Tag v0.3.13 created and pushed to remote origin

WARNING: failed to find secret kaniko-secret in namespace jx: secrets "kaniko-secret" not found
PipelineActivity for ruzickap-front-end-master-1
Applying changes
upserted PipelineResource ruzickap-front-end-master for the git repository https://github.com/ruzickap/front-end.git
upserted Task ruzickap-front-end-master-from-build-pack-1
upserted Pipeline ruzickap-front-end-master-1
created PipelineRun ruzickap-front-end-master-1
created PipelineStructure ruzickap-front-end-master-1

Showing logs for build ruzickap/front-end/master #1 stage from-build-pack and container step-credential-initializer-r9p2h
{"level":"warn","ts":1571326394.9351523,"logger":"fallback-logger","caller":"logging/config.go:69","msg":"Fetch GitHub commit ID from kodata failed: \"ref: refs/heads/master\" is not a valid GitHub commit ID"}
{"level":"info","ts":1571326394.9359286,"logger":"fallback-logger","caller":"creds-init/main.go:40","msg":"Credentials initialized."}

waiting for stage from-build-pack : container step-working-dir-initializer-x95mf to start...


Showing logs for build ruzickap/front-end/master #1 stage from-build-pack and container step-working-dir-initializer-x95mf
{"level":"warn","ts":1571326397.5499449,"logger":"fallback-logger","caller":"logging/config.go:69","msg":"Fetch GitHub commit ID from kodata failed: open /var/run/ko/HEAD: no such file or directory"}
{"level":"info","ts":1571326397.550805,"logger":"fallback-logger","caller":"bash/main.go:65","msg":"Successfully executed command \"mkdir -p /workspace/source\""}

waiting for stage from-build-pack : container step-place-tools to start...


Showing logs for build ruzickap/front-end/master #1 stage from-build-pack and container step-place-tools

waiting for stage from-build-pack : container step-git-source-ruzickap-front-end-master-5h7d7 to start...


Showing logs for build ruzickap/front-end/master #1 stage from-build-pack and container step-git-source-ruzickap-front-end-master-5h7d7
{"level":"warn","ts":1571326427.4969535,"logger":"fallback-logger","caller":"logging/config.go:69","msg":"Fetch GitHub commit ID from kodata failed: \"ref: refs/heads/master\" is not a valid GitHub commit ID"}
{"level":"info","ts":1571326430.9216669,"logger":"fallback-logger","caller":"git/git.go:102","msg":"Successfully cloned https://github.com/ruzickap/front-end.git @ v0.3.13 in path /workspace/source"}

waiting for stage from-build-pack : container step-git-merge to start...


Showing logs for build ruzickap/front-end/master #1 stage from-build-pack and container step-git-merge
Using SHAs from PULL_REFS=master:bc24537484dfa7aed349edf20f63034e3d4f72f6
WARNING: no SHAs to merge, falling back to initial cloned commit

waiting for stage from-build-pack : container step-setup-jx-git-credentials to start...


Showing logs for build ruzickap/front-end/master #1 stage from-build-pack and container step-setup-jx-git-credentials
Generated Git credentials file /workspace/xdg_config/git/credentials

waiting for stage from-build-pack : container step-build-npmrc to start...


Showing logs for build ruzickap/front-end/master #1 stage from-build-pack and container step-build-npmrc
WARNING: failed to find Secret npm-token in namespace jx

waiting for stage from-build-pack : container step-build-npm-install to start...


Showing logs for build ruzickap/front-end/master #1 stage from-build-pack and container step-build-npm-install
npm WARN deprecated istanbul@0.4.5: This module is no longer maintained, try this instead:
npm WARN deprecated   npm i nyc
npm WARN deprecated Visit https://istanbul.js.org/integrations for other alternatives.
npm WARN deprecated json3@3.3.2: Please use the native JSON object instead of JSON 3
npm WARN deprecated formatio@1.1.1: This package is unmaintained. Use @sinonjs/formatio instead
npm WARN deprecated superagent@2.3.0: Please note that v5.0.1+ of superagent removes User-Agent header by default, therefore you may need to add it yourself (e.g. GitHub blocks requests without a User-Agent header).  This notice will go away with v5.0.2+ once it is released.
npm WARN deprecated samsam@1.1.2: This package has been deprecated in favour of @sinonjs/samsam
npm WARN deprecated samsam@1.1.3: This package has been deprecated in favour of @sinonjs/samsam
npm notice created a lockfile as package-lock.json. You should commit this file.
added 246 packages from 706 contributors and audited 489 packages in 5.614s
found 4 vulnerabilities (3 low, 1 critical)
  run `npm audit fix` to fix them, or `npm audit` for details

waiting for stage from-build-pack : container step-build-npm-test to start...


Showing logs for build ruzickap/front-end/master #1 stage from-build-pack and container step-build-npm-test

> microservices-demo-front-end@0.0.1 test /workspace/source
> istanbul cover node_modules/.bin/_mocha -- test/*_test.js test/api/*_test.js



  helpers
    #errorHandler
      the rendered JSON
        ✓ includes an error message
        ✓ includes an error object
        ✓ returns the right HTTP status code
      given the error has no status defined
        ✓ responds with HTTP status code 500
    #respondSuccessBody
      ✓ renders the given body with status 200 OK
    #respondStatusBody
      ✓ sets the proper status code & body
    #respondStatus
      ✓ sets the proper status code
    #simpleHttpRequest
      ✓ performs a GET request to the given URL
      given the external service responds with success
        ✓ yields the external service response to the response body
        ✓ responds with success
      given the external service fails
        ✓ invokes the given callback with an error object
    #getCustomerId
      given the environment is development
        - returns the customer id from the query string
      given a customer id set in session
        - returns the customer id from the session
      given no customer id set in the cookies
        given no customer id set session
          - throws a 'User not logged in' error

  endpoints
    catalogueUrl
      ✓ points to the proper endpoint
    tagsUrl
      ✓ points to the proper endpoint
    cartsUrl
      ✓ points to the proper endpoint
    ordersUrl
      ✓ points to the proper endpoint
    customersUrl
      ✓ points to the proper endpoint
    addressUrl
      ✓ points to the proper endpoint
    cardsUrl
      ✓ points to the proper endpoint
    loginUrl
      ✓ points to the proper endpoint
    registerUrl
      ✓ points to the proper endpoint


  20 passing (69ms)
  3 pending

=============================================================================
Writing coverage object [/workspace/source/coverage/coverage.json]
Writing coverage reports at [/workspace/source/coverage]
=============================================================================

=============================== Coverage summary ===============================
Statements   : 65.96% ( 31/47 )
Branches     : 22.73% ( 5/22 )
Functions    : 75% ( 9/12 )
Lines        : 65.22% ( 30/46 )
================================================================================

waiting for stage from-build-pack : container step-build-container-build to start...


Showing logs for build ruzickap/front-end/master #1 stage from-build-pack and container step-build-container-build
INFO[0000] Downloading base image node:10-alpine
INFO[0001] Error while retrieving image from cache: getting file info: stat /workspace/sha256:5cf3bedcacc2b6ae46504c4c165c74ae88b5fb5eb67f9dfcc3b0cca193f366ff: no such file or directory
INFO[0001] Downloading base image node:10-alpine
INFO[0002] cmd: EXPOSE
INFO[0002] Adding exposed port: 8080/tcp
INFO[0002] Checking for cached layer 822044714040.dkr.ecr.eu-central-1.amazonaws.com/todo/cache:bd87bc6d085113780d96ba07a293b80fe884174ca0eacf65c175e0ba3c4cba94...
INFO[0002] No cached layer found for cmd RUN addgroup mygroup && adduser -D -G mygroup myuser && mkdir -p /usr/src/app && chown -R myuser /usr/src/app
INFO[0002] Unpacking rootfs as cmd RUN addgroup mygroup && adduser -D -G mygroup myuser && mkdir -p /usr/src/app && chown -R myuser /usr/src/app requires it.
INFO[0004] Taking snapshot of full filesystem...
INFO[0005] ENV NODE_ENV "production"
INFO[0005] No files changed in this command, skipping snapshotting.
INFO[0005] ENV PORT 8080
INFO[0005] No files changed in this command, skipping snapshotting.
INFO[0005] EXPOSE 8080
INFO[0005] cmd: EXPOSE
INFO[0005] Adding exposed port: 8080/tcp
INFO[0005] No files changed in this command, skipping snapshotting.
INFO[0005] RUN addgroup mygroup && adduser -D -G mygroup myuser && mkdir -p /usr/src/app && chown -R myuser /usr/src/app
INFO[0005] cmd: /bin/sh
INFO[0005] args: [-c addgroup mygroup && adduser -D -G mygroup myuser && mkdir -p /usr/src/app && chown -R myuser /usr/src/app]
INFO[0005] Taking snapshot of full filesystem...
INFO[0005] WORKDIR /usr/src/app
INFO[0005] cmd: workdir
INFO[0005] Changed working directory to /usr/src/app
INFO[0005] No files changed in this command, skipping snapshotting.
INFO[0005] Pushing layer 822044714040.dkr.ecr.eu-central-1.amazonaws.com/todo/cache:bd87bc6d085113780d96ba07a293b80fe884174ca0eacf65c175e0ba3c4cba94 to cache now
INFO[0005] Using files from context: [/workspace/source/package.json]
INFO[0005] COPY package.json /usr/src/app/
INFO[0005] Taking snapshot of files...
INFO[0005] Using files from context: [/workspace/source/yarn.lock]
INFO[0005] COPY yarn.lock /usr/src/app/
INFO[0005] Taking snapshot of files...
INFO[0005] RUN chown myuser /usr/src/app/yarn.lock
INFO[0005] cmd: /bin/sh
INFO[0005] args: [-c chown myuser /usr/src/app/yarn.lock]
INFO[0005] Taking snapshot of full filesystem...
INFO[0006] USER myuser
INFO[0006] cmd: USER
INFO[0006] No files changed in this command, skipping snapshotting.
INFO[0006] RUN yarn install
INFO[0006] cmd: /bin/sh
INFO[0006] args: [-c yarn install]
INFO[0006] Pushing layer 822044714040.dkr.ecr.eu-central-1.amazonaws.com/todo/cache:8d57e812fc750aa16ea221c05ce0f66849985b956fbcd185393e5845859caf77 to cache now
yarn install v1.17.3
[1/4] Resolving packages...
[2/4] Fetching packages...
[3/4] Linking dependencies...
[4/4] Building fresh packages...
success Saved lockfile.
Done in 3.39s.
INFO[0010] Taking snapshot of full filesystem...
INFO[0012] Pushing layer 822044714040.dkr.ecr.eu-central-1.amazonaws.com/todo/cache:65687f541e775add659f8825098e577c070b7aae5b46536acdb2e2e777c52970 to cache now
INFO[0012] Using files from context: [/workspace/source]
INFO[0013] COPY . /usr/src/app
INFO[0013] Taking snapshot of files...
INFO[0016] CMD ["/usr/local/bin/npm", "start"]
INFO[0016] No files changed in this command, skipping snapshotting.
WARN[0016] error uploading layer to cache: failed to push to destination 822044714040.dkr.ecr.eu-central-1.amazonaws.com/todo/cache:bd87bc6d085113780d96ba07a293b80fe884174ca0eacf65c175e0ba3c4cba94: NAME_UNKNOWN: The repository with name 'todo/cache' does not exist in the registry with id '822044714040'
2019/10/17 15:34:17 existing blob: sha256:50958466d97a8d1ceedc4e851de4fc43e3e3e0618e548597f63f0b546cb8797c
2019/10/17 15:34:17 existing blob: sha256:56174ae7ed1d5c96ca66882e205154d7067a1a8a325bd8fdec6d5f933f66e0a3
2019/10/17 15:34:17 existing blob: sha256:284842a36c0d8eea230cfd5e7a4a6b450fcd63d1c4737f236a91e1671455050a
2019/10/17 15:34:17 existing blob: sha256:e7c96db7181be991f19a9fb6975cdbbd73c65f4a2681348e63a141a2192a5f10
2019/10/17 15:34:17 pushed blob: sha256:9163581487edf11423ffc421fda77d06cf6d0957bffc4300ee240a4bda374b53
2019/10/17 15:34:17 pushed blob: sha256:5926b01463ec4a87cfba2318b6e4dab94db7175e9a54763a66c3004ac2bce8b1
2019/10/17 15:34:17 pushed blob: sha256:78d96deb504edacdfa883ba1e73171270e1e6d4973b21838f1962a4bca74e8cf
2019/10/17 15:34:17 pushed blob: sha256:d8331e67b06655ef960a19dc626c7d20769cd51c8db9aafa4a948f6c6678bf8f
2019/10/17 15:34:17 pushed blob: sha256:d1f29b270034b2c82ee12c29bc12f56c2dbe8fec63b17f3e04e4f6b6ef807675
2019/10/17 15:34:21 pushed blob: sha256:a36db1027eba54167d866a6d7e71be248d245f45a30e4126d741fa4ed382f162
2019/10/17 15:34:30 pushed blob: sha256:f29d7a0aae9b9222ca0bdab5b993a2a9856511981ca9d12bee9d67fab50d528f
2019/10/17 15:34:30 822044714040.dkr.ecr.eu-central-1.amazonaws.com/ruzickap/front-end:0.3.13: digest: sha256:6a78c378e6f4426eba0eda2ef658a3675b289b68f32b803845444f45a799c913 size: 1893

waiting for stage from-build-pack : container step-build-post-build to start...


Showing logs for build ruzickap/front-end/master #1 stage from-build-pack and container step-build-post-build
no CVE provider running in the current jx namespace so skip adding image to be analysed

waiting for stage from-build-pack : container step-promote-changelog to start...


Showing logs for build ruzickap/front-end/master #1 stage from-build-pack and container step-promote-changelog
Converted /workspace/source/charts/front-end to an unshallow repository
Generating change log from git ref b2308a3ecc70b9ac97b93ce73985e57d5d0c186b => 96b824f0dfe7fefb5e6300a1aecebe5952dc8bfb
Associating user x-0 in users.jenkins.io with email petr.ruzicka@gmail.com to git GitProvider user with login -0 as emails match
Adding label jenkins.io/git-github-userid=-0 to user x-0 in users.jenkins.io
WARNING: Failed to enrich commit bc24537484dfa7aed349edf20f63034e3d4f72f6 with issues: User.jenkins.io "x-0" is invalid: metadata.labels: Invalid value: "-0": a valid label must be an empty string or consist of alphanumeric characters, '-', '_' or '.', and must start and end with an alphanumeric character (e.g. 'MyValue',  or 'my_value',  or '12345', regex used for validation is '(([A-Za-z0-9][-A-Za-z0-9_.]*)?[A-Za-z0-9])?')
Finding issues in commit messages using git format
Associating user x-0 in users.jenkins.io with email petr.ruzicka@gmail.com to git GitProvider user with login -0 as emails match
Adding label jenkins.io/git-github-userid=-0 to user x-0 in users.jenkins.io
WARNING: Failed to enrich commit b7f3b45438e3c21a1df16fd6f4bd8d4fb2935dea with issues: User.jenkins.io "x-0" is invalid: metadata.labels: Invalid value: "-0": a valid label must be an empty string or consist of alphanumeric characters, '-', '_' or '.', and must start and end with an alphanumeric character (e.g. 'MyValue',  or 'my_value',  or '12345', regex used for validation is '(([A-Za-z0-9][-A-Za-z0-9_.]*)?[A-Za-z0-9])?')
Associating user x-0 in users.jenkins.io with email petr.ruzicka@gmail.com to git GitProvider user with login -0 as emails match
Adding label jenkins.io/git-github-userid=-0 to user x-0 in users.jenkins.io
WARNING: Failed to enrich commit b7f3b45438e3c21a1df16fd6f4bd8d4fb2935dea with issues: User.jenkins.io "x-0" is invalid: metadata.labels: Invalid value: "-0": a valid label must be an empty string or consist of alphanumeric characters, '-', '_' or '.', and must start and end with an alphanumeric character (e.g. 'MyValue',  or 'my_value',  or '12345', regex used for validation is '(([A-Za-z0-9][-A-Za-z0-9_.]*)?[A-Za-z0-9])?')
WARNING: Failed to enrich commit a73c693b4b4d4eb2cfc6d4d386a69d29648fc59c with issues: users.jenkins.io "x-0" already exists
WARNING: Failed to enrich commit a73c693b4b4d4eb2cfc6d4d386a69d29648fc59c with issues: users.jenkins.io "x-0" already exists
WARNING: Failed to enrich commit b1ff2c3b4b1af9fdee85320a8ce7b4af39e3c582 with issues: users.jenkins.io "x-0" already exists
WARNING: Failed to enrich commit b1ff2c3b4b1af9fdee85320a8ce7b4af39e3c582 with issues: users.jenkins.io "x-0" already exists
WARNING: Failed to enrich commit 8fc609241f33c15531d6997155a5ad25974d841b with issues: users.jenkins.io "x-0" already exists
WARNING: Failed to enrich commit 8fc609241f33c15531d6997155a5ad25974d841b with issues: users.jenkins.io "x-0" already exists
WARNING: Failed to enrich commit 61d1fdf3ed96a7172e0a2cbaf136d158784f09c0 with issues: users.jenkins.io "x-0" already exists
WARNING: Failed to enrich commit 61d1fdf3ed96a7172e0a2cbaf136d158784f09c0 with issues: users.jenkins.io "x-0" already exists
WARNING: Failed to enrich commit f7775f5882f39b0e3c579d880311f4020a4d97fd with issues: users.jenkins.io "x-0" already exists
WARNING: Failed to enrich commit f7775f5882f39b0e3c579d880311f4020a4d97fd with issues: users.jenkins.io "x-0" already exists
WARNING: Failed to enrich commit 82ebb7c9a018063aaeff91eeebdd9e8e4d810519 with issues: users.jenkins.io "x-0" already exists
WARNING: Failed to enrich commit 82ebb7c9a018063aaeff91eeebdd9e8e4d810519 with issues: users.jenkins.io "x-0" already exists
WARNING: Failed to enrich commit c3eebf2763bdd9a1e0cc5a0f2a9f402ed8c269aa with issues: users.jenkins.io "x-0" already exists
WARNING: Failed to enrich commit c3eebf2763bdd9a1e0cc5a0f2a9f402ed8c269aa with issues: users.jenkins.io "x-0" already exists
WARNING: Failed to enrich commit 390c3fd9ecd95db19eb1044d83161e3d4e261219 with issues: users.jenkins.io "x-0" already exists
WARNING: Failed to enrich commit 390c3fd9ecd95db19eb1044d83161e3d4e261219 with issues: users.jenkins.io "x-0" already exists
WARNING: Failed to enrich commit 7bc6120079b3d403d75a0bfc2248441e94bcdf4f with issues: users.jenkins.io "x-0" already exists
WARNING: Failed to enrich commit 7bc6120079b3d403d75a0bfc2248441e94bcdf4f with issues: users.jenkins.io "x-0" already exists
WARNING: Failed to enrich commit 5adfac298a9d65061b86eba813cab4f151132ae7 with issues: users.jenkins.io "x-0" already exists
WARNING: Failed to enrich commit 5adfac298a9d65061b86eba813cab4f151132ae7 with issues: users.jenkins.io "x-0" already exists
WARNING: Failed to enrich commit d2803dc6f6f46aa34494640b45b8b600d5fbef57 with issues: users.jenkins.io "x-0" already exists
WARNING: Failed to enrich commit d2803dc6f6f46aa34494640b45b8b600d5fbef57 with issues: users.jenkins.io "x-0" already exists
WARNING: Failed to enrich commit e0ed7795e74370b90a3f57da641f25764966768f with issues: users.jenkins.io "x-0" already exists
WARNING: Failed to enrich commit e0ed7795e74370b90a3f57da641f25764966768f with issues: users.jenkins.io "x-0" already exists
WARNING: Failed to enrich commit cd028e31f06a245fe641fb138c8e91165e80ccbb with issues: users.jenkins.io "x-0" already exists
WARNING: Failed to enrich commit cd028e31f06a245fe641fb138c8e91165e80ccbb with issues: users.jenkins.io "x-0" already exists
WARNING: No release found for ruzickap/front-end and tag v0.3.13 so creating a new release
Updated the release information at https://github.com/ruzickap/front-end/releases/tag/v0.3.13
generated: /workspace/source/charts/front-end/templates/release.yaml
Created Release front-end-0-3-13 resource in namespace jx
Updating PipelineActivity ruzickap-front-end-master-1 with version 0.3.13
Updated PipelineActivities ruzickap-front-end-master-1 with release notes URL: https://github.com/ruzickap/front-end/releases/tag/v0.3.13

waiting for stage from-build-pack : container step-promote-helm-release to start...


Showing logs for build ruzickap/front-end/master #1 stage from-build-pack and container step-promote-helm-release
WARNING: No $CHART_REPOSITORY defined so using the default value of: http://jenkins-x-chartmuseum:8080
Adding missing Helm repo: storage.googleapis.com https://storage.googleapis.com/chartmuseum.jenkins-x.io
Successfully added Helm repository storage.googleapis.com.
Adding missing Helm repo: jenkins-x-chartmuseum http://jenkins-x-chartmuseum:8080
Successfully added Helm repository jenkins-x-chartmuseum.
WARNING: No $CHART_REPOSITORY defined so using the default value of: http://jenkins-x-chartmuseum:8080
Uploading chart file front-end-0.3.13.tgz to http://jenkins-x-chartmuseum:8080/api/charts
Received 201 response: {"saved":true}

waiting for stage from-build-pack : container step-promote-jx-promote to start...


Showing logs for build ruzickap/front-end/master #1 stage from-build-pack and container step-promote-jx-promote
WARNING: prow based install so skip waiting for the merge of Pull Requests to go green as currently there is an issue with gettingstatuses from the PR, see https://github.com/jenkins-x/jx/issues/2410
Promoting app front-end version 0.3.13 to namespace jx-staging
Created Pull Request: https://github.com/ruzickap/environment-mylabs-staging/pull/1
Pull Request https://github.com/ruzickap/environment-mylabs-staging/pull/1 is merged at sha 46b4b8c22d3f1e1271170c96598c90f3a247f45c
Pull Request merged but we are not waiting for the update pipeline to complete!
WARNING: Could not find the service URL in namespace jx-staging for names front-end, jx-front-end, jx-staging-front-end
```

You should be able to see created container image in AWS ECR: [https://eu-central-1.console.aws.amazon.com/ecr/repositories?region=eu-central-1](https://eu-central-1.console.aws.amazon.com/ecr/repositories?region=eu-central-1)

Watch pipeline activity via:

```bash
jx get activity -f front-end
sleep 60
```

Output:

```text
STEP                                                STARTED AGO DURATION STATUS
ruzickap/front-end/master #1                              3m53s          Succeeded Version: 0.3.13
  meta pipeline                                           3m53s      41s Succeeded
    Credential Initializer Mxv7z                          3m53s       0s Succeeded
    Working Dir Initializer Jvx69                         3m53s       3s Succeeded
    Place Tools                                           3m50s       3s Succeeded
    Git Source Meta Ruzickap Front End Master Hxhmf       3m47s      12s Succeeded https://github.com/ruzickap/front-end.git
    Git Merge                                             3m35s       3s Succeeded
    Merge Pull Refs                                       3m32s       1s Succeeded
    Create Effective Pipeline                             3m31s       9s Succeeded
    Create Tekton Crds                                    3m22s      10s Succeeded
  from build pack                                          3m9s          Running
    Credential Initializer R9p2h                           3m9s       0s Succeeded
    Working Dir Initializer X95mf                          3m9s       3s Succeeded
    Place Tools                                            3m6s       2s Succeeded
    Git Source Ruzickap Front End Master 5h7d7             3m4s      31s Succeeded https://github.com/ruzickap/front-end.git
    Git Merge                                             2m33s       2s Succeeded
    Setup Jx Git Credentials                              2m31s       0s Succeeded
    Build Npmrc                                           2m31s       0s Succeeded
    Build Npm Install                                     2m31s       7s Succeeded
    Build Npm Test                                        2m24s       1s Succeeded
    Build Container Build                                 2m23s      30s Succeeded
    Build Post Build                                      1m53s       1s Succeeded
    Promote Changelog                                     1m52s      28s Succeeded
    Promote Helm Release                                  1m24s       4s Succeeded
    Promote Jx Promote                                    1m20s          Running
  Promote: staging                                        1m12s     1m6s Succeeded
    PullRequest                                           1m12s     1m6s Succeeded  PullRequest: https://github.com/ruzickap/environment-mylabs-staging/pull/1 Merge SHA: 46b4b8c22d3f1e1271170c96598c90f3a247f45c
    Update                                                   6s       0s Succeeded
```

Check the applications:

```bash
jx get applications
```

Output:

```text
APPLICATION  STAGING PODS URL                                    PRODUCTION PODS URL
carts                1/1                                                    1/1
carts-db             1/1                                                    1/1
catalogue            1/1                                                    1/1
catalogue-db         1/1                                                    1/1
front-end    0.3.13  1/1  http://front-end.jx-staging.mylabs.dev 0.3.13     1/1
orders               1/1                                                    1/1
orders-db            1/1                                                    1/1
payment              1/1                                                    1/1
queue-master         1/1                                                    1/1
rabbitmq             1/1                                                    1/1
shipping             1/1                                                    1/1
user                 1/1                                                    1/1
user-db              1/1                                                    1/1
```

Verify the URLs:

```bash
jx get urls -n jx-staging
```

Output:

```text
NAME      URL
front-end http://front-end.jx-staging.mylabs.dev
```

The application was only deployed on the staging environment [http://front-end.jx-staging.mylabs.dev/](http://front-end.jx-staging.mylabs.dev/)
and now it is time to promote it to production:

![Sock Shop - staging](./sock_shop-staging.png "Sock Shop - staging")

```bash
jx promote front-end --build="1" --version 0.3.13 --env production --batch-mode=true
```

Output:

```text
WARNING: prow based install so skip waiting for the merge of Pull Requests to go green as currently there is an issue with gettingstatuses from the PR, see https://github.com/jenkins-x/jx/issues/2410
Promoting app front-end version 0.3.13 to namespace jx-production
Created Pull Request: https://github.com/ruzickap/environment-mylabs-production/pull/1
WARNING: failed to parse git provider URL git@github.com:ruzickap/k8s-jenkins-x.git: parse git@github.com:ruzickap/k8s-jenkins-x.git: first path segment in URL cannot contain colon
Pull Request https://github.com/ruzickap/environment-mylabs-production/pull/1 is merged at sha ab7ff64339b03362a8a6a064258ba61ba8ccc120
Pull Request merged but we are not waiting for the update pipeline to complete!
WARNING: Could not find the service URL in namespace jx-production for names front-end, jx-production-front-end, jx-production-front-end
```

The promotion to production should be completed soon:

```bash
jx get activity
sleep 60
```

Output:

```text
STEP                                                 STARTED AGO DURATION STATUS
ruzickap/environment-mylabs-production/master #1             14s          Running
  meta pipeline                                              14s          Running
    Credential Initializer Gm5lq                             14s       0s Succeeded
    Working Dir Initializer 6zjqx                            14s       0s Succeeded
    Place Tools                                              14s       1s Succeeded
    Git Source Meta Ruzickap Environment Mylab Lx5fq         13s          Running https://github.com/ruzickap/environment-mylabs-production.git
    Git Merge                                                             Pending
    Merge Pull Refs                                                       Pending
    Create Effective Pipeline                                             Pending
    Create Tekton Crds                                                    Pending
ruzickap/environment-mylabs-production/PR-1 #1               43s          Running
  meta pipeline                                              43s      16s Succeeded
    Credential Initializer Dj45d                             43s       0s Succeeded
    Working Dir Initializer Xb56p                            43s       0s Succeeded
    Place Tools                                              43s       1s Succeeded
    Git Source Meta Ruzickap Environment Mylab R7dcd         42s       5s Succeeded https://github.com/ruzickap/environment-mylabs-production.git
    Git Merge                                                37s       2s Succeeded
    Merge Pull Refs                                          35s       2s Succeeded
    Create Effective Pipeline                                33s       5s Succeeded
    Create Tekton Crds                                       28s       1s Succeeded
  from build pack                                            23s          Running
    Credential Initializer H6t5f                             23s       0s Succeeded
    Working Dir Initializer 9765x                            23s       0s Succeeded
    Place Tools                                              23s       1s Succeeded
    Git Source Ruzickap Environment Mylabs Pro D9dnj         22s       6s Succeeded https://github.com/ruzickap/environment-mylabs-production.git
    Git Merge                                                16s       4s Succeeded
    Build Helm Build                                         12s          Running
ruzickap/environment-mylabs-staging/master #1              1m21s          Running
  meta pipeline                                            1m21s      18s Succeeded
    Credential Initializer L7wk6                           1m21s       0s Succeeded
    Working Dir Initializer Kjt4g                          1m21s       0s Succeeded
    Place Tools                                            1m21s       1s Succeeded
    Git Source Meta Ruzickap Environment Mylab Jgfj9       1m20s       5s Succeeded https://github.com/ruzickap/environment-mylabs-staging.git
    Git Merge                                              1m15s       1s Succeeded
    Merge Pull Refs                                        1m14s       0s Succeeded
    Create Effective Pipeline                              1m14s       5s Succeeded
    Create Tekton Crds                                      1m9s       6s Succeeded
  from build pack                                           1m2s          Running
    Credential Initializer Ml2z7                            1m2s       0s Succeeded
    Working Dir Initializer 9w6zf                           1m2s       1s Succeeded
    Place Tools                                             1m1s       1s Succeeded
    Git Source Ruzickap Environment Mylabs Sta V96rm        1m0s       7s Succeeded https://github.com/ruzickap/environment-mylabs-staging.git
    Git Merge                                                53s       1s Succeeded
    Setup Jx Git Credentials                                 52s       1s Succeeded
    Build Helm Apply                                         51s          Running
ruzickap/environment-mylabs-staging/PR-1 #1                 2m7s    1m31s Succeeded
  meta pipeline                                             2m7s      19s Succeeded
    Credential Initializer Pxxn6                            2m7s       0s Succeeded
    Working Dir Initializer K8s7w                           2m7s       1s Succeeded
    Place Tools                                             2m6s       1s Succeeded
    Git Source Meta Ruzickap Environment Mylab Xnsv9        2m5s       6s Succeeded https://github.com/ruzickap/environment-mylabs-staging.git
    Git Merge                                              1m59s       2s Succeeded
    Merge Pull Refs                                        1m57s       2s Succeeded
    Create Effective Pipeline                              1m55s       6s Succeeded
    Create Tekton Crds                                     1m49s       1s Succeeded
  from build pack                                          1m42s     1m6s Succeeded
    Credential Initializer Jzqnd                           1m42s       0s Succeeded
    Working Dir Initializer Cfdx4                          1m42s       2s Succeeded
    Place Tools                                            1m40s       1s Succeeded
    Git Source Ruzickap Environment Mylabs Sta Nx9rn       1m39s      28s Succeeded https://github.com/ruzickap/environment-mylabs-staging.git
    Git Merge                                              1m11s       5s Succeeded
    Build Helm Build                                        1m6s      30s Succeeded
ruzickap/front-end/master #1                               4m50s    3m47s Succeeded Version: 0.3.13
  meta pipeline                                            4m50s      41s Succeeded
    Credential Initializer Mxv7z                           4m50s       0s Succeeded
    Working Dir Initializer Jvx69                          4m50s       3s Succeeded
    Place Tools                                            4m47s       3s Succeeded
    Git Source Meta Ruzickap Front End Master Hxhmf        4m44s      12s Succeeded https://github.com/ruzickap/front-end.git
    Git Merge                                              4m32s       3s Succeeded
    Merge Pull Refs                                        4m29s       1s Succeeded
    Create Effective Pipeline                              4m28s       9s Succeeded
    Create Tekton Crds                                     4m19s      10s Succeeded
  from build pack                                           4m6s     3m3s Succeeded
    Credential Initializer R9p2h                            4m6s       0s Succeeded
    Working Dir Initializer X95mf                           4m6s       3s Succeeded
    Place Tools                                             4m3s       2s Succeeded
    Git Source Ruzickap Front End Master 5h7d7              4m1s      31s Succeeded https://github.com/ruzickap/front-end.git
    Git Merge                                              3m30s       2s Succeeded
    Setup Jx Git Credentials                               3m28s       0s Succeeded
    Build Npmrc                                            3m28s       0s Succeeded
    Build Npm Install                                      3m28s       7s Succeeded
    Build Npm Test                                         3m21s       1s Succeeded
    Build Container Build                                  3m20s      30s Succeeded
    Build Post Build                                       2m50s       1s Succeeded
    Promote Changelog                                      2m49s      28s Succeeded
    Promote Helm Release                                   2m21s       4s Succeeded
    Promote Jx Promote                                     2m17s    1m14s Succeeded
  Promote: staging                                          2m9s     1m6s Succeeded
    PullRequest                                             2m9s     1m6s Succeeded  PullRequest: https://github.com/ruzickap/environment-mylabs-staging/pull/1 Merge SHA: 46b4b8c22d3f1e1271170c96598c90f3a247f45c
    Update                                                  1m3s       0s Succeeded
ruzickap/k8s-jenkins-x/master #1                                          Running Version: 0.3.13
  Release                                                  1m46s     1m0s Succeeded
  Promote: production                                        46s      46s Succeeded
    PullRequest                                              46s      45s Succeeded  PullRequest: https://github.com/ruzickap/environment-mylabs-production/pull/1 Merge SHA: ab7ff64339b03362a8a6a064258ba61ba8ccc120
    Update                                                    1s       1s Succeeded
```

You should be able to see the same Sock Shop instance on this URL: [http://front-end.jx-production.mylabs.dev/](http://front-end.jx-production.mylabs.dev/)

Check the production URLs:

```bash
jx get urls -n jx-production
```

Output:

```text
NAME      URL
front-end http://front-end.jx-production.mylabs.dev
```

Verify the `pipelineactivity`:

```bash
kubectl get pipelineactivity -n jx
```

Output:

```text
NAME                                              GIT URL                                                         STATUS
ruzickap-environment-mylabs-production-master-1   https://github.com/ruzickap/environment-mylabs-production.git   Running
ruzickap-environment-mylabs-production-pr-1-1     https://github.com/ruzickap/environment-mylabs-production.git   Running
ruzickap-environment-mylabs-staging-master-1      https://github.com/ruzickap/environment-mylabs-staging.git      Succeeded
ruzickap-environment-mylabs-staging-pr-1-1        https://github.com/ruzickap/environment-mylabs-staging.git      Succeeded
ruzickap-front-end-master-1                       https://github.com/ruzickap/front-end.git                       Succeeded
ruzickap-k8s-jenkins-x-master-1                   git@github.com:ruzickap/k8s-jenkins-x.git                       Running
```

Look at the Tekton's `pipelineruns`:

```bash
kubectl get pipelineruns.tekton.dev -n jx
```

Output:

```text
NAME                                      AGE
meta-ruzickap-environment-mylab-1         2m10s
meta-ruzickap-environment-mylab-jb9k5-1   46s
meta-ruzickap-environment-mylab-rdchv-1   83s
meta-ruzickap-environment-mylab-zln5m-1   21s
meta-ruzickap-front-end-master-1          5m
ruzickap-environment-mylabs-pro-1         28s
ruzickap-environment-mylabs-sta-1         109s
ruzickap-environment-mylabs-sta-74ttt-1   64s
ruzickap-front-end-master-1               4m10s
```

Those pods were created by the pipelines during the build process:

```bash
kubectl get pods -l jenkins.io/pipelineType=build -n jx
```

Output:

```text
NAME                                                                       READY   STATUS      RESTARTS   AGE
ruzickap-environment-mylabs-pro-1-from-build-pack-lgzxp-pod-fa1dec         1/3     Running     0          26s
ruzickap-environment-mylabs-sta-1-from-build-pack-h4ds4-pod-2ef560         0/3     Completed   0          108s
ruzickap-environment-mylabs-sta-74ttt-1-from-build-pack-s2vbb-pod-f42bb6   0/4     Completed   0          65s
ruzickap-front-end-master-1-from-build-pack-8k8x9-pod-9ef55a               0/11    Completed   0          4m11s
```

See the labels on one of the pods created during the build process:

```bash
kubectl get pods $(kubectl get pods -l jenkins.io/pipelineType=build -n jx -o "jsonpath={.items[0].metadata.name}") -n jx --show-labels
```

Output:

```text
NAME                                                                 READY   STATUS    RESTARTS   AGE   LABELS
ruzickap-environment-mylabs-pro-1-from-build-pack-lgzxp-pod-fa1dec   1/3     Running   0          26s   branch=PR-1,build=1,context=promotion-build,created-by-prow=true,jenkins.io/pipelineType=build,jenkins.io/task-stage-name=from-build-pack,owner=ruzickap,prowJobName=e69bad31-f0f3-11e9-b692-7e7c118eea63,repository=environment-mylabs-production,tekton.dev/pipeline=ruzickap-environment-mylabs-pro-1,tekton.dev/pipelineRun=ruzickap-environment-mylabs-pro-1,tekton.dev/pipelineTask=from-build-pack,tekton.dev/task=ruzickap-environment-mylabs-pro-from-build-pack-1,tekton.dev/taskRun=ruzickap-environment-mylabs-pro-1-from-build-pack-lgzxp
```

List all the pipelines using the `tkn` command (default Tekton client):

```bash
tkn pipeline list -n jx
```

Output:

```text
NAME                                      AGE              LAST RUN                                  STARTED          DURATION     STATUS
meta-ruzickap-environment-mylab-1         18 minutes ago   meta-ruzickap-environment-mylab-1         18 minutes ago   23 seconds   Succeeded
meta-ruzickap-environment-mylab-jb9k5-1   16 minutes ago   meta-ruzickap-environment-mylab-jb9k5-1   16 minutes ago   21 seconds   Succeeded
meta-ruzickap-environment-mylab-rdchv-1   17 minutes ago   meta-ruzickap-environment-mylab-rdchv-1   17 minutes ago   22 seconds   Succeeded
meta-ruzickap-environment-mylab-zln5m-1   16 minutes ago   meta-ruzickap-environment-mylab-zln5m-1   16 minutes ago   1 minute     Succeeded
meta-ruzickap-front-end-master-1          21 minutes ago   meta-ruzickap-front-end-master-1          21 minutes ago   53 seconds   Succeeded
pipeline0                                 21 minutes ago   ---                                       ---              ---          ---
ruzickap-environment-mylabs-pro-1         16 minutes ago   ruzickap-environment-mylabs-pro-1         16 minutes ago   1 minute     Succeeded
ruzickap-environment-mylabs-pro-7r8jr-1   15 minutes ago   ruzickap-environment-mylabs-pro-7r8jr-1   15 minutes ago   47 seconds   Succeeded
ruzickap-environment-mylabs-sta-1         17 minutes ago   ruzickap-environment-mylabs-sta-1         17 minutes ago   1 minute     Succeeded
ruzickap-environment-mylabs-sta-74ttt-1   17 minutes ago   ruzickap-environment-mylabs-sta-74ttt-1   17 minutes ago   57 seconds   Succeeded
ruzickap-front-end-master-1               20 minutes ago   ruzickap-front-end-master-1               20 minutes ago   3 minutes    Succeeded
```

Screenshot form Prow [https://deck.jx.mylabs.dev](https://deck.jx.mylabs.dev):

![Prow status](./prow_status.png "Prow status")

You may also see the Tekton Pipelines using Tekton Dashboard [https://tekton-dashboard.jx.mylabs.dev](https://tekton-dashboard.jx.mylabs.dev):

![Tekton Dashboard](./tekton_dashboard.png "Tekton Dashboard")
