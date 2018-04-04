---
layout: post
title: Running test-kitchen with Docker
---
With some configurability, Docker is a great choice to test chef recipes in a cookbook. While there are a few small gotchas, I've marked my journey below. I configured this to work successfully on a set of existing cookbooks I inherited and the production instance they run on. Most of the following info involves spinning up a new cookbook and a boilerplate recipe, but the content I pulled directly from my solutions. Feel free to reach out to me on [twitter](https://twitter.com/fuhton) about questions, corrections, or comments.

## Pre-Setup
To start it's expected you have the most recent version of these tools already installed on your workstation.

* [Ruby](https://github.com/rbenv/rbenv)
  * Install the most recent version
* [Bundle - gem installer](http://bundler.io/v1.12/)
* [Docker](https://docs.docker.com/install/)
* [Docker CLI](https://docs.docker.com/docker-cloud/installing-cli/)
* [Chef](https://docs.chef.io/install_dk.html)

## Setup

Start by generating a new cookbook

```bash
$ chef generate cookbook new-cookbook-name
```

This will generate a new directory named after the supplied cookbook name (or `new-cookbook-name` if you copy/pasted). You can enter this directory and poke around. It's a bare cookbook with a default recipe that currently doesn't do anything.

Next we'll want to list our ruby dependencies into a Gemfile in our cookbooks.

```bash
# new-cookbook-name/Gemfile
source "https://rubygems.org"

gem "chef"
gem 'berkshelf'
gem "test-kitchen"
gem "kitchen-docker"
gem "kitchen-inspec"
```

Using `test-kitchen` as our testing harness we can utilize most kitchen settings. We specify `kitchen-docker` as a VM and `kitchen-inspec` as our testing framework. Using Docker keeps all results of the recipes from affecting our workstation and helps clean up sticky situations.

We'll also want to specify the testing config in a `.kitchen.yml` file. (You might have to override an existing `.kitchen.yml` file!)

```yml
# new-cookbook-name/.kitchen.yml
---
driver:
  name: docker
  use_sudo: false

provisioner:
  name: chef_zero
  always_update_cookbooks: true

verifier:
  name: inspec

platforms:
  - name: ubuntu-14.04
    driver_config:
      image: ubuntu:14.04
      platform: ubuntu

suites:
  - name: default
    run_list:
      - recipe[new-cookbook-name::default]
    verifier:
      inspec_tests:
        - test/recipes
    attributes:
```

Next we need a recipe and a test for the recipe. The first snippet below is a source file for our recipe and the second is our default recipe. This is what we'll be testing, and while it's a silly, lightweight example, it gives us a clear use case.

```bash
# new-cookbook-name/files/default/log.sh
#!/bin/bash

echo "this is the log.sh"
```
```ruby
# new-cookbook-name/recipes/default.rb
directory "/etc/testbook" do
  owner "root"
  group "root"
  mode "0755"
  action :create
end

cookbook_file "/etc/testbook/log.sh" do
  source "log.sh"
  group "root"
  owner "root"
  mode "0644"
end
```

And the testing file that confirms the above output:

```ruby
# new-cookbook-name/test/recipes/default_test.rb

describe directory("/etc/testbook") do
  it { should exist }
end

describe file("/etc/testbook/log.sh") do
  it { should exist }
  its('content') { should match /echo "this is the log.sh"/ }
end
```

To wrap up the setup, we'll install all ruby gems and create the [kitchen](https://kitchen.ci/docs/getting-started/instances). Be sure Docker is running locally.

```bash
$ bundle install
$ kitchen create
```

This will create a `.kitchen` directory in your cookbook. This directory can be ignored for now - it contains the definition of our VM, the connection keys to SSH in, and our logs.

Next up we'll want to create the VM and run our defined commands in `suites::run_list` in the `.kitchen.yml` using [converge](https://kitchen.ci/docs/getting-started/running-converge).

```bash
$ kitchen converge
```

Once that's complete, we're ready to test the output of our recipes - the files they create or processes they start. We'll use the [`verify`](https://kitchen.ci/docs/getting-started/running-verify) command for this.

```bash
$ kitchen verify
```

To keep our workstation (and our running Docker instances to minimum) wrap up with the [`destory`](https://kitchen.ci/docs/getting-started/excluding-platforms) command.

```bash
$ kitchen destroy
```

This should get you running with a lightweight setup! Reach out to me on [twitter](https://twitter.com/fuhton) with any questions, corrections, or comments.

