# Regex for Newbies

> Some people, when confronted with a problem, think “I know, I'll use regular expressions.” Now they have two problems.
>
> – Jamie Zawinski

## What's A Regular Expression?
A regular expression is a formal way to define a pattern used to match sections of a block of text. Most of the time, regex are delimited with forward-slashes, e.g. ```/.+/```, and most of the time they are completely unreadable. Take the following regex, which could be used to match emails:

```
.+\.?\_?.+@\w\.\w
```

If you a) haven't seen regex before or b) haven't used Perl before, then you probably have no idea what this regex does. This guide will try to help you understand one of the best hammers in your toolbox when it comes to pattern recognition and strings.

**An Important Note:** Regular expressions should only be used when:

* You *know* that there is a regular pattern (hence regular expression) to all the strings you're trying to find. Random patterns will force you to accommodate so many possibilities you end up with spaghetti code that can only be modelled with string theory.
* More importantly, you can write an *efficient* way to match that pattern.
* You're dealing with strings, preferably large chunks of text.

It's a fair statement to say that regex are overused, and there are often more readable and understandable ways to search for patterns in small strings, while poorly-written regex can [be 6200-character monsters](http://blog.codinghorror.com/regex-use-vs-regex-abuse/). But if you use it wisely, you'll be slinging regex like a pro in no time.

## Lessons

### Lesson 1: Matching Literals
Of course, the first thing you're going to want to match are words. If you've ever wanted to know how find-and-replace works, usually it's done with regex. To match the string "cat", all you have to do is write "cat".

**Exercise 1:** On the topic of pets, let's try to write a regex that'll match "dog".

Easy-peasy. Please note that if you put spaces in a regex, it *will* try to match the spaces as well. Then again, most modern regex dialects allow you to turn on a mode where whitespace is ignored, making it a bit easier to read.

#### Summary
|Token|Matches|
|-|-|
|abc|The string "abc"|

### Lesson 2: Wildcards and Repetition
An important part of looking for patterns is finding repetitions. Let's say that you like strings with a sequence of more than one consecutive "l" in them, so that it'll match "wall", "a5ll" and "ll&ma", but not "cal", "pail" or "lil". This is where the "\*" character comes in. If you write "l*", it'll match at least one repetition of "l". If you want to match at least zero repetitions of "l", use "l+". Now we have to take into account that there might be any character coming before or after your "l"s, so we have to use what's called the wildcard character, or ".". This will match any character *but* newlines, which means that it'll also match spaces and tabs. Although we might not want this, it'll do for now.

**Exercise 2:** Go ahead and write a regex that'll match words with more than one consecutive "l".

*Hint:* Do keep in mind that your run of "l"s might be in the middle of the word.

**Exercise 3:** You decide that "l"s aren't for you, but EBNF is! To celebrate your newfound love of EBNf, you decide to write a regex that matches the EBNF spec ```<character>EB{B}N{N}F{<character>}```.

*Hint:* X{X} means X is repeated once or more, while {X} means that X is repeated zero times or more.

Excelsior! You're learning great!

Despite breaking up with "l"s long ago, you decide that it was because of how unsure you were about how many times you wanted to repeat "l". Let's be more specific about the repetitions. EBNF and regex are more alike than you thought - if you want to repeat "l" four times, let's say, then the regex for this would be "l{4}". Maybe you'd like to repeat "l" at least four times? "l{4,}". Or perhaps you'd like to repeat "l" between 4 and 6 times? "l{4, 6}". Finally, if you want to make including "l" optional, then "l?" will solve that problem. ("?" is effectively "{0, 1}".)

**Exercise 4:** You define your own custom class of word known as a ```yippee```. A yippee is made up of:
* 4-6 "l"s
* an "i" (optionally)
* and between 2 to ∞ "m"s.
Write a regex to search for ```yippee```s in a block of text.

You've mastered the use of wildcards and repetition. Good job!

#### Summary
|Token|Matches|
|-|-|
|.|Any character except a newline.|
|a+|a, repeated at least zero times.|
|a*|a, repeated at least one times.|
|a{3}|a, repeated three times exactly.|
|a{2,}|a, repeated at least twice.|
|a{1, 3}|a, repeated from one to three times.|
|a?|a can be present or not.|

### Lesson 3: Ranges and Selections
Much like EBNF, you can choose to only match characters from a set of characters delimited by pipe characters. That is, if you want to match a character from [a, b, c], you can write "a|b|c". (Note that this is equivalent to "[abc]".)  Alternately, you could match a character from a range - if you want to match a character from "g" to "k", then you can write [g-k]. Note that the range matching is case-sensitive - that is, [A-E] will match "A", "B" and "E", but not "a", "b" nor "e". But you might be wondering if there's a way to exclude certain characters from the selection, and- of course- there's a way to do that. Change "[abc]" to "[^abc]" to match all characters *but* "a", "b" and "c". This does not work for the "a|b|c" syntax, but instead anchors the match - but more on that later. You can even string together ranges like "[a-zA-C1-9]" to match a-z, A-c and 1-9.

**Exercise 5:** You look through your contacts, and decide to write a regex to filter friends and enemies. Write two seperate regex that match your friends' names and your enemies' names respectively.

|Friends|Enemies|
|-|-|
|Steve|Dwayne|
|Reese|Tamra|
|Cheryl|Jem|
|Red|Gozer|

*Note:* You get more marks for being more specific in Regexville. Also, I know this is a table, but no SQL queries allowed. (Nice try.)

**Exercise 6:** You decide to go out and meet some more friends. Fifteen drinks later and ten hours of unaccounted time, you have a massive set of new friends. Since most of these people are only your friends because you bought them drinks, you decide to filter out your sham friends from your real friends. As "champagne for your real friends and real pain for your sham friends" isn't a valid regex, write a regex to filter only your sham friends' names.

|Sham Friends|Real Friends|
|-|-|
|Alice|Steve|
|Bob|Reese|
|Cheryl|Kyon|
|D-Wizzle|Graves|

#### Summary
|Token|Matches|
|-|-|
|(a|b|c)|a or b or c|
|[A-D]|A, B, C, or D|
|[a-d]|a, b, c or d|

### Lesson 4: Character Classes
Remember when we discussed wildcard characters, and the fact that they can also match whitespace? We can use character classes to effectively filter out certain types of characters. For example, if you'd like to match phone numbers of the form ```1234-5678```, we only want digits in the input. A "wildcard" for digits is represented by \d. To invert the wildcard (non-digits), capitalise the letter - e.g. if digits are \d, then non-digits are \D. \w represents a word, and \s represents whitespace. (You should be able to work out the rest.)

Now that we have some of the most useful filters in place, let's try to build a regex we can use for a practical project.

**Exercise 7:** Let's try to match phone numbers of the form ```1234-567-890```. Write a regex to match or not match the following data:

|Match|Don't Match|
|-|-|
|0413-333-456|0123456789|
|1234-178-932|1134 937 178|

**Exercise 8:** Now let's try to match phone numbers with area codes. Write a regex to:

|Match|Don't Match|
|-|-|
|+04 0413-333-456|041234567890|
|(+04) 0556-913-012|(+ 04) 0556-913-012|

**IMPORTANT:** Brackets are special characters in Regexville. You have to escape them with a backslash, and we'll go more into why they're special in the next chapter.

**Exercise 9:** Time to ditch the hyphens and allow some whitespace for better readability. You know the drill by now.

|Match|
|-|
|+ 04 0413 333 456|
|(+ 04) 0413-333-456|
|(+ 04) 0413 333 456|
|+04 0413 333 456|

### Lesson 5: Capture Groups and Brackets
The data you match with the regex isn't actually extracted automatically. You have to use what are known as capture groups, which are delimited by brackets. For example, if we wanted to capture the area code from a phone number formatted a la Exercise 8, we could use the regex ```\(?\+(\d{2})\)```. Let's step through this regex.

1. ```\(?```: Optional escaped bracket.
2. ```\+```: Escaped plus.
3. ```(\d{2})```: This is a capture group that extracts the 2 digits (\d{2} means two digits) inside it.
4. ```\)```: And to close off (ha), an escaped right bracket.

Much like mathematical expressions, sometimes you want to surround a group of characters with brackets to apply an operation to a whole set of characters, instead of capturing the group. For example, you could match at least one repetition of "blah" by using "(?:blah)\*". Alternately, you might want to use the result of a capture group later on down the line, using \x, where x is the number of the capture group you're using. One of the ways that this is useful is if you're trying to match up tags in HTML: ```<(\w)></\1>``` will match a tag (e.g. ```<p>```) and extract the name of the tag, before replacing \1 with the extracted value. Thus, the regex will match ```<p>This is a string!</p>```.

**Exercise 10:** Looks like you need a real challenge. OK then, let's take the idea of HTML tag matching and run with it. Let's say that a tag is defined as the following in EBNF:

```
tag = <<word> {<argument_name>={"}<argument_value>{"}{ }}> // Note that ALL SPACES ARE LITERAL.
```

Write a regex that will match all tags, whatever's between them, and the closing tag. Do make sure that "whatever's between them" includes newlines.

#### Summary
|Token|Matches|
|-|-|
|(abc)|Will extract the value of abc.|
|(?:)|A passive capture group (doesn't actually capture anything)|
|\1|The value of the first capture group defined.|

## Finally: Congrats!
You've learnt the basics of regular expressions and are now ready to wield them at their greatest. Just remember that with great power comes great responsibility. It's your responsibility to remember that at the end of the day, several readable regexes to cover each case are much better than one omni-regex that God himself might take a little while to read.

(Oh, and I'll provide answers later, as well as more chapters if you'd like them.)

– Jonah
