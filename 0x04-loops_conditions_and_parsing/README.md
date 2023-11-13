<div id="top" class="main">
    <div class="pagenav">
      <div class="homebutton">
      <a href="index.php">Home</a> |
      </div>
      <a href="lc3_lts0070.php">Previous</a> | <a href="lc3_learning_the_shell.php#contents">Contents</a> | <a href="lc3_lts0090.php">Next</a>
    </div>
<h1>Expansion</h1>

<p>Each time we type a command line and press the enter key, bash performs
several processes upon the text before it carries out our command. We have seen
a couple of cases of how a simple character sequence, for example “*”, can have
a lot of meaning to the shell. The process that makes this happen is called
<i>expansion</i>. With expansion, we type something and it is expanded into
something else before the shell acts upon it. To demonstrate what we mean by
this, let's take a look at the <code class="user"><a href="lc3_man_pages/echoh.html">echo</a></code> command.  <code class="user">echo</code> is a shell builtin that performs a very simple task.
It prints out its text arguments on standard output: </p>

<div class="display">[me@linuxbox me]$ <code class="cmd">echo this is a test</code>
this is a test</div>

<p>That's pretty straightforward. Any argument passed to <code class="user">echo</code> gets displayed. Let's try
another example:</p>

<div class="display">[me@linuxbox me]$ <code class="cmd">echo *</code>
Desktop Documents ls-output.txt Music Pictures Public Templates Videos</div>

<p>So what just happened? Why didn't <code class="user">echo</code> print “*”?
As we recall from our work with wildcards, the “*” character means match any
characters in a filename, but what we didn't see in our original discussion was
how the shell does that. The simple answer is that the shell expands the “*”
into something else (in this instance, the names of the files in the current
working directory) before the <code class="user">echo</code> command is
executed. When the enter key is pressed, the shell automatically expands any
qualifying characters on the command line before the command is carried out, so
the <code class="user">echo</code> command never saw the “*”, only its expanded
result. Knowing this, we can see that <code class="user">echo</code> behaved as
expected.  </p> 

<h2>Pathname Expansion</h2>

<p>The mechanism by which wildcards work is called <i>pathname expansion</i>. If we try some
of the techniques that we employed in our earlier lessons, we will see that they are really
expansions. Given a home directory that looks like this:</p>

<div class="display">[me@linuxbox me]$ <code class="cmd">ls</code>
Desktop
ls-output.txt
Documents Music
Pictures
Public
Templates
Videos</div>

<p>we could carry out the following expansions:</p>

<div class="display">[me@linuxbox me]$ <code class="cmd">echo D*</code>
Desktop Documents</div>

<p>and:</p>

<div class="display">[me@linuxbox me]$ <code class="cmd">echo *s</code>
Documents Pictures Templates Videos</div>

<p>or even:</p>

<div class="display">[me@linuxbox me]$ <code class="cmd">echo [[:upper:]]*</code>
Desktop Documents Music Pictures Public Templates Videos</div>

<p>and looking beyond our home directory:</p>

<div class="display">[me@linuxbox me]$ <code class="cmd">echo /usr/*/share</code>
/usr/kerberos/share /usr/local/share</div>

<h2>Tilde Expansion</h2>

<p>As we recall from our introduction to the <code class="user">cd</code>
command, the tilde character (“~”) has a special meaning. When used at the
beginning of a word, it expands into the name of the home directory of the
named user, or if no user is named, the home directory of the current user:</p>

<div class="display">[me@linuxbox me]$ <code class="cmd">echo ~</code>
/home/me</div>

<p>If user “foo” has an account, then:</p>

<div class="display">[me@linuxbox me]$ <code class="cmd">echo ~foo</code>
/home/foo</div>


<h2>Arithmetic Expansion</h2>

<p>The shell allows arithmetic to be performed by expansion. This allow us to use the shell
prompt as a calculator:</p>

<div class="display">[me@linuxbox me]$ <code class="cmd">echo $((2 + 2))</code>
4</div>

<p>Arithmetic expansion uses the form:</p>

<pre>$((expression))</pre>

<p>where expression is an arithmetic expression consisting of values and arithmetic
operators.</p>

<p>Arithmetic expansion only supports integers (whole numbers, no decimals), but can
perform quite a number of different operations.</p>

<p>Spaces are not significant in arithmetic expressions and expressions may be nested. For
example, to multiply five squared by three:</p>

<div class="display">[me@linuxbox me]$ <code class="cmd">echo $(($((5**2)) * 3))</code>
75</div>

<p>Single parentheses may be used to group multiple subexpressions. With this technique,
we can rewrite the example above and get the same result using a single expansion
instead of two:</p>

<div class="display">[me@linuxbox me]$ <code class="cmd">echo $(((5**2) * 3))</code>
75</div>

<p>Here is an example using the division and remainder operators. Notice the effect of
integer division:</p>

<div class="display">[me@linuxbox me]$ <code class="cmd">echo Five divided by two equals $((5/2))</code>
Five divided by two equals 2
[me@linuxbox me]$ <code class="cmd">echo with $((5%2)) left over.</code>
with 1 left over.</div>

<h2>Brace Expansion</h2>

<p>Perhaps the strangest expansion is called <i>brace expansion</i>. With it, we can create
multiple text strings from a pattern containing braces. Here's an example:</p>

<div class="display">[me@linuxbox me]$ <code class="cmd">echo Front-{A,B,C}-Back</code>
Front-A-Back Front-B-Back Front-C-Back</div>

<p>Patterns to be brace expanded may contain a leading portion called a <i>preamble</i> and a
trailing portion called a <i>postscript</i>. The brace expression itself may contain either a
comma-separated list of strings, or a range of integers or single characters. The pattern
may not contain embedded whitespace. Here is an example using a range of integers:</p>

<div class="display">[me@linuxbox me]$ <code class="cmd">echo Number_{1..5}</code>
Number_1 Number_2 Number_3 Number_4 Number_5</div>

<p>A range of letters in reverse order:</p>

<div class="display">[me@linuxbox me]$ <code class="cmd">echo {Z..A}</code>
Z Y X W V U T S R Q P O N M L K J I H G F E D C B A</div>

<p>Brace expansions may be nested:</p>

<div class="display">[me@linuxbox me]$ <code class="cmd">echo a{A{1,2},B{3,4}}b</code>
aA1b aA2b aB3b aB4b</div>

<p>So what is this good for? The most common application is to make lists of files or
directories to be created. For example, if we were a photographer and had a large
collection of images we wanted to organize into years and months, the first thing we
might do is create a series of directories named in numeric “Year-Month” format. This
way, the directory names will sort in chronological order. we could type out a complete
list of directories, but that's a lot of work and it's error-prone too. Instead, we could do
this:</p>

<div class="display">[me@linuxbox me]$ <code class="cmd">mkdir Photos</code>
[me@linuxbox me]$ <code class="cmd">cd Photos</code>
[me@linuxbox Photos]$ <code class="cmd">mkdir {2017..2019}-{01..12}</code>
[me@linuxbox Photos]$ <code class="cmd">ls</code>
2017-01 2017-07 2018-01 2018-07 2019-01 2019-07
2017-02 2017-08 2018-02 2018-08 2019-02 2019-08
2017-03 2017-09 2018-03 2018-09 2019-03 2019-09
2017-04 2017-10 2018-04 2018-10 2019-04 2019-10
2017-05 2017-11 2018-05 2018-11 2019-05 2019-11
2017-06 2017-12 2018-06 2018-12 2019-06 2019-12</div>

<p>Pretty slick!</p>

<h2>Parameter Expansion</h2>

<p>We're only going to touch briefly on <i>parameter expansion</i> in this lesson, but we'll be
covering it more later. It's a feature that is more useful in shell scripts than directly
on the command line. Many of its capabilities have to do with the system's ability to
store small chunks of data and to give each chunk a name. Many such chunks, more
properly called <i>variables</i>, are available for our examination. For example, the variable
named “USER” contains our user name. To invoke parameter expansion and reveal the
contents of USER we would do this:</p>

<div class="display">[me@linuxbox me]$ <code class="cmd">echo $USER</code>
me</div>

<p>To see a list of available variables, try this:</p>

<div class="display">[me@linuxbox me]$ <code class="cmd">printenv | less</code></div>

<p>With other types of expansion, if we mistype a pattern, the
expansion will not take place and the echo command will simply display the mistyped
pattern. With parameter expansion, if we misspell the name of a variable, the expansion
will still take place, but will result in an empty string:</p>

<div class="display">[me@linuxbox me]$ <code class="cmd">echo $SUER</code>
[me@linuxbox ~]$</div>

<h2>Command Substitution</h2>

<p><i>Command substitution</i> allows us to use the output of a command as an expansion:</p>

<div class="display">[me@linuxbox me]$ <code class="cmd">echo $(ls)</code>
Desktop Documents ls-output.txt Music Pictures Public Templates Videos</div>

<p>A clever one goes something like this:</p>

<div class="display">[me@linuxbox me]$ <code class="cmd">ls -l $(which cp)</code>
-rwxr-xr-x 1 root root 71516 2007-12-05 08:58 /bin/cp</div>

<p>Here we passed the results of <code class="user">which cp</code> as an
argument to the <code class="user">ls</code> command, thereby getting the
listing of of the <code class="user">cp</code> program without having to know
its full pathname. We are not limited to just simple commands. Entire pipelines
can be used (only partial output shown):</p>

<div class="display">[me@linuxbox me]$ <code class="cmd">file $(ls /usr/bin/* | grep bin/zip)</code>
/usr/bin/bunzip2:
/usr/bin/zip:      ELF 32-bit LSB executable, Intel 80386, version 1 
(SYSV), dynamically linked (uses shared libs), for GNU/Linux 2.6.15, stripped
/usr/bin/zipcloak: ELF 32-bit LSB executable, Intel 80386, version 1
(SYSV), dynamically linked (uses shared libs), for GNU/Linux 2.6.15, stripped
/usr/bin/zipgrep:  POSIX shell script text executable
/usr/bin/zipinfo:  ELF 32-bit LSB executable, Intel 80386, version 1
(SYSV), dynamically linked (uses shared libs), for GNU/Linux 2.6.15, stripped
/usr/bin/zipnote:  ELF 32-bit LSB executable, Intel 80386, version 1
(SYSV), dynamically linked (uses shared libs), for GNU/Linux 2.6.15, stripped
/usr/bin/zipsplit: ELF 32-bit LSB executable, Intel 80386, version 1
(SYSV), dynamically linked (uses shared libs), for GNU/Linux 2.6.15, stripped</div>

<p>In this example, the results of the pipeline became the argument list of the file
command.
There is an alternate syntax for command substitution in older shell programs which is
also supported in <code class="user">bash</code>. It uses back-quotes instead of the dollar sign and parentheses:</p>

<div class="display">[me@linuxbox me]$ <code class="cmd">ls -l `which cp`</code>
-rwxr-xr-x 1 root root 71516 2007-12-05 08:58 /bin/cp</div>

<h2>Quoting</h2>

<p>Now that we've seen how many ways the shell can perform expansions, it's time to learn
how we can control it. Take for example:</p>

<div class="display">[me@linuxbox me]$ <code class="cmd">echo this is a&nbsp;&nbsp;&nbsp;&nbsp; test</code>
this is a test</div>

<p>or:</p>

<div class="display">[me@linuxbox me]$ <code class="cmd">[me@linuxbox ~]$ echo The total is $100.00</code>
The total is 00.00</div>

<p>In the first example, word-splitting by the shell removed extra whitespace
from the echo command's list of arguments. In the second example, parameter
expansion substituted an empty string for the value of “$1” because it was an
undefined variable. The shell provides a mechanism called <i>quoting</i> to
selectively suppress unwanted expansions.</p>

<h2>Double Quotes</h2>

<p>The first type of quoting we will look at is double quotes. If we place text inside double
quotes, all the special characters used by the shell lose their special meaning and are
treated as ordinary characters. The exceptions are “$”, “\” (backslash), and “`” (back-
quote). This means that word-splitting, pathname expansion, tilde expansion, and brace
expansion are suppressed, but parameter expansion, arithmetic expansion, and command
substitution are still carried out. Using double quotes, we can cope with filenames
containing embedded spaces. Imagine we were the unfortunate victim of a file called
<code>two words.txt</code>. If we tried to use this on the command line, word-splitting would
cause this to be treated as two separate arguments rather than the desired single argument:</p>

<div class="display">[me@linuxbox me]$ <code class="cmd">ls -l two words.txt</code>
ls: cannot access two: No such file or directory
ls: cannot access words.txt: No such file or directory</div>

<p>By using double quotes, we can stop the word-splitting and get the desired result; further, we
can even repair the damage:</p>

<div class="display">[me@linuxbox me]$ <code class="cmd">ls -l "two words.txt"</code>
-rw-rw-r-- 1 me me 18 2020-02-20 13:03 two words.txt
[me@linuxbox me]$ <code class="cmd">mv "two words.txt" two_words.txt</code></div>

<p>There! Now we don't have to keep typing those pesky double quotes.
Remember, parameter expansion, arithmetic expansion, and command substitution still
take place within double quotes:</p>

<div class="display">[me@linuxbox me]$ <code class="cmd">echo "$USER $((2+2)) $(cal)"</code>
me 4
February 2020
Su Mo Tu We Th Fr Sa
                1  2
 3  4  5  6  7  8  9
10 11 12 13 14 15 16
17 18 19 20 21 22 23
24 25 26 27 28 29</div>

<p>We should take a moment to look at the effect of double quotes on command substitution.
First let's look a little deeper at how word splitting works. In our earlier example, we saw
how word-splitting appears to remove extra spaces in our text:</p>

<div class="display">[me@linuxbox me]$ <code class="cmd">echo this is a &nbsp;&nbsp;&nbsp;&nbsp;test</code>
this is a test</div>

<p>By default, word-splitting looks for the presence of spaces, tabs, and newlines (linefeed
characters) and treats them as delimiters between words. This means that unquoted
spaces, tabs, and newlines are not considered to be part of the text. They only serve as
separators. Since they separate the words into different arguments, our example
command line contains a command followed by four distinct arguments. If we add
double quotes:</p>

<div class="display">[me@linuxbox me]$ <code class="cmd">echo "this is a &nbsp;&nbsp;&nbsp;&nbsp;test"</code>
this is a &nbsp;&nbsp;&nbsp;&nbsp;test</div>

<p>word-splitting is suppressed and the embedded spaces are not treated as delimiters, rather
they become part of the argument. Once the double quotes are added, our command line
contains a command followed by a single argument.
The fact that newlines are considered delimiters by the word-splitting mechanism causes
an interesting, albeit subtle, effect on command substitution. Consider the following:</p>

<div class="display">[me@linuxbox me]$ <code class="cmd">echo $(cal)</code>
February 2020 Su Mo Tu We Th Fr Sa 1 2 3 4 5 6 7 8 9 10 11 12 13 14
15 16 17 18 19 20 21 22 23 24 25 26 27 28 29
[me@linuxbox me]$ <code class="cmd"> echo "$(cal)"</code>
February 2020
Su Mo Tu We Th Fr Sa
                1  2
 3  4  5  6  7  8  9
10 11 12 13 14 15 16
17 18 19 20 21 22 23
24 25 26 27 28 29</div>

<p>In the first instance, the unquoted command substitution resulted in a command line
containing thirty-eight arguments. In the second, a command line with one argument that
includes the embedded spaces and newlines.</p>

<h2>Single Quotes</h2>

<p>When we need to suppress all expansions, we use single quotes. Here is a comparison of
unquoted, double quotes, and single quotes:</p>

<div class="display">[me@linuxbox me]$ <code class="cmd">echo text ~/*.txt {a,b} $(echo foo) $((2+2)) $USER</code>
text /home/me/ls-output.txt a b foo 4 me
[me@linuxbox me]$ <code class="cmd">echo "text ~/*.txt {a,b} $(echo foo) $((2+2)) $USER"</code>
text ~/*.txt {a,b} foo 4 me
[me@linuxbox me]$ <code class="cmd">echo 'text ~/*.txt {a,b} $(echo foo) $((2+2)) $USER'</code>
text ~/*.txt {a,b} $(echo foo) $((2+2)) $USER</div>

<p>As we can see, with each succeeding level of quoting, more and more of the expansions
are suppressed.</p>

<h2>Escaping Characters</h2>

<p>Sometimes we only want to quote a single character. To do this, we can precede a
character with a backslash, which in this context is called the <i>escape character</i>. Often
this is done inside double quotes to selectively prevent an expansion:</p>

<div class="display">[me@linuxbox me]$ <code class="cmd">echo "The balance for user $USER is: \$5.00"</code>
The balance for user me is: $5.00</div>

<p>It is also common to use escaping to eliminate the special meaning of a character in a
filename. For example, it is possible to use characters in filenames that normally have
special meaning to the shell. These would include “$”, “!”, “&amp;”, “ “, and others. To
include a special character in a filename we can to this:</p>

<div class="display">[me@linuxbox me]$ <code class="cmd">mv bad\&amp;filename good_filename</code></div>

<p>To allow a backslash character to appear, escape it by typing “\\”. Note that within single
quotes, the backslash loses its special meaning and is treated as an ordinary character.</p>

<h2>More Backslash Tricks</h2>

<p>If we look at the <code class="user">man</code> pages for any program
written by the <a href="http://www.gnu.org">GNU project</a>, we will see that
in addition to command line options consisting of a dash and a single letter,
there are also long option names that begin with two dashes.  For example, the
following are equivalent:</p>

<div class="codeexample">ls -r
ls --reverse</div>

<p>Why do they support both? The short form is for lazy typists on the command
line and the long form is mostly for scripts though some options may only be
available in long form.  Sometimes it is better to use a long option when the
option is obscure or we want to document more clearly what an option is. This
is especially useful when writing scripts where maximum readability is
desired, and besides, anytime we can save ourselves a trip to the man page is a
good thing.

</p><p>As we might suspect, using the long form options can make a single command
line very long.  To combat this problem, we can use a backslash to get the
shell to ignore a newline character like this:</p>

<div class="codeexample">ls -l \
   --reverse \
   --human-readable \
   --full-time</div>

<p>Using the backslash in this way allows us to embed newlines in our command.
Note that for this trick to work, the newline must be typed immediately after
the backslash. If we put a space after the backslash, the space will be
ignored, not the newline. Backslashes are also used to insert special
characters into our text. These are called <i>backslash escape characters</i>.
Here are the common ones: </p>

<div style="overflow-x: auto"><table>
  <tbody><tr>
    <th><strong>Escape Character</strong></th>

    <th><strong>Name</strong></th>

    <th><strong>Possible Uses</strong></th>
  </tr>

  <tr>
    <td>\n</td>

    <td>newline</td>

    <td>Adding blank lines to text</td>
  </tr>

  <tr>
    <td>\t</td>

    <td>tab</td>

    <td>Inserting horizontal tabs to text</td>
  </tr>

  <tr>
    <td>\a</td>

    <td>alert</td>

    <td>Makes our terminal beep</td>
  </tr>

  <tr>
    <td>\\</td>

    <td>backslash</td>

    <td>Inserts a backslash</td>
  </tr>

  <tr>
    <td>\f</td>

    <td>formfeed</td>

    <td>Sending this to our printer ejects the page</td>
  </tr>
</tbody></table></div>

<p>The use of the backslash escape characters is very common. This idea first
appeared in the C programming language. Today, the shell, C++, Perl, python,
awk, tcl, and many other programming languages use this concept. Using the
<code class="user">echo</code> command with the -e option will allow us to
demonstrate:</p>

<div class="display">[me@linuxbox me]$ <code class="cmd">echo -e "Inserting several blank lines\n\n\n"</code>
Inserting several blank lines

[me@linuxbox me]$ <code class="cmd">echo -e "Words\tseparated\tby\thorizontal\ttabs."</code>
Words separated&nbsp;&nbsp;&nbsp;by&nbsp;&nbsp;horizontal&nbsp;&nbsp;tabs
[me@linuxbox me]$ <code class="cmd">echo -e "\aMy computer went \"beep\"."</code>
My computer went "beep".
[me@linuxbox me]$ <code class="cmd">echo -e "DEL C:\\WIN2K\\LEGACY_OS.EXE"</code>
DEL C:\WIN2K\LEGACY_OS.EXE</div>
</div>
<span id="Shell-Arithmetic-1"></span><h3 class="section">6.5 Shell Arithmetic</h3>
<span id="index-arithmetic_002c-shell"></span>
<span id="index-shell-arithmetic"></span>
<span id="index-expressions_002c-arithmetic"></span>
<span id="index-evaluation_002c-arithmetic"></span>
<span id="index-arithmetic-evaluation"></span>

<p>The shell allows arithmetic expressions to be evaluated, as one of
the shell expansions or by using the <code>((</code> compound command, the
<code>let</code> builtin, or the <samp>-i</samp> option to the <code>declare</code> builtin.
</p>
<p>Evaluation is done in fixed-width integers with no check for overflow,
though division by 0 is trapped and flagged as an error.
The operators and their precedence, associativity, and values
are the same as in the C language.
The following list of operators is grouped into levels of
equal-precedence operators.
The levels are listed in order of decreasing precedence. 
</p>
<dl compact="compact">
<dt><span><code><var>id</var>++ <var>id</var>--</code></span></dt>
<dd><p>variable post-increment and post-decrement 
</p>
</dd>
<dt><span><code>++<var>id</var> --<var>id</var></code></span></dt>
<dd><p>variable pre-increment and pre-decrement
</p>
</dd>
<dt><span><code>- +</code></span></dt>
<dd><p>unary minus and plus
</p>
</dd>
<dt><span><code>! ~</code></span></dt>
<dd><p>logical and bitwise negation
</p>
</dd>
<dt><span><code>**</code></span></dt>
<dd><p>exponentiation
</p>
</dd>
<dt><span><code>* / %</code></span></dt>
<dd><p>multiplication, division, remainder
</p>
</dd>
<dt><span><code>+ -</code></span></dt>
<dd><p>addition, subtraction
</p>
</dd>
<dt><span><code>&lt;&lt; &gt;&gt;</code></span></dt>
<dd><p>left and right bitwise shifts
</p>
</dd>
<dt><span><code>&lt;= &gt;= &lt; &gt;</code></span></dt>
<dd><p>comparison
</p>
</dd>
<dt><span><code>== !=</code></span></dt>
<dd><p>equality and inequality
</p>
</dd>
<dt><span><code>&amp;</code></span></dt>
<dd><p>bitwise AND
</p>
</dd>
<dt><span><code>^</code></span></dt>
<dd><p>bitwise exclusive OR
</p>
</dd>
<dt><span><code>|</code></span></dt>
<dd><p>bitwise OR
</p>
</dd>
<dt><span><code>&amp;&amp;</code></span></dt>
<dd><p>logical AND
</p>
</dd>
<dt><span><code>||</code></span></dt>
<dd><p>logical OR
</p>
</dd>
<dt><span><code>expr ? expr : expr</code></span></dt>
<dd><p>conditional operator
</p>
</dd>
<dt><span><code>= *= /= %= += -= &lt;&lt;= &gt;&gt;= &amp;= ^= |=</code></span></dt>
<dd><p>assignment
</p>
</dd>
<dt><span><code>expr1 , expr2</code></span></dt>
<dd><p>comma
</p></dd>
</dl>

<p>Shell variables are allowed as operands; parameter expansion is
performed before the expression is evaluated. 
Within an expression, shell variables may also be referenced by name
without using the parameter expansion syntax.
A shell variable that is null or unset evaluates to 0 when referenced
by name without using the parameter expansion syntax.
The value of a variable is evaluated as an arithmetic expression
when it is referenced, or when a variable which has been given the  
<code>integer</code> attribute using ‘<samp>declare -i</samp>’ is assigned a value.
A null value evaluates to 0.
A shell variable need not have its <code>integer</code> attribute turned on
to be used in an expression.
</p>
<p>Integer constants follow the C language definition, without suffixes or
character constants.
Constants with a leading 0 are interpreted as octal numbers.
A leading ‘<samp>0x</samp>’ or ‘<samp>0X</samp>’ denotes hexadecimal.  Otherwise,
numbers take the form [<var>base</var><code>#</code>]<var>n</var>, where the optional <var>base</var>
is a decimal number between 2 and 64 representing the arithmetic
base, and <var>n</var> is a number in that base.
If <var>base</var><code>#</code> is omitted, then base 10 is used.
When specifying <var>n</var>,
if a non-digit is required,
the digits greater than 9 are represented by the lowercase letters,
the uppercase letters, ‘<samp>@</samp>’, and ‘<samp>_</samp>’, in that order.
If <var>base</var> is less than or equal to 36, lowercase and uppercase
letters may be used interchangeably to represent numbers between 10
and 35.
</p>
<p>Operators are evaluated in order of precedence.  Sub-expressions in
parentheses are evaluated first and may override the precedence
rules above.
</p>
<div class="sect1"><h1 class="sect1"><a name="sect_03_02"></a>3.2. Variables</h1><div class="sect2"><h2 class="sect2"><a name="sect_03_02_01"></a>3.2.1. Types of variables</h2><p>As seen in the examples above, shell variables are in uppercase
characters by convention.  Bash keeps a list of two types of variables:</p><div class="sect3"><h3 class="sect3"><a name="sect_03_02_01_01"></a>3.2.1.1. Global variables</h3><p>Global variables or environment variables are available in all shells.  The <b class="command">env</b> or <b class="command">printenv</b> commands can be used to display environment variables.  These programs come with the <em>sh-utils</em> package.</p><p>Below is a typical output:</p><table border="0" bgcolor="#E0E0E0" width="100%"><tbody><tr><td><font color="#000000"><pre class="screen">
<tt class="prompt">franky ~&gt;</tt> <b class="command">printenv</b>
CC=gcc
CDPATH=.:~:/usr/local:/usr:/
CFLAGS=-O2 -fomit-frame-pointer
COLORTERM=gnome-terminal
CXXFLAGS=-O2 -fomit-frame-pointer
DISPLAY=:0
DOMAIN=hq.garrels.be
e=
TOR=vi
FCEDIT=vi
FIGNORE=.o:~
G_BROKEN_FILENAMES=1
GDK_USE_XFT=1
GDMSESSION=Default
GNOME_DESKTOP_SESSION_ID=Default
GTK_RC_FILES=/etc/gtk/gtkrc:/nethome/franky/.gtkrc-1.2-gnome2
GWMCOLOR=darkgreen
GWMTERM=xterm
HISTFILESIZE=5000
history_control=ignoredups
HISTSIZE=2000
HOME=/nethome/franky
HOSTNAME=octarine.hq.garrels.be
INPUTRC=/etc/inputrc
IRCNAME=franky
JAVA_HOME=/usr/java/j2sdk1.4.0
LANG=en_US
LDFLAGS=-s
LD_LIBRARY_PATH=/usr/lib/mozilla:/usr/lib/mozilla/plugins
LESSCHARSET=latin1
LESS=-edfMQ
LESSOPEN=|/usr/bin/lesspipe.sh %s
LEX=flex
LOCAL_MACHINE=octarine
LOGNAME=franky
LS_COLORS=no=00:fi=00:di=01;34:ln=01;36:pi=40;33:so=01;35:bd=40;33;01:cd=40;33;01:or=01;05;37;41:mi=01;05;37;41:ex=01;32:*.cmd=01;32:*.exe=01;32:*.com=01;32:*.btm=01;32:*.bat=01;32:*.sh=01;32:*.csh=01;32:*.tar=01;31:*.tgz=01;31:*.arj=01;31:*.taz=01;31:*.lzh=01;31:*.zip=01;31:*.z=01;31:*.Z=01;31:*.gz=01;31:*.bz2=01;31:*.bz=01;31:*.tz=01;31:*.rpm=01;31:*.cpio=01;31:*.jpg=01;35:*.gif=01;35:*.bmp=01;35:*.xbm=01;35:*.xpm=01;35:*.png=01;35:*.tif=01;35:
MACHINES=octarine
MAILCHECK=60
MAIL=/var/mail/franky
MANPATH=/usr/man:/usr/share/man/:/usr/local/man:/usr/X11R6/man
MEAN_MACHINES=octarine
MOZ_DIST_BIN=/usr/lib/mozilla
MOZILLA_FIVE_HOME=/usr/lib/mozilla
MOZ_PROGRAM=/usr/lib/mozilla/mozilla-bin
MTOOLS_FAT_COMPATIBILITY=1
MYMALLOC=0
NNTPPORT=119
NNTPSERVER=news
NPX_PLUGIN_PATH=/plugin/ns4plugin/:/usr/lib/netscape/plugins
OLDPWD=/nethome/franky
OS=Linux
PAGER=less
PATH=/nethome/franky/bin.Linux:/nethome/franky/bin:/usr/local/bin:/usr/local/sbin:/usr/X11R6/bin:/usr/bin:/usr/sbin:/bin:/sbin:.
PS1=\[\033[1;44m\]franky is in \w\[\033[0m\]
PS2=More input&gt;
PWD=/nethome/franky
SESSION_MANAGER=local/octarine.hq.garrels.be:/tmp/.ICE-unix/22106
SHELL=/bin/bash
SHELL_LOGIN=--login
SHLVL=2
SSH_AGENT_PID=22161
SSH_ASKPASS=/usr/libexec/openssh/gnome-ssh-askpass
SSH_AUTH_SOCK=/tmp/ssh-XXmhQ4fC/agent.22106
START_WM=twm
TERM=xterm
TYPE=type
USERNAME=franky
USER=franky
_=/usr/bin/printenv
VISUAL=vi
WINDOWID=20971661
XAPPLRESDIR=/nethome/franky/app-defaults
XAUTHORITY=/nethome/franky/.Xauthority
XENVIRONMENT=/nethome/franky/.Xdefaults
XFILESEARCHPATH=/usr/X11R6/lib/X11/%L/%T/%N%C%S:/usr/X11R6/lib/X11/%l/%T/%N%C%S:/usr/X11R6/lib/X11/%T/%N%C%S:/usr/X11R6/lib/X11/%L/%T/%N%S:/usr/X11R6/lib/X11/%l/%T/%N%S:/usr/X11R6/lib/X11/%T/%N%S
XKEYSYMDB=/usr/X11R6/lib/X11/XKeysymDB
XMODIFIERS=@im=none
XTERMID=
XWINHOME=/usr/X11R6
X=X11R6
YACC=bison -y
</pre></font></td></tr></tbody></table></div><div class="sect3"><h3 class="sect3"><a name="sect_03_02_01_02"></a>3.2.1.2. Local variables</h3><p>Local variables are only available in the current shell.  Using the <b class="command">set</b> built-in command without any options will display a list of all variables (including environment variables) and functions.  The output will be sorted according to the current locale and displayed in a reusable format.</p><p>Below is a diff file made by comparing <b class="command">printenv</b> and <b class="command">set</b> output, after leaving out the functions which are also displayed by the <b class="command">set</b> command:</p><table border="0" bgcolor="#E0E0E0" width="100%"><tbody><tr><td><font color="#000000"><pre class="screen">
<tt class="prompt">franky ~&gt;</tt> <b class="command">diff <tt class="filename">set.sorted</tt> <tt class="filename">printenv.sorted</tt></b> | <b class="command">grep <tt class="parameter"><i>"&lt;"</i></tt></b> | <b class="command">awk <tt class="parameter"><i>'{ print $2 }'</i></tt></b>
BASE=/nethome/franky/.Shell/hq.garrels.be/octarine.aliases
BASH=/bin/bash
BASH_VERSINFO=([0]="2"
BASH_VERSION='2.05b.0(1)-release'
COLUMNS=80
DIRSTACK=()
DO_FORTUNE=
EUID=504
GROUPS=()
HERE=/home/franky
HISTFILE=/nethome/franky/.bash_history
HOSTTYPE=i686
IFS=$'
LINES=24
MACHTYPE=i686-pc-linux-gnu
OPTERR=1
OPTIND=1
OSTYPE=linux-gnu
PIPESTATUS=([0]="0")
PPID=10099
PS4='+
PWD_REAL='pwd
SHELLOPTS=braceexpand:emacs:hashall:histexpand:history:interactive-comments:monitor
THERE=/home/franky
UID=504
</pre></font></td></tr></tbody></table><div class="note"><p></p><table class="note" width="100%" border="0"><tbody><tr><td width="25" align="CENTER" valign="TOP"><img src="../images/note.gif" hspace="5" alt="Note"></td><th align="LEFT" valign="CENTER"><b>Awk</b></th></tr><tr><td>&nbsp;</td><td align="LEFT" valign="TOP"><p>the GNU Awk programming language is explained in <a href="chap_06.html">Chapter 6</a>.</p></td></tr></tbody></table></div></div><div class="sect3"><h3 class="sect3"><a name="sect_03_02_01_03"></a>3.2.1.3. Variables by content</h3><p>Apart from dividing variables in local and global variables, we can also divide them in categories according to the sort of content the variable contains.  In this respect, variables come in 4 types:</p><p></p><ul><li><p>String variables</p></li><li><p>Integer variables</p></li><li><p>Constant variables</p></li><li><p>Array variables</p></li></ul><p>We'll discuss these types in <a href="chap_10.html">Chapter 10</a>.  For now, we will work with integer and string values for our variables.</p></div></div><div class="sect2"><h2 class="sect2"><a name="sect_03_02_02"></a>3.2.2. Creating variables</h2><p>Variables are case sensitive and capitalized by default.  Giving local variables a lowercase name is a convention which is sometimes applied.  However, you are free to use the names you want or to mix cases.  Variables can also contain digits, but a name starting with a digit is not allowed:</p><table border="0" bgcolor="#E0E0E0" width="100%"><tbody><tr><td><font color="#000000"><pre class="screen">
<tt class="prompt">prompt&gt;</tt> <b class="command">export <tt class="varname">1number</tt>=<tt class="parameter"><i>1</i></tt></b>
bash: export: `1number=1': not a valid identifier
</pre></font></td></tr></tbody></table><p>To set a variable in the shell, use</p><p><b class="command"><tt class="varname">VARNAME</tt>="value"</b> </p><p>Putting spaces around the equal sign will cause errors.  It is a good habit to quote content strings when assigning values to variables: this will reduce the chance that you make errors.</p><p>Some examples using upper and lower cases, numbers and spaces:</p><table border="0" bgcolor="#E0E0E0" width="100%"><tbody><tr><td><font color="#000000"><pre class="screen">
<tt class="prompt">franky ~&gt;</tt> <b class="command"><tt class="varname">MYVAR1</tt>=<tt class="parameter"><i>"2"</i></tt></b>

<tt class="prompt">franky ~&gt;</tt> <b class="command">echo <tt class="varname">$MYVAR1</tt></b>
2

<tt class="prompt">franky ~&gt;</tt> <b class="command"><tt class="varname">first_name</tt>=<tt class="parameter"><i>"Franky"</i></tt></b>

<tt class="prompt">franky ~&gt;</tt> <b class="command">echo <tt class="varname">$first_name</tt></b>
Franky

<tt class="prompt">franky ~&gt;</tt> <b class="command"><tt class="varname">full_name</tt>=<tt class="parameter"><i>"Franky M. Singh"</i></tt></b>

<tt class="prompt">franky ~&gt;</tt> <b class="command">echo <tt class="varname">$full_name</tt></b>
Franky M. Singh

<tt class="prompt">franky ~&gt;</tt> <b class="command"><tt class="varname">MYVAR-2</tt>=<tt class="parameter"><i>"2"</i></tt></b>
bash: MYVAR-2=2: command not found

<tt class="prompt">franky ~&gt;</tt> <b class="command"><tt class="varname">MYVAR1</tt> =<tt class="parameter"><i>"2"</i></tt></b>
bash: MYVAR1: command not found

<tt class="prompt">franky ~&gt;</tt> <b class="command"><tt class="varname">MYVAR1</tt>= <tt class="parameter"><i>"2"</i></tt></b>
bash: 2: command not found

<tt class="prompt">franky ~&gt;</tt> <b class="command">unset <tt class="varname">MYVAR1</tt> <tt class="varname">first_name</tt> <tt class="varname">full_name</tt></b>

<tt class="prompt">franky ~&gt;</tt> <b class="command">echo <tt class="varname">$MYVAR1</tt> <tt class="varname">$first_name</tt> <tt class="varname">$full_name</tt></b>
&lt;--no output--&gt;

<tt class="prompt">franky ~&gt;</tt>
</pre></font></td></tr></tbody></table></div><div class="sect2"><h2 class="sect2"><a name="sect_03_02_03"></a>3.2.3. Exporting variables</h2><p>A variable created like the ones in the example above is only available to the current shell.  It is a local variable: child processes of the current shell will not be aware of this variable.  In order to pass variables to a subshell, we need to <em>export</em> them using the <b class="command">export</b> built-in command.  Variables that are exported are referred to as environment variables.  Setting and exporting is usually done in one step:</p><p><b class="command">export <tt class="varname">VARNAME</tt>="<tt class="parameter"><i>value</i></tt>"</b> </p><p>A subshell can change variables it inherited from the parent, but the changes made by the child don't affect the parent.  This is demonstrated in the example:</p><table border="0" bgcolor="#E0E0E0" width="100%"><tbody><tr><td><font color="#000000"><pre class="screen">
<tt class="prompt">franky ~&gt;</tt> <b class="command"><tt class="varname">full_name</tt>=<tt class="parameter"><i>"Franky M. Singh"</i></tt></b>

<tt class="prompt">franky ~&gt;</tt> <b class="command">bash</b>

<tt class="prompt">franky ~&gt;</tt> <b class="command">echo <tt class="varname">$full_name</tt></b>


<tt class="prompt">franky ~&gt;</tt> <b class="command">exit</b>

<tt class="prompt">franky ~&gt;</tt> <b class="command">export <tt class="varname">full_name</tt></b>

<tt class="prompt">franky ~&gt;</tt> <b class="command">bash</b>

<tt class="prompt">franky ~&gt;</tt> <b class="command">echo <tt class="varname">$full_name</tt></b>
Franky M. Singh

<tt class="prompt">franky ~&gt;</tt> <b class="command">export <tt class="varname">full_name</tt>=<tt class="parameter"><i>"Charles the Great"</i></tt></b>

<tt class="prompt">franky ~&gt;</tt> <b class="command">echo <tt class="varname">$full_name</tt></b>
Charles the Great

<tt class="prompt">franky ~&gt;</tt> <b class="command">exit</b>

<tt class="prompt">franky ~&gt;</tt> <b class="command">echo <tt class="varname">$full_name</tt></b>
Franky M. Singh

<tt class="prompt">franky ~&gt;</tt>
</pre></font></td></tr></tbody></table><p>When first trying to read the value of <tt class="varname">full_name</tt> in a subshell, it is not there (<b class="command">echo</b> shows a null string).  The subshell quits, and <tt class="varname">full_name</tt> is exported in the parent - a variable can be exported after it has been assigned a value.  Then a new subshell is started, in which the variable exported from the parent is visible.  The variable is changed to hold another name, but the value for this variable in the parent stays the same.</p></div><div class="sect2"><h2 class="sect2"><a name="sect_03_02_04"></a>3.2.4. Reserved variables</h2><div class="sect3"><h3 class="sect3"><a name="sect_03_02_04_01"></a>3.2.4.1. Bourne shell reserved variables</h3><p>Bash uses certain shell variables in the same way as the Bourne shell.  In some cases, Bash assigns a default value to the variable.  The table below gives an overview of these plain shell variables:</p><div class="table"><a name="table_03_01"></a><p><b>Table 3-1. Reserved Bourne shell variables</b></p><table border="1" class="CALSTABLE"><thead><tr><th align="LEFT" valign="MIDDLE">Variable name</th><th align="LEFT" valign="MIDDLE">Definition</th></tr></thead><tbody><tr><td align="LEFT" valign="MIDDLE">CDPATH</td><td align="LEFT" valign="MIDDLE">A colon-separated list of directories used as a search path for the <b class="command">cd</b> built-in command.</td></tr><tr><td align="LEFT" valign="MIDDLE">HOME</td><td align="LEFT" valign="MIDDLE">The current user's home directory; the default for the <b class="command">cd</b> built-in.  The value of this variable is also used by tilde expansion.</td></tr><tr><td align="LEFT" valign="MIDDLE">IFS</td><td align="LEFT" valign="MIDDLE">A list of characters that separate fields; used when the shell splits words as part of expansion.</td></tr><tr><td align="LEFT" valign="MIDDLE">MAIL</td><td align="LEFT" valign="MIDDLE">If this parameter is set to a file name and the <tt class="varname">MAILPATH</tt> variable is not set, Bash informs the user of the arrival of mail in the specified file.</td></tr><tr><td align="LEFT" valign="MIDDLE">MAILPATH</td><td align="LEFT" valign="MIDDLE">A colon-separated list of file names which the shell periodically checks for new mail.</td></tr><tr><td align="LEFT" valign="MIDDLE">OPTARG</td><td align="LEFT" valign="MIDDLE">The value of the last option argument processed by the <b class="command">getopts</b> built-in.</td></tr><tr><td align="LEFT" valign="MIDDLE">OPTIND</td><td align="LEFT" valign="MIDDLE">The index of the last option argument processed by the <b class="command">getopts</b> built-in.</td></tr><tr><td align="LEFT" valign="MIDDLE">PATH</td><td align="LEFT" valign="MIDDLE">A colon-separated list of directories in which the shell looks for commands.</td></tr><tr><td align="LEFT" valign="MIDDLE">PS1</td><td align="LEFT" valign="MIDDLE">The primary prompt string.  The default value is <span class="QUOTE">"'\s-\v\$ '"</span>.  </td></tr><tr><td align="LEFT" valign="MIDDLE">PS2</td><td align="LEFT" valign="MIDDLE">The secondary prompt string.  The default value is <span class="QUOTE">"'&gt; '"</span>.</td></tr></tbody></table></div></div><div class="sect3"><h3 class="sect3"><a name="sect_03_02_04_02"></a>3.2.4.2. Bash reserved variables</h3><p>These variables are set or used by Bash, but other shells do not normally treat them specially.</p><div class="table"><a name="table_03_02"></a><p><b>Table 3-2. Reserved Bash variables</b></p><table border="1" class="CALSTABLE"><thead><tr><th align="LEFT" valign="MIDDLE">Variable name</th><th align="LEFT" valign="MIDDLE">Definition</th></tr></thead><tbody><tr><td align="LEFT" valign="MIDDLE">auto_resume</td><td align="LEFT" valign="MIDDLE">This variable controls how the shell interacts with the user and job control.</td></tr><tr><td align="LEFT" valign="MIDDLE">BASH</td><td align="LEFT" valign="MIDDLE">The full pathname used to execute the current instance of Bash.</td></tr><tr><td align="LEFT" valign="MIDDLE">BASH_ENV</td><td align="LEFT" valign="MIDDLE">If this variable is set when Bash is invoked to execute a shell script, its value is expanded and used as the name of a startup file to read before executing the script.</td></tr><tr><td align="LEFT" valign="MIDDLE">BASH_VERSION</td><td align="LEFT" valign="MIDDLE">The version number of the current instance of Bash.</td></tr><tr><td align="LEFT" valign="MIDDLE">BASH_VERSINFO</td><td align="LEFT" valign="MIDDLE">A read-only array variable whose members hold version information for this instance of Bash.</td></tr><tr><td align="LEFT" valign="MIDDLE">COLUMNS</td><td align="LEFT" valign="MIDDLE">Used by the <b class="command">select</b> built-in to determine the terminal width when printing selection lists.  Automatically set upon receipt of a <em>SIGWINCH</em> signal.</td></tr><tr><td align="LEFT" valign="MIDDLE">COMP_CWORD</td><td align="LEFT" valign="MIDDLE">An index into <tt class="varname">${COMP_WORDS}</tt> of the word containing the current cursor position.</td></tr><tr><td align="LEFT" valign="MIDDLE">COMP_LINE</td><td align="LEFT" valign="MIDDLE">The current command line.</td></tr><tr><td align="LEFT" valign="MIDDLE">COMP_POINT</td><td align="LEFT" valign="MIDDLE">The index of the current cursor position relative to the beginning of the current command.</td></tr><tr><td align="LEFT" valign="MIDDLE">COMP_WORDS</td><td align="LEFT" valign="MIDDLE">An array variable consisting of the individual words in the current command line.</td></tr><tr><td align="LEFT" valign="MIDDLE">COMPREPLY</td><td align="LEFT" valign="MIDDLE">An array variable from which Bash reads the possible completions generated by a shell function invoked by the programmable completion facility.</td></tr><tr><td align="LEFT" valign="MIDDLE">DIRSTACK</td><td align="LEFT" valign="MIDDLE">An array variable containing the current contents of the directory stack.</td></tr><tr><td align="LEFT" valign="MIDDLE">EUID</td><td align="LEFT" valign="MIDDLE">The numeric effective user ID of the current user.</td></tr><tr><td align="LEFT" valign="MIDDLE">FCEDIT</td><td align="LEFT" valign="MIDDLE">The editor used as a default by the <tt class="option">-e</tt> option to the <b class="command">fc</b> built-in command.</td></tr><tr><td align="LEFT" valign="MIDDLE">FIGNORE</td><td align="LEFT" valign="MIDDLE">A colon-separated list of suffixes to ignore when performing file name completion.</td></tr><tr><td align="LEFT" valign="MIDDLE">FUNCNAME</td><td align="LEFT" valign="MIDDLE">The name of any currently-executing shell function.</td></tr><tr><td align="LEFT" valign="MIDDLE">GLOBIGNORE</td><td align="LEFT" valign="MIDDLE">A colon-separated list of patterns defining the set of file names to be ignored by file name expansion.</td></tr><tr><td align="LEFT" valign="MIDDLE">GROUPS</td><td align="LEFT" valign="MIDDLE">An array variable containing the list of groups of which the current user is a member.</td></tr><tr><td align="LEFT" valign="MIDDLE">histchars</td><td align="LEFT" valign="MIDDLE">Up to three characters which control history expansion, quick substitution, and <em>tokenization</em>.</td></tr><tr><td align="LEFT" valign="MIDDLE">HISTCMD</td><td align="LEFT" valign="MIDDLE">The history number, or index in the history list, of the current command.</td></tr><tr><td align="LEFT" valign="MIDDLE">HISTCONTROL</td><td align="LEFT" valign="MIDDLE">Defines whether a command is added to the history file.</td></tr><tr><td align="LEFT" valign="MIDDLE">HISTFILE</td><td align="LEFT" valign="MIDDLE">The name of the file to which the command history is saved.  The default value is <tt class="filename">~/.bash_history</tt>.</td></tr><tr><td align="LEFT" valign="MIDDLE">HISTFILESIZE</td><td align="LEFT" valign="MIDDLE">The maximum number of lines contained in the history file, defaults to 500.</td></tr><tr><td align="LEFT" valign="MIDDLE">HISTIGNORE</td><td align="LEFT" valign="MIDDLE">A colon-separated list of patterns used to decide which command lines should be saved in the history list.</td></tr><tr><td align="LEFT" valign="MIDDLE">HISTSIZE</td><td align="LEFT" valign="MIDDLE">The maximum number of commands to remember on the history list, default is 500.</td></tr><tr><td align="LEFT" valign="MIDDLE">HOSTFILE</td><td align="LEFT" valign="MIDDLE">Contains the name of a file in the same format as <tt class="filename">/etc/hosts</tt> that should be read when the shell needs to complete a hostname.</td></tr><tr><td align="LEFT" valign="MIDDLE">HOSTNAME</td><td align="LEFT" valign="MIDDLE">The name of the current host.</td></tr><tr><td align="LEFT" valign="MIDDLE">HOSTTYPE</td><td align="LEFT" valign="MIDDLE">A string describing the machine Bash is running on.</td></tr><tr><td align="LEFT" valign="MIDDLE">IGNOREEOF</td><td align="LEFT" valign="MIDDLE">Controls the action of the shell on receipt of an <em>EOF</em> character as the sole input.</td></tr><tr><td align="LEFT" valign="MIDDLE">INPUTRC</td><td align="LEFT" valign="MIDDLE">The name of the Readline initialization file, overriding the default <tt class="filename">/etc/inputrc</tt>.</td></tr><tr><td align="LEFT" valign="MIDDLE">LANG</td><td align="LEFT" valign="MIDDLE">Used to determine the locale category for any category not specifically selected with a variable starting with <tt class="varname">LC_</tt>.</td></tr><tr><td align="LEFT" valign="MIDDLE">LC_ALL</td><td align="LEFT" valign="MIDDLE">This variable overrides the value of <tt class="varname">LANG</tt> and any other <tt class="varname">LC_</tt> variable specifying a locale category.</td></tr><tr><td align="LEFT" valign="MIDDLE">LC_COLLATE</td><td align="LEFT" valign="MIDDLE">This variable determines the collation order used when sorting the results of file name expansion, and determines the behavior of range expressions, equivalence classes, and collating sequences within file name expansion and pattern matching.</td></tr><tr><td align="LEFT" valign="MIDDLE">LC_CTYPE</td><td align="LEFT" valign="MIDDLE">This variable determines the interpretation of characters and the behavior of character classes within file name expansion and pattern matching.</td></tr><tr><td align="LEFT" valign="MIDDLE">LC_MESSAGES</td><td align="LEFT" valign="MIDDLE">This variable determines the locale used to translate double-quoted strings preceded by a <span class="QUOTE">"$"</span> sign.</td></tr><tr><td align="LEFT" valign="MIDDLE">LC_NUMERIC</td><td align="LEFT" valign="MIDDLE">This variable determines the locale category used for number formatting.</td></tr><tr><td align="LEFT" valign="MIDDLE">LINENO</td><td align="LEFT" valign="MIDDLE">The line number in the script or shell function currently executing.</td></tr><tr><td align="LEFT" valign="MIDDLE">LINES</td><td align="LEFT" valign="MIDDLE">Used by the <b class="command">select</b> built-in to determine the column length for printing selection lists.</td></tr><tr><td align="LEFT" valign="MIDDLE">MACHTYPE</td><td align="LEFT" valign="MIDDLE">A string that fully describes the system type on which Bash is executing, in the standard GNU CPU-COMPANY-SYSTEM format.</td></tr><tr><td align="LEFT" valign="MIDDLE">MAILCHECK</td><td align="LEFT" valign="MIDDLE">How often (in seconds) that the shell should check for mail in the files specified in the <tt class="varname">MAILPATH</tt> or <tt class="varname">MAIL</tt> variables.</td></tr><tr><td align="LEFT" valign="MIDDLE">OLDPWD</td><td align="LEFT" valign="MIDDLE">The previous working directory as set by the <b class="command">cd</b> built-in.</td></tr><tr><td align="LEFT" valign="MIDDLE">OPTERR</td><td align="LEFT" valign="MIDDLE">If set to the value 1, Bash displays error messages generated by the <b class="command">getopts</b> built-in.</td></tr><tr><td align="LEFT" valign="MIDDLE">OSTYPE</td><td align="LEFT" valign="MIDDLE">A string describing the operating system Bash is running on.</td></tr><tr><td align="LEFT" valign="MIDDLE">PIPESTATUS</td><td align="LEFT" valign="MIDDLE">An array variable containing a list of exit status values from the processes in the most recently executed foreground pipeline (which may contain only a single command).</td></tr><tr><td align="LEFT" valign="MIDDLE">POSIXLY_CORRECT</td><td align="LEFT" valign="MIDDLE">If this variable is in the environment when <b class="command">bash</b> starts, the shell enters POSIX mode.</td></tr><tr><td align="LEFT" valign="MIDDLE">PPID</td><td align="LEFT" valign="MIDDLE">The process ID of the shell's parent process.</td></tr><tr><td align="LEFT" valign="MIDDLE">PROMPT_COMMAND</td><td align="LEFT" valign="MIDDLE">If set, the value is interpreted as a command to execute before the printing of each primary prompt (<tt class="varname">PS1</tt>).</td></tr><tr><td align="LEFT" valign="MIDDLE">PS3</td><td align="LEFT" valign="MIDDLE">The value of this variable is used as the prompt for the <b class="command">select</b> command.  Defaults to <span class="QUOTE">"'#? '"</span></td></tr><tr><td align="LEFT" valign="MIDDLE">PS4</td><td align="LEFT" valign="MIDDLE">The value is the prompt printed before the command line is echoed when the <tt class="option">-x</tt> option is set; defaults to <span class="QUOTE">"'+ '"</span>.</td></tr><tr><td align="LEFT" valign="MIDDLE">PWD</td><td align="LEFT" valign="MIDDLE">The current working directory as set by the <b class="command">cd</b> built-in command.</td></tr><tr><td align="LEFT" valign="MIDDLE">RANDOM</td><td align="LEFT" valign="MIDDLE">Each time this parameter is referenced, a random integer between 0 and 32767 is generated.  Assigning a value to this variable seeds the random number generator.</td></tr><tr><td align="LEFT" valign="MIDDLE">REPLY</td><td align="LEFT" valign="MIDDLE">The default variable for the <b class="command">read</b> built-in.</td></tr><tr><td align="LEFT" valign="MIDDLE">SECONDS</td><td align="LEFT" valign="MIDDLE">This variable expands to the number of seconds since the shell was started.</td></tr><tr><td align="LEFT" valign="MIDDLE">SHELLOPTS</td><td align="LEFT" valign="MIDDLE">A colon-separated list of enabled shell options.</td></tr><tr><td align="LEFT" valign="MIDDLE">SHLVL</td><td align="LEFT" valign="MIDDLE">Incremented by one each time a new instance of Bash is started.</td></tr><tr><td align="LEFT" valign="MIDDLE">TIMEFORMAT</td><td align="LEFT" valign="MIDDLE">The value of this parameter is used as a format string specifying
     how the timing information for pipelines prefixed with the <b class="command">time</b> reserved word should be displayed.</td></tr><tr><td align="LEFT" valign="MIDDLE">TMOUT</td><td align="LEFT" valign="MIDDLE">If set to a value greater than zero, <tt class="varname">TMOUT</tt> is treated as the default timeout for the <b class="command">read</b> built-in.  In an interative shell, the value is interpreted as the number of seconds to wait for input after issuing the primary prompt when the shell is interactive.  Bash terminates after that number of seconds if input does not arrive.</td></tr><tr><td align="LEFT" valign="MIDDLE">UID</td><td align="LEFT" valign="MIDDLE">The numeric, real user ID of the current user.</td></tr></tbody></table></div><p>Check the Bash man, info or doc pages for extended information.  Some variables are read-only, some are set automatically and some lose their meaning when set to a different value than the default.</p></div></div><div class="sect2"><h2 class="sect2"><a name="sect_03_02_05"></a>3.2.5. Special parameters</h2><p>The shell treats several parameters specially.  These parameters may only be referenced; assignment to them is not allowed.</p><div class="table"><a name="table_03_03"></a><p><b>Table 3-3. Special bash variables</b></p><table border="1" class="CALSTABLE"><thead><tr><th align="LEFT" valign="MIDDLE">Character</th><th align="LEFT" valign="MIDDLE">Definition</th></tr></thead><tbody><tr><td align="LEFT" valign="MIDDLE"><tt class="varname">$*</tt></td><td align="LEFT" valign="MIDDLE">Expands to the positional parameters, starting from one.  When the expansion occurs within double quotes, it expands to a single word with the value of each parameter separated by the first character of the <tt class="varname">IFS</tt> special variable.</td></tr><tr><td align="LEFT" valign="MIDDLE"><tt class="varname">$@</tt></td><td align="LEFT" valign="MIDDLE">Expands to the positional parameters, starting from one.  When the expansion occurs within double quotes, each parameter expands to a separate word.</td></tr><tr><td align="LEFT" valign="MIDDLE"><tt class="varname">$#</tt></td><td align="LEFT" valign="MIDDLE">Expands to the number of positional parameters in decimal.</td></tr><tr><td align="LEFT" valign="MIDDLE"><tt class="varname">$?</tt></td><td align="LEFT" valign="MIDDLE">Expands to the exit status of the most recently executed foreground pipeline.</td></tr><tr><td align="LEFT" valign="MIDDLE"><tt class="varname">$-</tt></td><td align="LEFT" valign="MIDDLE">A hyphen expands to the current option flags as specified upon invocation, by the <b class="command">set</b> built-in command, or those set by the shell itself (such as the <tt class="option">-i</tt>).</td></tr><tr><td align="LEFT" valign="MIDDLE"><tt class="varname">$$</tt></td><td align="LEFT" valign="MIDDLE">Expands to the process ID of the shell.</td></tr><tr><td align="LEFT" valign="MIDDLE"><tt class="varname">$!</tt></td><td align="LEFT" valign="MIDDLE">Expands to the process ID of the most recently executed background (asynchronous) command.</td></tr><tr><td align="LEFT" valign="MIDDLE"><tt class="varname">$0</tt></td><td align="LEFT" valign="MIDDLE">Expands to the name of the shell or shell script.</td></tr><tr><td align="LEFT" valign="MIDDLE"><tt class="varname">$_</tt></td><td align="LEFT" valign="MIDDLE">The underscore variable is set at shell startup and contains the absolute file name of the shell or script being executed as passed in the argument list.  Subsequently, it expands to the last argument to the previous command, after expansion.  It is also set to the full pathname of each command executed and placed in the environment exported to that command.  When checking mail, this parameter holds the name of the mail file.
</td></tr></tbody></table></div><div class="note"><p></p><table class="note" width="100%" border="0"><tbody><tr><td width="25" align="CENTER" valign="TOP"><img src="../images/note.gif" hspace="5" alt="Note"></td><th align="LEFT" valign="CENTER"><b>$* vs. $@</b></th></tr><tr><td>&nbsp;</td><td align="LEFT" valign="TOP"><p>The implementation of <span class="QUOTE">"$*"</span> has always been a problem and realistically should have been replaced with the behavior of <span class="QUOTE">"$@"</span>.  In almost every case where coders use <span class="QUOTE">"$*"</span>, they mean <span class="QUOTE">"$@"</span>.  <span class="QUOTE">"$*"</span> Can cause bugs and even security holes in your software.</p></td></tr></tbody></table></div><p>The positional parameters are the words following the name of a shell script.  They are put into the variables <tt class="varname">$1</tt>, <tt class="varname">$2</tt>, <tt class="varname">$3</tt> and so on.  As long as needed, variables are added to an internal array.  <tt class="varname">$#</tt> holds the total number of parameters, as is demonstrated with this simple script:</p><table border="0" bgcolor="#E0E0E0" width="100%"><tbody><tr><td><font color="#000000"><pre class="screen">
#!/bin/bash

# positional.sh
# This script reads 3 positional parameters and prints them out.

POSPAR1="$1"
POSPAR2="$2"
POSPAR3="$3"

echo "$1 is the first positional parameter, \$1."
echo "$2 is the second positional parameter, \$2."
echo "$3 is the third positional parameter, \$3."
echo
echo "The total number of positional parameters is $#."
</pre></font></td></tr></tbody></table><p>Upon execution one could give any numbers of arguments:</p><table border="0" bgcolor="#E0E0E0" width="100%"><tbody><tr><td><font color="#000000"><pre class="screen">
<tt class="prompt">franky ~&gt;</tt> <b class="command">positional.sh <tt class="parameter"><i>one two three four five</i></tt></b>
one is the first positional parameter, $1.
two is the second positional parameter, $2.
three is the third positional parameter, $3.

The total number of positional parameters is 5.

<tt class="prompt">franky ~&gt;</tt> <b class="command">positional.sh <tt class="parameter"><i>one two</i></tt></b>
one is the first positional parameter, $1.
two is the second positional parameter, $2.
 is the third positional parameter, $3.

The total number of positional parameters is 2.
</pre></font></td></tr></tbody></table><p>More on evaluating these parameters is in <a href="chap_07.html">Chapter 7</a> and <a href="sect_09_07.html">Section 9.7</a>.</p><p>Some examples on the other special parameters:</p><table border="0" bgcolor="#E0E0E0" width="100%"><tbody><tr><td><font color="#000000"><pre class="screen">
<tt class="prompt">franky ~&gt;</tt> <b class="command">grep <tt class="parameter"><i>dictionary</i></tt> <tt class="filename">/usr/share/dict/words</tt></b>
dictionary

<tt class="prompt">franky ~&gt;</tt> <b class="command">echo <tt class="varname">$_</tt></b>
/usr/share/dict/words

<tt class="prompt">franky ~&gt;</tt> <b class="command">echo <tt class="varname">$$</tt></b>
10662

<tt class="prompt">franky ~&gt;</tt> <b class="command">mozilla &amp;</b>
[1] 11064

<tt class="prompt">franky ~&gt;</tt> <b class="command">echo <tt class="varname">$!</tt></b>
11064

<tt class="prompt">franky ~&gt;</tt> <b class="command">echo <tt class="varname">$0</tt></b>
bash

<tt class="prompt">franky ~&gt;</tt> <b class="command">echo <tt class="varname">$?</tt></b>
0

<tt class="prompt">franky ~&gt;</tt> <b class="command">ls <tt class="filename">doesnotexist</tt></b>
ls: doesnotexist: No such file or directory

<tt class="prompt">franky ~&gt;</tt> <b class="command">echo <tt class="varname">$?</tt></b>
1

<tt class="prompt">franky ~&gt;</tt>
</pre></font></td></tr></tbody></table><p>User <em>franky</em> starts entering the <b class="command">grep</b> command, which results in the assignment of the <tt class="varname">_</tt> variable.  The process ID of his shell is 10662.  After putting a job in the background, the <tt class="varname">!</tt> holds the process ID of the backgrounded job.  The shell running is <b class="command">bash</b>.  When a mistake is made, <tt class="varname">?</tt> holds an exit code different from 0 (zero).</p></div><div class="sect2"><h2 class="sect2"><a name="sect_03_02_06"></a>3.2.6. Script recycling with variables</h2><p>Apart from making the script more readable, variables will also enable you to faster apply a script in another environment or for another purpose.  Consider the following example, a very simple script that makes a backup of <em>franky</em>'s home directory to a remote server:</p><table border="0" bgcolor="#E0E0E0" width="100%"><tbody><tr><td><font color="#000000"><pre class="screen">
#!/bin/bash

# This script makes a backup of my home directory.

cd /home

# This creates the archive
tar cf /var/tmp/home_franky.tar franky &gt; /dev/null 2&gt;&amp;1

# First remove the old bzip2 file.  Redirect errors because this generates some if the archive
# does not exist.  Then create a new compressed file.
rm /var/tmp/home_franky.tar.bz2 2&gt; /dev/null
bzip2 /var/tmp/home_franky.tar

# Copy the file to another host - we have ssh keys for making this work without intervention.
scp /var/tmp/home_franky.tar.bz2 bordeaux:/opt/backup/franky &gt; /dev/null 2&gt;&amp;1

# Create a timestamp in a logfile.
date &gt;&gt; /home/franky/log/home_backup.log
echo backup succeeded &gt;&gt; /home/franky/log/home_backup.log
</pre></font></td></tr></tbody></table><p>First of all, you are more likely to make errors if you name files and directories manually each time you need them.  Secondly, suppose <em>franky</em> wants to give this script to <em>carol</em>, then carol will have to do quite some editing before she can use the script to back up her home directory.  The same is true if <em>franky</em> wants to use this script for backing up other directories.  For easy recycling, make all files, directories, usernames, servernames etcetera variable.  Thus, you only need to edit a value once, without having to go through the entire script to check where a parameter occurs.  This is an example:</p><table border="0" bgcolor="#E0E0E0" width="100%"><tbody><tr><td><font color="#000000"><pre class="screen">
#!/bin/bash
                                                                                                 
# This script makes a backup of my home directory.

# Change the values of the variables to make the script work for you:
BACKUPDIR=/home
BACKUPFILES=franky
TARFILE=/var/tmp/home_franky.tar
BZIPFILE=/var/tmp/home_franky.tar.bz2
SERVER=bordeaux
REMOTEDIR=/opt/backup/franky
LOGFILE=/home/franky/log/home_backup.log

cd $BACKUPDIR

# This creates the archive
tar cf $TARFILE $BACKUPFILES &gt; /dev/null 2&gt;&amp;1
                                                                                                 
# First remove the old bzip2 file.  Redirect errors because this generates some if the archive 
# does not exist.  Then create a new compressed file.
rm $BZIPFILE 2&gt; /dev/null
bzip2 $TARFILE

# Copy the file to another host - we have ssh keys for making this work without intervention.
scp $BZIPFILE $SERVER:$REMOTEDIR &gt; /dev/null 2&gt;&amp;1

# Create a timestamp in a logfile.
date &gt;&gt; $LOGFILE
echo backup succeeded &gt;&gt; $LOGFILE
</pre></font></td></tr></tbody></table><div class="note"><p></p><table class="note" width="100%" border="0"><tbody><tr><td width="25" align="CENTER" valign="TOP"><img src="../images/note.gif" hspace="5" alt="Note"></td><th align="LEFT" valign="CENTER"><b>Large directories and low bandwidth</b></th></tr><tr><td>&nbsp;</td><td align="LEFT" valign="TOP"><p>The above is purely an example that everybody can understand, using a small directory and a host on the same subnet.  Depending on your bandwidth, the size of the directory and the location of the remote server, it can take an awful lot of time to make backups using this mechanism.  For larger directories and lower bandwidth, use <b class="command">rsync</b> to keep the directories at both ends synchronized.</p></td></tr></tbody></table></div></div></div>
