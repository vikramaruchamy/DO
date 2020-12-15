<h1 id="java-tutorial-java-command-line-arguments">Java Tutorial: Java Command Line Arguments</h1>
<h2 id="introduction">Introduction</h2>
<!-- What is a command line program and why is it useful? -->
<p>Java command line program is a program which accepts values for program variables from the command line during the execution. With command line programs, you do not need any graphical user interface to interact or pass any configurable values to your program. You can do it by using the command line arguments.</p>
<!-- What are command line arguments? What do they allow you to do?-->
<p>Java command line arguments are arguments which are passed to your  program while executing it via command line. It is useful to make your java program more configurable rather making it a static one.  Command Line arguments typically follow the name of the program when it is executed.</p>
<p>In this tutorial, I’ll show you how to</p>
<ul>
<li>Use Single and Multiple command line arguments</li>
<li>Use flags and CLI responses in the command line arguments</li>
<li>Use Third party libraries to handle command line arguments</li>
</ul>
<!-- Introduce the tutorial: learn to use command line arguments in Java-->
<p>You’ll start with the basics.</p>
<h2 id="the-basics">The Basics</h2>
<!-- - Basic command line script - create a simple command line script in Java and show readers how to run it&#10;&#10;- Using a single command line argument&#10;&#10;- Using multiple command line arguments-->
<p>Main method is the entry point for any Java program. The JVM passes the command line argument through the <code>String[] args</code> parameter of the main method. The command line arguments can be fetched by accessing this parameter.</p>
<h3 id="simple-command-line-program">Simple Command Line Program</h3>
<p>In this section, you’ll create a simple command line program which can accept single or multiple arguments from the command line and print it one the screen.</p>
<pre><code>class SimpleCommandLinePgm {
  public static void main(String[] args) {
    System.out.println("This is a simple command line program!");
    System.out.println("Your Command Line arguments are :");

    // loop through all arguments and print it to the user
    for(String str: args) {
      System.out.println(str);
    }
  }
}
</code></pre>
<p>To compile the program from command line, use</p>
<p><code>javac SimpleCommandLinePgm.java</code></p>
<ul>
<li><code>javac</code> is the keyword to compile your java program</li>
<li><code>SimpleCommandLinePgm.java</code> is your Java program name</li>
</ul>
<p>This will compile your Java program and create a <code>.class</code> file which can be executed with the <code>java</code> command.</p>
<h3 id="execute-with-one-argument">Execute with One Argument</h3>
<p>Now, you’ll execute your Java program with the one command line argument using the <code>java</code> command.</p>
<p><code>java SimpleCommandLinePgm one</code></p>
<ul>
<li><code>Java</code> keyword to execute the compiled Java program</li>
<li><code>SimpleCommandLinePgm</code> name of the compiled Java program without <code>.java</code> extension.</li>
<li><code>one</code> your first command line argument to the program</li>
</ul>
<p>You’ll see the below output.</p>
<p><code>This is a simple command line program!</code><br>
<code>Your Command Line arguments are :</code><br>
<code>one</code></p>
<h3 id="execute-with-multiple-arguments">Execute with Multiple arguments</h3>
<p>Now, you’ll execute your Java program with the multiple command line arguments using the <code>java</code> command.</p>
<p><code>java SimpleCommandLinePgm one two three four</code></p>
<ul>
<li><code>Java</code> keyword to execute the compiled Java program</li>
<li><code>SimpleCommandLinePgm</code> name of the compiled Java program without <code>.java</code> extension.</li>
<li><code>one two three four</code> your list of command line arguments to the program separated by space.</li>
</ul>
<p>You’ll see the below output.</p>
<p><code>This is a simple command line program!</code><br>
<code>Your Command Line arguments are :</code><br>
<code>one</code><br>
<code>two</code><br>
<code>three</code><br>
<code>four</code></p>
<h2 id="advanced-topics">Advanced topics</h2>
<ul>
<li>[Writer’s choice] Make your command line demo code gradually more complicated by showing other features like options with flags, CLI responses, etc.</li>
</ul>
<h2 id="other-methods">Other methods</h2>
<ul>
<li>
<p>Using a third-party library (like <a href="https://picocli.info/">Picocli</a>) to handle/parse command line arguments</p>
</li>
<li>
<p>Using an IDE to test/run a command line script</p>
</li>
</ul>
<h2 id="conclusion">Conclusion</h2>
<ul>
<li>Summarize why command line arguments are used and what readers just learned</li>
</ul>

