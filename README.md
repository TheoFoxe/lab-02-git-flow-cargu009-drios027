# GitHub Flow

> Author(s): Andrew Lvovsky ([@borninla](https://github.com/borninla)) and Brian Crites ([@brrcrites]())

For most of your professional careers in industry, you will work on projects alongside other contributors. In order to reduce the friction that can occur between developers with different backgrounds, styles, and opinions most companies, organizations, and projects dictate their preferred style and method of contribution. Dictating a preferred style gives your codebase a consistent look and makes it easier for developers to understand since things are done in a consistent manner. Having a preferred method of contribution, or workflow, makes sure that different contributors know who is working on what and enforce that their contribution meets the standards set out by the organization. Having a good workflow is an important step to make sure your organziation (or for this course, your team) is working efficiently to create well tested and high quality code.

Many [git workflows](https://www.endpoint.com/blog/2014/05/02/git-workflows-that-work) exist today and they all have their pros and cons. However, GitHub flow is one of the simplest to learn and is our preferred method for this course. A brief overview of GitHub flow is found in the diagram below.

<img src="https://github.com/cs100/template-lab-XX-git-flow/blob/dev/images/github-flow.png?raw=true" width="1000">

Source: [GitHub Guides](https://guides.github.com/introduction/flow/)

The goal of this lab is to get you more comfortable using Git and GitHub when working on a team. You and your partner will work on extending the Bash Unit Testing lab by adding a count function, along with unit tests for it. By the end of this lab, you should be familiar with

* how to create issues to manage tasks
* how to use branches to reduce conflicts
* how to use pull requests to review and merge changes
* how to tackle merge conflicts
* how to tag releases
* how to revert to previous commits

## Issue Tracking

The first thing we are going to do is use the built-in GitHub issue system to track and manage tasks. Issues are useful to keep track of bugs that appear in projects as well as tracking work that still needs to be done. 

In GitHub each repository has its own associated set of issues, which are located under the `Issues` tab near the top of the repository. Currently, there aren't any open issues for this lab, so let's add some. Click on the green `New issue` button. On this page, you should notice some sections on the right. They are:

* Assignees - the developer assigned to the task, who will recieve email alerts when comments are made on the issue. Keep in mind that more than one person can be assigned to the same issue, especially when they require some discussion.
* Labels - help to categorize the types of tasks that need to be completed. GitHub will autopopulate a list of tags for your repo, but you are free to edit or remove them as you see fit. We recommend using a basic set of `bug`, `test`, and `develop` tags to start with, and adding more as needed. GitHub tags can range from [relatively small and simple sets](https://medium.com/@dave_lunny/sane-github-labels-c5d2e6004b63) to [large hierarchies of color coded tags](https://robinpowered.com/blog/best-practice-system-for-organizing-and-tagging-github-issues/).
* Projects - which project this issue falls under. Projects are typically used to split the issues across teams or large features, and for this course we recommend you just have a single TODO project if you use it at all. If you are using the Projects feature, it is possible to to have issues automatically added to a project's Kanban board. [You can read this link for more information](https://help.github.com/en/articles/configuring-automation-for-project-boards).
* Milestone - which milestone this issue falls under. This is similar to Projects, except milestones are usually associated with specific sprints and track completion of issues towards a particular feature or release.

Let's start by creating two issues for today's lab, with each partner creating one of the issues. Here we have listed the title is in bold followed by the text for the body of the issue. Note that you can use markup in the body of your issues which we encourage you to do.

* **Create a character counting function**: Add a function to `main.cpp` which counts the number of characters input from the user
* **creating a character counting unit test**: Create unit tests for the character count function

Throughout the remainder of the lab, Partner 1 will be working on the count function and Partner 2 will be creating the unit test, so make sure to set the assignees according to who will be completing which portion. For creating a count function, choose the "enhancement" label. For the unit test, choose the "unit test" label. Don't worry about selecting Projects or Milestone for this lab.

> Note: If the "enhancement" or "unit test" label doesn't exist, click on "Edit labels" and then "New label" at the top right and add it.

## Copying Files from the Bash Lab

Did you think we were done adding issues? Nope! ( ͡° ͜ʖ ͡°) 

We should probably add one for copying some necessary files over from the bash lab. Go ahead and do so now, assigning the issue to both partners and giving it an "enhancement" label.

Since this is an extension of the previous lab, let's copy some necessary files over (pick one partner to do this). Hopefully, you are working on the machine you used for the bash lab, whether it be hammer or a local environment. Make sure that both the bash lab's directory and this lab's directory are on the same level of hierarchy (located in the same directory). Go ahead and use the `cp` command to copy the following files over to this lab's directory. From the bash lab directory, run the following command:

```
cp c-echo.h test.cpp CMakeLists.txt main.cpp ../<this lab's directory name>
```

Four new files should now be located in this lab's directory. Make sure to also run `git submodule add https://github.com/google/googletest.git` to add the Google Testing framework.

Go ahead and git add, commit, and push these four files. You're now ready to close your first issue!

## The Importance of Effective Branching

In the Git lab, we learned that branching is used when developers want to change code without having conflicts commit after commit. When working on a team, it is wise to separate what someone is working on by making a branch unique to that addition or edit. When that team member finishes their part, they can then merge back into the master or main development branch.

Before branching, let's do some preliminary work. For the purpose of a future exercise, one partner should create a file named `c-count.h` and add the following code.

```c++
#include <iostream>

// count function should go here
```

Once you commit and push the above change, each partner should now create a branch from the master branch. Both partners should title their branches as such, respectively:

```
$ git branch <partner-1-github-username>/count-func
$ git branch <partner-2-github-username>/count-test
```

**Make sure to checkout the branches right after creating them.**

### The First Branch

Partner 1 should add the following count function to `c-count.h`:

```c++
#include <iostream>

unsigned int count(const std::string& str) {
    unsigned int counter = 0;
    bool on_space = true;
    
    for(int i = 0; i < str.size(); i++) {
        if (std::isspace(str[i]))
            on_space = true;
        else if (on_space) {
            counter++;
            on_space = false;
        }
    }

    return counter;
}
```

This function counts the number of words in a string, delimited by whitespace. It takes a string and iterates through every character. When whitespace is encountered, a flag gets set. If the next character doesn't contain whitespace, the counter is incremented and the flag is unset.

### The Second Branch

Partner 2 should add the following unit tests to `test.cpp`:

```c++
#include "c-echo.h"
#include "c-count.h"

#include "gtest/gtest.h"

TEST(EchoTest, HelloWorld) {
    char* test_val[3]; test_val[0] = "./c-echo"; test_val[1] = "hello"; test_val[2] = "world";
    EXPECT_EQ("hello world", echo(3,test_val));
}

TEST(EchoTest, EmptyString) {
    char* test_val[1]; test_val[0] = "./c-echo";
    EXPECT_EQ("", echo(1, test_val));
}

TEST(CountTest, HelloWorld) {
    std::string test_str = "hello world";
    EXPECT_EQ(2, count(test_str));
}

TEST(CountTest, EmptyString) {
    std::string test_str = "";
    EXPECT_EQ(0, count(test_str));
}

TEST(CountTest, ManySpaces) {
    std::string test_str = "   this   string has     weird   spacing";
    EXPECT_EQ(5, count(test_str));
}

int main(int argc, char **argv) {
    ::testing::InitGoogleTest(&argc, argv);
    return RUN_ALL_TESTS();
}
```

One more thing, Partner 2. Your `c-count.h` should still look like below:

```c++
#include <iostream>

// count function should go here
```

Let's say you didn't know your partner was editing the same file in another branch. Let's edit it like so:

```c++
#include <iostream>

// count function still needs to go here
```

Uh-oh, Partner 2. Looks like you are the catalyst for a possible merge conflict! Not to worry. Git provides a nice way to review merge conflicts, which we will discuss in a future section.

### The Push

Both partners should now commit and push these changes.

> Aside: Since these branches were made locally and need to be pushed up to GitHub, you will be prompted to run `git push --set-upstream origin <branch name>` when pushing a branch for the first time.

Each partner should also refresh their local git by running `git fetch`. This will update your local repository so that the other partner's branch will show up.

Before merging the branches, it is good practice to open a pull request that can be reviewed by your team members.

## Pull Requests
The pull request system allows for maintainers of repositories to block changes from being added to certain branches until they meet specific requirements such as having a minimum number of approved code reviews and successfully passing automated tests. By having GitHub enforce these rules for us before our stable branches (like master) can have code added to them we greatly increase the changes that our stable branches will continue to be stable.

Let's try doing this now. Both partners should open up their GitHub repository to prepare the PR. 

### Creating a Pull Request

Partner 1 should press on the branch dropdown menu and select `<partner-1-github-username>/count-func`. Next, click on "New pull request". This will bring up the editing screen for the PR. You might notice that this page looks similar to creating a new issue, with an added "Reviewers" section. Partner 1 should add a reviewer by clicking on the "Reviewers" section. Search Partner 2's username and click on their username. This will assign Partner 2 to review the PR. Make sure to also assign yourself (Partner 1) under the Assignees section, and give it a label that best suits the addition. Title it `Count Function` and leave a brief description of what is being added. In most cases, a description should be well-detailed so the reviewer can easily understand.

Once done finalizing the PR, click on "Create pull request".

Partner 2 should follow the same steps above, except by selecting `<partner-2-github-username>/count-test` from the branch dropdown menu. Also, title it `Count Unit Test`. Make sure to choose Partner 1 as the reviewer and Partner 2 as the assignee.

### Issue Tracking Strikes Back!

Remember that issue you made? There's a nifty way to close an issue without having to go to it directly and close it. 

Both partner's should amend their latest commit message. Type the following to do so:

```bash
git commit --amend
```

This will open up your editor. The very top should contain your most recent commit message. Go ahead and change the message to `Fixed #<issue number>`.

> The issue number can be found to the right of your issue's title (after the hashtag).

Exit the editor, then push once more. Go to your PRs on GitHub. You'll notice that every commit made to a branch that has an open PR will show up. You should also notice that your commit message has a hyperlink. If you click on the number, it should send you to the specified issue. Once the PR is merged and closed, GitHub will take care of closing the issue for you automatically. Nice!

### Reviewing a Pull Request / Code Reviews

Throughout development, team members will most likely be working on integrating features into existing code. To help understand what is being added, code reviews are usually held with the team to better explain the addition. The traditional way is to meet and work through explaining every critical section of code, but doing this through pull requests is also acceptable.

Review this [guide](https://smartbear.com/learn/code-review/best-practices-for-peer-code-review/) for some good practices for code reviews.

Partner 2 should now review Partner 1's PR. Navigate to the "Pull requests" tab and click on `Count Function`. From here, you can review comments made about the PR, all commits associated with that PR, and what changed within the files.

Near the bottom, you should see a green checkmark. This means GitHub found no possible merge conflicts. Go ahead and press "Merge pull request". This completes the merge from the `<partner-1-github-username>/count-func` branch to the master branch. You are now given the option to delete the branch. It's nice to keep repositories neat and tidy, so go ahead and delete the branch.

Partner 1 should navigate to the "Pull requests" tab and click on `Count Unit Test`. Unlike the first PR, this one has a merge conflict.

<img src="https://github.com/cs100/template-lab-XX-git-flow/blob/dev/images/resolve-conflicts.png?raw=true" width="600">

#### Fixing a Merge Conflict

Let's resolve that now. Partner 1 should type the following commands:

```bash
git checkout master
git pull
git checkout <partner-2-github-username>/count-test
```

This will update any changes made to master, then checkout Partner 2's branch.

Finally, type `git merge master` to merge the current branch you checked out into master. You should see something like below:

```
Auto-merging c-count.h
CONFLICT (content): Merge conflict in c.count.h
Automatic merge failed; fix conflicts and then commit the result.
```
If you open `c-count.h` in a text editor, you should see some lines that include `<<<<<<< HEAD`, `=======`, and `>>>>>>> master`. 

<img src="https://github.com/cs100/template-lab-XX-git-flow/blob/dev/images/conflict.jpg?raw=true" width="600">

Everything past `<<<<<<< HEAD` and before `=======` is the new code coming in from the branch being merged, while everything past `=======` and before `>>>>>>> master` is existing code from the master branch (the branch that is being merged into). We'd like to accept the new code, so go ahead and delete everything past `=======` and before `>>>>>>> master`. Make sure to also delete these indicators, or you will get obvious compilation errors.

> Aside: While this merge was relatively straight-forward and we just wanted to accept what was in master, there are times when you will need to merge both branches in order to get what you actually want. This may lead to additional errors, so its always important to test the code to make sure it's still working after a merge has been resolved.

Once you are done, be sure to commit and push as usual.

Note that when committing a merge, simply typing `git commit` without the `-m` flag will automatically populate a merge message.

Partner 1 should now refresh the `Count Unit Test` PR on GitHub. Notice that every commit made in the branch should appear within the PR. 

Using the good practices for code reviews reference above, make sure everything looks good with this PR.

There should no longer be any merge conflicts, so go ahead and press "Merge pull request" and repeat the same process as stated above.

## Tagging

Often, you’ll want to tag certain git commits because they are special. The most common reason to tag a commit in git is to tag it as a beta or release version, especially when using semantic versioning schemes. Let's assume that our code is now ready for release as a v1.0.0 and tag it as such.

```
$ git tag -a v1.0.0 -m “Initial release”
```

The -m in this case works the same as it does when performing a commit, allowing us to add a message but bypassing the editor to do so. Note that if you wanted to tag a commit besides the current commit you would need to add the commit hash after the version number (but before the message). Normally a 1.0.0 release would have lots of additional notes on the current state of the software, but this is just an example. These tagged versions are stored in a special section of the repository on GitHub along with compressed versions of the source files to make public releases easier.

## Reverting Commits

Throughout the development process, there are times where new commits may introduce problems in existing code. For these situations, git handles reversion quite well.

Let's say you push a commit that breaks something. To revert back to the last commit, type the following command.

```
git revert HEAD
```

This will create a new commit that is left off at the last commit. To revert to 2 or more commits down the chain, replace `HEAD` with the commit reference (the long string of characters that identify each commit).
