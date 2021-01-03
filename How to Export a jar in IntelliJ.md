<!DOCTYPE html>
<html>

<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>How to Export a jar in IntelliJ</title>
  <link rel="stylesheet" href="https://stackedit.io/style.css" />
</head>

<body class="stackedit">
  <div class="stackedit__html"><h2 id="introduction">Introduction</h2>
<!-- What is a JAR, why is it useful? (briefly) -->
<p>Jar file is a <strong>J</strong>ava <strong>ar</strong>chive file. It is typically a package of your application in a single archive file. Jar files are platform independent.</p>
<p>There are two types of Jar file.</p>
<ol>
<li>Normal Jar File</li>
<li>Executable Jar File</li>
</ol>
<p>Normal Jar files are jars which is a package of your application in archive format without an entry point. It means you cannot directly execute this type of Jar file. You can only add this as a dependency to other programs and use it. On the other hand, executable Jar file is a package of your application in archive format which can be executed directly without any external program. In Simple words, the executable jar files can be started directly by doubling clicking on it.</p>
<!--  Why do some Java devs have trouble creating a JAR from IntelliJ? -->
<p>IntelliJ provides multiple ways to Export a Jar file from your workspace. But it involves setting up your project architecture in an appropriate way to export a jar without any difficulty.</p>
<p>In this tutorial, We’ll explain you the different ways to export a Jar from IntelliJ. The two methods explained below are</p>
<ol>
<li>Exporting a Jar Using Build Artifact Method</li>
<li>Exporting a Jar Using Maven Build</li>
</ol>
<h2 id="exporting-a-jar-using-build-artifact-method">Exporting a Jar Using Build Artifact Method</h2>
<ul>
<li>
<p>Why use this method? What are the pros/cons?</p>
</li>
<li>
<p>Walk readers step-by-step through exporting a JAR, and include screenshots</p>
</li>
</ul>
<hr>
<p>In this method, you’ll export a jar file using the IntelliJ build option. Before starting up, you’ll need to <a href="https://www.jetbrains.com/help/idea/new-project-wizard.html">create a project</a> and add the necessary <a href="https://www.jetbrains.com/help/idea/working-with-module-dependencies.html#add-a-new-dependency">dependencies to your project</a>. Dependencies are external programs packaged as jar which has some functionalities implemented already and can easily be reused in your program.</p>
<p>Also create a main class with some simple functionality. In this example program, you’ll see a simple program which accepts user profile information as a command line arguments. This program also uses the PicoCli and Apache Commons Cli as dependencies which will support in validating and parsing the command line parameters.</p>
<p>You can download the project from the GitHub to your local machine from this link.</p>
<p>You’ve created project and created a Run Configuration which can be used to execute the project. Now you’ll see how to setup your program to export a Jar from the IntelliJ workspace.</p>
<p>First you need to setup the project settings to define the artifacts of this project. Click <em>Project structure</em> from the file menu as shown in the below image.</p>
<p><img src="https://imgur.com/hxOrsAz.jpg" alt="enter image description here"></p>
<p>Project structure window will be opened. Select the Artifacts option from the left pane and Click the Plus symbol. You’ll see the Jar option. Expand the Jar option and you’ll see <em>From module with dependencies</em> option as shown in the below image.</p>
<p><img src="https://imgur.com/Fowxs6W.jpg" alt="enter image description here"></p>
<p>It’ll open the create Jar from modules window as shown below.</p>
<p><img src="https://imgur.com/5jR9VIl.jpg" alt="enter image description here"></p>
<p>Select the Main class of your Project. It will be displayed when you click the folder button on the right side of the Main Class option. If you know the name of the main class, you start typing and you’ll see the suggestion of the main class.</p>
<p>Next, you’ll set the option on how the external libraries should be handled while creating the Jar File. There are two options.</p>
<p><strong>Extract to the target Jar</strong></p>
<p>You’ll select this option this tutorial. It’ll extract all the class files of your external libraries to the jar you are creating.<br>
Now a single jar file will be created which will contain your class files and also the class files from your external dependencies.</p>
<p>Using this option is recommended because handling a single Jar is easier when compared to handling multiple jar files. You can easily add this jar file to your another programs and execute it with a double click.</p>
<p><strong>Copy to the output directory and link via Manifest</strong></p>
<p>This option will copy your dependency jars to the output directory and create a link with your Java program using the <a href="https://docs.oracle.com/javase/tutorial/deployment/jar/manifestindex.html">Manifest file</a>.</p>
<p>When you select this option, you should also specify a location where the Manifest file needs to be created.</p>
<p>This method is recommended when you have lot of Jar files especially when any of the Jar files are signed. <a href="https://docs.oracle.com/javase/tutorial/deployment/jar/signing.html">Signed Jar files</a> may not work properly when you extract the class files into your jar file. Hence, its better to copy the Jar files to the output directory and use it appropriately via the manifest file.</p>
<p>Select the appropriate option and <em>click Ok</em>. This will create an artifact in your Artifacts window as shown below.</p>
<p><img src="https://imgur.com/tvfYXlf.jpg" alt="enter image description here"></p>
<h2 id="exporting-a-jar-using-maven-build">Exporting a Jar Using Maven Build</h2>
<ul>
<li>Why use this method? What are the pros/cons?</li>
<li></li>
</ul>
<ul>
<li>Walk readers step-by-step through exporting a JAR, and include screenshots<br>
<a href="https://www.jetbrains.com/help/idea/maven-support.html#create_new_maven_project">https://www.jetbrains.com/help/idea/maven-support.html#create_new_maven_project</a></li>
</ul>
<h2 id="conclusion">Conclusion</h2>
<ul>
<li>Reiterate what readers learned in this post</li>
</ul>
</div>
</body>

</html>
