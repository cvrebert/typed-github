[![Build Status](https://travis-ci.org/cvrebert/typed-github.svg?branch=master)](https://travis-ci.org/cvrebert/typed-github)
[![Maven Central](https://maven-badges.herokuapp.com/maven-central/com.chrisrebert/typed-github/badge.svg)](https://maven-badges.herokuapp.com/maven-central/com.chrisrebert/typed-github)
[![JavaDoc](https://img.shields.io/badge/javadoc-html-blue.svg)](http://www.javadoc.io/doc/com.chrisrebert/typed-github)

More details are here: [WEBSITE LINK](http://example.com/).

Java 7 or higher is required.

The `com.chrisrebert.github` package presents an object-oriented interface to the GitHub API:

```java
public class Main {
  public static void main(String[] args) throws IOException {
    Github github = new RtGithub(".. your OAuth token ..");
    Repo repo = github.repos().get(
        new Coordinates.Simple("cvrebert/typed-github")
    );
    Issue issue = repo.issues().create("How are you?", "Please tell me...");
    issue.comments().post("My first comment!");
  }
}
```

We also provide `MkGithub`, a mock version of GitHub server, which
you can use in unit tests, for example:

```java
public class FooTest {
  public void submitsCommentToGithubIssue() {
    final Repo repo = new MkGithub().repos().create(
      Json.createObjectBuilder().add("name", "test").build()
    );
    final Issue issue = repo.issues().create("how are you?", "");
    new Foo(issue).doSomething(); // should post a message to the issue
    MasterAssert.assertThat(
      issue.comments().iterate(),
      Matchers.iterableWithSize(1)
    );
  }
}
```

## How to contribute?

Fork the repository, make changes, submit a pull request. See [`CONTRIBUTING.md`](https://github.com/cvrebert/typed-github/blob/master/CONTRIBUTING.md) for more info.

There are many integration tests that check our classes against live GitHub accounts. In order to run them, you should create new GitHub OAuth access tokens
([how?](https://help.github.com/articles/creating-an-access-token-for-command-line-use)), and provide them in command line, like this:

```
$ mvn clean install -Dit.test=RtGistITCase -Dfailsafe.github.key=<token> -Dfailsafe.github.key.second=<second-token> -Dfailsafe.github.repo=<repo>
```

Replace `<token>` and `<second-token>` with the OAuth access tokens of two different GitHub accounts. This test case will try to fork a gist from first account into second. Replace `<repo>` with the name of repository you create in your first account (for test purposes only), for example `cvrebert/test`. OAuth access tokens should have permissions in their respective repos to all scopes needed by the integration test suite you want to run (including `delete_repo`, which is not set by default!).

Please note that different integration tests may need keys with permissions to different [scopes](https://developer.github.com/v3/oauth/#scopes).
To run all integration tests, the key should have the following OAuth scopes:
* read:org
* repo
* delete_repo
* admin:public_key
* gist
* admin:repo_hook
* user
* user:email

`RtForksITCase` requires the additional parameter `-Dfailsafe.github.organization=<organization>` where `<organization>` is an organization name to fork a test GitHub repository.

In order to run only the static analysis checks, use this:

```
$ mvn clean install -DskipTests -Dinvoker.skip=true -Pqulice
```
