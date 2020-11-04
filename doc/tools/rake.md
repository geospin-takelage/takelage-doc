# takelage-doc: rake

[Table of contents](../../README.md)

## Overview 

- [Introduction](#introduction)
  - [rake](#rake)
  - [example](#example)
- [Usage rake ansible](#ansible)

<a name="introduction"/>

# Introduction

<a name="rake"/>

## rake Task Management Tool

[rake](https://github.com/ruby/rake) is ruby's make.
In takelage it is used as a command wrapper.
While the 
[tau](https://github.com/geospin-takelage/takelage-cli) 
handles global not project specific tasks
like “log in to the development environment”
or “download updates of bit components”
rake handles project specific tasks like
“test the build” or “tag the image”.

rake tasks reside in Rakefiles in directories
below a rake directory in the project root directory.
The directory nesting is echoed in namespaces
which create a command hierarchy.

Lots of takelage Rakefiles are shared as 
[bit](../tau/tau_bit.md)
components. They can be added to a project
if needed but no project needs all Rakefile.

Some of takelage's Rakefiles are parametrized
and outsource the heavy work to library files
which are plain ruby files that can be found 
in lib directories.

You can have a look at the rake tasks by running

```
rake
```

In takelage, this is a shortcut for `rake -T` which
shows all currently available rake tasks.
You can display all tasks with their dependencies with

```bash
rake -P
```

<a name="example"/>

## Example

Let's have a look at a minimal example.
We want a rake shortcut to create a git tag
with the project version if such a tag 
does not exist yet 
and push the local tags to remote afterwards.

Relative to our project root, we create the file 
`rake/git/Rakefile` with this content:

```ruby
# frozen_string_literal: true

require 'rake'

cmd_git_tag = 'git rev-parse ' \
    "#{@project['version']} " \
    '>/dev/null 2>&1 || ' \
    '(git tag -s -m ' \
    "'#{@project['version']}' " \
    "#{@project['version']} && " \
    'git push --tags)'

namespace :git do
  desc 'Create and push git tag'
  task :tag do
    @commands << cmd_git_tag
  end
end
```

This Rakefile (in combination with the 
[rake meta bit component](https://github.com/geospin-takelage/takelage-dev/blob/master/rake/meta/Rakefile))
gives us a rake shortcut

```bash
rake git:tag
```

<a name="ansible"/>

# Usage rake ansible

These rake tasks handle the development of a project or a role. 

Here is an example:

```bash
rake ansible:docker:takelbase:project:prod:from_base:converge
```

This task will run `molecule converge` on the
production environment building on the 
[takelbase](https://hub.docker.com/r/takelage/takelbase)
docker base image.

Similar tasks exist for every role and for every molecule command.
So in this case, a handful of Rakefiles can lead to dozens of tasks.

You can have a look what commands a task will run by appending `run=dry`:

```
$ rake ansible:docker:takelbase:project:prod:from_base:converge run=dry
cd ansible && bash -c 'TAKELAGE_PROJECT_ENV=prod TAKELAGE_PROJECT_BASE_IMAGE=takelage/takelbase TAKELAGE_PROJECT_NAME=takelage-dev TAKELAGE_MOLECULE_CONVERGE_PLAYBOOK=playbook-project-base.yml molecule converge --scenario-name default'
```