

# Twelve Factor App
App and Twelve factor app are used interchangeably in the notes

## 1. Codebase
- App should always be tracked in a version control system(such as git, subversion, mercurial)
- Copy of tracking DB is known as code repository, code repo or repo
- There is always 1:1 relation between condebase and repo
  - If there are multiple codebases, it not an app, it's a distributed syste,
  - Each componen in a distributed system is an app
- Multiple apps can share the code via libraries, which can be included via dependency manager
- Deploy is an running instance of an app
- There can be multiple deploys of an app. For Ex:
  - Production site
  - Staging site
  - Local developers deploys
 Different deploys can have different versions(commits/tags), but all share same codebase.

## 2. Dependencies
- App should declare all dependencies via dependency declaration manifest
- App should use dependency isolation to separate out local environments
- App should never rely on implicit existance of system-wide packages
- For Ex:
  a) Python uses pip for declaration(requirements.txt) & virtualenv for isolation
  b) Ruby   uses Gemfile for decralation               & bundle exec for isolation
  c) C      has  autoconf for declaration              & static library can provide isolation

## 3. Configs
**Store config in environment**
- An app config might vary between deloyments(Prod, staging, local dev)
- Config might include:
  - Resource handles to the DB, Redis/Memcached configurations, other backigng services configurations.
  - Creds to external services, such as Amazon S3, Google bucket, Auth0
  - Deploy specific values such as hostnames, network details
- Constants and configs should be separated.
  Constants are the values which will be shared across deploys, while config varies substantially across deploys
- App should maintain a strict separation of config from cache. App should not store any config in cache. Internal app config can be stored in cache, which does not vary across deploys
- App should not store config in codebase


- App will maintain config in environment variable, which provide a easy way to change config without changing codebase.
  Advantages of maintyaining config in environments:
  - Language or OS agnostic
  - Very less probability of getting cached-in
  - Common place for all config instead of scattered config files in codebase
  - Configs are independently managed for eac h deploys, without affecting other deloys


## 4. Services or Backing services(DBs)
**Treat services as outbound resources**
- Any service which app consumes should be consumed over the network(i.e. services should be accessible via URL).
- App might use different services:
  - Databases (MySQL or PostgreSQL)
  - Caching systems (Redis/Memcached)
  - Object storage (Amazon S3, Google Cloud storage)
  - messaging/queuing systems (RabbitMQ, BeanStalkd)
  - Metric gathering services (New Relic, Loggly)
  - SMTP services for outbound mail (PostFix)
- App should have loose coupling when accessing the services i.e. app should make no distinction while accessing the local services(For Ex: MySQL) or external services(For Ex: Google Cloud MySQL)
- App deploys should be able to swap a local services with the external provider services without changing the code(there will be a config change)
- For Example:
  - migration of DB from local hosted Mysql server to Cloud based Mysql server(such as amazon RDS or google cloud SQL), OR
  - migration of on-prem object storage to cloud based object storage,
should not affect App, and should be driven by config change only.

## 5. Build Release and Run
**Separate Build & Run stages**
A codebase is transformed into a deploy through 3 stages:
- Build stage	: Converts code into executable bundle known as build. This stage also include any external dependencies or external binaries
- Release stage	: Combine build and deploy's config
- Run stage	: Run the app in execution environment

![Deploy Stages](/images/deploy-stages.jpeg)

- Every release should have a unique ID, such as timestamp
- Release are immutable i.e. any new changes will result in new release
- After build stage, no new changes can be introduced, only config can be modified
- Deployment tools can be used for build and deploys.
  Deployment tools offers the capability to rollbacl to previous working release, in case of issues

## 6. Processes
**Execute App as one or more stateless processes**
- App should be statless and shares nothing between requests.
  Any data that needs to be persist should be stored in backing service(DB)
- App never assumes, data will be available in memory or in filesystem
- 
- No sticky sessions are allowed i.e. caching user session data and using it in future requests
- Caching might lead to following issues:
  - A restart(due to system restart, migrating to new machine, deloy change, config change) might cache(both memory and filedisk cache)
  - More then 1 processes(HA) willbe available for request handling
  - Multiple request from same user might go to different process

## 7. Port binding
**Export service by port binding**
- App is self-contained and run as an individual service.
- Web app(http service) or any functionality, exposed as a specific service which binds with a specific port.
- Rest server runs within the app. Execution environment is only responsible for port binding.
- Generally, in prod deloyments, a routing layer is responsible to map DNS name to a corresponding port-binded service.

## 8. Concurrency
**Scale out via process model**
- Apps runs as services(processes). For Ex: Unix service deamons
- Apps can be architected as web-facing service for request handling and another worker processes for actual processing. For Ex: HTTP req may be handled by a eb process and long-running background tasks by a worker process
- Individual processes can internally multiplex via threads or async(event model)
- Above model scales out well, as services shares no data
- App should not handle process management instead use OS based process manager should be used to run the process.
  Such as systemd, or a distributed process manager on a cloud platform

## 9. Disposability
**Max robustness with fast startup and graceful shutdown**
- App should be started ot stopped in a moments notice
- Processes should have minimal startup time, ideally few secs.
- Process should shut down gracefully when it receive a SIGTERM signal from process manager
- Graceful termination:
  - Graceful termination on web process should stop listening on configured port, also allow any existing requests to finish(assuming HTTP reqs are small)
  - Graceful shutdown on worker process should return current unfinished job to the job queue, assuming jobs are reenterant else fail that job and revert to previous state
- Recommend queueing backends, which should return jobs to the queue when client disconnects or timeout

## 10. Dev/prod parity
- It is observed, usually there exists gap between dev and production deployments
  - The time gap     : Dev will be working in features/issues, which might take time to go into production
  - The personal gap : Developers writes code, ops engineers deploys it
  - The tools gap    : Developers might be using tools/environment which is different then production.Might be due to cose-effect or ease of installation.
		       For Ex: Many times observed that production environment uses postgreql but dev uses SQLite, dev uses local cache instead of redis/memcached.
- App should sdhere to continuous development and make the dev, prod gap as small as possible
- Summarizing the issues

|                               | Traditional App | Twelve Factor App   |
|-------------------------------|------------------|--------------------|
|Time between deloys            |      Weeks       |       Hours        |
|Code authors vs code deloyers  | Different people |    Same people     |
|Dev vs prod environments       |   Divergent      | As same as possible|


- To resolve above issues:
  - Dev fixes should be deployed or sent to QA cycle within hours
  - Dev are involved in deployments & will be noticing the App's behavior
  - Make the dev, prod tools/environments same.
    Using tools like docker, vagrant to resolve environment vaiables
- Developers should not use different backing service(DB) between dev and production
- All deploys of app(dev, staging, prod) should be using the same type & version of the backing service.

## 11. Log Streams
**Treat logs as event streams**
- Logs are streams of time-ordered events collected from the output streams of all running processes and backing services
- Logs are the record of requests/operations/events performed by the app
- Logs doesn't start or stop, it provides a continuous flow until the app operates
- App should not maintain routing or output of its output stream i.e. app should not manage or write to log files
- Each process in app, should write its event streams, unbuffered to _stdout_
- Execution environment or process manager will be responsible for collecting logs from different processes output stream. It'll log the route to 1 or more final destinations for viewing and long-term archival
- Log streams can be
  - stored in local log file
  - send to another machine for long-term archival
  - send to log indexnig and analysis system such as *splunk*
  - send to general data warehosuing such as *Hadoop/hive*
  - These systems allow analysis such as
    - finding a specific event
    - analytics, timely report of req handled by app
    - trigering alerts/notifications when req/errors per minute is greaterthen configured threshold value

## 12. Admin Process
**Runs admin/management process as one-off process**
- Sometimes developers/ops need to do administrative or maintenance of the app as
  - Running DB migration
  - Running a console(REPL shell), to get some data or determine app's health
  - Running one time script
- Admin process should 
  - run in identical environment as the regular processes of the app
  - run against same release, config and codebase of the app
  - shipped with application code to avoid any synchronization issues
- In local deploys, developers can invoke admin process by a direct shell command, whereas
  In prod deploys, developers should use ssh or other remote execution mechanisms to run such commands


