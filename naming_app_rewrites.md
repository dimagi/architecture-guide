# Naming an App Rewrite

## When to use

- you're rewriting a feature
- the feature is written as a single django app
- you've decided you want the features to live side by side for a time (so you have flexibibility in rolling out the new version)

## What to do

1. Pick a more descriptive name if the app is not well-named
2. Name the old/current version to `${name}_v1`
3. Call the new version `${name}_v2` in the same directory


## Example

Let's say I am rewriting the case importer.
The current case importer is implemented in a django app called `importer`, located at `corehq.apps.importer`.
I want to write my feature so that both the old and new versions can exist at the same time,
so I can work on the new version behind a toggle,
and so that there's flexibility in how to roll out the new version once it's complete.

1. Since `importer` is a poor name to begin with, I'm going to call it `case_importer`
2. I rename `importer` to `case_importer_v1`
3. Next to it I add the directory `case_importer_v2` and start working on my new app in there.


## Links

* This convention was first proposed in on Nov. 7, 2016 in this PR: https://github.com/dimagi/commcare-hq/pull/13851.
