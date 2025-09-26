

<h1>Dart regular expressions</h1>

<p>
In this article we show how to work with regular expressions in Dart.
</p>


<p>
Regular expressions are used for text searching and more advanced text
manipulation. We find them tools such as grep and sed, text editors such as vi
and Emacs, and programming languages.
</p>

<p>
<code>RegExp</code> is used to define a regular expression. Dart regular
expressions have the same syntax and semantics as JavaScript regular
expressions.
</p>

<h2>Dart regex hasMatch</h2>

<p>
The <code>hasMatch</code> function checks whether the regular expression has a
match in the specified string.
</p>

<div class="codehead">main.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
void main() {
  var words = &lt;String&gt;["Seven", "even", "Maven", "Amen", "eleven"];

  var rx = RegExp(r".even");

  for (var word in words) {
    if (rx.hasMatch(word)) {
      print("${word} does match");
    } else {
      print("${word} does not match");
    }
  }
}
</pre>

<p>
In the example, we have five words in a list. We check which words match the
<code>.even</code> regular expression.
</p>

<pre class="explanation">
var rx = RegExp(r".even");
</pre>

<p>
We define a regular expression with <code>RegExp</code>. The dot (.)
metacharacter stands for any single character in the text.
</p>

<pre class="explanation">
for (var word in words) {
  if (rx.hasMatch(word)) {
      print("${word} does match");
  } else {
      print("${word} does not match");
  }
}
</pre>

<p>
We go over the elements of the list and check if the elements match the regular
expression with <code>hasMatch</code>.
</p>

<pre class="compact">
$ dart main.dart
Seven does match
even does not match
Maven does not match
Amen does not match
eleven does match
</pre>

<h2>Dart regex alternation</h2>

<p>
The alternation operator | creates a regular expression with several choices.
</p>

<div class="codehead">main.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
void main() {
  var words = [
    "Jane",
    "Thomas",
    "Robert",
    "Lucy",
    "Beky",
    "John",
    "Peter",
    "Andy"
  ];

  var rx = RegExp("Jane|Beky|Robert");

  words.forEach((word) {
    if (rx.hasMatch(word)) {
      print("the ${word} matches");
    }
  });
}
</pre>

<p>
We have eight names in the list.
</p>

<pre class="explanation">
var rx = RegExp("Jane|Beky|Robert");
</pre>

<p>
This regular expression looks for "Jane", "Beky", or "Robert" strings.
</p>

<pre class="compact">
$ dart main.dart
the Jane matches
the Robert matches
the Beky matches
</pre>


<h2>Dart regex subpatterns</h2>

<p>
Subpatterns are patterns within patterns. Subpatterns are created with ()
characters.
</p>

<div class="codehead">main.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
void main() {
  var words = [
    "book",
    "bookshelf",
    "bookworm",
    "bookcase",
    "bookish",
    "bookkeeper",
    "booklet",
    "bookmark"
  ];

  var rx = RegExp(r"^book(worm|mark|keeper)?$");

  for (var word in words) {
    if (rx.hasMatch(word)) {
      print("${word} does match");
    } else {
      print("${word} does not match");
    }
  }
}
</pre>

<p>
We find all words that matches the subpatterns.
</p>

<pre class="explanation">
var rx = RegExp(r"^book(worm|mark|keeper)?$");
</pre>

<p>
The regular expression uses a subpattern. It matches bookworm, bookmark,
bookkeeper, and book words.
</p>

<pre class="compact">
$ dart main.dart
book does match
bookshelf does not match
bookworm does match
bookcase does not match
bookish does not match
bookkeeper does match
booklet does not match
bookmark does match
</pre>





<h2>Dart RegExp allMatches</h2>

<p>
The <code>allMatches</code> function finds all matches of the given regular
exressions.
</p>

<div class="codehead">main.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
void main() {
  var text = 'The Sun was shining; I went for a walk.';

  var rx = RegExp(r"\w+");

  var found = rx.allMatches(text);
  print("There are ${found.length} words");

  found.forEach((match) {
    print("${match.group(0)} ${match.start}:${match.end}");
  });
}
</pre>

<p>
In the example, we find all words in the text.
</p>

<pre class="explanation">
var rx = RegExp(r"\w+");
</pre>

<p>
The regular expression matches all words; where a word is defined as a one or
more characters.
</p>

<pre class="explanation">
print("There are ${found.length} words");
</pre>

<p>
We print the number of words.
</p>

<pre class="explanation">
found.forEach((match) {
  print("${match.group(0)} ${match.start}:${match.end}");
});
</pre>

<p>
We iterate over all found matches and print them and their indexes.
</p>

<pre class="compact">
$ dart mains.dart
There are 9 words
The 0:3
Sun 4:7
was 8:11
shining 12:19
I 21:22
went 23:27
for 28:31
a 32:33
walk 34:38
</pre>


<h2>Dart regex capturing groups</h2>

<p>
Round brackets are used to create capturing groups. This allows us to apply a
quantifier to the entire group or to restrict alternation to a part of the
regular expression. 
</p>

<div class="codehead">main.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
void main() {
  var sites = ["webcode.me", "zetcode.com", "freebsd.org", "netbsd.org"];

  var rx = RegExp(r"(\w+)\.(\w+)");

  for (var site in sites) {
    var matches = rx.allMatches(site);
    matches.forEach((match) {
      print(match.group(0));
      print(match.group(1));
      print(match.group(2));
    });
    print('----------------------');
  }
}
</pre>

<p>
In the example, we divide the domain names into two parts by using groups. 
</p>

<pre class="explanation">
var rx = RegExp(r"(\w+)\.(\w+)");
</pre>

<p>
We define two groups with parentheses. The dot character is escaped because 
it is used in the literal meaning.
</p>

<pre class="explanation">
var matches = rx.allMatches(site);
</pre>

<p>
We find all matches with <code>allMatches</code>.
</p>

<pre class="explanation">
print(match.group(0));
print(match.group(1));
print(match.group(2));
</pre>

<p>
The groups are accessed via the <code>group</code> function. The default whole 
match is available with <code>match.group(0)</code>.
</p>








