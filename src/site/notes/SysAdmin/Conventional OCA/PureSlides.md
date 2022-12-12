---
{"dg-publish":true,"permalink":"/sys-admin/conventional-oca/pure-slides/","dgPassFrontmatter":true}
---

# Semantic Versioning and  Conventional Commits  in  OCA

---
## Semantic Versioning
____

Add semantics to versioning dividing the version number in three areas:
 
- [MAJOR].[MINOR].[PATCH] 

i.e: `1.4.56-dev` 

> *1* is the MAJOR version <br/>
> *4* is the MINOR version <br/>
> *56-dev* is the PATCH version

--
 
 There also are other proposals for versioning like `Zero-versioning`   
 *(where major version never reaches `1`)*
 
 and `Cal-Versioning`   
 *(a date-based versioning system)*

---

### How we should bump versions?
____
If our changes **BREAK** the retrocompatibility, we must bump our MAJOR version.
>1.4.56 -> 2.0.0

--

### How we should bump versions?
____
When we **ADD A FEATURE** we must bump our MINOR version.
>1.4.56 -> 1.5.0

--

### How we should bump versions?
____
Every other change bumps PATCH version.  
>1.4.56 -> 1.4.57
  
--

### How we should bump versions?
____

If our changes **BREAK** the retrocompatibility, we must bump our MAJOR version.   
>1.4.56 -> 2.0.0

When we **ADD A FEATURE** we must bump our MINOR version.  
>1.4.56 -> 1.5.0

Every other change bumps PATCH version.  
>1.4.56 -> 1.4.57


---

### Odoo Modules Semantic Versioning 
____
Odoo and OCA guidelines adheres to semantic versioning with a particularity;
<br/> **the Odoo version precedes the MAJOR version.**
<br/>

i.e:

> 12.1.0.23 is a module at the major version 1, minor version 0 and patch version 23 for the Odoo 12 version.



---
## Conventional Commits
____
The proposal of Conventional Commit is to use a standard to write commit messages.

---
## Conventional Commits
____

It have several objectives: <br/>

- automate version bumps and changelogs 

- trigger building and publishing
  
 -  make `git log` provides valuable info  
   to any person who reads it  
   
---
### How is that done?
____
Conventional Commits divides the commit message in three main parts:

```
<type>([optional scope]): <description>

[optional body]

[optional footer(s)]
```


---
 
 #### First line (1/3)
 ```
 <type>([optional scope]): <description>`
 ```
____
- A prefix `<type>` :
<br/>
	+ Mandatory  
	+ Must be part of an accepted list  
	+ Version bumps and changelogs are based on it  

--
 
 #### First line (2/3)
 ```
 <type>([optional scope]): <description>`
 ```
 ____
- An `scope`:
	+ Optional
	+ Narrows down the ambit of the change
	+ Its surrounded by parenthesis.

--
 
 #### First line (3/3)
 ```
 <type>([optional scope]): <description>`
 ```
____
- A `<description>` 
	+ Mandatory
	+ Imperative tense
		+ Completes the sentence:<br/>
	  > *Applying this commit will...*
	
---

 #### Body
 ```
 [optional body]
 ```
____
- Optional longer description explaining why changes are made.

---

 #### Footers (1/2)
```
[optional footer(s)]
```

- `BREAKING CHANGE: <message>`
	+ Adding this footer to any type of commit adverts that makes a breaking change (what implies a major version bump)

--

 #### Footers (2/2)
```
[optional footer(s)]
```

- `Fix <#issue>`, `Closes <#issue>` is used to automatically close an issue.<br/>
- `Rel <#issue>`, `<#issue>` to link issues to the present commit.

---
## OCA's implementation of conventional commit
____
While there is a [proposal spec](https://www.conventionalcommits.org/en/v1.0.0/) that defines, furthermore than behaviour, the values a type can get, OCA has its own, based on Odoo Guidelines.
<br/>
```
[<TAG>] scope: <description>
[optional body]
[optional footer(s)]
```

--

### Types of commits
*Types* from Conventional Commits are called *tags* within Odoo ecosystem. They are written in caps and surrounded by brackets.



--

#### Development related tags 

--



`[FIX]`   
for bug fixes: mostly used in stable version but also valid if you are fixing a recent bug in development version; 
<br/>



`[IMP]`  
for improvements: most of the changes done in development version are incremental improvements not related to another tag; 
  <br/>

`[REF]`  
for refactoring: when a feature is heavily rewritten;

--



`[MOV]` 

for moving files: use git move and do not change content of moved file otherwise Git may loose track and history of the file; also used when moving code from one file to another; 
<br/>  

`[REM]`  
for removing resources: removing dead code, removing views, removing modules, …; 

--

#### Git-ops related tags
--


`[MERGE]`  

for merge commits: used in forward port of bug fixes but also as main commit for feature involving several separated commits;  

`[REV]`  

for reverting commits: if a commit causes issues or is not wanted reverting it is done using this tag;  

--

#### Contributing related tags
--

`[ADD]` 

for adding new modules;    
  
  
`[CLA]`

for signing the Odoo Individual Contributor License;  

`[I18N]`  

for changes in translation files;  

`[MIG]`  

for module migrations   

--

#### Building related tags
--

`[REL]`  

 for release commits: new major or minor stable versions;   
 
---
### Commit example
____
```
[IMP] module_name: solve perfomance issues

We're bypassing ORM in order to save time,
preventing from timeout in API ops.

BREAKING CHANGE: ORM is no longer used
Fix #2
```

---
# Tooling

--

## Commitizen
____
`commitizen-tool` is a Python application to easy follow conventions in commits and semantic versioning.
<br/>
Its default implementation follows Conventional Commit spec, but is highly configurable to follow any other convention.

--
#### commitizen-oca
____
`commitizen-oca` is an addon for `commitizen-tool` which provides it the OCA standard rules.

--

#### Using Commitizen to generate commits messages
____
When we have done with our changes, we add them to the stage area and run commitizen.
<br/>
```bash
cz -n cz_commitizen_oca c
```
<br/>
An interactive prompt will show where choose `tag` and write the optional scope, mandatory description and optional body and footer(s).
<br/>
At the end, it will build the commit message and use it to commit the changes.

--

[![asciicast](https://gitlab.com/coopdevs/tooling/commitizen-oca/-/raw/master/img/commitizen_oca.gif)](https://asciinema.org/a/479753)

--

#### Using Commitizen to generate changelog
____
```bash
cz -n cz_commitizen_oca ch
```

#### How it works?
--

#### Using Commitizen to bump version   
____
```bash
cz -n cz_commitizen_oca bump
```
#### How it works?
--

#### Linking bump version with changelog
We can save time if we configure `commitizen` to  generate a changelog in every bump.

---
## Worflow
1. Work
2. Commit
   (...)
3. Release

note: go ahead with this

--

### How often should we release?
___
A **release** implies a version bump and a changelog update.
We shouldn't release *too* often.

Instead, we should consider a release only when a complete batch of changes is done.
<br/>
> Keep in mind: a release could have more than one complete batch of changes. 