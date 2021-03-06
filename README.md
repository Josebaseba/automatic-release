# Automate your releases

A release is just a number; It doesn't matter too much, but the important is have one.

If you are not releasing your software, then it will be complicated to rollback and differentiate changes between versions. 

But the real problem will be from a **user perspective**: It will not be easy to make evident what has changed.

Most of the problem with releasing software is because developers don't do that often: They tend to run it manually, making the releasing process a source of errors and problems.

Nowadays, we have the best tools ever dreamed to automate this task.

For your next release, it will be automatically doing:

- [x] Create a commit release in the git history.
- [x] Create a new git tag.
- [x] Create a `CHANGELOG` entry associated with your tag.
- [x] Create a GitHub Release.
- [x] Communicate the new version at Slack, Twitter, etc.

Let me show you how to.

## Git commit convention

> **Tip**: You can use a different [convention configuration](https://github.com/marionebl/commitlint#shared-configuration).

The most basic thing is to define a git commit convention to follow.

We are going to use [commitlint](https://github.com/marionebl/commitlint) for linting git messages.

![](https://i.imgur.com/nZOE5Vu.png)

It forces to follow a strict format into your git messages.

The format is used for automagically determinate the next version to release. Also, it is used to classify the commits by type into `CHANGELOG.md`.

If the format is invalid, you **can't** do the commit. 

<small>(Actually, you can use `--no-verify` ).</small>

## Commit Message Guidelines

> **Tip**: Read more on [Angular contribution guideline](https://github.com/angular/angular/blob/22b96b9/CONTRIBUTING.md#type).

The git message must to have a **type**, it can be one of the following:

* **build**: Changes that affect the build system or external dependencies.
* **ci**: Changes to our CI configuration files and scripts.
* **docs**: Documentation only changes.
* **feat**: A new feature.
* **fix**: A bug fix.
* **perf**: A code change that improves performance.
* **refactor**: A code change that neither fixes a bug or adds a feature.
* **style**: Changes that do not affect the meaning of the code.
* **test**: Adding missing tests or correcting existing tests.

I know. really, I know.

The first time you use a convention for commits you think it's *over engineering*. 

But after a time, it's **very helpful**. It makes easy read all the commits quickly or just focus in a determinate type of commits.

## Examples of git commits

All the following examples are common and valid:

```bash
build: update dependencies
ci: setup travis credentials
refactor: move scripts
fix: use user agent provided by parameters
test: update snapshots
style: use space instead of tabs
```

## Determinate the next version

After following a git commit schema, we are going to use [standard-version](https://github.com/conventional-changelog/standard-version) 

It will read your git history and will determinate what is the next version to release.

**patches** (`1.0.0` → `1.0.1`)

```sh
git commit -a -m "fix(parsing): fixed a bug in our parser"
```

**features** (`1.0.0` → `1.1.0`)

```sh
git commit -a -m "feat(parser): we now have a parser \o/"
```

**breaking changes** (`1.0.0` → `2.0.0`)

```sh
git commit -a -m "feat(new-parser): introduces a new parsing library
BREAKING CHANGE: new library does not support foo-construct"
```

In addition, GitHub usernames (`@kikobeats`) and issue references (`#133`) will be swapped out for the
appropriate URLs in your `CHANGELOG.md`.

For do that, add `npm run release` as npm script associated with `release`:

```json
{
  "scripts": {
    "release": "standard-version",
    "release:tags": "git push --follow-tags origin master",
    "postrelease": "npm run release:tags"
  }
}
```

Note that `postrelease` is not mandatory but it will push your git tags.

Then just do `npm run release` for releasing a new version

![](https://i.imgur.com/AmOfMV9.png)

The first time you release a version, a `CHANGELOG.md` will be created.

![](https://i.imgur.com/B2CoFsG.png)

The successive versions will increment the file adding a new entry.

## GitHub Release

> **Tip**: Use [direnv](https://direnv.net/) for declaring local development variables. 

GitHub (and GitLab as well) has a special space into the repository page for seeing the releases.

![](https://i.imgur.com/butKsZ6.png)

When you create a new git tag, it will show there, but as you can see it doesn't have information attached.

We are going to use a tool called [releaser-tools](https://github.com/conventional-changelog/releaser-tools) that will connect with GitHub/GitLab and setup a pretty release.

For do that, we are going to declare it as part of our `postrelease` tasks:

```json
{
  "scripts": {
  "postrelease": "npm run release:tags && npm run release:github",
  "release": "standard-version",
  "release:github": "conventional-github-releaser -p angular",
  "release:tags": "git push --follow-tags origin master"
  }
```

Next time, your metadata will be associated with the GitHub/GitLab release 🎉

![](https://i.imgur.com/4Am8xIx.png)

## Communicate your changes

> **Tip**: Use [tom](http://tom.js.org/) for sending multiple notification (Slack/Twitter/Telegram/Email).

It's the moment to make your changes public for the general public.

First, we need to retrieve our latest release published. 

In the case of GitHub, we can use:

**GitHub API**

<blockquote>
<p>e.g. <a href="https://api.github.com/repos/Kikobeats/automatic-release/releases/latest">https://api.github.com/repos/Kikobeats/automatic-release/releases/latest</a></p>
</blockquote>

**GitHub RSS Feed**

> e.g. [https://github.com/Kikobeats/automatic-release/releases.atom](https://github.com/Kikobeats/automatic-release/releases.atom)

Then, just we need to connect our release data with the service where we want to share the information.

This can be as simple as creating an [IFTTT](https://ifttt.com) recipe

<div align="center">
<img src="https://i.imgur.com/ZgUB7w5.png" width='200px' />
</div>