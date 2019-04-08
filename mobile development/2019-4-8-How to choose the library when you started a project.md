# How to choose the library when you started a project
- Author:[MarcSteven](https://twitter.com/marcstevencoder)
- Date:2019.4.8 PM 12:18

---

As we all know ,library is a serial tools to help you to develop some features fast ,but as a developer if time is enough ,don't use library directly ,if you wanna know why don't do like that ,pls read the article
You should not assume that just because an existing library appears to provide the functionality you need, you will automatically use it. If it actually does what you need with minimal configuration, you still need to consider some important factors before deciding to use third-party code.
# Solution: Follow TAM
There are three guidelines to use when evaluating third-party tools: tests, activity, and maturity (which we refer to as TAM).

## Check for Tests

The most important thing to look for when evaluating a library is that it comes with an automated test suite. Without one, there is little guarantee that it works at all or won’t repeatedly break down the road.
If a library seems truly valuable and doesn’t have tests, then you should write tests for the code and contribute them to the project.

## Check for Activity

It’s not enough to know that a library just exists. You also want the comfort of knowing that it is actively being used by the community. If there is very little activity in a library’s source code, trouble tickets, and mailing list, this could be a red flag that the code is out of date or not well supported. You don’t want to start using inactive code unless you’re prepared to make an investment in supporting it yourself.

## Check for Maturity

The preceding two guidelines are closely tied to the maturity of the third-party code. However, tests and activity are not the only measures of maturity. In fact, it’s entirely possible for a very new, unstable library to have both tests and a lot of interest and activity. Time and usage are the real measures of maturity. If a library has been around for a while, is well maintained, and has many users, it can be considered mature.

There can be consequences associated with using immature code. It’s likely that your application will break or have bugs in the current release or future releases. As with inactive code, you’ll want to shy away from immature code unless you’re willing to make an investment in contributing to supporting it yourself.

