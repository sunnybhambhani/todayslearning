Different environment variables available in Java

So basically, the requirement was to run a Java application with a system property, which can easily be accomplished by the -D flag while running the application, but I didn't want to hardcode that in the dockerfile.

INFO:
Syntax:
       -Dproperty=value
           Sets a system property value. The property variable is a string with no spaces that represents the name of the property. The value variable is a string that represents the value of the
           property. If value is a string with spaces, then enclose it in quotation marks (for example -Dfoo="foo bar").

The above definition is captured from the Linux manuals, or so to say, man command <$ man java | less -p -D> 
Just to add here, we are just pipeing man java to the less command, where -p (is used for pattern matching) -D is the pattern which we want to match.
Since we are pipeing it to less, all the keys w.r.t. less will work without issues (for instance: n for next available match pattern).


And the reason was simple; I wanted to make sure my Docker image is flexible enough to support other values of the property as well.

My initial impression and approach were that it could be easily accomplished using the JAVA_OPTS environment variable, and that when the application runs, it will automatically take that into account, and my job is done.

But unfortunately, I was wrong, and after setting up that environment value with the intended value, it didn't work, and I was really reluctant to hard code that.

Later on, I did a small search and below are the findings:
