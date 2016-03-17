# Introduction

The initial draft of this is actually been created based on an excellent stackoverflow answer, "[git branch naming best practices](http://stackoverflow.com/a/6065944/171217)".

# Naming

## Branch naming conventions

* Use grouping tokens (words) at the beginning of your branch names.
* Define and use short lead tokens to differentiate branches in a way that is meaningful to your workflow.
* Use slashes to separate parts of your branch names.
* Do not use bare numbers as leading parts. 
* Avoid long descriptive names for long-lived branches.
* Group tokens

Use "grouping" tokens in front of your branch names.

```
group1/foo
group2/foo
group1/bar
group2/bar
group3/bar
group1/baz
```

The groups can be named whatever you like to match your workflow. Use short nouns. Read on for more clarity.

## Short well-defined tokens

Choose short tokens so they do not add too much noise to every one of your branch names. 

Here are some examples:

|Prefix   | Use Case                                              |
|:-------:|-------------------------------------------------------|
| wip     | Works in progress; stuff I know won't be finished soon|
| feature | Feature I'm adding or expanding                       |
| bug     | Bug fix or experiment                                 |
| junk    | Throwaway branch created to experiment                |


Each of these tokens can be used to tell you to which part of your workflow each branch belongs.

It sounds like you have multiple branches for different cycles of a change. I do not know what your cycles are, but let's assume they are 'new', 'testing' and 'verified'. You can name your branches with abbreviated versions of these tags, always spelled the same way, to both group them and to remind you which stage you're in.

```
new/frabnotz
new/foo
new/bar
test/foo
test/frabnotz
ver/foo
```

*Rational: One can quickly tell which branches have reached each different stage, and you can group them together easily using Git's pattern matching options.*

E.g:

$ git branch --list "test/*"
test/foo
test/frabnotz

$ git branch --list "*/foo"
new/foo
test/foo
ver/foo

$ gitk --branches="*/foo"
Use slashes to separate parts

### Branch name delimiters

* Use any delimiter you like in branch names. However, slashes tend to be the most flexible: Use dashes or dots, slashes let you do some branch renaming when pushing or fetching to/from a remote.

```
$ git push origin 'refs/heads/feature/*:refs/heads/phord/feat/*'
$ git push origin 'refs/heads/bug/*:refs/heads/review/bugfix/*'
```

Slashes also work better for tab expansion (or command completion) in a shell. This allows easy searching with the TAB key. Zsh then gives me a list of branches which match the part of the token I have typed. This works for preceding tokens as well as embedded ones.

```
$ git checkout new<TAB>
Menu:  new/frabnotz   new/foo   new/bar
```

```
$ git checkout foo<TAB>
Menu:  new/foo   test/foo   ver/foo
```

zshell is very configurable about command completion and I could also configure it to handle dashes, underscores or dots the same way. 
It also lets you search for branches in many git commands, like this:

```
git branch --list "feature/*"
git log --graph --oneline --decorate --branches="feature/*" 
gitk --branches="feature/*" 
```
Caveat: Slashes can cause problems. Because branches are implemented as paths, you cannot have a branch named "foo" and another branch named "foo/bar". This can be confusing for new users.

### Do not use bare numbers

Do not use use bare numbers (or hex numbers) as part of your branch naming scheme. Inside tab-expansion of a reference name, git may decide that a number is part of a sha-1 instead of a branch name. For example, my issue tracker names bugs with decimal numbers. I name my related branches `CRnnnnn` rather than just `nnnnn` to avoid confusion.

```
$ git checkout CR15032<TAB>
Menu:   fix/CR15032    test/CR15032
```

If I tried to expand just 15032, git would be unsure whether I wanted to search SHA-1's or branch names, and my choices would be somewhat limited.


## Avoid long descriptive names

Long branch names can be very helpful when you are looking at a list of branches. However,it can get in the way when looking at decorated one-line logs as the branch names can eat up most of the single line and abbreviate the visible part of the log.

On the other hand long branch names can be more helpful in "merge commits" if you do not habitually rewrite them by hand. The default merge commit message is Merge branch 'branch-name'. You may find it more helpful to have merge messages show up as Merge branch `'fix/CR15032/crash-when-unformatted-disk-inserted'` instead of just Merge branch `'fix/CR15032'`.
