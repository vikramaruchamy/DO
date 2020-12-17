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
<pre><code>This is a simple command line program!
Your Command Line arguments are:
one
</code></pre>
<h3 id="execute-with-multiple-arguments">Execute with Multiple arguments</h3>
<p>Now, you’ll execute your Java program with the multiple command line arguments using the <code>java</code> command.</p>
<p><code>java SimpleCommandLinePgm one two three four</code></p>
<ul>
<li><code>Java</code> keyword to execute the compiled Java program</li>
<li><code>SimpleCommandLinePgm</code> name of the compiled Java program without <code>.java</code> extension.</li>
<li><code>one two three four</code> your list of command line arguments to the program separated by space.</li>
</ul>
<p>You’ll see the below output.</p>
<pre><code>This is a simple command line program!
Your Command Line arguments are :
one
two
three
four
</code></pre>
<p>You’ve seen how to use command line programs which accepts single and multiple command line parameters. Now you’ll see how to use command line parameters as a key value pairs.</p>
<h2 id="advanced-topics">Advanced topics</h2>
<p>In this section, you’ll create a program which will accept command line argument as a key value pair and access the arguments.</p>
<p>By default, Java doesn’t accept key value pair. However, the arguments can be passed using the -D parameter <code>-Dproperty=value</code> which will pass the key value pair arguments as arguments for JVM itself.</p>
<p>In your java program, you can access this value using the method <code>system.getProperty()</code> method</p>
<p>The below example shows how the key value pair arguments can be accessed from your Java program.</p>
<pre><code>public class AdvancedCommandLinePgm {
	public static void main(String[] args) {
		System.out.println("This is a advanced command line program!");
		System.out.println("Your Key value command line argument is :");
		// to get the value using the System.get Property and print it.
		System.out.println("User Name is : " + System.getProperty("userName"));
	}
}

</code></pre>
<p>You can execute the above program using the below command line code.</p>
<pre><code>C:\Program Files\Java\jre1.8.0_181\bin\javaw.exe "-DuserName=Vikram Aruchamy" -Dfile.encoding=Cp1252 -classpath "G:\Eclipse_Workspace\com.draftdev.cmdlineargs\bin;" com.draftdev.cmdlineargs.AdvancedCommandLinePgm
</code></pre>
<p>You’ve learnt simple and advanced commandline programs supported natively by Java. Now, you’ll learn other methods supported by third party libraries.</p>
<h2 id="other-methods">Other methods</h2>
<p>In this section, you’ll learn about the third party libraries to handle the command line parameters with ease. Using third party libraries saves you lot of time too. The two third party libraries you’ll learn are</p>
<ul>
<li>Apache Commons Cli</li>
<li>PicoCli</li>
</ul>
<p>There are three stages for the command line processing. Definition, Parsing and Interrogation.</p>
<p><strong>Definition</strong> - To define your command line parameters in your program<br>
<strong>Parsing</strong> - To parse the parameters passed from the commandline and store it in your model<br>
<strong>Interrogation</strong> - To check the parsed model and use it as required</p>
<p>Now, you’ll see how the two third party libraries handle all the three stages.</p>
<h3 id="apache-commons-cli">Apache Commons Cli</h3>
<p>Apache Commons Cli library provides you an API for parsing the command line arguements you pass to the command line programs.</p>
<h4 id="definition">Definition</h4>
<p>You can define the command line option using the Options and Option objects available in Commons Cli.</p>
<p><strong>Options</strong> list to hold all the option you define for your program.</p>
<p><strong>Option</strong> lets you define the characteristics of the each parameter.</p>
<pre><code>Option name = new Option("opt", "longOpt", hasArgs, "Description");
</code></pre>
<p>Above line is explained below.</p>
<p><em>opt</em> -  small notation(name) for your parameter<br>
<em>longOpt</em> - long notation(name) for your parameter<br>
<em>hasArgs</em> -  flag -to denote if this parameter will have an argument attached or this parameter will just act as a flag. You either pass <em>true</em> or <em>false</em><br>
<em>Description</em> - Description for your parameter.</p>
<p>You can set if this option is a mandatory option by using the <code>setRequired()</code> method available in the <code>Option</code> object.</p>
<p>Once you have defined this, you can add this option to the Options list by using <code>Options.addOption()</code> method available in <code>Option</code>.</p>
<h4 id="parsing">Parsing</h4>
<p>You can parse the command line parameters using Command line Cli using the  <code>DefaultParser</code> implementation of the <code>CommandlineParser</code> interface.</p>
<p><code>DefaultParser</code> Implementation has the <code>parse()</code> which accepts the <code>options</code> object defined and the <code>args</code> from the command line.</p>
<p>It will parse the command line parameters sent in args and returns the model <code>CommandLine</code> which has all the options stored. You can use it for interrogation.</p>
<h4 id="interrogation">Interrogation</h4>
<p>You’ve parsed and stored the command line parameters in the <code>CommandLine</code> model.</p>
<p>Now, you’ll see how to use it in your program.</p>
<p><code>getOptionValue()</code> method in the <code>CommandLine</code> can be used to get the value of the command line parameter.</p>
<p>If your parameter is just a flag and doesn’t have an argument value, then <code>hasOption()</code> method can be used to check if the specific flag is set or not.</p>
<p>You can use the <code>HelpFormatter</code> to print the help information to the user, in case the command line parameters are not valid. Use <code>printHelp()</code> to print the help. It will print your complete command line definition available in <code>Options</code>.</p>
<pre><code>public class CommonsCliPgm {

	public static void main(String[] args) throws Exception {

		Options options = new Options();

		Option name = new Option("f", "name", true, "First Name");
		name.setRequired(true);
		options.addOption(name);

		Option lastName = new Option("l", "lastname", true, "Last Name");
		lastName.setRequired(true);
		options.addOption(lastName);

		Option email = new Option("e", "email", true, "Email");
		email.setRequired(true);
		options.addOption(email);

		Option mobileNumber = new Option("m", "mobilenumber", true, "Mobile Number");
		mobileNumber.setRequired(false);
		options.addOption(mobileNumber);

		HelpFormatter formatter = new HelpFormatter();

		CommandLineParser parser = new DefaultParser();
		CommandLine cmd;

		try {
			cmd = parser.parse(options, args);
		} catch (ParseException e) {
			System.out.println(e.getMessage());
			formatter.printHelp("User Profile Info", options);

			System.exit(1);
			return;
		}

		System.out.println("User First Name is: " + cmd.getOptionValue("name"));
		System.out.println("User Last Name is: " + cmd.getOptionValue("lastname"));
		System.out.println("User Email is: " + cmd.getOptionValue("email"));
		if (cmd.hasOption("m")) {
			System.out.println("User Mobile Number is: " + cmd.getOptionValue("mobilenumber"));

		}
	}
}
</code></pre>
<pre><code>C:\Program Files\Java\jre1.8.0_181\bin\javaw.exe -Dfile.encoding=Cp1252 -classpath "G:\Eclipse_Workspace\com.draftdev.cmdlineargs\bin;G:\Eclipse_Workspace\com.draftdev.cmdlineargs\lib\commons-cli-1.4.jar" com.draftdev.cmdlineargs.CommonsCliPgm -f Vikram -l Aruchamy -e questions@askvikram.com -m 8015500500
</code></pre>
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

