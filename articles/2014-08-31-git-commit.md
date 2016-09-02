---
layout: post
title: How to Write a Git Commit Message
date: Sun Aug 31 02:45:42 +0200 2014
---
<div style="text-align:center;">
    <a href="http://xkcd.com/1296/"><img src="http://imgs.xkcd.com/comics/git_commit.png"/></a>
</div>

As a project drags on, my git commit messages get less and less informative.  
프로젝트가 지루하게 늘어지면, 커밋 메시지는 점점 더 무의미해진다.

---

[Introduction](#intro) | [The Seven Rules](#seven-rules) | [Tips](#tips)

---

<a name="intro"></a>
## Introduction: Why good commit messages matter
## 들어가며: 좋은 커밋 메시지는 왜 중요한가

If you browse the log of any random git repository, you will probably find its commit messages are more or less a mess. For example, take a look at [these gems](https://github.com/spring-projects/spring-framework/commits/e5f4b49?author=cbeams) from my early days committing to Spring:

Git 저장소 중 아무거나 골라 살펴보면, 커밋 메시지가 뒤죽박죽인 것을 발견할 수 있을 것이다. 예를 들어 내가 초창기에 Spring에 커밋한 [gem들](https://github.com/spring-projects/spring-framework/commits/e5f4b49?author=cbeams)을 보라.

    $ git log --oneline -5 --author cbeams --before "Fri Mar 26 2009"

    e5f4b49 Re-adding ConfigurationPostProcessorTests after its brief removal in r814. @Ignore-ing the testCglibClassesAreLoadedJustInTimeForEnhancement() method as it turns out this was one of the culprits in the recent build breakage. The classloader hacking causes subtle downstream effects, breaking unrelated tests. The test method is still useful, but should only be run on a manual basis to ensure CGLIB is not prematurely classloaded, and should not be run as part of the automated build.
    2db0f12 fixed two build-breaking issues: + reverted ClassMetadataReadingVisitor to revision 794 + eliminated ConfigurationPostProcessorTests until further investigation determines why it causes downstream tests to fail (such as the seemingly unrelated ClassPathXmlApplicationContextTests)
    147709f Tweaks to package-info.java files
    22b25e0 Consolidated Util and MutableAnnotationUtils classes into existing AsmUtils
    7f96f57 polishing

Yikes. Compare that with these [more recent](https://github.com/spring-projects/spring-framework/commits/5ba3db?author=philwebb) commits from the same repository:

맙소사. 이것과 같은 저장소에 있는 [최근](https://github.com/spring-projects/spring-framework/commits/5ba3db?author=philwebb) 커밋들을 비교해 보자.

    $ git log --oneline -5 --author pwebb --before "Sat Aug 30 2014"

    5ba3db6 Fix failing CompositePropertySourceTests
    84564a0 Rework @PropertySource early parsing logic
    e142fd1 Add tests for ImportSelector meta-data
    887815f Update docbook dependency and generate epub
    ac8326d Polish mockito usage

Which would you rather read?
어떤 커밋 메시지가 더 읽기 좋은가?

The former varies wildly in length and form; the latter is concise and consistent. The former is what happens by default; the latter never happens by accident.

앞의 것은 길이와 형식이 제각각이지만, 뒤의 것은 간결하고 일관성이 있다. 앞의 것은 그냥 두면 나타나는 것이지만, 뒤의 것은 손을 대지 않는 이상 우연히 만들어지지 않는다.

While many repositories' logs look like the former, there are exceptions. The [Linux kernel](https://github.com/torvalds/linux/commits/master) and [git itself](https://github.com/git/git/commits/master) are great examples. Look at [Spring Boot](https://github.com/spring-projects/spring-boot/commits/master), or any repository managed by [Tim Pope](https://github.com/tpope/vim-pathogen/commits/master).

많은 저장소들의 로그가 앞의 것과 비슷하지만, 예외도 존재한다. [리눅스 커널](https://github.com/torvalds/linux/commits/master)과 [Git](https://github.com/git/git/commits/master)은 훌륭한 견본이다. [스프링 부트](https://github.com/spring-projects/spring-boot/commits/master)나 [Tim Pope](https://github.com/tpope/vim-pathogen/commits/master)가 운영하는 다른 저장소도 살펴보라.

The contributors to these repositories know that a well-crafted git commit message is the best way to communicate _context_ about a change to fellow developers (and indeed to their future selves). A diff will tell you _what_ changed, but only the commit message can properly tell you _why_. Peter Hutterer [makes this point](http://who-t.blogspot.co.at/2009/12/on-commit-messages.html) well:

이 저장소들의 기여자들은 동료 개발자(뿐만 아니라 실제로 미래의 자기 자신)와 변경사항에 대한 _맥락_을 공유할 수 있는 최고의 수단은 잘 다듬어진 커밋 메시지라는 것을 잘 알고 있다. diff로 _어떤 것_이 변경되었는지 확인할 수 있지만, 오직 커밋 메시지를 통해서만 그 _이유_를 알 수 있을 것이다. Peter Hutter가 [이 점을 잘 설명해 놓았다](http://who-t.blogspot.co.at/2009/12/on-commit-messages.html).

> Re-establishing the context of a piece of code is wasteful. We can't avoid it completely, so our efforts should go to [reducing it](http://www.osnews.com/story/19266/WTFs_m) [as much] as possible. Commit messages can do exactly that and as a result, _a commit message shows whether a developer is a good collaborator_.
 
> 코드 조각의 앞뒤 맥락을 다시 살펴야 하는 것은 가치 없는 일이다. 이를 완벽하게 피할 수는 없기에, [코드 맥락을 다시 살피는 일을 줄이기 위해](http://www.osnews.com/story/19266/WTFs_m) 최대한 노력해야 한다. 커밋 메시지는 정확히 그런 일을 할 수 있고, 이로 인해 _커밋 메시지 하나로 어떤 개발자가 좋은 협력자인지 아닌지 알 수 있다_.

If you haven't given much thought to what makes a great git commit message, it may be the case that you haven't spent much time using `git log` and related tools. There is a vicious cycle here: because the commit history is unstructured and inconsistent, one doesn't spend much time using or taking care of it. And because it doesn't get used or taken care of, it remains unstructured and inconsistent.

만약 당신이 Git 커밋 메시지를 훌륭하게 쓰기 위해 깊은 고민을 하지 않았다면, `git log`와 이에 연관된 도구를 사용하는데에도 그리 많은 시간을 쏟지 않았을 것이다. 악의 고리는 다음과 같이 발생한다. 커밋 이력이 체계와 일관성이 없기 때문에, 누구도 이를 사용하거나 관리하기 위해 많은 시간을 들이지 않는다. 그러면 사용되거나 관리되지도 않기 때문에 여전히 체계와 일관성이 없는 채로 남겨진다.

But a well-cared for log is a beautiful and useful thing. `git blame`, `revert`, `rebase`, `log`, `shortlog` and other subcommands come to life. Reviewing others' commits and pull requests becomes something worth doing, and suddenly can be done independently. Understanding why something happened months or years ago becomes not only possible but efficient.

하지만 잘 관리된 로그는 아름답고 유용하다. `git blame`이나 `revert`, `rebase`, `log`, `shortlog` 뿐만 아니라 다른 하위 명령어들도 활력을 얻게 된다. 다른 사람의 커밋과 풀 리퀘스트를 리뷰하는 것이 가치있는 활동이 되고, 어느새 독립적으로 완료할 수 있게 된다. 몇 달 전이나 몇 년 전에 어떤 일이 일어난 이유에 대해 알 수 있음은 물론, 효과적으로 이해할 수 있게 된다.

A project's long-term success rests (among other things) on its maintainability, and a maintainer has few tools more powerful than his project's log. It's worth taking the time to learn how to care for one properly. What may be a hassle at first soon becomes habit, and eventually a source of pride and productivity for all involved.

한 프로젝트가 오랜 기간 동안 성공할 수 있을지의 여부는 (다른 것들 중에서)유지보수성에 달려 있다. 그리고 유지보수를 하는 사람에게 프로젝트 로그보다 더 강력한 도구는 별로 없다. 따라서 이를 정확히 다루는 법을 배우는 데 시간을 쏟을 만한 가치가 있다. 처음에는 혼란스럽겠지만, 이내 습관이 될 것이고, 점차 관련된 모든 사람들의 자신감과 생산성의 원천이 될 것이다.

In this post, I am addressing just the most basic element of keeping a healthy commit history: how to write an individual commit message. There are other important practices like commit squashing that I am not addressing here. Perhaps I'll do that in a subsequent post.

이 글에서 나는 그저 건강한 커밋 이력을 유지하기 위한 가장 기본적인 요소만를 주장하고 있다. 바로 개별 커밋 메시지를 어떻게 쓸 것인가 이다. 여기서 언급하지 않은 커밋 스쿼싱(commit squashing: 여러 커밋을 하나로 모으는 것) 같은 다른 중요한 실천요소들도 많다. 그것은 아마도 다음 글에서 다루게 될 것이다.

Most programming languages have well-established conventions as to what constitutes idiomatic style, i.e. naming, formatting and so on. There are variations on these conventions, of course, but most developers agree that picking one and sticking to it is far better than the chaos that ensues when everybody does their own thing.

대부분의 프로그램의 언어들은 기여자들의 관용적인 스타일(명명법, 포매팅 등등)에 대한 컨벤션(코딩 스타일 규약)이 잘 세워져 있다. 물론 컨벤션의 종류가 다양하지만, 모두가 각자의 스타일을 따라 개발해서 혼돈을 겪는 것보다는 한 가지 스타일 골라 그것만 사용하는 것이 훨씬 좋다는 것에 대부분의 개발자들은 동의한다.

A team's approach to its commit log should be no different. In order to create a useful revision history, teams should first agree on a commit message convention that defines at least the following three things:

팀 차원의 커밋 로그에 대한 접근법도 별반 다를 것이 없다. 유용한 정정 이력을 만들기 위해 팀은 먼저 적어도 다음 세 가지를 정의하는 커밋 메시지 컨벤션에 동의해야한다.


**Style.** Markup syntax, wrap margins, grammar, capitalization, punctuation. Spell these things out, remove the guesswork, and make it all as simple as possible. The end result will be a remarkably consistent log that's not only a pleasure to read but that actually _does get read_ on a regular basis.

**스타일** 마크업 문법, 여백 감싸기, 문법, 대문자 사용, 구두점. 이것들을 문서화 시켜 추측을 제거하고, 가능한 한 간단하게 만들어야 한다. 그 결과 눈의 띄게 일관성을 갖게 되어 읽기 즐겁고, 실제로 규칙적으로 _읽히는_ 로그가 될 것이다.

**Content.** What kind of information should the body of the commit message (if any) contain? What should it _not_ contain?

**내용** 커밋 메시지의 본문에는 어떤 종류의 내용이 들어가야 할까? 어떤 것은 들어가지 _않아야_ 할까?

**Metadata.** How should issue tracking IDs, pull request numbers, etc. be referenced?

**메타데이터(Metadata)** 이슈 트래킹 아이디, 풀 리퀘스트 번호 등등은 어떻게 참조할 수 있어야 하나?

Fortunately, there are well-established conventions as to what makes an idiomatic git commit message. Indeed, many of them are assumed in the way certain git commands function. There's nothing you need to re-invent. Just follow the [seven rules](#seven-rules) below and you're on your way to committing like a pro.

다행히도 관용적인 Git 커밋 메시지에 대해 잘 만들어진 컨벤션이 있다. 사실 컨벤션의 상당 부분은 Git 명령어 기능처럼 보인다. 다시 발명해야 할 것은 없다. 그저 아래의 [7가지 규칙](#seven-rules)을 따르고, 자신있게 프로처럼 커밋하면 된다.

<a name="seven-rules"></a>
## The seven rules of a great git commit message

## 훌륭한 git 커밋 메시지의 7가지 규칙

> _Keep in mind: [This][1] [has][2] [all][3] [been][4] [said][5] [before][6]._

> _기억해두자. [이 내용은][1] [이미][2] [이전에][3] [다][4] [했던][5] [이야기다][6]._

 1. [Separate subject from body with a blank line](#separate)
 1. [Limit the subject line to 50 characters](#limit-50)
 1. [Capitalize the subject line](#capitalize)
 1. [Do not end the subject line with a period](#end)
 1. [Use the imperative mood in the subject line](#imperative)
 1. [Wrap the body at 72 characters](#wrap-72)
 1. [Use the body to explain _what_ and _why_ vs. _how_](#why-not-how)

For example:

다음은 위 규칙을 따르는 커밋 예시다.

    Summarize changes in around 50 characters or less

    50자 또는 그 이하로 변경 사항을 요약

    More detailed explanatory text, if necessary. Wrap it to about 72
    characters or so. In some contexts, the first line is treated as the
    subject of the commit and the rest of the text as the body. The
    blank line separating the summary from the body is critical (unless
    you omit the body entirely); various tools like `log`, `shortlog`
    and `rebase` can get confused if you run the two together.

    필요하다면 더 자세한 설명을 서술한다. 한 행은 72자 정도에서 변경한다.
    맥락에 따라서 첫 행이 커밋의 제목처럼, 나머지 내용이 본문처럼 다뤄지는
    경우도 있다. 첫 행의 요약과 본문 사이에 빈 행을 넣는 것은 중요하다.
    (물론 본문을 입력하지 않는 경우라면 무관하다.) 이 규칙을 지키지 않은
    경우에는 `log` 또는 `shortlog`, `rebase`와 같은 도구를 사용할 때
    혼란스러울 수 있다.

    Explain the problem that this commit is solving. Focus on why you
    are making this change as opposed to how (the code explains that).
    Are there side effects or other unintuitive consequences of this
    change? Here's the place to explain them.

    이 커밋이 해결한 문제에 대해 설명한다. 어떻게 문제를 해결했는지 설명하기
    보다는 왜 이런 변화를 만들었는가에 집중한다. (어떻게는 코드가 설명한다.)
    이 변경으로 인해 나타나는 부작용이나 직관적이지 않은 결과가 나타나는가?
    이 내용을 여기에서 설명한다.

    Further paragraphs come after blank lines.
    
    문단을 더 추가하고 싶다면 문단 사이에 빈 행을 넣는다.

     - Bullet points are okay, too

     - 개조식 서술도 괜찮음

     - Typically a hyphen or asterisk is used for the bullet, preceded
       by a single space, with blank lines in between, but conventions
       vary here

     - 블릿(bullet)으로 하이픈(-)이나 별표(*)를 사용하고, 한 칸의 공간을
       띄고 시작하며, 각 항목 사이 빈 행을 넣는 방식이 일반적이나 다양한
       관례가 있음

    If you use an issue tracker, put references to them at the bottom,
    like this:

    만약 이슈 트래커를 사용한다면 다음처럼 내용 하단에 참조를 추가한다.

    Resolves: #123
    See also: #456, #789

    해결: #123
    참고: #456, #789


<a name="separate"></a>
### 1. Separate subject from body with a blank line

### 1. 제목과 본문을 빈 행으로 분리한다

From the `git commit` [manpage](https://www.kernel.org/pub/software/scm/git/docs/git-commit.html#_discussion):

`git commit`의 [man 도움말 페이지](https://www.kernel.org/pub/software/scm/git/docs/git-commit.html#_discussion)의 내용이다.

> Though not required, it's a good idea to begin the commit message with a single short (less than 50 character) line summarizing the change, followed by a blank line and then a more thorough description. The text up to the first blank line in a commit message is treated as the commit title, and that title is used throughout Git. For example, git-format-patch(1) turns a commit into email, and it uses the title on the Subject line and the rest of the commit in the body.

> 필수는 아니지만 커밋 메시지를 작성하는 경우에 변경 사항을 (50자 이내로)요약하고 빈 행을 추가한 다음, 더 자세한 설명을 적은 것은 좋은 방식이라 할 수 있다. 구분을 위한 빈 행을 추가하면 짧은 요약을 커밋 제목과 같이 처리하게 되고 그 제목은 Git에서 두루두루 활용할 수 있다. 예를 들어 git-format-patch(1)을 사용하면, 커밋은 메일 형태로 변경되고, 첫 행은 제목으로, 나머지 커밋 내용은 본문이 된다.

Firstly, not every commit requires both a subject and a body. Sometimes a single line is fine, especially when the change is so simple that no further context is necessary. For example:

먼저, 모든 커밋이 제목과 본문으로 이뤄져야 하는 것은 아니다. 한 줄만 작성해도 괜찮은 경우도 많다. 너무나도 사소한 변경이라서 맥락에 대한 자세한 설명이 필요 없을 정도로 간단하면 말이다. 다음과 같은 경우다.

    Fix typo in introduction to user guide

    사용자 가이드 서문의 오타를 수정함

Nothing more need be said; if the reader wonders what the typo was, she can simply take a look at the change itself, i.e. use `git show` or `git diff` or `git log -p`.

더 설명할 필요가 없다. 만약 이 커밋을 읽은 사람이 어떤 오타인지 궁금하다면 간단하게 어떤 내용을 변경한 커밋인지 살펴보면 된다. `git show`, `git diff`, 또는 `git log -p`와 같은 명령을 사용해서 말이다.

If you're committing something like this at the command line, it's easy to use the `-m` switch to `git commit`:

이런 내용의 커밋 메시지를 명령행에서 작성한다면 `git commit`의 `-m` 스위치로 쉽게 작성할 수 있다.

    $ git commit -m"Fix typo in introduction to user guide"

    $ git commit -m"사용자 가이드 서문의 오타를 수정함"

However, when a commit merits a bit of explanation and context, you need to write a body. For example:

하지만 어떤 변경 사항인지 맥락과 설명이 필요하다면 본문을 작성해야 한다. 다음 예를 보자.

    Derezz the master control program

    마스터 컨트롤 프로그램 삭제

    MCP turned out to be evil and had become intent on world domination.
    This commit throws Tron's disc into MCP (causing its deresolution)
    and turns it back into a chess game.

    마스터 컨트롤 프로그램(MCP)이 사악하게 변해서 세계를 통제하려고 함.
    이 커밋은 트론의 디스크를 MCP에 (삭제를 위해) 던져 넣어 MCP는 다시 체스
    게임으로 돌아감.

    (주: 영화 트론 줄거리)

This is not so easy to commit this with the `-m` switch. You really need a proper editor. If you do not already have an editor set up for use with git at the command line, read [this section of Pro Git](https://git-scm.com/book/en/v2/Customizing-Git-Git-Configuration).

이런 커밋은 `-m` 스위치를 사용해서 입력하기 어렵다. 이런 내용을 입력하기 위해서는 적합한 편집기를 사용해야 한다. 명령행 git에서 사용하는 편집기를 아직 설정하지 않았다면 [프로 Git의 내용](https://git-scm.com/book/en/v2/Customizing-Git-Git-Configuration)을 읽어보도록 한다.

In any case, the separation of subject from body pays off when browsing the log. Here's the full log entry:

제목과 본문 사이의 공백을 넣으면 로그를 확인할 때 어떤 경우라도 제목과 본문을 분리해서 출력하게 될 것이다. 로그 전체를 살펴보자.

    $ git log
    commit 42e769bdf4894310333942ffc5a15151222a87be
    Author: Kevin Flynn <kevin@flynnsarcade.com>
    Date:   Fri Jan 01 00:00:00 1982 -0200

     Derezz the master control program

     마스터 컨트롤 프로그램 삭제

     MCP turned out to be evil and had become intent on world domination.
     This commit throws Tron's disc into MCP (causing its deresolution)
     and turns it back into a chess game.

     마스터 컨트롤 프로그램(MCP)이 사악하게 변해서 세계를 통제하려고 함.
     이 커밋은 트론의 디스크를 MCP에 (삭제를 위해) 던져 넣어 MCP는 다시 체스
     게임으로 돌아감.

And now `git log --oneline`, which prints out just the subject line:

이제 `git log --oneline` 명령을 사용하면 제목 행만 출력할 수 있다.

    $ git log --oneline
    42e769 Derezz the master control program

    $ git log --oneline
    42e769 마스터 컨트롤 프로그램 삭제

Or, `git shortlog`, which groups commits by user, again showing just the subject line for concision:

또는 각 커밋을 사용자별로 묶어서 확인하는 명령인 `git shortlog`을 사용할 수 있다. 이 경우에도 간결하게 제목만 표시된다.

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


    $ git shortlog
    Kevin Flynn (1):
          마스터 컨트롤 프로그램 삭제

    Alan Bradley (1):
          보안 프로그램 "트론" 도입

    Ed Dillinger (3):
          체스 프로그램 명칭 "MCP"으로 변경
          체스 프로그램 수정
          체스 프로그램 개선

    Walter Gibbs (1):
          프로토타입 체스 프로그램 도입
          
There are a number of other contexts in git where the distinction between subject line and body kicks in—but none of them work properly without the blank line in between.

여기서 예로 든 경우 외에도 git의 다양한 상황에서 제목 행과 본문을 구분해서 작성해야 한다. 어떤 상황에서든 제목 행과 본문 사이 빈 행이 존재해야 제대로 동작할 것이다.


<a name="limit-50"></a>
### 2. Limit the subject line to 50 characters

### 2. 제목 행을 50자로 제한한다

50 characters is not a hard limit, just a rule of thumb. Keeping subject lines at this length ensures that they are readable, and forces the author to think for a moment about the most concise way to explain what's going on.

제목 행을 50자로 작성하는 것은 강제로 제한하는 것이 아니라 단지 경험에 의한 규칙에 해당한다. 제목 행을 이 길이에 맞춰 작성하면 읽기 편할 뿐더러 작성자로 하여금 무슨 일이 일어나는지 간결하게 작성하는데 집중할 수 있도록 돕게 된다.

> _Tip: If you're having a hard time summarizing, you might be committing too many changes at once. Strive for [_atomic commits_](http://www.freshconsulting.com/atomic-commits/) (a topic for a separate post)._

> _팁: 제목을 요약하는 것이 너무 어렵다면 아마도 한 번에 커밋하기에 너무 많은 변경을 포함하는 경우인지도 모른다. [_원자적 커밋_](http://www.freshconsulting.com/atomic-comits/)을 하도록 노력하자. (별도의 포스트 주제다.)_

GitHub's UI is fully aware of these conventions. It will warn you if you go past the 50 character limit:

Github의 UI는 이런 관례를 잘 알고 있다. 만약 50자 이상을 입력하려고 한다면 경고 표시가 나타난다.

![gh1](http://i.imgur.com/zyBU2l6.png)

And will truncate any subject line longer than 69 characters with an ellipsis:

그리고 69자 이상의 제목 행이라면 다음처럼 줄임 표시가 나타난다.

![gh2](http://i.imgur.com/27n9O8y.png)

So shoot for 50 characters, but consider 69 the hard limit.

그러므로 50자를 기준으로 적되, 최대 상한선은 69자임을 염두에 두자.


<a name="capitalize"></a>
### 3. Capitalize the subject line

### 3. 제목 행 첫 글자는 대문자로 쓴다

This is as simple as it sounds. Begin all subject lines with a capital letter.

이 규칙은 말 그대로 간단하다. 제목 행의 모든 단어는 대문자로 시작한다.

For example:

 - <font color="green">Accelerate to 88 miles per hour</font>

Instead of:

 - <font color="red">accelerate to 88 miles per hour</font>

이렇게 작성하는 것 보다는,

 - <font color="red">accelerate to 88 miles per hour</font>

다음처럼 작성하자.

 - <font color="green">Accelerate to 88 miles per hour</font>


<a name="end"></a>
### 4. Do not end the subject line with a period

### 4. 제목 행 끝에 마침표 넣지 않는다

Trailing punctuation is unnecessary in subject lines. Besides, space is precious when you're trying to keep them to [50 chars or less](#limit-50).

제목 행 끝에는 구두점이 필요 없다. 게다가 [50자 미만](#limit-50) 규칙을 따르기 위해서는 이런 사소한 공간도 소중하다.

Example:

 - <font color="green">Open the pod bay doors</font>

Instead of:

 - <font color="red">Open the pod bay doors.</font>


이렇게 작성하는 것 보다는,

 - <font color="red">Open the pod bay doors.</font>

다음과 같이 작성한다.

 - <font color="green">Open the pod bay doors</font>

<a name="imperative"></a>
### 5. Use the imperative mood in the subject line
### 5. 제목 행에 명령문을 사용한다

_Imperative mood_ just means "spoken or written as if giving a command or instruction". A few examples:
여기서 _명령문_ 이란 "명령이나 설명하듯 말하는 것"을 의미한다.
예를 들어보자면

 - Clean your room
 - Close the door
 - Take out the trash
 - 네 방을 치운다 (Clean your room)
 - 문을 닫는다 (Close the door)
 - 쓰레기를 갖다 버린다 (Take out the trash)

같은 것을 말한다.
 
Each of the seven rules you're reading about right now are written in the imperative ("Wrap the body at 72 characters", etc.).
당신이 지금 읽고 있는 이 글의 각 일곱 규칙 또한 명령조다. ("본문을 72자 단위로 개행한다" 등등)

The imperative can sound a little rude; that's why we don't often use it. But it's perfect for git commit subject lines. One reason for this is that **git itself uses the imperative whenever it creates a commit on your behalf**.
명령문은 우리가 자주 쓰지 않기 때문에 조금은 무례하게 보일 수 있다.
하지만 명령문은 Git 커밋의 제목 행에 완벽하게 부합한다.
그 이유로는 일단 **Git 자체가 우리 대신 자동으로 커밋을 생성하는 경우, 명령조를 사용하기 때문**이다.

For example, the default message created when using `git merge` reads:
예를 들어, `git merge`를 썼을 때 생성되는 기본 메시지는

    Merge branch 'myfeature'
    ('myfeature' branch를 병합한다)

And when using `git revert`:
그리고 `git revert`를 사용했을 때는

    Revert "Add the thing with the stuff"

    This reverts commit cc87791524aedd593cff5a74532befe7ab69ce9d.

해석하면

    "이런저런 것을 추가한다"를 되돌린다

    이것은 커밋 cc87791524aedd593cff5a74532befe7ab69ce9d을 되돌린다.

Or when clicking the "Merge" button on a GitHub pull request:
또 GitHub에서 Pull Request에서 Merge 버튼을 누르면

    Merge pull request #123 from someuser/somebranch
    (someuser/somebranch에서 온 pull request #123을 병합한다)

같이 된다.

So when you write your commit messages in the imperative, you're following git's own built-in conventions. For example:
따라서 여러분의 커밋 메시지를 명령문으로 쓸 때, Git의 컨벤션을 따르라.
예를 들면

<font color="green">

 - Refactor subsystem X for readability
 - Update getting started documentation
 - Remove deprecated methods
 - Release version 1.0.0

</font>
<div style="color: green;">

 - 가독성을 위해 서브시스템 X를 리팩토링한다 (Refactor subsystem X for readability)
 - Getting Started 문서를 갱신한다 (Update getting started documentation)
 - Deprecated된 메소드를 삭제한다 (Remove deprecated methods)
 - 버전 1.0.0으로 판올림한다 (Release version 1.0.0)

</div>

Writing this way can be a little awkward at first. We're more used to speaking in the _indicative mood_, which is all about reporting facts. That's why commit messages often end up reading like this:
이렇게 적는 것은 처음엔 조금 어색하다.
그래서 우리는 직설법을 더 많이 사용하고 그 결과 사실을 알리는 것에 치중하게 된다.
그렇게 작성된 커밋 메시지는 이런 식으로 보여지게 된다.


<font color="red">

 - Fixed bug with Y
 - Changing behavior of X

</font>
<div style="color: red;">

 - Y로 버그가 고쳐짐 (Fixed bug with Y)
 - X의 동작의 변화 (Changing behavior of X)

</div>

And sometimes commit messages get written as a description of their contents:
그리고 때때로 커밋 메시지가 내용의 설명으로 쓰여지기도 한다.

<font color="red">

 - More fixes for broken stuff
 - Sweet new API methods

</font>
<div style="color: red;">

 - 망가진 것을 좀 더 고친 것들 (More fixes for broken stuff)
 - 좋은 새 API 메소드 (Sweet new API methods)

</div>

To remove any confusion, here's a simple rule to get it right every time.
혼란함을 해결하는 간단하고 언제나 쓸 수 있는 규칙이 있다.

**A properly formed git commit subject line should always be able to complete the following sentence**:
**이 문장에 기존 커밋 내용을 대입하였을 때 문장으로써 적절하면 그것은 적절한 Git 커밋 제목 행이다.**

 - If applied, this commit will _<u>your subject line here</u>_
 - 만약 이 커밋이 적용되면 이 커밋은 _<u>커밋 제목 행을 여기에</u>_

For example:
예를 들자면

 - If applied, this commit will <font color="green">_refactor subsystem X for readability_</font>
 - If applied, this commit will <font color="green">_update getting started documentation_</font>
 - If applied, this commit will <font color="green">_remove deprecated methods_</font>
 - If applied, this commit will <font color="green">_release version 1.0.0_</font>
 - If applied, this commit will <font color="green">_merge pull request #123 from user/branch_</font>
 - 만약 이 커밋이 적용되면 이 커밋은 <span style="color: green;">가독성을 위해 서브시스템 X를 리팩토링한다</span>
 - 만약 이 커밋이 적용되면 이 커밋은 <span style="color: green;">Getting Started 문서를 갱신한다</span>
 - 만약 이 커밋이 적용되면 이 커밋은 <span style="color: green;">Deprecated된 메소드를 삭제한다</span>
 - 만약 이 커밋이 적용되면 이 커밋은 <span style="color: green;"버전 1.0.0으로 판올림한다></span>
 - 만약 이 커밋이 적용되면 이 커밋은 <span style="color: green;">someuser/somebranch에서 온 pull request #123을 병합한다</span>

Notice how this doesn't work for the other non-imperative forms:
주의할 점은 명령문이 아닌 문장형태는 이 문장으로 완성시킬 수 없다.

 - If applied, this commit will <font color="red">_fixed bug with Y_</font>
 - If applied, this commit will <font color="red">_changing behavior of X_</font>
 - If applied, this commit will <font color="red">_more fixes for broken stuff_</font>
 - If applied, this commit will <font color="red">_sweet new API methods_</font>
 - 만약 이 커밋이 적용되면 이 커밋은 <span style="color: red;">Y로 버그가 고쳐짐</span>
 - 만약 이 커밋이 적용되면 이 커밋은 <span style="color: red;">X의 동작의 변화</span>
 - 만약 이 커밋이 적용되면 이 커밋은 <span style="color: red;">망가진 것을 좀 더 고친 것들</span>
 - 만약 이 커밋이 적용되면 이 커밋은 <span style="color: red;">좋은 새 API 메소드</span>

> _Remember: Use of the imperative is important only in the subject line. You can relax this restriction when you're writing the body._
> _기억할 것: 명령문을 쓰는 것은 오직 제목 행에서만 중요하다. 본문을 쓸 때는 이 제한이 적용되지 않는다._

<a name="wrap-72"></a>
### 6. Wrap the body at 72 characters
### 6. 본문을 72자 단위로 개행한다

Git never wraps text automatically. When you write the body of a commit message, you must mind its right margin, and wrap text manually.
Git은 본문을 절대 자동으로 개행하지 않는다.
커밋 메시지의 본문을 적을 때엔 본문 우측 여백을 신경쓰며 작성해야하고, 본문을 정해진대로 손수 개행해야 한다.

The recommendation is to do this at 72 characters, so that git has plenty of room to indent text while still keeping everything under 80 characters overall.
72자 기준으로 개행하는 것을 추천한다.
그렇게 하면 전체 80자의 공간 중 Git이 들여쓰기 문자를 위해 여유 문자를 가질 수 있다.

A good text editor can help here. It's easy to configure Vim, for example, to wrap text at 72 characters when you're writing a git commit. Traditionally, however, IDEs have been terrible at providing smart support for text wrapping in commit messages (although in recent versions, IntelliJ IDEA has [finally](http://youtrack.jetbrains.com/issue/IDEA-53615) [gotten](http://youtrack.jetbrains.com/issue/IDEA-53615#comment=27-448299) [better](http://youtrack.jetbrains.com/issue/IDEA-53615#comment=27-446912) about this).
좋은 텍스트 에디터는 이 들여쓰기 작업을 도와준다.
예를 들어, Vim에서는 Git 커밋에 맞춘 설정을 하기 쉽다.
전통적으로 Vim에서 Git 커밋 메시지를 작성하면 72자 단위로 개행시켜준다.
하지만 IDE들은 커밋 메시지에서의 개행에 대한 지원이 끔찍하다.
(최근 버전의 IntelliJ IDEA는 [마침내](http://youtrack.jetbrains.com/issue/IDEA-53615) [좀](http://youtrack.jetbrains.com/issue/IDEA-53615#comment=27-448299) [쓸만해졌지만](http://youtrack.jetbrains.com/issue/IDEA-53615#comment=27-446912).)


<a name="why-not-how"></a>
### 7. Use the body to explain what and why vs. how
### 7. 어떻게 보다는 무엇과 왜를 설명한다

This [commit from Bitcoin Core](https://github.com/bitcoin/bitcoin/commit/eb0b56b19017ab5c16c745e6da39c53126924ed6) is a great example of explaining what changed and why:
이 [Bitcoin Core의 커밋](https://github.com/bitcoin/bitcoin/commit/eb0b56b19017ab5c16c745e6da39c53126924ed6)은 무엇이 바뀌었고 왜 바꿨는지 설명하는 멋진 예시다.

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

    commit eb0b56b19017ab5c16c745e6da39c53126924ed6
    Author: Pieter Wuille <pieter.wuille@gmail.com>
    Date:   Fri Aug 1 22:57:55 2014 +0200

       serialize.h의 예외 처리를 간략화한다

       serialize.h의 stream 구현과 관련된 메소드에서 'state'와 'exceptmask'를
       삭제한다.

       exceptmask는 언제나 'failbit'을 포함하고, setstate는 언제나 bits = failbit
       과 함께 호출되며 이 모든 것은 즉각적으로 예외를 발생시킨다. 이 변수들을 삭제하고
       setstate가 즉각적으로 예외를 발생시키게 바꾼다. (물론 몇몇 죽은 코드도 지운다)

       그 결과 good()은 실패 후 절대 도달할 수 없고 (딱 두 군데서 호출 되는데 한 곳은
       테스트 안임) 이것은 단순히 !eof()로 대체할 수 있다.

       fail(), clear(n),exceptions()은 전혀 호출되지 않는다. 해당 요소들은 삭제한다.

Take a look at the [full diff](https://github.com/bitcoin/bitcoin/commit/eb0b56b19017ab5c16c745e6da39c53126924ed6) and just think how much time the author is saving fellow and future committers by taking the time to provide this context here and now.
If he didn't, it would probably be lost forever.
[전체 변경사항](https://github.com/bitcoin/bitcoin/commit/eb0b56b19017ab5c16c745e6da39c53126924ed6)을 보고 작성자가 이 내용을 제공하는 데에 시간을 씀으로써 동료, 그리고 앞으로의 커미터들의 시간을 얼마나 절약시켜줄지 상상해보라.
만약 그가 이 메시지를 남기지 않았다면 이것은 영원히 묻혔을 것이다.

In most cases, you can leave out details about how a change has been made. Code is generally self-explanatory in this regard (and if the code is so complex that it needs to be explained in prose, that's what source comments are for). Just focus on making clear the reasons why you made the change in the first place—the way things worked before the change (and what was wrong with that), the way they work now, and why you decided to solve it the way you did.
대부분의 경우, 당신은 만든 것이 어떻게 바뀌었는지를 남길 것이다.
이 관점에서 보면 코드는 보통 따로 설명이 필요 없다.
(그리고 만약 코드가 너무 복잡하다면 산문으로 설명되어야할 필요가 있지만 그런 것은 코드의 주석에 적을 수 있다.)
당신이 왜 이렇게 바꾸었는지에 먼저 주목하라.
바꾸기 전에 무엇을 했는지 (그리고 무엇이 잘못 동작했는지), 지금은 어떻게 동작하는지, 그리고 왜 당신이 그렇게 바꾸기로 했는지 적어라.

The future maintainer that thanks you may be yourself!
미래의 메인테이너가 감사할 것이다. 물론 자기 자신도!


<a name="tips"></a>
## Tips

### Learn to love the command line. Leave the IDE behind.
### 커맨드 라인을 애용하고 IDE를 멀리하라.

For as many reasons as there are git subcommands, it's wise to embrace the command line. Git is insanely powerful; IDEs are too, but each in different ways. I use an IDE every day (IntelliJ IDEA) and have used others extensively (Eclipse), but I have never seen IDE integration for git that could begin to match the ease and power of the command line (once you know it).
Git 서브커맨드가 여러가지 있는 만큼 커맨드 라인을 애용하는 것이 현명하다.
Git은 미친듯 강력하다.
IDE들 또한 마찬가지지만 둘은 다른 길을 걷는다.
나는 IDE를 매일 쓴다.(IntelliJ IDEA)
그리고 다른 것도 광범위하게 쓴다.(Eclipse)
하지만 나는 IDE의 Git 지원 기능 중 커맨드라인만큼 쉽고 강력한 것을 보지 못했다.
(당신도 이미 이런 점을 알고 있을겁니다.)

Certain git-related IDE functions are invaluable, like calling `git rm` when you delete a file, and doing the right stuff with `git` when you rename one. Where everything falls apart is when you start trying to commit, merge, rebase, or do sophisticated history analysis through the IDE.
가령 파일을 지울 때 `git rm`을 해주거나 파일명을 바꿀 때 `git`으로 연계해주는 것 같은 몇몇 Git 연관의 IDE 기능들은 매우 가치있다.
하지만 커밋, 머지, 리베이스나 수준 높은 기록 분석을 IDE로 시도하면 기능이 부족하다는 점을 알 게 될 것이다.

When it comes to wielding the full power of git, it's command-line all the way.
Git이 진가를 발휘하는 때는 바로 커맨드 라인을 사용할 때다.

Remember that whether you use Bash or Z shell, there are [tab completion scripts](http://git-scm.com/book/en/Git-Basics-Tips-and-Tricks) that take much of the pain out of remembering the subcommands and switches.
Bash나 Z shell을 쓰고 있다면 tab키 완성 스크립트가 서브커맨드나 설정을 전환할 때의 고통을 줄여주니 기억하자.

### Read Pro Git
### Pro Git 을 읽을 것

The [Pro Git](http://git-scm.com/book) book is available online for free, and it's fantastic. Take advantage!
Pro Git은 온라인 상에서 무료로 읽어볼 수 있고 내용도 환상적이다.
이용하라!



[1]: http://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html
[2]: http://www.git-scm.com/book/en/Distributed-Git-Contributing-to-a-Project#Commit-Guidelines
[3]: https://github.com/torvalds/subsurface/blob/master/README#L82-109
[4]: http://who-t.blogspot.co.at/2009/12/on-commit-messages.html
[5]: https://github.com/erlang/otp/wiki/writing-good-commit-messages
[6]: https://github.com/spring-projects/spring-framework/blob/30bce7/CONTRIBUTING.md#format-commit-messages

