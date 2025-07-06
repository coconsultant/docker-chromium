TITLE: Install Fly.io CLI (flyctl) using Homebrew
DESCRIPTION: This command installs the Fly.io command-line interface (flyctl) on macOS and Linux systems using Homebrew. It is the recommended first step for developers to interact with Fly.io services and manage their applications.
SOURCE: https://fly.io/docs/index

LANGUAGE: Shell
CODE:
```
brew install flyctl
```

----------------------------------------

TITLE: Deploying a JavaScript application with flyctl
DESCRIPTION: After installing the `flyctl` CLI tool and logging in to your Fly.io account, use this command to deploy your JavaScript application. This command initiates the build and deployment process based on your application's configuration.
SOURCE: https://fly.io/docs/js/the-basics

LANGUAGE: Shell
CODE:
```
fly deploy
```

----------------------------------------

TITLE: Configure Fly.io App for Ollama Deployment
DESCRIPTION: This TOML configuration file sets up a Fly.io application to run the Ollama image on a GPU. It defines the app name, primary region, specifies the Ollama image for the build, mounts a volume for models, configures the HTTP service, and requests an A100 GPU.
SOURCE: https://fly.io/docs/python/do-more/add-ollama

LANGUAGE: TOML
CODE:
```
app = '<your-app>'
primary_region = 'ams'

[build]
  image = 'ollama/ollama'

[[mounts]]
  source = 'models'
  destination = '/root/.ollama'
  initial_size = '10gb'

[http_service]
  internal_port = 11434
  force_https = false
  auto_stop_machines = 'stop'
  auto_start_machines = true
  min_machines_running = 0
  processes = ['app']

[[vm]]
  size = 'a100-80gb'

```

----------------------------------------

TITLE: Rails Controller for Handling Read-Only Transaction Errors
DESCRIPTION: This Rails `ApplicationController` snippet demonstrates how to gracefully handle `PG::ReadOnlySqlTransaction` errors that occur when a write operation is attempted on a read replica. It intercepts the error, sets the `fly-replay` HTTP header to redirect the request to the primary region, and returns a 409 status, ensuring strong consistency for write requests.
SOURCE: https://fly.io/docs/postgres/high-availability-and-global-replication

LANGUAGE: Ruby
CODE:
```
class ApplicationController < ActionController::Base
  rescue_from ActiveRecord::StatementInvalid do |e|
    if e.cause.is_a?(PG::ReadOnlySqlTransaction)
      r = ENV["PRIMARY_REGION"]
      response.headers["fly-replay"] = "region=#{r}"
      Rails.logger.info "Replaying request in #{r}"
      render plain: "retry in region #{r}", status: 409
    else
      raise e
    end
  end
end
```

----------------------------------------

TITLE: View Live Logs for an App using flyctl
DESCRIPTION: This command prints the most recent application logs and then tails current logs in real-time. It's useful for monitoring logs during deployment or other operations. The output continues until the terminal is closed or stopped.
SOURCE: https://fly.io/docs/monitoring/live-tail-logs

LANGUAGE: Shell
CODE:
```
fly logs --app my-app-name
```

----------------------------------------

TITLE: Usage of fly launch command
DESCRIPTION: This command is used to create and configure a new application on Fly.io. It can take source code or a Docker image as input. Options provided after a double dash (`--`) are passed directly to the language-specific scanner or Dockerfile generator.
SOURCE: https://fly.io/docs/flyctl/launch

LANGUAGE: Shell
CODE:
```
fly launch [flags]
```

----------------------------------------

TITLE: Add TLS Certificate for Custom Domain using flyctl
DESCRIPTION: Create a TLS certificate for your custom domain to enable HTTPS connections and proper routing for apps using shared IPv4 addresses. The `fly certs add` command initiates the certificate issuance process, including support for wildcard domains.
SOURCE: https://fly.io/docs/networking/custom-domain

LANGUAGE: Shell
CODE:
```
fly certs add example.com
```

LANGUAGE: Shell Output
CODE:
```
  Hostname                    = example.com
  Configured                  = true
  Issued                      =
  Certificate Authority       = lets_encrypt
  DNS Provider                = enom
  DNS Validation Instructions =
  DNS Validation Hostname     =
  DNS Validation Target       = example.com.5xzw.flydns.net
  Source                      = fly
  Created At                  = 0001-01-01T00:00:00Z
  Status                      =
```

LANGUAGE: Shell
CODE:
```
fly certs add "*.example.com"
```

----------------------------------------

TITLE: Launching a Fly.io App
DESCRIPTION: Demonstrates the `fly launch` command, which initializes a new Fly.io application, generates a `fly.toml` configuration file and a Dockerfile, and initiates the deployment process. It shows the interactive prompts and the successful creation and initial deployment of the app.
SOURCE: https://fly.io/docs/js/frameworks/meteor

LANGUAGE: Shell
CODE:
```
fly launch
```

LANGUAGE: Shell
CODE:
```
Scanning source code
Detected a Meteor app
Creating app in ~/Demo/hello-meteor
We're about to launch your Meteor app on Fly.io. Here's what you're getting:

Organization: Demo                       (fly launch defaults to the personal org)
Name:         hello-meteor               (generated)
Region:       Johannesburg, South Africa (this is the fastest region for you)
App Machines: shared-cpu-1x, 1GB RAM     (most apps need about 1GB of RAM)
Postgres:     <none>                     (not requested)
Redis:        <none>                     (not requested)
Tigris:       <none>                     (not requested)

? Do you want to tweak these settings before proceeding? No
Created app 'hello-meteor' in organization 'demo'
Admin URL: https://fly.io/apps/hello-meteor
Hostname: hello-meteor.fly.dev
installing: npm install @flydotio/dockerfile@latest --save-dev

added 33 packages, and audited 143 packages in 4s

32 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
     create  Dockerfile
Wrote config file fly.toml
Validating ~/Demo/hello-meteor/fly.toml
✓ Configuration is valid
==> Building image
Remote builder fly-builder-frosty-night-3947 ready
==> Building image with Docker
...

Watch your deployment at https://fly.io/apps/hello-meteor/monitoring

Provisioning ips for hello-meteor
  Dedicated ipv6: 2a09:8280:1::3c:c2d4:0
  Shared ipv4: 66.241.124.224
  Add a dedicated ipv4 with: fly ips allocate-v4

This deployment will:
 * create 2 "app" machines

No machines in group app, launching a new machine

Creating a second machine to increase service availability
Finished launching new machines
-------
NOTE: The machines for [app] have services with 'auto_stop_machines = true' that will be stopped when idling

-------
Checking DNS configuration for hello-meteor.fly.dev

Visit your newly deployed app at https://hello-meteor.fly.dev/
...
```

----------------------------------------

TITLE: Initialize Fly.io App Deployment
DESCRIPTION: Navigate to your application's root directory and execute `fly launch` to start the interactive deployment setup for your Phoenix app on Fly.io.
SOURCE: https://fly.io/docs/elixir/getting-started/migrate-from-heroku

LANGUAGE: Shell
CODE:
```
cd my_app_name
fly launch
```

----------------------------------------

TITLE: Output of flyctl launch Command
DESCRIPTION: Displays the interactive output of the `flyctl launch` command, showing detected application type, default organization, app name, region, and resource allocations before prompting for user confirmation.
SOURCE: https://fly.io/docs/languages-and-frameworks/golang

LANGUAGE: bash
CODE:
```
Scanning source code
WARN no go.sum file found, please adjust your Dockerfile to remove references to go.sum
Detected a Go app
Creating app in /path/to/your/app
We're about to launch your Go app on Fly.io. Here's what you're getting:

Organization: Your                   (fly launch defaults to the personal org)
Name:         hellofly               (derived from your directory name)
Region:       Ashburn, Virginia (US) (this is the fastest region for you)
App Machines: shared-cpu-1x, 1GB RAM (most apps need about 1GB of RAM)
Postgres:     <none>                 (not requested)
Redis:        <none>                 (not requested)

? Do you want to tweak these settings before proceeding? n
...
Your app is ready! Deploy with `flyctl deploy`
```

----------------------------------------

TITLE: Deploy a new version of a Fly.io application
DESCRIPTION: Deploys updates to an existing application on Fly.io from the project directory.
SOURCE: https://fly.io/docs/rust/frameworks/warp

LANGUAGE: Shell
CODE:
```
fly deploy
```

----------------------------------------

TITLE: Initial Dockerfile for Rails Application Deployment
DESCRIPTION: This Dockerfile sets up a basic Rails application for deployment. It uses the `ruby` base image, installs Rails, creates a new Rails project, copies a custom `routes.rb` file using a heredoc, sets the working directory and environment, exposes port 3000, and defines the command to run the Rails server.
SOURCE: https://fly.io/docs/rails/cookbooks/minimal

LANGUAGE: Dockerfile
CODE:
```
# syntax = docker/dockerfile:1

FROM ruby

RUN gem install rails
RUN rails new demo --minimal --skip-active-record

COPY <<-"EOF" /demo/config/routes.rb
Rails.application.routes.draw { root "rails/welcome#index" }
EOF

WORKDIR demo
ENV RAILS_ENV=production
EXPOSE 3000
CMD bin/rails server
```

----------------------------------------

TITLE: Basic Elixir CI Workflow with GitHub Actions
DESCRIPTION: This YAML configuration defines a GitHub Actions workflow named 'Elixir CI'. It triggers on pushes and pull requests to the 'main' branch, ensuring automated builds and tests are run whenever code changes are integrated or proposed for the main development line. Comments within the file explain each section and highlight areas for adaptation, such as the branch name.
SOURCE: https://fly.io/docs/elixir/advanced-guides/github-actions-elixir-ci-cd

LANGUAGE: YAML
CODE:
```
name: Elixir CI

# Define workflow that runs when changes are pushed to the
# `main` branch or pushed to a PR branch that targets the `main`
# branch. Change the branch name if your project uses a
# different name for the main branch like "master" or "production".
on:
  push:
    branches: [ "main" ]  # adapt branch for project
  pull_request:
    branches: [ "main" ]  # adapt branch for project
```

----------------------------------------

TITLE: Deploying Fly Application
DESCRIPTION: This command initiates the deployment of the application to Fly.io. It triggers the build process and pushes the configured application to the Fly platform.
SOURCE: https://fly.io/docs/app-guides/planetscale

LANGUAGE: Shell
CODE:
```
fly deploy
```

----------------------------------------

TITLE: Fly.io Machine Pricing Table (Set 1)
DESCRIPTION: This table details the pricing for various Fly.io machine configurations, including shared and performance CPUs, along with their corresponding RAM, per-second, per-hour, and per-month costs. This specific set of pricing might apply to a particular region or time period.
SOURCE: https://fly.io/docs/about/pricing

LANGUAGE: APIDOC
CODE:
```
|  | CPU(s) | RAM | Price/second | Price/hour | Price/month |
| --- | --- | --- | --- | --- | --- |
| shared-cpu-1x | 1 shared | 256MB | $0.00000121 | $0.0044 | $3.14 |
|  | 512MB | $0.00000199 | $0.0072 | $5.16 |
|  | 1GB | $0.00000355 | $0.0128 | $9.20 |
|  | 2GB | $0.00000667 | $0.0240 | $17.28 |
| shared-cpu-2x | 2 shared | 512MB | $0.00000242 | $0.0087 | $6.28 |
|  | 1GB | $0.00000398 | $0.0143 | $10.32 |
|  | 2GB | $0.00000710 | $0.0256 | $18.40 |
|  | 4GB | $0.00001333 | $0.0480 | $34.56 |
| shared-cpu-4x | 4 shared | 1GB | $0.00000485 | $0.0174 | $12.56 |
|  | 2GB | $0.00000796 | $0.0287 | $20.64 |
|  | 4GB | $0.00001420 | $0.0511 | $36.80 |
|  | 8GB | $0.00002667 | $0.0960 | $69.13 |
| shared-cpu-8x | 8 shared | 2GB | $0.00000969 | $0.0349 | $25.12 |
|  | 4GB | $0.00001593 | $0.0573 | $41.28 |
|  | 8GB | $0.00002840 | $0.1022 | $73.61 |
|  | 16GB | $0.00005334 | $0.1920 | $138.26 |
| performance-1x | 1 performance | 2GB | $0.00001932 | $0.0696 | $50.08 |
|  | 4GB | $0.00002556 | $0.0920 | $66.24 |
|  | 8GB | $0.00003803 | $0.1369 | $98.56 |
| performance-2x | 2 performance | 4GB | $0.00003864 | $0.1391 | $100.15 |
|  | 8GB | $0.00005111 | $0.1840 | $132.48 |
|  | 16GB | $0.00007605 | $0.2738 | $197.13 |
| performance-4x | 4 performance | 8GB | $0.00007728 | $0.2782 | $200.31 |
|  | 16GB | $0.00010222 | $0.3680 | $264.96 |
|  | 32GB | $0.00015210 | $0.5476 | $394.26 |
| performance-8x | 8 performance | 16GB | $0.00015456 | $0.5564 | $400.62 |
|  | 32GB | $0.00020444 | $0.7360 | $529.92 |
|  | 64GB | $0.00030421 | $1.0952 | $788.51 |
| performance-16x | 16 performance | 32GB | $0.00030912 | $1.1128 | $801.24 |
|  | 64GB | $0.00040889 | $1.4720 | $1059.83 |
|  | 128GB | $0.00060842 | $2.1903 | $1577.02 |
```

----------------------------------------

TITLE: Create a Fly App
DESCRIPTION: This command initializes a new application on the Fly.io platform. Replace <app-name> with your desired application name. It sets up the basic structure for your Fly.io project.
SOURCE: https://fly.io/docs/gpus/python-gpu-example

LANGUAGE: Shell
CODE:
```
fly apps create <app-name>
```

----------------------------------------

TITLE: Understanding the `fly.toml` Configuration File
DESCRIPTION: Illustrates the default structure of the `fly.toml` file generated by `fly launch`, showing essential settings like app name, primary region, environment variables (PORT, ROOT_URL), and HTTP service configurations (internal port, HTTPS enforcement, machine auto-scaling).
SOURCE: https://fly.io/docs/js/frameworks/meteor

LANGUAGE: TOML
CODE:
```
app = "hello-meteor"
primary_region = "jnb"

[env]
  PORT = "3000"
  ROOT_URL = "https://hello-meteor.fly.dev/"

[http_service]
  internal_port = 3000
  force_https = true
  auto_stop_machines = true
  auto_start_machines = true
  min_machines_running = 0
  processes = ["app"]
...
```

----------------------------------------

TITLE: Common flyctl Commands for Application Management
DESCRIPTION: A quick reference table listing common `flyctl` commands for tasks such as logging in, launching, deploying, viewing dashboard, SSH, tailing logs, managing secrets, and viewing releases, useful for users migrating from other hosting services.
SOURCE: https://fly.io/docs/django/getting-started/existing

LANGUAGE: APIDOC
CODE:
```
Task | Command
--- | ---
Log in | `fly login`
Launch an app | `fly launch`
Deployments | `fly deploy`
Open web dashboard | `fly dashboard`
SSH | `fly ssh console`
Tail log files | `fly logs`
Secrets | `fly secrets`
View releases | `fly releases`
Help | `fly help`
```

----------------------------------------

TITLE: Set Laravel Database Credentials as Fly.io Secrets
DESCRIPTION: This command uses `fly secrets set` to securely configure the database username and password for a Laravel application deployed on Fly.io. These secrets are injected into the application's environment at runtime, providing a secure way to manage sensitive credentials. Replace `<DB_USERNAME>` and `<DB_PASSWORD>` with the actual PlanetScale credentials.
SOURCE: https://fly.io/docs/laravel/database-guides/laravel-mysql

LANGUAGE: Shell
CODE:
```
fly secrets set DB_USERNAME=<DB_USERNAME> DB_PASSWORD=<DB_PASSWORD>
```

----------------------------------------

TITLE: Initialize a New Fly.io Application
DESCRIPTION: Run this command from your project's source directory to create a new application on Fly.io. It initiates the setup process, including generating a `fly.toml` configuration file and preparing for the first deployment.
SOURCE: https://fly.io/docs/launch/create

LANGUAGE: Shell
CODE:
```
fly launch
```

----------------------------------------

TITLE: Deploying Python Application with fly launch
DESCRIPTION: This command is used to deploy most popular Python frameworks to Fly.io. It simplifies the initial setup and deployment process by automatically detecting project configurations.
SOURCE: https://fly.io/docs/python/the-basics

LANGUAGE: Shell
CODE:
```
fly launch
```

----------------------------------------

TITLE: Set a secret environment variable for Rails on Fly.io
DESCRIPTION: This command sets a sensitive environment variable as a secret in your Fly.io application. Secrets are not viewable except when the container is running, ensuring data security for values like database passwords.
SOURCE: https://fly.io/docs/rails/the-basics/configuration

LANGUAGE: Shell
CODE:
```
fly secrets set SUPER_SECRET_KEY=password1234
```

----------------------------------------

TITLE: Set Database Connection String as Fly Secret
DESCRIPTION: Sets the `DATABASE_URL` environment variable as a secret for your Fly.io application. This allows your application to securely connect to the PostgreSQL database using the provided connection string.
SOURCE: https://fly.io/docs/python/do-more/add-postgres

LANGUAGE: cmd
CODE:
```
fly secrets set DATABASE_URL=postgres://postgres:<password>@<db-name>.flycast:5432
```

----------------------------------------

TITLE: Launch Rust Application on Fly.io
DESCRIPTION: This command uses the `flyctl` CLI to initiate the deployment of a Rust application to Fly.io. It automates the configuration and launch process, making it simple to get an app running.
SOURCE: https://fly.io/docs/rust/the-basics

LANGUAGE: Shell
CODE:
```
fly launch
```

----------------------------------------

TITLE: GitHub Actions Workflow for Elixir/Phoenix Tests with PostgreSQL
DESCRIPTION: This YAML defines a comprehensive GitHub Actions workflow for Elixir/Phoenix applications. It configures a PostgreSQL service, sets up Elixir and Erlang versions, caches project dependencies and compiled builds, and executes various checks including dependency installation, compilation with warning-as-errors, code formatting checks, and running tests. It also includes logic to clean caches on re-runs to address potential flakiness from incremental builds.
SOURCE: https://fly.io/docs/elixir/advanced-guides/github-actions-elixir-ci-cd

LANGUAGE: YAML
CODE:
```
env:
  MIX_ENV: test

permissions:
  contents: read

jobs:
  test:
    services:
      db:
        image: postgres:12
        ports: ['5432:5432']
        env:
          POSTGRES_PASSWORD: postgres
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5

    runs-on: ubuntu-latest
    name: Test on OTP ${{matrix.otp}} / Elixir ${{matrix.elixir}}
    strategy:
      matrix:
        otp: ['25.0.4']
        elixir: ['1.14.1']
    steps:
    - name: Set up Elixir
      uses: erlef/setup-beam@v1
      with:
        otp-version: ${{matrix.otp}}
        elixir-version: ${{matrix.elixir}}

    - name: Checkout code
      uses: actions/checkout@v3

    - name: Cache deps
      id: cache-deps
      uses: actions/cache@v3
      env:
        cache-name: cache-elixir-deps
      with:
        path: deps
        key: ${{ runner.os }}-mix-${{ env.cache-name }}-${{ hashFiles('**/mix.lock') }}
        restore-keys: |
          ${{ runner.os }}-mix-${{ env.cache-name }}-

    - name: Cache compiled build
      id: cache-build
      uses: actions/cache@v3
      env:
        cache-name: cache-compiled-build
      with:
        path: _build
        key: ${{ runner.os }}-mix-${{ env.cache-name }}-${{ hashFiles('**/mix.lock') }}
        restore-keys: |
          ${{ runner.os }}-mix-${{ env.cache-name }}-
          ${{ runner.os }}-mix-

    - name: Clean to rule out incremental build as a source of flakiness
      if: github.run_attempt != '1'
      run: |
        mix deps.clean --all
        mix clean
      shell: sh

    - name: Install dependencies
      run: mix deps.get

    - name: Compiles without warnings
      run: mix compile --warnings-as-errors

    - name: Check Formatting
      run: mix format --check-formatted

    - name: Run tests
      run: mix test
```

----------------------------------------

TITLE: Fly.io CLI (flyctl) Command Reference
DESCRIPTION: A comprehensive list of `flyctl` commands for managing applications, machines, organizations, and other resources on the Fly.io platform, including installation and integration guides.
SOURCE: https://fly.io/docs/flyctl/launch

LANGUAGE: APIDOC
CODE:
```
flyctl - The Fly.io CLI:
  Install flyctl: /docs/flyctl/install/
  Integrate flyctl: /docs/flyctl/integrating/
Commands:
  launch: Launch an App (/docs/flyctl/launch/)
  apps: Manage Apps (/docs/flyctl/apps/)
  machine: Manage Machines (/docs/flyctl/machine/)
  auth: Login and Authentication (/docs/flyctl/auth/)
  certs: Certificates for Apps (/docs/flyctl/certs/)
  checks: Manage health checks (/docs/flyctl/checks/)
  config: Get or check App config (/docs/flyctl/config/)
  console: Run a console (/docs/flyctl/console/)
  consul: Manage Consul clusters (/docs/flyctl/consul/)
  dashboard: Open web dashboard (/docs/flyctl/dashboard/)
  deploy: Deploy an App (/docs/flyctl/deploy/)
  docs: View Fly.io docs (/docs/flyctl/docs/)
  image: View or update App image (/docs/flyctl/image/)
  ips: Manage IP addresses (/docs/flyctl/ips/)
  litefs-cloud: LiteFS Cloud (/docs/flyctl/litefs-cloud/)
  logs: View App logs (/docs/flyctl/logs/)
  orgs: Manage Organizations (/docs/flyctl/orgs/)
  ping: Ping your App (/docs/flyctl/ping/)
  platform: Platform information (/docs/flyctl/platform/)
  postgres: Manage Postgres clusters (/docs/flyctl/postgres/)
  proxy: Proxy connection to App (/docs/flyctl/proxy/)
  redis: Manage Redis instances (/docs/flyctl/redis/)
  releases: Manage App releases (/docs/flyctl/releases/)
  scale: Scaling Apps (/docs/flyctl/scale/)
  secrets: Manage App secrets (/docs/flyctl/secrets/)
  services: View services configured on an App (/docs/flyctl/services/)
  settings: Manage settings (/docs/flyctl/settings/)
  sftp: Move files to or from a VM (/docs/flyctl/sftp/)
  ssh: Manage SSH (/docs/flyctl/ssh/)
  status: View App status (/docs/flyctl/status/)
  tokens: Create a token (/docs/flyctl/tokens/)
  version: Check flyctl version (/docs/flyctl/version/)
  volumes: Manage Disks (/docs/flyctl/volumes/)
  wireguard: WireGuard VPN (/docs/flyctl/wireguard/)
```

----------------------------------------

TITLE: Set Application Secrets with fly secrets
DESCRIPTION: Demonstrates how to set environment variables as secrets for your Fly.io application using the `fly secrets set` command. This secures sensitive information by preventing it from being committed to source control.
SOURCE: https://fly.io/docs/elixir/getting-started

LANGUAGE: bash
CODE:
```
fly secrets set MY_SECRET_KEY=my_secret_value
```

----------------------------------------

TITLE: Install Fly.io CLI on macOS using Homebrew
DESCRIPTION: This snippet provides the command to install the Fly.io command-line interface (flyctl) on macOS using the Homebrew package manager. Ensure Homebrew is installed before running this command.
SOURCE: https://fly.io/docs/getting-started/launch-demo

LANGUAGE: shell
CODE:
```
brew install flyctl
```

----------------------------------------

TITLE: Launch a new Fly.io application
DESCRIPTION: Run `fly launch` from your project's source directory to create and configure a new application on Fly.io. This command automatically detects common languages and frameworks, simplifying the initial setup.
SOURCE: https://fly.io/docs/getting-started/launch

LANGUAGE: Shell
CODE:
```
fly launch
```

----------------------------------------

TITLE: Create Fly.io Application with fly launch
DESCRIPTION: The `fly launch` command initializes a new application on Fly.io. It automatically detects the project's framework and provides useful default configurations for deployment.
SOURCE: https://fly.io/docs/getting-started/essentials

LANGUAGE: CLI
CODE:
```
fly launch
```

----------------------------------------

TITLE: Deploy Fly.io Application with fly deploy
DESCRIPTION: The `fly deploy` command is used to deploy an application's Machines as a group. It relies on the `fly.toml` configuration file to manage the deployment process.
SOURCE: https://fly.io/docs/getting-started/essentials

LANGUAGE: CLI
CODE:
```
fly deploy
```

----------------------------------------

TITLE: Deploy Django Application to Fly.io
DESCRIPTION: This command initiates the deployment of your Django application to Fly.io. It handles uploading your application, verifying the configuration, building the Docker image, and monitoring the application to ensure it starts successfully on the platform.
SOURCE: https://fly.io/docs/django/getting-started/existing

LANGUAGE: Shell
CODE:
```
fly deploy
```

----------------------------------------

TITLE: Deploy a Fly app
DESCRIPTION: Deploys the Fly.io application based on the current `fly.toml` configuration. This command pushes the application code and applies any changes, including new volume mounts defined in the configuration file.
SOURCE: https://fly.io/docs/launch/volume-storage

LANGUAGE: shell
CODE:
```
fly deploy
```

----------------------------------------

TITLE: View Application Logs on Fly.io
DESCRIPTION: Retrieves and displays the application logs in real-time, which is crucial for debugging and troubleshooting deployment failures or runtime errors. It's often used in conjunction with a new deployment.
SOURCE: https://fly.io/docs/rails/the-basics/deployments

LANGUAGE: Shell
CODE:
```
fly logs
```

----------------------------------------

TITLE: Launch Fly.io Application Wizard
DESCRIPTION: Initiates the interactive `fly launch` wizard to configure and deploy a new application on Fly.io. This command automatically detects the application type (e.g., Rails) and suggests default settings for organization, name, region, and resources.
SOURCE: https://fly.io/docs/rails/getting-started

LANGUAGE: shell
CODE:
```
fly launch
```

----------------------------------------

TITLE: Fly.io Service Reachability Configuration Cheat Sheet
DESCRIPTION: A quick reference table summarizing the key configuration differences for services exposed via Fly Proxy (public internet/Flycast) versus those accessible directly over the internal 6PN WireGuard network. It covers required binding addresses, the necessity of `services` or `http_service` definitions in `fly.toml`, and whether the app needs an allocated IP address.
SOURCE: https://fly.io/docs/networking/app-services

LANGUAGE: Conceptual Configuration
CODE:
```
|  | Fly Proxy | Internal (6PN) |
| --- | --- | --- |
| Bind to | `0.0.0.0:<port>` ([not UDP](#udp-is-special)) | `fly-local-6pn:<port>` |
| Needs `services` or `http_service` in config? | YES | NO |
| App needs an IP? | YES ([not for Fly-Replay](#a-note-on-fly-app-ips-and-fly-replay-routing)) | NO |
```

----------------------------------------

TITLE: Deploy Application to Fly.io using fly deploy
DESCRIPTION: Executes the `fly deploy` command to push application changes to the Fly.io platform. This command reads the `fly.toml` file to identify the application and initiates the deployment process using the Dockerfile.
SOURCE: https://fly.io/docs/languages-and-frameworks/dotnet

LANGUAGE: Shell
CODE:
```
fly deploy
```

----------------------------------------

TITLE: Deploy application to Fly.io
DESCRIPTION: This command initiates the deployment of the updated application to the Fly.io platform. It pushes the new image and applies all configured changes.
SOURCE: https://fly.io/docs/rails/advanced-guides/anycable

LANGUAGE: Bash
CODE:
```
fly deploy

```

----------------------------------------

TITLE: Deploy Application to Fly.io
DESCRIPTION: This command initiates the deployment of your application to Fly.io. It pushes the latest code changes, including the updated Dockerfile and `fly.toml` configurations, to your production environment.
SOURCE: https://fly.io/docs/blueprints/supercronic

LANGUAGE: Shell
CODE:
```
$ fly deploy

```

----------------------------------------

TITLE: Launch a Fly.io Application
DESCRIPTION: Initiates the process of creating and configuring a new application on Fly.io. This command scans the source code, prompts for an app name, organization, region, and optional database setup, then generates a `fly.toml` configuration file.
SOURCE: https://fly.io/docs/languages-and-frameworks/ruby

LANGUAGE: shell
CODE:
```
flyctl launch
```

----------------------------------------

TITLE: Configure Django URL and Basic View
DESCRIPTION: This Python code modifies the `urls.py` file to define a simple view function that returns 'Hello, Fly!' and maps it to the root URL path. It demonstrates how to add custom views and URL patterns in Django, making your application accessible via a web browser.
SOURCE: https://fly.io/docs/django/getting-started

LANGUAGE: Python
CODE:
```
# hello_django/urls.py
from django.contrib import admin
from django.http import HttpResponse
from django.urls import path

# ↓ New basic view returning "Hello, Fly!" ↓
def hello(request):
    return HttpResponse("Hello, Fly!")

urlpatterns = [
    path("admin/", admin.site.urls),
    path("", hello, name="hello")  # ← Added!
]
```

----------------------------------------

TITLE: Multi-stage Dockerfile for Rails API with React Frontend
DESCRIPTION: This Dockerfile orchestrates a multi-stage build process for a Rails API application serving a React frontend. It includes stages for building the React client, setting up the Rails application, and copying compiled assets and version information to the final production image. It demonstrates how to embed application code directly within the Dockerfile using heredoc syntax.
SOURCE: https://fly.io/docs/rails/cookbooks/api

LANGUAGE: Dockerfile
CODE:
```
# syntax = docker/dockerfile:1

FROM node:slim AS react

RUN npx create-react-app client

RUN node --version > client/.node-version

COPY <<-"EOF" client/src/App.js
import logo from './logo.svg';
import './App.css';
import React, { useState, useEffect } from 'react';

function App() {
  let [versions, setVersions] = useState('loading...');

  useEffect(() => {
    fetch('api/versions')
    .then(response => response.json())
    .then(versions => {
      setVersions(Object.entries(versions)
        .map(([name, version]) => `${name}: ${version}`).join(', ')
      )
    });
  });

  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <p>{ versions }</p>
      </header>
    </div>
  );
}

export default App;
EOF

RUN cd client; npm run build

FROM ruby:slim AS build

RUN apt-get update &&\
    apt-get install --yes build-essential git

RUN gem install rails
RUN rails new demo --minimal --skip-active-record --api

FROM ruby:slim

COPY --from=build /demo /demo
COPY --from=build /usr/local/bundle /usr/local/bundle

COPY --from=react /client/build /demo/public
COPY --from=react /client/.node-version /demo

WORKDIR demo

RUN bin/rails generate controller Api versions

COPY <<-"EOF" app/controllers/api_controller.rb
class ApiController < ApplicationController
  def versions
    render json: {
      ruby: RUBY_VERSION,
      rails: Rails::VERSION::STRING,
      node: IO.read('.node-version').strip.sub(/^v/, '')
    }
  end
end
EOF

ENV RAILS_ENV=production
ENV RAILS_SERVE_STATIC_FILES=true
EXPOSE 3000
CMD bin/rails server
```

----------------------------------------

TITLE: GitHub Actions Workflow: Deploy Fly.io PR Preview App with Postgres
DESCRIPTION: An updated version of the GitHub Actions workflow that includes the integration of a dedicated Postgres cluster. By specifying the `postgres` input, the staging database is automatically attached to the test application, making the `DATABASE_URL` environment variable available for the application to connect.
SOURCE: https://fly.io/docs/django/advanced-guides/staging-environments-with-github-actions

LANGUAGE: yaml
CODE:
```
# .github/workflows/fly_pr_preview.yml

name: Start preview app

on:
  pull_request:
    types: [labeled, synchronize, opened, reopened, closed]

concurrency:
  group: ${{ github.workflow }}-pr-${{ github.event.number }}
  cancel-in-progress: true

permissions:
  contents: read

env:
  FLY_API_TOKEN: ${{ secrets.FLY_API_TOKEN }}

jobs:
  preview-app:
    if: contains(github.event.pull_request.labels.*.name, 'PR preview app')
    runs-on: ubuntu-latest
    name: Preview app
    environment:
      name: pr-${{ github.event.number }}
      url: ${{ steps.deploy.outputs.url }}
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: Deploy preview app
        uses: superfly/fly-pr-review-apps@1.2.0
        id: deploy
        with:
          postgres: pg-fly-pr-staging-preview  # ← Added
          region: waw
          org: personal
```

----------------------------------------

TITLE: Deploy Changes to a Fly.io App
DESCRIPTION: Use the `fly deploy` command to push new code or configuration changes to your existing Fly.io application, initiating a new release.
SOURCE: https://fly.io/docs/getting-started/launch-demo

LANGUAGE: Shell
CODE:
```
fly deploy
```

----------------------------------------

TITLE: SSH into Fly.io Laravel Application Console
DESCRIPTION: This command allows you to establish an SSH connection to a running instance of your Laravel application deployed on Fly.io, providing direct console access for interaction.
SOURCE: https://fly.io/docs/laravel/the-basics/post-deployment

LANGUAGE: Shell
CODE:
```
fly ssh console
```

LANGUAGE: Shell
CODE:
```
Connecting to top1.nearest.of.frosty-glitter-7650.internal... complete
#
```

----------------------------------------

TITLE: Configure Top-Level Health Checks
DESCRIPTION: This snippet illustrates how to define top-level health checks for a Fly.io Machine, independent of public-facing services. It includes examples for both HTTP and TCP checks, specifying parameters like port, type, grace period, interval, and for HTTP, method, path, timeout, and custom headers.
SOURCE: https://fly.io/docs/reference/configuration

LANGUAGE: TOML
CODE:
```
[checks]
  [checks.name_of_your_http_check]
    grace_period = "30s"
    interval = "15s"
    method = "get"
    path = "/path/to/status"
    port = 5500
    timeout = "10s"
    type = "http"
    [checks.name_of_your_http_check.headers]
      Content-Type = "application/json"
      Authorization = "super-secret"

  [checks.name_of_your_tcp_check]
    grace_period = "30s"
    interval = "15s"
    port = 1234
    timeout = "10s"
    type = "tcp"

```

----------------------------------------

TITLE: HTTP Service Health Check Parameters Reference
DESCRIPTION: Detailed description of parameters available for `http_service.checks` configuration, including `grace_period`, `interval`, `timeout`, `method`, `path`, `protocol`, `tls_server_name`, `tls_skip_verify`, and `http_service.checks.headers`.
SOURCE: https://fly.io/docs/reference/configuration

LANGUAGE: APIDOC
CODE:
```
http_service.checks parameters:
  grace_period: The time to wait after a Machine starts before checking its health. (e.g., "10s")
  interval: The time between connectivity checks. (e.g., "30s")
  timeout: The maximum time a connection can take before being reported as failing its health check. (e.g., "5s")
  method: The HTTP method to be used for the check. If omitted, default is 'get'.
  path: The path of the URL to be requested.
  protocol: The protocol to be used ('http' or 'https'). If omitted, default is 'http'.
  tls_server_name: If protocol is 'https', the hostname for TLS certificate validation.
  tls_skip_verify: When 'true' (and using HTTPS), skip verifying server certificates.
  http_service.checks.headers: A sub-section for key/value pairs of headers to be passed with the check call.
```

----------------------------------------

TITLE: Deploy Fly.io Application
DESCRIPTION: Execute this command to deploy your application to Fly.io. The AWS SDK within your application will automatically use the configured IAM role for S3 access.
SOURCE: https://fly.io/docs/security/openid-connect

LANGUAGE: Shell
CODE:
```
fly deploy
```

----------------------------------------

TITLE: Setting DATABASE_URL Secret with Multiple Connection Strings
DESCRIPTION: This command sets the `DATABASE_URL` as a secret environment variable for the Fly application. It expects a comma-separated list of PlanetScale connection strings, where the first string is for the primary database and subsequent ones are for read-only regions.
SOURCE: https://fly.io/docs/app-guides/planetscale

LANGUAGE: Shell
CODE:
```
fly secrets set DATABASE_URL='mysql://your-primary-region-one,mysql://optional-for-read-region,mysql://optional-for-another-read-region'
```

----------------------------------------

TITLE: Deploy Fly.io Application
DESCRIPTION: This command deploys the updated Fly.io application, applying any changes made to the code or configuration. This is a crucial step after modifying the application to include Ollama integration or any other updates.
SOURCE: https://fly.io/docs/python/do-more/add-ollama

LANGUAGE: Shell
CODE:
```
fly deploy

```

----------------------------------------

TITLE: Deploy Rails Application to Fly.io
DESCRIPTION: Initiates the deployment process for the current application to the Fly.io platform. This command pushes your application code and configuration to Fly.io.
SOURCE: https://fly.io/docs/rails/the-basics/deployments

LANGUAGE: Shell
CODE:
```
fly deploy
```

----------------------------------------

TITLE: Flyctl Account Management Commands
DESCRIPTION: Commands for managing your Fly.io account, including signing up for a new account and logging in.
SOURCE: https://fly.io/docs/flyctl

LANGUAGE: APIDOC
CODE:
```
fly auth signup: Sign up for a new Fly.io account
fly auth login: Log in to your Fly.io account
```

----------------------------------------

TITLE: Stream Real-time Logs for a Fly.io App
DESCRIPTION: The `fly logs` command continuously displays an application's console output from all its instances as events occur. This is useful for real-time debugging and monitoring. The command stays active until manually stopped (Ctrl-C).
SOURCE: https://fly.io/docs/apps/info

LANGUAGE: shell
CODE:
```
fly logs -a testrun
```

LANGUAGE: text
CODE:
```
2023-03-07T16:17:48Z runner[5683606c41098e] lhr [info]Pulling container image
2023-03-07T16:17:51Z runner[5683606c41098e] lhr [info]Unpacking image
2023-03-07T16:18:00Z runner[5683606c41098e] lhr [info]Setting up volume 'data'
2023-03-07T16:18:00Z runner[5683606c41098e] lhr [info]Uninitialized volume 'data', initializing...
2023-03-07T16:18:00Z runner[5683606c41098e] lhr [info]Encrypting volume
2023-03-07T16:18:05Z runner[5683606c41098e] lhr [info]Opening encrypted volume
2023-03-07T16:18:07Z runner[5683606c41098e] lhr [info]Formatting volume
2023-03-07T16:18:08Z runner[5683606c41098e] lhr [info]Configuring firecracker
2023-03-07T16:18:08Z app[5683606c41098e] lhr [info]Starting init (commit: 08b4c2b)...
2023-03-07T16:18:08Z app[5683606c41098e] lhr [info]Mounting /dev/vdb at /storage w/ uid: 0, gid: 0 and chmod 0755
2023-03-07T16:18:08Z app[5683606c41098e] lhr [info]Preparing to run: `sleep infinity` as root
2023-03-07T16:18:08Z app[5683606c41098e] lhr [info]2023/03/07 16:18:08 listening on [fdaa:0:3b99:a7b:7e:3155:9844:2]:22 (DNS: [fdaa::3]:53)
```

----------------------------------------

TITLE: Example Output of flyctl launch Command
DESCRIPTION: Shows the interactive prompts and output when running `flyctl launch`, including source code detection, app naming, organization and region selection, and the creation of the `fly.toml` configuration file. It also indicates the next step: `fly deploy`.
SOURCE: https://fly.io/docs/languages-and-frameworks/ruby

LANGUAGE: shell
CODE:
```
Creating app in /Users/rubys/tmp/snapshot
Scanning source code
Detected a Ruby app
? Choose an app name (leave blank to generate one):
? Select Organization: Sam Ruby (personal)
Some regions require a paid plan (fra, maa).
See https://fly.io/plans to set up a plan.

? Choose a region for deployment: Ashburn, Virginia (US) (iad)
Created app 'late-bird-1771' in organization 'personal'
Admin URL: https://fly.io/apps/late-bird-1771
Hostname: late-bird-1771.fly.dev
? Would you like to set up a Postgresql database now? No
? Would you like to set up an Upstash Redis database now? No
Wrote config file fly.toml

Your Ruby app is prepared for deployment.

If you need custom packages installed, or have problems with your deployment
build, you may need to edit the Dockerfile for app-specific changes. If you
need help, please post on https://community.fly.io.

Now: run 'fly deploy' to deploy your Ruby app.
...
```

----------------------------------------

TITLE: Deploy Laravel App Changes to Fly.io
DESCRIPTION: This `flyctl` command initiates a deployment of your Laravel application to Fly.io. It applies any changes made to the application's configuration, code, or secrets, making them active on your deployed instance.
SOURCE: https://fly.io/docs/laravel/database-guides/laravel-mysql

LANGUAGE: Shell
CODE:
```
fly deploy
```

----------------------------------------

TITLE: Initialize New Fly App with fly launch
DESCRIPTION: The `fly launch` command automates the initial setup and deployment of a new Fly App. It detects project types, configures defaults, builds Docker images, and provisions resources like Postgres clusters, Redis databases, and IP addresses.
SOURCE: https://fly.io/docs/reference/fly-launch

LANGUAGE: Shell
CODE:
```
fly launch
```

----------------------------------------

TITLE: Initiating a Canary Deployment
DESCRIPTION: This command demonstrates how to use the `fly deploy` command with the `--strategy canary` flag. The canary strategy deploys a single new machine, verifies its health, and then proceeds with a rolling restart for the rest of the machines, minimizing downtime and risk.
SOURCE: https://fly.io/docs/launch/deploy

LANGUAGE: bash
CODE:
```
fly deploy --strategy canary
```

----------------------------------------

TITLE: Common Fly.io Post-Deployment CLI Commands
DESCRIPTION: A collection of useful `fly` commands for managing your deployed application. These commands allow you to open the app in a browser, tail logs, check deployment status for the app or its database, and redeploy after changes.
SOURCE: https://fly.io/docs/languages-and-frameworks/crystal

LANGUAGE: shell
CODE:
```
fly apps open
fly logs
fly status
fly status -a postgres-database-app-name
fly deploy
```

----------------------------------------

TITLE: Deploy Fly.io Application to Production
DESCRIPTION: This command deploys your application to the production environment on Fly.io using the `fly deploy` command. It specifies the path to your production configuration file. After deployment, you should open your application in a browser to verify its functionality.
SOURCE: https://fly.io/docs/blueprints/going-to-production-with-healthcare-apps

LANGUAGE: shell
CODE:
```
fly deploy --path fly.production.yml
```

----------------------------------------

TITLE: Set DATABASE_URL Secret for Fly.io Application
DESCRIPTION: This command sets the DATABASE_URL environment variable as a secret in your Fly.io application. This allows your application to connect to the Managed Postgres instance using the provided connection string, which defaults to SSL for security.
SOURCE: https://fly.io/docs/mpg/overview

LANGUAGE: Shell
CODE:
```
fly secrets set DATABASE_URL="postgres://username:password@host:port/database"
```

----------------------------------------

TITLE: Configure Fly.io Service Proxy Behavior
DESCRIPTION: Defines how Fly Proxy connects incoming requests to services running within Machines. This example shows basic settings for an internal port, protocol, and auto-scaling behavior like `auto_stop_machines`, `auto_start_machines`, and `min_machines_running`.
SOURCE: https://fly.io/docs/reference/configuration

LANGUAGE: TOML
CODE:
```
[[services]]
  internal_port = 8080
  protocol = "tcp"
  auto_stop_machines = "stop"
  auto_start_machines = true
  min_machines_running = 0
```

----------------------------------------

TITLE: Go HTTP Server Application (main.go)
DESCRIPTION: This Go application uses the standard library's HTTP server and templates to serve an HTML page. It embeds templates using the `embed` package (Go 1.16+) and displays the Fly.io region that served the request. The server listens on port 8080 by default or a port specified by the `PORT` environment variable.
SOURCE: https://fly.io/docs/languages-and-frameworks/golang

LANGUAGE: Go
CODE:
```
package main

import (
    "embed"
    "html/template"
    "log"
    "net/http"
    "os"
)

//go:embed templates/*
var resources embed.FS

var t = template.Must(template.ParseFS(resources, "templates/*"))

func main() {
    port := os.Getenv("PORT")
    if port == "" {
        port = "8080"
    }

    http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        data := map[string]string{
            "Region": os.Getenv("FLY_REGION"),
        }

        t.ExecuteTemplate(w, "index.html.tmpl", data)
    })

    log.Println("listening on", port)
    log.Fatal(http.ListenAndServe(":"+port, nil))
}
```

----------------------------------------

TITLE: Heroku vs. Fly.io CLI Command Cheat Sheet
DESCRIPTION: A quick reference guide comparing common Heroku CLI commands with their equivalent Fly.io CLI commands for various tasks.
SOURCE: https://fly.io/docs/rails/getting-started/migrate-from-heroku

LANGUAGE: CLI Commands
CODE:
```
Task	Heroku	Fly
Deployments	git push heroku	fly deploy
Rails console	heroku console	fly ssh console --pty -C "/app/bin/rails console"
Database migration	heroku rake db:migrate	fly ssh console -C "/app/bin/rake db:migrate"
Postgres console	heroku psql	fly postgres connect -a <name-of-database-app-server>
Tail log files	heroku logs	fly logs
View configuration	heroku config	fly ssh console -C "printenv"
View releases	heroku releases	fly releases
Help	heroku help	fly help
```

----------------------------------------

TITLE: Create a basic FastAPI application
DESCRIPTION: Python code for a minimal FastAPI application that returns 'hello from fly.io' at the root URL.
SOURCE: https://fly.io/docs/python/frameworks/fastapi

LANGUAGE: python
CODE:
```
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
async def hello_fly():
    return 'hello from fly.io'
```

----------------------------------------

TITLE: Set Secure Application Secrets with flyctl secrets set
DESCRIPTION: Use `flyctl secrets set` to securely store sensitive information, such as API keys or database credentials, that should not be committed to version control. This command stages the secret for the next deployment, making it available to your application.
SOURCE: https://fly.io/docs/languages-and-frameworks/dockerfile

LANGUAGE: Shell
CODE:
```
flyctl secrets set MY_SECRET=romance
```

----------------------------------------

TITLE: Create a Kafka Cluster using flyctl
DESCRIPTION: This command initiates the creation of a new Upstash Kafka cluster. It prompts the user for organization, cluster name, and primary region.
SOURCE: https://fly.io/docs/upstash/kafka

LANGUAGE: flyctl
CODE:
```
flyctl ext kafka create
```

----------------------------------------

TITLE: Broadcast Turbo Stream Updates from Rails Controller
DESCRIPTION: Modify the `update` action in `app/controllers/names_controller.rb` to broadcast changes to the 'names' channel using `broadcast_replace_later_to`. This ensures that real-time updates are pushed to connected clients after a successful name update.
SOURCE: https://fly.io/docs/rails/the-basics/turbo-streams-and-action-cable

LANGUAGE: Ruby
CODE:
```
  # PATCH/PUT /names/1 or /names/1.json
  def update
    respond_to do |format|
      if @name.update(name_params)
        format.html { redirect_to name_url(@name), notice: "Name was successfully updated." }
        format.json { render :show, status: :ok, location: @name }
+
+       @name.broadcast_replace_later_to 'names', partial: 'names/name'
      else
        format.html { render :edit, status: :unprocessable_entity }
        format.json { render json: @name.errors, status: :unprocessable_entity }
      end
    end
  end
```

----------------------------------------

TITLE: Basic Usage of fly machine run
DESCRIPTION: Shows the command-line syntax for `fly machine run`, including the required image argument and optional command and flags.
SOURCE: https://fly.io/docs/machines/run

LANGUAGE: bash
CODE:
```
fly machine run <image> [command] [flags]
```

----------------------------------------

TITLE: Deploy Fly.io Application with Volume Changes
DESCRIPTION: Deploys the application to apply any changes made to the `fly.toml` configuration, including new volume mounts. This step ensures that newly created volumes are properly attached to the Machines.
SOURCE: https://fly.io/docs/launch/volume-storage

LANGUAGE: bash
CODE:
```
fly deploy
```

----------------------------------------

TITLE: GitHub Actions Workflow for Fly.io Continuous Deployment
DESCRIPTION: This YAML configuration defines a GitHub Actions workflow named 'Fly Deploy'. It triggers on pushes to the 'master' (or 'main') branch, runs on 'ubuntu-latest', and ensures only one deployment action runs at a time. The workflow checks out the code, sets up the `flyctl` CLI tool, and then executes `flyctl deploy --remote-only` using a `FLY_API_TOKEN` stored as a GitHub secret for authentication.
SOURCE: https://fly.io/docs/app-guides/continuous-deployment-with-github-actions

LANGUAGE: yaml
CODE:
```
name: Fly Deploy
on:
  push:
    branches:
      - master    # change to main if needed
jobs:
  deploy:
    name: Deploy app
    runs-on: ubuntu-latest
    concurrency: deploy-group    # optional: ensure only one action runs at a time
    steps:
      - uses: actions/checkout@v4
      - uses: superfly/flyctl-actions/setup-flyctl@master
      - run: flyctl deploy --remote-only
        env:
          FLY_API_TOKEN: ${{ secrets.FLY_API_TOKEN }}
```

----------------------------------------

TITLE: Example Fly.io Autostop/Autostart Configuration in fly.toml
DESCRIPTION: This example demonstrates how to configure autostop and autostart settings within the `[[services]]` section of a `fly.toml` file. It sets `auto_stop_machines` to 'stop', `auto_start_machines` to true, and `min_machines_running` to 1, ensuring machines stop when idle but at least one remains running in the primary region.
SOURCE: https://fly.io/docs/launch/autostop-autostart

LANGUAGE: TOML
CODE:
```
...
[[services]]
  internal_port = 8080
  protocol = "tcp"
  auto_stop_machines = "stop"
  auto_start_machines = true
  min_machines_running = 1
...
```

----------------------------------------

TITLE: Install flyctl on macOS or Linux using install script
DESCRIPTION: Downloads and executes the official Fly.io install script to set up `flyctl` on macOS or Linux. Users must manually add the `flyctl` directory to their shell's PATH environment variable after installation for global access.
SOURCE: https://fly.io/docs/flyctl/install

LANGUAGE: Shell
CODE:
```
curl -L https://fly.io/install.sh | sh
```

----------------------------------------

TITLE: Deploy Application to Fly.io
DESCRIPTION: Deploys the application to Fly.io, applying the configurations defined in `fly.toml` and triggering the build and release process.
SOURCE: https://fly.io/docs/rails/getting-started/migrate-from-heroku

LANGUAGE: bash
CODE:
```
fly deploy
```

----------------------------------------

TITLE: Deploy Application to Fly.io
DESCRIPTION: Deploys the containerized application to Fly.io using the `fly.toml` configuration and Dockerfile. The command identifies the app name from `fly.toml`.
SOURCE: https://fly.io/docs/js/frameworks/svelte

LANGUAGE: shell
CODE:
```
fly deploy
```

----------------------------------------

TITLE: Set a runtime secret for a Fly App
DESCRIPTION: Use the `fly secrets set` command to define a secret, which will be available as an environment variable (e.g., `DATABASE_URL`) within your application processes. This action triggers a restart of affected Machines and resets their ephemeral file systems.
SOURCE: https://fly.io/docs/reference/secrets

LANGUAGE: Shell
CODE:
```
fly secrets set DATABASE_URL=postgres://example.com/mydb
```

----------------------------------------

TITLE: Fly.io Proxy Reachability Warning Message
DESCRIPTION: This warning message indicates that the application is not listening on the expected address, preventing it from being reachable by the Fly.io proxy. It suggests configuring the application to listen on "0.0.0.0:3000" to resolve the issue.
SOURCE: https://fly.io/docs/js/the-basics/listening-ports

LANGUAGE: Plain Text
CODE:
```
WARNING The app is not listening on the expected address and will not be reachable by fly-proxy.
You can fix this by configuring your app to listen on the following addresses:
  - 0.0.0.0:3000
```

----------------------------------------

TITLE: Set DATABASE_URL Environment Variable with flyctl
DESCRIPTION: This command demonstrates how to set the `DATABASE_URL` environment variable using `flyctl secrets set`, making it available to your application running on Fly.io.
SOURCE: https://fly.io/docs/postgres/connecting/app-connection-examples

LANGUAGE: bash
CODE:
```
flyctl secrets set DATABASE_URL=postgres://postgres:secret123@postgresapp.internal:5432/yourdb
```

----------------------------------------

TITLE: Connect to Fly Postgres with psql
DESCRIPTION: Demonstrates how to connect to a Fly Postgres cluster using `psql` from a local machine via a WireGuard tunnel or from a Fly app shell. It starts an interactive terminal session on the cluster leader.
SOURCE: https://fly.io/docs/postgres/connecting/app-connection-examples

LANGUAGE: Shell
CODE:
```
psql postgres://postgres:secret123@appname.internal:5432
```