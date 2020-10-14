# Next.js deployment. Elephant SQL. Heroku

## [Deployment](https://nextjs.org/docs/deployment)

## Getting started
If you haven’t already done so, push your Next.js app to a Git provider of your choice: GitHub, GitLab, or BitBucket. Your repository can be private or public.

Then, follow these steps:

1. Sign up to Vercel (no credit card is required).
2. After signing up, you’ll arrive on the “Import Project” page. Under “From Git Repository”, choose the Git provider you use and set up an integration. (Instructions: GitHub / GitLab / BitBucket).
3. Once that’s set up, click “Import Project From …” and import your Next.js app. It auto-detects that your app is using Next.js and sets up the build configuration for you. No need to change anything — everything should work just fine!
4. After importing, it’ll deploy your Next.js app and provide you with a deployment URL. Click “Visit” to see your app in production.
Congratulations! You’ve just deployed your Next.js app! If you have questions, take a look at the Vercel documentation.

Node.js Server
Next.js can be deployed to any hosting provider that supports Node.js. This is the approach you should take if you’re using a custom server.

Make sure your package.json has the "build" and "start" scripts:

{
  "scripts": {
    "dev": "next",
    "build": "next build",
    "start": "next start"
  }
}

## [ElephantSQL](https://www.elephantsql.com/docs/index.html)

ElephantSQL is a PostgreSQL database hosting service. ElephantSQL will manage administrative tasks of PostgreSQL, such as installation, upgrades to latest stable version and backup handling.

ElephantSQL is also integrated to several cloud application platforms (also known as PaaS). With a click of a button your database is provisioned in the same data center as your application is hosted, and is ready to be used immediately.

## Getting started
To get started you need to sign up for a customer plan. What plan you want to use depend of your needs. We offer five different service plans, three dedicated clusters and two shared clusters.

You can try ElephantSQL for free with the plan Tiny Turtle. All other plans are billed by the second, so you can try out even the largest instance types for mere pennies. Billing occurs at the end of each month, and you're only charged for the time an instance has been available to you.

It is easy to get started with ElephantSQL. Create an account by clicking Sign up and enter your email address or use a third-party service. If you already have an account, just enter your email and password and press the Log in button.

## Create a new instance
If you would like to create plan Pretty Panda or larger, you need to start by adding a credit card to your ElephantSQL account.

To create a new instnace, press + Create New Instance

* Name: The name of the instance. This name should be a name that helps you identify which application the instance is used by.
* Plan: The plan you would like to have.
* Tags: used to separate your instances between projects. This is primarily used in the project listing view for easier navigation and access control. Tags allow admins to manage team members access to different groups of instances.
* Data center: Choose the datacenter and region of where your instance will be located. Choose the datacenter that is closest to the servers of your application - you want the latency to be as low as possible. When creating a shared instance, not all data centers will be available.

## [Building Docker Images with heroku.yml](https://devcenter.heroku.com/articles/build-docker-images-heroku-yml)

### Getting started
1. Create a heroku.yml file in your application’s root directory. The following example heroku.yml specifies the Docker image to build for the app’s web process:

build: \
  docker:\
    web: Dockerfile\
run:\
  web: bundle exec puma -C config/puma.rb

2. Commit the file to your repo:

git add heroku.yml\
git commit -m "Add heroku.yml"

3. Set the stack of your app to container:

heroku stack:set container

4. Push your app to Heroku:

git push heroku master

## heroku.yml Overview
A heroku.yml manifest has 4 top-level sections:

setup - Specifies the add-ons and config vars to create during app provisioning
build - Specifies the Dockerfile to build
release - Specifies the release phase tasks to execute
run - Specifies process types and the commands to run for each
Details for each of these sections are described below.

Here’s an example that illustrates using a heroku.yml manifest to build Docker images:

setup:
  addons:
    - plan: heroku-postgresql
      as: DATABASE
  config:
    S3_BUCKET: my-example-bucket
build:
  docker:
    web: Dockerfile
    worker: worker/Dockerfile
  config:
    RAILS_ENV: development
    FOO: bar
release:
  command:
    - ./deployment-tasks.sh
  image: worker
run:
  web: bundle exec puma -C config/puma.rb
  worker: python myworker.py
  asset-syncer:
    command:
      - python asset-syncer.py
    image: worker