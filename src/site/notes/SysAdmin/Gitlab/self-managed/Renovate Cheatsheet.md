---
{"dg-publish":true,"type":"webclip","url":"https://www.augmentedmind.de/2023/07/30/renovate-bot-cheat-sheet/","author":"Marius - Augmented Mind","topics":["renovatebot"],"permalink":"/sys-admin/gitlab/self-managed/renovate-cheatsheet/","dgPassFrontmatter":true}
---

# Renovate bot cheat sheet – the 11 most useful customizations

2023-09-042023-07-30 by [Marius](https://www.augmentedmind.de/author/marius/ "View all posts by Marius")

	Renovate bot is a tool that automatically updates third-party dependencies declared in your Git repository via pull requests. This Renovate bot cheat sheet helps teams who adopt Renovate with customizing the most common (and useful) configuration options, without having to read the _entire_, extensive Renovate bot documentation.

## Introduction

Renovate (Bot) is a CLI tool that regularly scans your Git repositories for outdated dependencies. If it finds any, it automatically creates a pull request (PR) in which the dependency is updated. I highly recommend my Renovate Bot [introduction article](https://www.augmentedmind.de/2023/07/30/renovate-bot-introduction/) to get you started with the basics.

If you want to introduce automatic dependency updates (using Renovate) to the development teams in your organization, you want to make sure that they actually use it, and that they are happy with it. Happiness only comes if your teams manage to configure the Renovate bot well, so that it does the _right_ thing, and does not cause them unnecessary work. Since your developers might shy away from the verboseness of the official [Renovate docs](https://docs.renovatebot.com), I suggest that you provide them with a simplified cheat sheet, like the one presented here. The cheat sheet (which you need to adapt in some cases) should cover the _onboarding process_ (answering the question: “as a developer, I want to know how to make the Renovate bot visit my Git repository”), and include a set of the most sensible configuration options.

## Renovate bot cheat sheet

The following subsections are topics that make sense to be placed on a cheat sheet. **Those starting with “\[Meta\]” are meta-level instructions that you need to adapt first.** The other sections you can simply copy and paste.

**You can find a suggestion for the `renovate.json5` configuration file [here](https://gist.github.com/MShekow/9fb18735a4c1ac4ca6554351b859c3da)**. It incorporates all tips from below in a single file. Just like with the \[Meta\] sections, **you should also adapt this file, prior to offering it to your developers!**

### #1 \[Meta\] Configure Renovate bot to visit your Git repository

If your repository is on GitHub.com, your cheat sheet instructions should instruct the developer to follow the [official Renovate docs](https://docs.renovatebot.com/getting-started/installing-onboarding/#hosted-githubcom-app) to install the Renovate GitHub app. The app allows the teams to configure which of their repositories the bot should visit.

Otherwise, e.g. if your repository is on GitLab or other SCM systems, the team that operates the bot needs to provide instructions, which depend on the concrete platform and configuration of Renovate. These instructions should also include the bot’s execution interval, because that interval causes a delay that the developers should be made aware of. Otherwise, developers might wonder why nothing is happening, after they followed your instructions to invite the Renovate SCM user into their repository. It’s not that they did something wrong, it’s just that the bot may visit their repository only once per hour.

### #2 Configuring the bot’s behavior

To be flexible, you can customize Renovate’s behavior for each Git repository that it visits. The configuration is stored in the `_renovate_.json` (or `_renovate_.json**5**`) file, located at the _root_ of the _default_ branch your repository. The official reference for what you can put into this file is found on the [Configuration Options](https://docs.renovatebot.com/configuration-options/) documentation page, but a good first start is to just apply the tips from this cheat sheet.

After you configured the bot to visit your repository (as instructed above in tip #1), the first thing it will do is to look for this `_renovate_.json` file. If it doesn’t find one, the bot creates an _onboarding_ branch and corresponding Pull Request (PR).

The initial content of the `renovate.json` file (in the onboarding branch) is a _very basic_ configuration, which you should tweak (following this cheat sheet), _prior_ to clicking “merge” for the onboarding PR. To do this, check out the PR’s corresponding branch (“`renovate/configure`“) with Git, and update the file’s content with new commits (and push them). The next time the Renovate bot visits your repository, it will detect that you changed the `renovate.json` file, and update the PR’s _description text_ accordingly.

Note that if you make mistakes while changing the `renovate.json` file (e.g. syntax errors), the bot will create an _issue_ in your repository, that contains the error message. To avoid this, you can use the `renovate-config-validator` CLI tool (see [docs](https://docs.renovatebot.com/config-validation/)) to _validate_ the `renovate.json` file, prior to committing. If you have Docker, you can run the following command to validate the file:

```bash
docker run -v /path/on/host/to/renovate.json:/usr/src/app/renovate.json -it renovate/renovate renovate-config-validator
```

Once the onboarding PR was merged, Renovate bot will start creating new branches (and PRs), for outdated dependencies.


> [!NOTE] Updating the configuration later
> You can still update the `renovate.json` file even _after_ the onboarding PR was merged. Renovate bot will always use the most recent `renovate.json` file it finds in the repository’s default branch.


### #3 Disable updates for specific dependencies or programming languages

Put `"enabled": false` into an object inside the `packageRules` array. For instance, to disable dependency updates for the dependency named `neutrino`, the `packageRules` array should look as follows:

```json
"packageRules": [
  {
    "matchPackageNames": ["neutrino"],
    "enabled": false
  }
]
```

An alternative to the `enabled` setting is the `enabledManagers` option, where you configure an _allow_\-list of package managers (see [official docs](https://docs.renovatebot.com/configuration-options/#enabledmanagers) for more information), e.g.:


```json
{
  "enabledManagers": ["dockerfile", "npm"]
}
```

### #4 \[Meta\] Configure PR assignees

By default, Renovate creates a _PR_ for the new _branch_ in which the dependency is updated. You may want the bot to automatically assign specific developers of your team to these PRs. This is possible with the `assignees` setting ([docs](https://docs.renovatebot.com/configuration-options/#assignees)), which you set to a list of usernames or email addresses (depending on the SCM platform – **adapt this!**), e.g.:

```json
"assignees": ["peter.pan", "mister.proper"]
```

### #5 Avoid spam via scheduling and grouping

If the dependencies your application uses do change _often_, Renovate’s activities of constantly creating new PRs will become annoying. Even if you merged them right away, new ones would continuously be created.

Using `schedule` ([docs](https://docs.renovatebot.com/configuration-options/#schedule)), you can tell Renovate bot to only update the dependency on specific times of day/week/month.

Example use case #1: avoid interference during working hours


Set `schedule` to `["after 10pm every weekday", "before 5am every weekday", "every weekend"]`, to stop Renovate bot from creating branches and PRs during working hours (in this example: Mo-Fri between 5 AM – 10 PM each). Without such a rule, the bot might interrupt the merge process of _your own_ feature branches into the default branch. In this example, the bot is limited to only update dependencies on weekends (at _any_ time during weekends!), _or_ outside of working hours during week days. Regarding the logic: the bot logically ORs the different array entries, but ANDs the meaning of the words _within_ a specific entry. If none of the entries evaluate to `true`, the bot will not update this dependency in this execution cycle.

Renovate uses the [later.js](https://breejs.github.io/later/) library under the hood to parse the `schedule` strings. You can find out more about the text parser grammar [here](https://breejs.github.io/later/parsers.html) (see section “Text parser” on that page). You can also use [this CodePen](https://codepen.io/rationaltiger24/full/ZExQEgK) to check whether your statements work.

Example use case #2: manually oversee dependency updates

Suppose that you want to _manually_ verify whether new dependencies _really_ work. This might be the case if certain aspects of your application cannot be _automatically_ tested in a CI pipeline, or you simply don’t have automatic tests yet. Suppose you are willing to do this verification once per week, on Wednesday morning. You can achieve this with a `schedule` set to `["on wednesday before 6am"]`. By the time you get to work (let’s say at 8 AM on Wednesday), Renovate bot should have updated the dependencies, and you can take over and confirm that everything still works.

Regarding the scope: you can either configure the `schedule` option _globally_ (by defining the `schedule` key at the _root_ level of the `renovate.json` file), or limit it to particular dependencies, by adding the `schedule` key to a rule object that is part of the `packageRules` array.

You can reduce spam even further using the _grouping_ feature. Here you specify a list of dependency / package names (or update types) that should be grouped together. When the bot (in a single execution cycle) discovers that 3 dependencies (matching the group selectors) have updates, then the bot will only create a _single_ branch (and corresponding PR), rather than 3, because of that grouping. The branch / PR will contain the updates of all 3 dependencies combined.

It can be beneficial to combine the _schedule_ feature with the _grouping_ feature, as otherwise the grouping may often not take any effect. I recommend you take a closer look at the official docs [here](https://docs.renovatebot.com/noise-reduction/#package-grouping) (also useful: see [here](https://docs.renovatebot.com/configuration-options/#packagerules) and [here](https://docs.renovatebot.com/configuration-options/#groupname)).

### #6 \[Meta\] Avoid spam via automatic merging

If a third-party dependency follows [semantic versioning](https://semver.org/), where breaking changes only happen in _major_ updates, you can (usually) safely merge _minor_– or _patch_\-level updates in a _fully automated_ manner. Renovate bot offers the `automerge` ([docs](https://docs.renovatebot.com/configuration-options/#automerge)) configuration option for this purpose. A snippet like the following automatically merges minor and patch updates, for any dependency of any ecosystem / programming language:

```json
"packageRules": [
  {
    "description": "Automatically merge minor and patch-level updates",
    "matchUpdateTypes": ["minor", "patch", "digest"],
    "automerge": true,
    // Force Renovate to not create a PR (but merge its branches directly), to avoid PR-related email spam
    "automergeType": "branch"
  }
]
```

You might want to be _notified via email_ by your SCM (such as GitLab) whenever such an automatic update was merged by Renovate bot. A reason to do so might be that you don’t fully trust your CI pipeline (or don’t have one yet). If you do want PR-related notifications, just remove the `**"automergeType": "branch"**` line. Then, the default automatic merge behavior (`"automergeType": "**pr**"`) applies, where Renovate always creates a PR right after it created the branch. The creation of the PR then causes the email notification.

Note that Renovate won’t automatically merge PRs with failed CI pipeline runs.

The default flow of an automatic merge is as follows:

1.  Renovate bot visits your repository, finds an outdated dependency (for which the bot has not yet created a branch/PR), and thus it creates a branch (and possibly a PR, depending on configuration options like `automergeType` or `[prCreation](https://docs.renovatebot.com/configuration-options/#prcreation)`).
2.  The CI pipeline runs on the just-created branch and/or (virtual) merge commit of the PR
3.  Renovate bot visits your repository again (e.g. one hour later, depending on how it is configured – **adapt this**). It finds the outdated dependency again, recognizes that it has already created a branch/PR for it. Therefore, the bot clicks the “merge” button for you – but only if the CI pipeline for the just-created branch has successfully finished. If this assumption does not hold, Renovate bot does not automatically merge the dependency (yet), and waits for you to fix the branch so that the CI pipeline passes.

As you can see, automatic merging takes some time. It cannot be completed in just a single execution cycle of the bot.

**Adapt this:** In case you use any SCM other than Bitbucket, you can enable the `platformAutoMerge` ([docs](https://docs.renovatebot.com/configuration-options/#platformautomerge)) setting, which speeds up the automatic merging process. Assuming you allowed automatic merges on the SCM platform, Renovate can use that feature in the PRs it creates. The platform will then automatically merge the PR for you, once the corresponding pipeline has successfully finished (after step 2 above has finished).

In practice, you may want to limit automatic merging only to specific dependencies/packages, or programming languages. See the [Disabling updates section](https://www.augmentedmind.de/2023/07/30/renovate-bot-cheat-sheet//#disable-updates) for how to achieve this.



> [!NOTE] Privilege issues
> If the branch (into which Renovate shall automatically merge changes to) is a _protected_ branch, you have to ensure that the bot’s user account has sufficient privileges. See the [official docs](https://docs.renovatebot.com/key-concepts/automerge/#frequent-problems-and-how-to-resolve-them) for more information. Otherwise the merge attempt will just _silently_ fail!



### #7 Configure branches considered by the bot

By default, Renovate bot looks for `renovate.json` in the _default_ branch of your repository (typically `master` or `main`), and then scans for outdated dependencies _only_ in that default branch. In other words, the bot creates new, temporary branches (in which the dependencies are updated to the newest version) from the default branch, and configures the PR to merge that temporary branch into the default branch again.

If you want Renovate bot to scan for outdated dependencies in other branches, you have to set the `baseBranches` option ([docs](https://docs.renovatebot.com/configuration-options/#basebranches)) to an array of branch names. A possible use case might be that you are not actively developing on the default branch (but some other one, e.g. `dev`), or you might want Renovate to keep multiple release streams up to date, e.g. by setting `"baseBranches": ["main", "next"]`.

There are two caveats to be aware of:

-   By default, Renovate does read the contents of the `renovate.json` file in any of the `baseBranches` , but uses the `renovate.json` file on the _default branch_. If you want to change this behavior, set `[useBaseBranchConfig](https://docs.renovatebot.com/configuration-options/#usebasebranchconfig)` to `merge`.
-   If you specify _multiple_ branches in `baseBranches`, and the bot detects that a specific dependency is outdated in, say, 2 of these base branches, it will create _two_ new, temporary branches (and corresponding PRs) – one for each base branch.

A possible solution to the second caveat is to use the `matchBaseBranches` ([docs](https://docs.renovatebot.com/configuration-options/#matchbasebranches)) option, which lets you scope `packageRules` to specific branches, and thus, you can create rules which are branch-specific.

### #8 Fix default branch _rebasing_ behavior

Whenever Renovate bot creates a temporary branch from, say, the _default_ branch, this temporary branch (and corresponding PR) might be open for a longer time period (e.g. until you find time to merge the PR). In the meantime the target branch (here: the default branch) might evolve, and the temporary branch becomes _stale_.

The default behavior of Renovate bot is to only rebase the temporary branch if merging with the target branch would cause merge conflicts. This choice was made to avoid overloading your CI/CD pipelines (e.g. when you have 10 PRs, rebasing every PR at once would trigger 10 pipelines). However, you can tell Renovate bot to _always_ rebase any still-open _stale_ temporary branch. Simply set `rebaseWhen` to `"behind-base-branch"`, or add “`:rebaseStalePrs`” at the end of your `"extends"` block:

```json
"extends": ["config:recommended", ":rebaseStalePrs"]
```

Also, Renovate never rebases PRs that contain commits made by a _different_ SCM user, to avoid losing work done in these commits. You can still force Renovate to rebase these branches, via a checkbox shown at the bottom of the PR’s description.

### #9 Handle pulled dependency updates

A dependency is said to be “pulled” if the author of that dependency first uploaded it to the corresponding repository (e.g. NPM or PyPi), but then decided to delete (“pull”) it shortly after, e.g. because it was incorrectly packaged, or contained critical bugs. Renovate offers two approaches to deal with pulled updates – _reaction_ (via `rollbackPrs`) and _prevention_ (via `` `minimumReleaseAge` ``):

-   `rollbackPrs` ([docs](https://docs.renovatebot.com/configuration-options/#rollbackprs)), when set to `true`, tells the bot to create PRs that roll back versions, if the currently-pinned version is higher than the newest one found in the registry
-   `minimumReleaseAge` ([docs](https://docs.renovatebot.com/configuration-options/#minimumreleaseage)) simply _delays_ the creation of a PR for `x` days (but still _immediately_ creates the temporary _branch_), after detecting an update for a specific dependency. If Renovate detects that this update has unexpectedly disappeared from the registry (within these `x` days), the bot will simply delete the corresponding branch again. I recommend you read the docs to learn how this feature works in detail.

### #10 Improve overview of open PRs (created by Renovate Bot)

Especially in big projects you might lose overview of the large number of PRs. Which ones were created by your team, and which ones are just automatically created by the bot? There are two mechanisms that assist you: the _dependency dashboard_, and automatically _adding labels_.

`addLabels` ([docs](https://docs.renovatebot.com/configuration-options/#addlabels)) tells Renovate bot to add labels to the PRs it created. Since these labels are represented by colored “pills” in the list of PRs (shown in some browser web view), you can visually tell apart issues created by the bot from other issues. You can also use labels to e.g. indicate the affected programming language, or the severity (how fast you should take care of merging the PR).

The Dependency Dashboard (which is enabled by default in the `config:recommended` preset, see also `[dependencyDashboard](https://docs.renovatebot.com/configuration-options/#dependencydashboard)`) is a GitHub/GitLab/etc. _issue_, whose description text lists all PRs created by the bot in one place. These PRs can have any state, including pending, open, closed, or error. It contains clickable links, e.g. to rebase/retry multiple PRs without having to open each one individually. I recommend you read the docs of `dependencyDashboard` and its sub-options (such as `dependencyDashboardApproval`) to understand how it works. The Dependency Dashboard also informs you of any errors Renovate encountered while trying to create PRs, e.g. missing credentials for private registries (or the inability to connect to them).

### #11 Keep up to date with Renovate Bot’s development

Renovate itself is software that is updated frequently. Whenever a new _major_ version of Renovate is released, there might be breaking changes, such as syntax changes in the `renovate.json` configuration file, or new features. Thus, assuming that whoever _operates_ the bot always uses the most recent (major) version, it makes sense for you to implement a mechanism that notifies you in case of major version updates of Renovate. This triggers you to look at Renovate’s changelog, profit from new features, and keep up to speed regarding possible syntax deprecations. Note that if you don’t care about new features or deprecations, you don’t strictly need this, because Renovate will create an _issue_ in your repository, if the `renovate.json` file contains errors.

As a notification mechanism, you can create a “fake” `Dockerfile` (which is never built into an image, but Renovate still processes it), e.g. located at `"renovate-update-notification/Dockerfile"`. Below is an example for it’s content:

```dockerfile
# This file is processed by Renovate bot so that it creates a PR (notifying us) on new major Renovate versions
FROM renovate/renovate:35
```

You should also customize the configuration for that `Dockerfile` in your `renovate.json` file, e.g.:

```json
{
  "packageRules": [
    {
      "description": "Disables the creation of branches/PRs for any minor/patch updates etc. of Renovate bot",
      "matchPaths": ["renovate-update-notification/Dockerfile"],
      "matchUpdateTypes": ["minor", "patch", "pin", "digest", "rollback"],
      "enabled": false
    },
    {
      "description": "Causes the bot to create a PR (and thus, an email notification), whenever there is a new major Renovate version",
      "matchPaths": ["renovate-update-notification/Dockerfile"],
      "matchUpdateTypes": ["major"],
      // you can also set automerge to true - emails for the PRs will already have been sent anyway, so there is
      // no strict reason to keep the PR open - unless you want to associate it with updates you make to renovate.json5
      "automerge": false,
      // just re-states the default and ensures that PRs are really created - you can remove this line
      // if you did not change "prCreation" elsewhere to some non-default value
      "prCreation": "immediate",
    }
  ]
}
```

At the time of writing, there is a new major Renovate version release coming roughly every 2 months. If this is too noisy for you, you can reduce the update frequency, by adding something like the following to the second object in the `packageRules` array of the above example.

```json
"schedule": ["every 3 months on the first day of the month"],
```

## Conclusion

Keeping up to date with third-party dependencies is important, to avoid software rot or technical debt. The fact that Renovate bot automates this task is a great time saver. A cheat sheet like the one presented here helps your developers get the most out of the tool, without having to spend extensive amounts of time to study the in’s & out’s of the Renovate bot manual.

However, in some cases, going through the official documentation may be worth your while. While it does take some time to go through all of the [Configuration options](https://docs.renovatebot.com/configuration-options/), you might discover things (omitted in this cheat sheet) that are perfect for your particular project!

For advanced Renovate users, I also recommend my [advanced tips and tricks article](https://www.augmentedmind.de/2023/09/03/renovate-bot-advanced-tips-part-1/).