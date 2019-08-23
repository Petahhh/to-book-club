# Chapter 4 - Comments

* Comments are a “failure to express yourself in code” - should be avoided when possible because:
  * Comments rot really fast. It’s really hard to have accurately maintained comments over several people/commits/months or years. It’s better to have expressive code instead (via restructuring, good naming and small “do only one thing” methods)
* Legal comments are the only exception - you can’t really avoid them Ex ApacheSoftwareFoundation comments
* An example where a comment is necessary and help when expressing yourself through code is not possible is commenting out complex regular expressions or complex input like creating timestamps or other date related things. Here **adding clarity** to something that can be restructured or renamed is a key situation to add a comment.
* Sometimes comments are helpful to explain the intent of weird code.
* TODOs can be a useful way to indicate improvements that can be made but not possible at the current moment. 
* Amplification - comments can amplify the significance of some code that may seem insignificant.
* Mandated comments such as **javadoc can sometimes be noise instead of helpful**. Example when the comment is obviously redundant to the well named method and its arguments or a well named variable.
* When tempted to write a comment, it might be a signal to either restructure or wrap logic into well named helper functions
* Position/Banner markers should be used very sparingly. They stand out when you see them but only when used sparingly
* Close brace comments are a sign that your function may be too long

```
func() {



} //end of func
```

* Commented out code is a definite no-no. We have git and other versioning tools. We’ll never need commented out code to revive old code.
Examples:

https://github.com/concourse/concourse/blob/82dac8d08a215231f875dfaa6985809649dad6bd/cmd/concourse/worker_linux.go#L38
https://github.com/concourse/concourse/blob/82dac8d08a215231f875dfaa6985809649dad6bd/cmd/concourse/worker_linux.go#L113
https://github.com/concourse/concourse/blob/82dac8d08a215231f875dfaa6985809649dad6bd/cmd/concourse/worker_linux.go#L141
https://github.com/concourse/concourse/blob/82dac8d08a215231f875dfaa6985809649dad6bd/cmd/concourse/worker_linux.go#L278
https://github.com/concourse/concourse/blob/82dac8d08a215231f875dfaa6985809649dad6bd/cmd/concourse/worker.go#L119
https://github.com/concourse/concourse/blob/82dac8d08a215231f875dfaa6985809649dad6bd/testflight/list_builds_test.go#L27
https://github.com/concourse/concourse/blob/master/go-concourse/concourse/teams.go#L17
https://github.com/concourse/concourse/blob/82dac8d08a215231f875dfaa6985809649dad6bd/go-concourse/concourse/teams.go#L38
https://github.com/concourse/concourse/blob/82dac8d08a215231f875dfaa6985809649dad6bd/go-concourse/concourse/teams.go#L76

