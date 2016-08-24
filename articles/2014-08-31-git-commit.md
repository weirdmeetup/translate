---
layout: post
title: How to Write a Git Commit Message
date: Sun Aug 31 02:45:42 +0200 2014
---

<a href="http://xkcd.com/1296/"><img src="http://imgs.xkcd.com/comics/git_commit.png"/></a>

As a project drags on, my git commit messages get less and less informative.
프로젝트가 지루하게 늘어질 수록 커밋 메시지는 점점 줄어들고, 무의미해집니다.

---

[Introduction](#intro) | [The Seven Rules](#seven-rules) | [Tips](#tips)

---

<a name="intro"></a>
## Introduction: Why good commit messages matter
## 들어가며 : 왜 좋은 커밋 메시지가 중요한가

If you browse the log of any random git repository, you will probably find its commit messages are more or less a mess. For example, take a look at [these gems](https://github.com/spring-projects/spring-framework/commits/e5f4b49?author=cbeams) from my early days committing to Spring:

Git 저장소 중 아무거나 골라서 살펴보면 보면, 커밋 메시지가 뒤죽박죽임을 발견할 수 있을 겁니다. 예를 들어 제가 초창기에 Spring에 커밋한 이  [gem](https://github.com/spring-projects/spring-framework/commits/e5f4b49?author=cbeams)들을 보십시오.



    $ git log --oneline -5 --author cbeams --before "Fri Mar 26 2009"

    e5f4b49 Re-adding ConfigurationPostProcessorTests after its brief removal in r814. @Ignore-ing the testCglibClassesAreLoadedJustInTimeForEnhancement() method as it turns out this was one of the culprits in the recent build breakage. The classloader hacking causes subtle downstream effects, breaking unrelated tests. The test method is still useful, but should only be run on a manual basis to ensure CGLIB is not prematurely classloaded, and should not be run as part of the automated build.
    2db0f12 fixed two build-breaking issues: + reverted ClassMetadataReadingVisitor to revision 794 + eliminated ConfigurationPostProcessorTests until further investigation determines why it causes downstream tests to fail (such as the seemingly unrelated ClassPathXmlApplicationContextTests)
    147709f Tweaks to package-info.java files
    22b25e0 Consolidated Util and MutableAnnotationUtils classes into existing AsmUtils
    7f96f57 polishing

Yikes. Compare that with these [more recent](https://github.com/spring-projects/spring-framework/commits/5ba3db?author=philwebb) commits from the same repository:

이런. 이것을 동일 저장소의 [최근](https://github.com/spring-projects/spring-framework/commits/5ba3db?author=philwebb) 커밋들과 비교해 보십시오.

    $ git log --oneline -5 --author pwebb --before "Sat Aug 30 2014"

    5ba3db6 Fix failing CompositePropertySourceTests
    84564a0 Rework @PropertySource early parsing logic
    e142fd1 Add tests for ImportSelector meta-data
    887815f Update docbook dependency and generate epub
    ac8326d Polish mockito usage

Which would you rather read?
어떤 것이 더 읽고 싶습니까?

The former varies wildly in length and form; the latter is concise and consistent. The former is what happens by default; the latter never happens by accident.

전자는 길이와 형식이 제각각이지만, 후자는 간결하고 일관적입니다. 전자는 기본적으로 일어나는 일이고, 후자는 절대 우연히 일어나는 것이 아닙니다..

While many repositories' logs look like the former, there are exceptions. The [Linux kernel](https://github.com/torvalds/linux/commits/master) and [git itself](https://github.com/git/git/commits/master) are great examples. Look at [Spring Boot](https://github.com/spring-projects/spring-boot/commits/master), or any repository managed by [Tim Pope](https://github.com/tpope/vim-pathogen/commits/master).

많은 저장소들의 로그가 전자와 유사하지만, 예외도 존재합니다.  [리눅스 커널](https://github.com/torvalds/linux/commits/master) 과 [Git](https://github.com/git/git/commits/master) 은 훌륭한 예시입니다. [스프링 부트](https://github.com/spring-projects/spring-boot/commits/master)나 [Tim Pope](https://github.com/tpope/vim-pathogen/commits/master)가 운영하는 다른 저장소도 살펴보십시오..

The contributors to these repositories know that a well-crafted git commit message is the best way to communicate _context_ about a change to fellow developers (and indeed to their future selves). A diff will tell you _what_ changed, but only the commit message can properly tell you _why_. Peter Hutterer [makes this point](http://who-t.blogspot.co.at/2009/12/on-commit-messages.html) well:

이 저장소들의 기여자들은 잘 다듬어진 커밋 메시지는 변경사항에 대한 _컨텍스트_ 를 동료 개발자(뿐만 아니라 실제로 미래의 자기 자신)와 공유할 수 있는 최고의 수단임을 잘 알고 있습니다. 

> Re-establishing the context of a piece of code is wasteful. We can't avoid it completely, so our efforts should go to [reducing it](http://www.osnews.com/story/19266/WTFs_m) [as much] as possible. Commit messages can do exactly that and as a result, _a commit message shows whether a developer is a good collaborator_.
  
  
> 코드 조각에 대한 컨텍스트를 다시 수립하는 것은 낭비입니다. 이를 완벽하게 피할 수는 없기 때문에 가능한 한 점차 [줄여 가려고](http://www.osnews.com/story/19266/WTFs_m) 노력해야만 합니다.

If you haven't given much thought to what makes a great git commit message, it may be the case that you haven't spent much time using `git log` and related tools. There is a vicious cycle here: because the commit history is unstructured and inconsistent, one doesn't spend much time using or taking care of it. And because it doesn't get used or taken care of, it remains unstructured and inconsistent.

만약 당신이 훌륭한 git 커밋 메시지를 쓰는데 깊이 고민하지 않았다면, `git log` 와 연관된 도구를 사용하는데에도 많은 시간을 쏟지 않았을 것이다. 악의 순환고리는 다음과 같다. 커밋 이력이 체계와 일관성이 없기 때문에, 누구도 이를 사용하거나 관리하기 위해 많은 시간을 쏟지 않는다. 그리고 사용되거나 관리되지도 않기 때문에 여전히 체계와 일관성이 없는 채로 남겨진다.

But a well-cared for log is a beautiful and useful thing. `git blame`, `revert`, `rebase`, `log`, `shortlog` and other subcommands come to life. Reviewing others' commits and pull requests becomes something worth doing, and suddenly can be done independently. Understanding why something happened months or years ago becomes not only possible but efficient.

하지만 잘 다듬어진 로그는 아름답고 유용하다. `git blame`, `revert`, `rebase`, `log`, `shortlog` 와 다른 하위 명령들이 생명을 얻게 된다. 다른 사람의 커밋과 풀 리퀘스트를 리뷰하는 것이 가치있는 활동이 되고, 갑자기 독립적으로 완료할 수 있게 된다. 어떤 일이 몇 달 전이나 몇 년 전에 일어난 이유를 이해할 수 있게 될 뿐만 아니라 효율적으로 이해할 수 있게 된다.

A project's long-term success rests (among other things) on its maintainability, and a maintainer has few tools more powerful than his project's log. It's worth taking the time to learn how to care for one properly. What may be a hassle at first soon becomes habit, and eventually a source of pride and productivity for all involved.

한 프로젝트가 오랜 기간 동안 성공할 수 있을지의 여부는 (다른 것들 중에서)유지보수성에 달려있고, 유지보수를 하는 사람에게 그의 프로젝트 로그보다 더 강력한 도구는 별로 없다.

In this post, I am addressing just the most basic element of keeping a healthy commit history: how to write an individual commit message. There are other important practices like commit squashing that I am not addressing here. Perhaps I'll do that in a subsequent post.

이 글에서 나는 단지 건강한 커밋 이력을 유지하는데 가장 기본적인 요소를 주장하고 있다. 개인 커밋 메시지를 어떻게 쓸 것인가. 여기서 주장하지 않은 커밋 스쿼싱(commit squashing: 여러 커밋을 하나로 모으는 것) 같은 다른 중요한 실천요소들도 많다. 아마도 다음 글에서 다룰 예정이다.

Most programming languages have well-established conventions as to what constitutes idiomatic style, i.e. naming, formatting and so on. There are variations on these conventions, of course, but most developers agree that picking one and sticking to it is far better than the chaos that ensues when everybody does their own thing.

대부분의 프로그램의 언어들은 기여자들의 관용적인 스타일(네이밍, 포매팅 등등)에 대한 컨벤션이 잘 세워져 있다. 물론 이러한 컨벤션들 종류도 다양하지만, 대부분의 개발자들은 한 가지 스타일 골라 그것만 사용하는 것이 모두가 각자 스타일로 개발한 결과로 혼돈을 겪는 것보다는 훨씬 좋다는 것에 동의한다.

A team's approach to its commit log should be no different. In order to create a useful revision history, teams should first agree on a commit message convention that defines at least the following three things:

팀 차원의 커밋 로그에 대한 접근법도 별반 다를 것이 없다. 유용한 정정 이력을 만들기 위해서는 팀에서 적어도 다음 세 가지를 정의하는 커밋 메시지 컨벤션에 동의해야한다.


**Style.** Markup syntax, wrap margins, grammar, capitalization, punctuation. Spell these things out, remove the guesswork, and make it all as simple as possible. The end result will be a remarkably consistent log that's not only a pleasure to read but that actually _does get read_ on a regular basis.

**스타일** 마크업 문법, 여백 감싸기, 문법, 대문자 사용, 구두점. 이것들을 문서화 시키고 추측을 제거하고, 가능한 한 간단하게 만들어야 한다.  

**Content.** What kind of information should the body of the commit message (if any) contain? What should it _not_ contain?

**내용** 커밋 메시지의 바디에 어떤 종류의 내용이 들어가야 할까? 어떤 것은 들어가지 _않아야_ 할까?

**Metadata.** How should issue tracking IDs, pull request numbers, etc. be referenced?

**메타데이터(Metadata)** 이슈 트래킹 아이디, 풀 리퀘스트 번호 등등은 어떻게 참조할 수 있어야 하나?

Fortunately, there are well-established conventions as to what makes an idiomatic git commit message. Indeed, many of them are assumed in the way certain git commands function. There's nothing you need to re-invent. Just follow the [seven rules](#seven-rules) below and you're on your way to committing like a pro.

다행히도 관용적인 git 커밋 메시지에 대해 잘 만들어진 컨벤션이 있다. 사실  컨벤션의 상당 부분은 git 명령의 기능처럼 여겨진다. 당신이 다시 발명해야 할 것은 없다. 그저 아래 [7가지 규칙](#seven-rules)을 따르고, 프로처럼 커밋을 하면 된다.

<a name="seven-rules"></a>
## The seven rules of a great git commit message

> _Keep in mind: [This][1] [has][2] [all][3] [been][4] [said][5] [before][6]._

 1. [Separate subject from body with a blank line](#separate)
 1. [Limit the subject line to 50 characters](#limit-50)
 1. [Capitalize the subject line](#capitalize)
 1. [Do not end the subject line with a period](#end)
 1. [Use the imperative mood in the subject line](#imperative)
 1. [Wrap the body at 72 characters](#wrap-72)
 1. [Use the body to explain _what_ and _why_ vs. _how_](#why-not-how)

For example:

    Summarize changes in around 50 characters or less

    More detailed explanatory text, if necessary. Wrap it to about 72
    characters or so. In some contexts, the first line is treated as the
    subject of the commit and the rest of the text as the body. The
    blank line separating the summary from the body is critical (unless
    you omit the body entirely); various tools like `log`, `shortlog`
    and `rebase` can get confused if you run the two together.

    Explain the problem that this commit is solving. Focus on why you
    are making this change as opposed to how (the code explains that).
    Are there side effects or other unintuitive consequences of this
    change? Here's the place to explain them.

    Further paragraphs come after blank lines.

     - Bullet points are okay, too

     - Typically a hyphen or asterisk is used for the bullet, preceded
       by a single space, with blank lines in between, but conventions
       vary here

    If you use an issue tracker, put references to them at the bottom,
    like this:

    Resolves: #123
    See also: #456, #789

<a name="separate"></a>
### 1. Separate subject from body with a blank line

From the `git commit` [manpage](https://www.kernel.org/pub/software/scm/git/docs/git-commit.html#_discussion):

> Though not required, it's a good idea to begin the commit message with a single short (less than 50 character) line summarizing the change, followed by a blank line and then a more thorough description. The text up to the first blank line in a commit message is treated as the commit title, and that title is used throughout Git. For example, git-format-patch(1) turns a commit into email, and it uses the title on the Subject line and the rest of the commit in the body.

Firstly, not every commit requires both a subject and a body. Sometimes a single line is fine, especially when the change is so simple that no further context is necessary. For example:

    Fix typo in introduction to user guide

Nothing more need be said; if the reader wonders what the typo was, she can simply take a look at the change itself, i.e. use `git show` or `git diff` or `git log -p`.

If you're committing something like this at the command line, it's easy to use the `-m` switch to `git commit`:

    $ git commit -m"Fix typo in introduction to user guide"

However, when a commit merits a bit of explanation and context, you need to write a body. For example:

    Derezz the master control program

    MCP turned out to be evil and had become intent on world domination.
    This commit throws Tron's disc into MCP (causing its deresolution)
    and turns it back into a chess game.

This is not so easy to commit this with the `-m` switch. You really need a proper editor. If you do not already have an editor set up for use with git at the command line, read [this section of Pro Git](https://git-scm.com/book/en/v2/Customizing-Git-Git-Configuration).

In any case, the separation of subject from body pays off when browsing the log. Here's the full log entry:

    $ git log
    commit 42e769bdf4894310333942ffc5a15151222a87be
    Author: Kevin Flynn <kevin@flynnsarcade.com>
    Date:   Fri Jan 01 00:00:00 1982 -0200

     Derezz the master control program

     MCP turned out to be evil and had become intent on world domination.
     This commit throws Tron's disc into MCP (causing its deresolution)
     and turns it back into a chess game.

And now `git log --oneline`, which prints out just the subject line:

    $ git log --oneline
    42e769 Derezz the master control program

Or, `git shortlog`, which groups commits by user, again showing just the subject line for concision:

    $ git shortlog
    Kevin Flynn (1):
          Derezz the master control program

    Alan Bradley (1):
          Introduce security program "Tron"

    Ed Dillinger (3):
          Rename chess program to "MCP"
          Modify chess program
          Upgrade chess program

    Walter Gibbs (1):
          Introduce protoype chess program
          
There are a number of other contexts in git where the distinction between subject line and body kicks in—but none of them work properly without the blank line in between.


<a name="limit-50"></a>
### 2. Limit the subject line to 50 characters

50 characters is not a hard limit, just a rule of thumb. Keeping subject lines at this length ensures that they are readable, and forces the author to think for a moment about the most concise way to explain what's going on.

> _Tip: If you're having a hard time summarizing, you might be committing too many changes at once. Strive for [_atomic commits_](http://www.freshconsulting.com/atomic-commits/) (a topic for a separate post)._

GitHub's UI is fully aware of these conventions. It will warn you if you go past the 50 character limit:

![gh1](http://i.imgur.com/zyBU2l6.png)

And will truncate any subject line longer than 69 characters with an ellipsis:

![gh2](http://i.imgur.com/27n9O8y.png)

So shoot for 50 characters, but consider 69 the hard limit.


<a name="capitalize"></a>
### 3. Capitalize the subject line

This is as simple as it sounds. Begin all subject lines with a capital letter.

For example:

 - <font color="green">Accelerate to 88 miles per hour</font>

Instead of:

 - <font color="red">accelerate to 88 miles per hour</font>

<a name="end"></a>
### 4. Do not end the subject line with a period

Trailing punctuation is unnecessary in subject lines. Besides, space is precious when you're trying to keep them to [50 chars or less](#limit-50).

Example:

 - <font color="green">Open the pod bay doors</font>

Instead of:

 - <font color="red">Open the pod bay doors.</font>


<a name="imperative"></a>
### 5. Use the imperative mood in the subject line

_Imperative mood_ just means "spoken or written as if giving a command or instruction". A few examples:

 - Clean your room
 - Close the door
 - Take out the trash
 
Each of the seven rules you're reading about right now are written in the imperative ("Wrap the body at 72 characters", etc.).

The imperative can sound a little rude; that's why we don't often use it. But it's perfect for git commit subject lines. One reason for this is that **git itself uses the imperative whenever it creates a commit on your behalf**.

For example, the default message created when using `git merge` reads:

    Merge branch 'myfeature'

And when using `git revert`:

    Revert "Add the thing with the stuff"

    This reverts commit cc87791524aedd593cff5a74532befe7ab69ce9d.

Or when clicking the "Merge" button on a GitHub pull request:

    Merge pull request #123 from someuser/somebranch

So when you write your commit messages in the imperative, you're following git's own built-in conventions. For example:

<font color="green">

 - Refactor subsystem X for readability
 - Update getting started documentation
 - Remove deprecated methods
 - Release version 1.0.0

</font>

Writing this way can be a little awkward at first. We're more used to speaking in the _indicative mood_, which is all about reporting facts. That's why commit messages often end up reading like this:

<font color="red">

 - Fixed bug with Y
 - Changing behavior of X

</font>

And sometimes commit messages get written as a description of their contents:

<font color="red">

 - More fixes for broken stuff
 - Sweet new API methods

</font>

To remove any confusion, here's a simple rule to get it right every time.

**A properly formed git commit subject line should always be able to complete the following sentence**:

 - If applied, this commit will _<u>your subject line here</u>_

For example:

 - If applied, this commit will <font color="green">_refactor subsystem X for readability_</font>
 - If applied, this commit will <font color="green">_update getting started documentation_</font>
 - If applied, this commit will <font color="green">_remove deprecated methods_</font>
 - If applied, this commit will <font color="green">_release version 1.0.0_</font>
 - If applied, this commit will <font color="green">_merge pull request #123 from user/branch_</font>

Notice how this doesn't work for the other non-imperative forms:

 - If applied, this commit will <font color="red">_fixed bug with Y_</font>
 - If applied, this commit will <font color="red">_changing behavior of X_</font>
 - If applied, this commit will <font color="red">_more fixes for broken stuff_</font>
 - If applied, this commit will <font color="red">_sweet new API methods_</font>

> _Remember: Use of the imperative is important only in the subject line. You can relax this restriction when you're writing the body._

<a name="wrap-72"></a>
### 6. Wrap the body at 72 characters

Git never wraps text automatically. When you write the body of a commit message, you must mind its right margin, and wrap text manually.

The recommendation is to do this at 72 characters, so that git has plenty of room to indent text while still keeping everything under 80 characters overall.

A good text editor can help here. It's easy to configure Vim, for example, to wrap text at 72 characters when you're writing a git commit. Traditionally, however, IDEs have been terrible at providing smart support for text wrapping in commit messages (although in recent versions, IntelliJ IDEA has [finally](http://youtrack.jetbrains.com/issue/IDEA-53615) [gotten](http://youtrack.jetbrains.com/issue/IDEA-53615#comment=27-448299) [better](http://youtrack.jetbrains.com/issue/IDEA-53615#comment=27-446912) about this).


<a name="why-not-how"></a>
### 7. Use the body to explain what and why vs. how

This [commit from Bitcoin Core](https://github.com/bitcoin/bitcoin/commit/eb0b56b19017ab5c16c745e6da39c53126924ed6) is a great example of explaining what changed and why:

    commit eb0b56b19017ab5c16c745e6da39c53126924ed6
    Author: Pieter Wuille <pieter.wuille@gmail.com>
    Date:   Fri Aug 1 22:57:55 2014 +0200

       Simplify serialize.h's exception handling

       Remove the 'state' and 'exceptmask' from serialize.h's stream
       implementations, as well as related methods.

       As exceptmask always included 'failbit', and setstate was always
       called with bits = failbit, all it did was immediately raise an
       exception. Get rid of those variables, and replace the setstate
       with direct exception throwing (which also removes some dead
       code).

       As a result, good() is never reached after a failure (there are
       only 2 calls, one of which is in tests), and can just be replaced
       by !eof().

       fail(), clear(n) and exceptions() are just never called. Delete
       them.

Take a look at the [full diff](https://github.com/bitcoin/bitcoin/commit/eb0b56b19017ab5c16c745e6da39c53126924ed6) and just think how much time the author is saving fellow and future committers by taking the time to provide this context here and now. If he didn't, it would probably be lost forever.

In most cases, you can leave out details about how a change has been made. Code is generally self-explanatory in this regard (and if the code is so complex that it needs to be explained in prose, that's what source comments are for). Just focus on making clear the reasons why you made the change in the first place—the way things worked before the change (and what was wrong with that), the way they work now, and why you decided to solve it the way you did.

The future maintainer that thanks you may be yourself!


<a name="tips"></a>
## Tips

### Learn to love the command line. Leave the IDE behind.

For as many reasons as there are git subcommands, it's wise to embrace the command line. Git is insanely powerful; IDEs are too, but each in different ways. I use an IDE every day (IntelliJ IDEA) and have used others extensively (Eclipse), but I have never seen IDE integration for git that could begin to match the ease and power of the command line (once you know it).

Certain git-related IDE functions are invaluable, like calling `git rm` when you delete a file, and doing the right stuff with `git` when you rename one. Where everything falls apart is when you start trying to commit, merge, rebase, or do sophisticated history analysis through the IDE.

When it comes to wielding the full power of git, it's command-line all the way.

Remember that whether you use Bash or Z shell, there are [tab completion scripts](http://git-scm.com/book/en/Git-Basics-Tips-and-Tricks) that take much of the pain out of remembering the subcommands and switches.

### Read Pro Git

The [Pro Git](http://git-scm.com/book) book is available online for free, and it's fantastic. Take advantage!


[1]: http://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html
[2]: http://www.git-scm.com/book/en/Distributed-Git-Contributing-to-a-Project#Commit-Guidelines
[3]: https://github.com/torvalds/subsurface/blob/master/README#L82-109
[4]: http://who-t.blogspot.co.at/2009/12/on-commit-messages.html
[5]: https://github.com/erlang/otp/wiki/writing-good-commit-messages
[6]: https://github.com/spring-projects/spring-framework/blob/30bce7/CONTRIBUTING.md#format-commit-messages

