# Different environment variables available in Java #

So basically, the requirement was to run a Java application with a system property, which can easily be accomplished by the -D flag while running the application, but I didn't want to hardcode that in the dockerfile.

**INFO:**
```
Syntax:
       -Dproperty=value
           Sets a system property value. The property variable is a string with no spaces that represents the name of the property. The value variable is a string that represents the value of the
           property. If value is a string with spaces, then enclose it in quotation marks (for example -Dfoo="foo bar").

```
The above definition is captured from the Linux manuals, or so to say, man command **_<$ man java | less -p -D>_**
- Just to add here, we are just pipeing man java to the less command, where -p (is used for pattern matching) -D is the pattern which we want to match.
- Since we are pipeing it to less, all the keys w.r.t. less will work without issues (for instance: n for next available match pattern).

And the reason was simple; I wanted to make sure my Docker image is flexible enough to support other values of the property as well.

</br>
My initial impression and approach were that it could be easily accomplished using the JAVA_OPTS environment variable, and that when the application runs, it will automatically take that into account, and my job is done.

But unfortunately, I was wrong, and after setting up that environment value with the intended value, it didn't work. The JVM was not picking that up, and I was really reluctant to hard-code that.

</br>
Later on, I found there are multiple other environment variables as well, and also a quick note about JAVA_OPTS:

- JAVA_OPTS:
>JAVA_OPTS is not an environment variable that the java executable will recognise on its own. Instead, various scripts that wrap the start-up of Java will often use an environment variable named JAVA_OPTS to configure the java executable (for example, the tomcat startup script does this).

Ref: https://community.oracle.com/tech/developers/discussion/1542017/cannot-get-java-opts-to-work for more details.

- JAVA_TOOL_OPTIONS:
>In some scenarios, the CLI is not accessible, like in mine, wherein we cannot pass the intended properties. In those cases, the JAVA_TOOL_OPTIONS environment variable can be useful to append the command with the intended property.

Ref: https://docs.oracle.com/javase/8/docs/technotes/guides/troubleshoot/envvars002.html

- _JAVA_OPTIONS

> For this one, based on my understanding, it is kind of similar in usage to the above one, but it differs in some contexts like order presedence and error handling, and also this one is not that widely used, and unfortunately, was not able to find official documentation about it.

In a nutshell, I **ended up using "JAVA_TOOL_OPTIONS"** and it worked without issues :)

Please feel free to add or correct anything I missed. 
