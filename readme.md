# Simple Git

A light weight interface for running git commands in any [node.js](http://nodejs.org) application.

# Installation

Easiest through [npm](http://npmjs.org): `npm install simple-git`

# Dependencies

Relies on [git](http://git-scm.com/downloads) already having been installed on the system, and that it can be called
using the command `git`.

# Usage

Include into your app using:

    var simpleGit = require('simple-git')( workingDirPath );

> where the `workingDirPath` is optional, defaulting to the current directory.

Use `simpleGit` by chaining any of its functions together. Each function accepts an optional final argument which will
be called when that step has been completed. When it is called it has two arguments - firstly an error object (or null
when no error occurred) and secondly the data generated by that call.

|API | What it does |
|---------------------|---------------|
| `.addConfig(key, value[, handlerFn])` |  add a local configuration property |
|`.init(bare, handlerFn)`| initialize a repository, optional `bare` parameter makes intialized repository bare |
|`.catFile(options[, handlerFn])`| generate `cat-file` detail, `options` should be an array of strings as supported arguments to the [cat-file](https://git-scm.com/docs/git-cat-file) command |
|`.clone(repoPath, localPath, [options], [handlerFn])`| clone a remote repo at `repoPath` to a local directory at `localPath` with an optional array of additional arguments to include between `git clone` and the trailing `repo local` arguments |
|`.diff(options, handlerFn)`| get the diff of the current repo compared to the last commit with a set of options supplied as a string |
|`.diff(handlerFn)`| get the diff for all file in the current repo compared to the last commit |
|`.diffSummary(handlerFn)`| gets a summary of the diff for files in the repo, uses the `git diff --stat` format to calculate changes. Handler is called with a nullable error object and an instance of the [DiffSummary](src/DiffSummary.js) |
|`.diffSummary(options, handlerFn)`| includes options in the call to `diff --stat options` and returns a [DiffSummary](src/DiffSummary.js) |
|`.pull(remote, branch, handlerFn)`| pull all updates from the repo ('origin'/'master') |
|`.fetch(remote, branch, handlerFn)`| update the local working copy database with changes from a remote repo |
|`.fetch(handlerFn)`| update the local working copy database with changes from the default remote repo and branch |
|`.tags(handlerFn)`| list all tags |
|`.addTag(name, handlerFn)`| adds a lightweight tag to the head of the current branch |
|`.addAnnotatedTag(tagName, tagMessage, handlerFn)`| adds an annotated tag to the head of the current branch |
|`.log([options], handlerFn)`| list commits between `options.from` and `options.to` tags or branch (if not specified will show all history). Additionally you can provide `options.file`, which is the path to a file in your repository. Then only this file will be considered. For any other set of options, supply `options` as an array of strings to be appended to the `git log` command. To use a custom splitter in the log format, set `options.splitter` to be the string the log should be split on |
|`.checkout(checkoutWhat, handlerFn)`| checks out the supplied tag, revision or branch. `checkoutWhat` can be one or more strings to be used as parameters appended to the `git checkout` command. |
|`.checkoutBranch(branchName, startPoint, handlerFn)`| checks out a new branch from the supplied start point |
|`.checkoutLocalBranch(branchName, handlerFn)`| checks out a new local branch |
|`.checkoutLatestTag(handlerFn)`| convenience method to pull then checkout the latest tag |
|`.add([fileA, ...], handlerFn)`| adds one or more files to be under source control |
|`.branch([handlerFn])`| gets a list of all branches, calls `handlerFn` with two arguments, an error object and [BranchSummary](src/BranchSummary.js) instance |
|`.commit(message, handlerFn)`| commits changes in the current working directory with the supplied message where the message can be either a single string or array of strings to be passed as separate arguments (the `git` command line interface converts these to be separated by double line breaks) |
|`.commit(message, [fileA, ...], options, handlerFn)`| commits changes on the named files with the supplied message, when supplied, the optional options object can contain any other parameters to pass to the commit command, setting the value of the property to be a string will add `name=value` to the command string, setting any other type of value will result in just the key from the object being passed (ie: just `name`), an example of setting the author is below |
|`.customBinary(gitPath)`| sets the command to use to reference git, allows for using a git binary not available on the path environment variable |
|`.mergeFromTo(from, to, [[options,] handlerFn])`| merge from one branch to another, when supplied the options should be an array of additional parameters to pass into the `git merge` command |
|`.mirror(repoPath, localPath, handlerFn])`| clone and mirror the repo to local |
|`.push(remote, branch, handlerFn)`| pushes to a named remote and named branch |
|`.pushTags(remote, handlerFn)`| pushes tags to a named remote |
|`.silent(isSilent)`| sets whether the console should be used for logging errors (defaults to `true` when the `NODE_ENV` contains the string `prod`) |
|`.stashList([options, ][handlerFn])`| Retrieves the stash list, optional first argument can be an object specifying `options.splitter` to override the default value of `:`, alternatively options can be a set of arguments as supported by the `git stash list` command. |
|`.submoduleAdd(repo, path[, handlerFn])`| adds a new sub module |
|`.submoduleInit([args, ][handlerFn])`| inits sub modules, args should be an array of string arguments to pass to the `git submodule init` command |
|`.submoduleUpdate([args, ][handlerFn])`| updates sub modules, args should be an array of string arguments to pass to the `git submodule update` command |
|`.rm([fileA, ...], handlerFn)`| removes any number of files from source control |
|`.rmKeepLocal([fileA, ...], handlerFn)`| removes files from source control but leaves them on disk |
|`.addRemote(name, repo, handlerFn)`| adds a new named remote to be tracked as `name` at the path `repo` |
|`.removeRemote(name, handlerFn)`| removes the named remote |
|`.getRemotes([verbose], handlerFn)`| gets a list of the named remotes, when the verbose option is supplied as true, includes the URLs and purpose of each ref |
|`.reset([resetMode,] handlerFn)`| resets the repository, the optional first argument can either be an array of options supported by the `git reset` command or one of the string constants `hard` or `soft`, if omitted the reset will be a soft reset to head, handlerFn: (err) |
|`.revparse([options], handlerFn)`| wraps git rev-parse. Primarily used to convert friendly commit references (ie branch names) to SHA1 hashes. Options should be an array of string options compatible with the [git rev-parse](http://git-scm.com/docs/git-rev-parse) |
|`.status(handlerFn)`| gets the status of the current repo |
|`.show([options], handlerFn)`| Show various types of objects, for example the file content at a certain commit. `options` is the single value string or array of string commands you want to run |
|`.checkIgnore([filepath, ...], handlerFn)`| checks if filepath excluded by .gitignore rules |
|`.listRemote([args], handlerFn)`| lists remote repositories - there are so many optional arguments in the underlying  `git ls-remote` call, just supply any you want to use as the optional `args` array of strings eg: `git.listRemote(['--heads', '--tags'], console.log.bind(console))` |
|`outputHandler(handlerFn)`| attaches a handler that will be called with the name of the command being run and the `stdout` and `stderr` [readable streams](http://nodejs.org/api/stream.html#stream_class_stream_readable) created by the [child process](http://nodejs.org/api/child_process.html#child_process_class_childprocess) running that command |
|`.then(handlerFn)`| calls a simple function in the current step |

# Release History

Bumped to a new major revision in the 1.x branch, now uses `ChildProcess.spawn` in place of `ChildProcess.exec` to
add escaping to the arguments passed to each of the tasks.

# Deprecated APIs

Use of these APIs is deprecated and should be avoided as support for them will be removed in future release:

`.log([from, to], handlerFn)` list commits between `from` and `to` tags or branch, switch to supplying the revisions
as an options object instead.

# Examples
```js
    // update repo and get a list of tags
    require('simple-git')(__dirname + '/some-repo')
         .pull()
         .tags(function(err, tags) {
            console.log("Latest available tag: %s", tags.latest);
         });

    // update repo and when there are changes, restart the app
    require('simple-git')()
         .pull(function(err, update) {
            if(update && update.summary.changes) {
               require('child_process').exec('npm restart');
            }
         });

    // starting a new repo
    require('simple-git')()
         .init()
         .add('./*')
         .commit("first commit!")
         .addRemote('origin', 'https://github.com/user/repo.git')
         .push('origin', 'master');

    // push with -u
    require('simple-git')()
         .add('./*')
         .commit("first commit!")
         .addRemote('origin', 'some-repo-url')
         .push(['-u', 'origin', 'master'], function () {
            // done.
         });

    // piping to the console for long running tasks
    require('simple-git')()
         .outputHandler(function (command, stdout, stderr) {
            stdout.pipe(process.stdout);
            stderr.pipe(process.stderr);
         })
         .checkout('https://github.com/user/repo.git');

    // update repo and print messages when there are changes, restart the app
    require('simple-git')()
         .then(function() {
            console.log('Starting pull...');
         })
         .pull(function(err, update) {
            if(update && update.summary.changes) {
               require('child_process').exec('npm restart');
            }
         })
         .then(function() {
            console.log('pull done.');
         });

    // get a full commits list, and then only between 0.11.0 and 0.12.0 tags
    require('simple-git')()
      .log(function(err, log) {
        console.log(log);
      })
      .log('0.11.0', '0.12.0', function(err, log) {
        console.log(log);
      })

    // set the local configuration for author, then author for an individual commit
    require('simple-git')()
      .addConfig('user.name', 'Some One')
      .addConfig('user.email', 'some@one.com')
      .commit('committed as "Some One", 'file-one')
      .commit('committed as "Another Person"', 'file-two', { '--author': '"Another Person <another@person.com>"' })
```
