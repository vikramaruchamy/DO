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
<p>Jar file is a <strong>J</strong>ava <strong>ar</strong>chive file. It is typically a package of your application in a single archive file. Jar files are platform independent.</p>
<p>There are two types of Jar file.</p>
<ol>
<li>Normal Jar File</li>
<li>Executable Jar File</li>
</ol>
<p>Normal Jar files are jars which is a package of your application in archive format without an entry point. It means you cannot directly execute this type of Jar file. You can only add this as a dependency to other programs and use it. On the other hand, executable Jar file is a package of your application in archive format which can be executed directly without any external program. In Simple words, the executable jar files can be started directly by doubling clicking on it.</p>
<p>IntelliJ provides multiple ways to Export a Jar file from your workspace. But it involves setting up your project architecture in an appropriate way to export a jar without any difficulty.</p>
<p>In this tutorial, We’ll explain you the different ways to export a Jar from IntelliJ. The two methods explained below are</p>
<ol>
<li>Exporting a Jar Using Build Artifact Method</li>
<li>Exporting a Jar Using Maven Build</li>
</ol>
<p>To understand the tutorial better, you’ll use a simple example program which accepts user profile information as a command line arguments. This program also uses the PicoCli and Apache Commons Cli as external dependencies which will support in validating and parsing the command line parameters.</p>
<p>You can download the project from the GitHub to your local machine from this link.</p>
<h2 id="exporting-a-jar-using-build-artifact-method">Exporting a Jar Using Build Artifact Method</h2>
<p>In this method, you’ll export a jar file using the IntelliJ build option.</p>
<p>Before starting up, you’ll need to <a href="https://www.jetbrains.com/help/idea/new-project-wizard.html">create a project</a> and add the necessary <a href="https://www.jetbrains.com/help/idea/working-with-module-dependencies.html#add-a-new-dependency">dependencies to your project</a>. Dependencies are external programs packaged as jar which has some functionalities implemented already and can easily be reused in your program.  Also create a main class with some simple functionality.</p>
<p>You’ve created project and created a <a href="https://www.jetbrains.com/help/idea/run-debug-configuration.html">Run Configuration</a> which can be used to execute the project. Now you’ll see how to setup your program to export a Jar from the IntelliJ workspace.</p>
<p>First you need to setup the project settings to define the artifacts of this project. Click <em>Project structure</em> from the file menu as shown in the below image.</p>
<p><img src="https://imgur.com/hxOrsAz.jpg" alt="Setting up Project Structure"></p>
<p>Project structure window will be opened. Select the Artifacts option from the left pane and Click the Plus symbol. You’ll see the Jar option. Expand the Jar option and you’ll see <em>From module with dependencies</em> option as shown in the below image.</p>
<p><img src="https://imgur.com/Fowxs6W.jpg" alt="Creating Artifacts"></p>
<p>It’ll open the create Jar from modules window as shown below.</p>
<p><img src="https://imgur.com/5jR9VIl.jpg" alt="Configuring Jars"></p>
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
<p><img src="https://imgur.com/tvfYXlf.jpg" alt="Configuring Artifacts"></p>
<p>You’ll see the name of the jar in the <em>Name</em> option and the location of the output jar file in the <em>Output directory</em> option. You can change the output directory to your desired location.</p>
<p>Next option is <em>Include in project build.</em> You can enable this option, if you need a jar to be created during each and every project build.</p>
<p>If you do not want a jar to be created, you can leave it unselected. Now the Jar will not be created during each and every project build.</p>
<p>In that case, you can build the Jar finally when you are done with your project development using the <em>Build artifacts</em> option as shown in the below image.</p>
<p><img src="https://imgur.com/48xH535.jpg" alt="Build Artifacts"></p>
<p>When you click <em>Build artifacts</em>, the Jar will be generated in the Output directory you have selected.</p>
<p>This is how you can Export a jar file using the options available in the Intellij.</p>
<p>Next, you’ll see how to export a Jar using the Maven build.</p>
<h2 id="exporting-a-jar-using-maven-build">Exporting a Jar Using Maven Build</h2>
<p>In this section, you’ll export a Jar using Maven build. Maven is developed to make the build process easier. You can create scripts which is used build projects based on any triggers such as</p>
<ul>
<li>Build the project and create a Jar file after each and every commit to your Git Repo.</li>
<li>Build the project nighthly and deploy the Jars in the production systems     when all the defined test cases are passing.</li>
<li>You can use <a href="https://www.jenkins.io/">Jenkins</a> to automate the build and deployment of the maven projects.</li>
</ul>
<p>Create a Maven project in the Intelli using the <a href="https://www.jetbrains.com/help/idea/maven-support.html#create_new_maven_project">Create maven Project tutorial</a>.</p>
<p><strong>Note:</strong> Add the necessary classes for your project. You can use the same class files and dependencies used in the Method 1 example.</p>
<p>When you create a new Maven project, you’ll have the <a href="https://maven.apache.org/guides/introduction/introduction-to-the-pom.html#minimal-pom">minimum pom file</a> created in your project directory with the below contents.</p>
<pre><code>&lt;?xml version="1.0" encoding="UTF-8"?&gt;  
&lt;project xmlns="http://maven.apache.org/POM/4.0.0"  
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"&gt;  
    &lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;  
  
    &lt;groupId&gt;com.draft.dev.maven.demo&lt;/groupId&gt;  
    &lt;artifactId&gt;com.draft.dev.maven.demo&lt;/artifactId&gt;  
    &lt;version&gt;1.0-SNAPSHOT&lt;/version&gt; 
&lt;/project&gt;

</code></pre>
<p>It contains three important artifacts for the project.</p>
<ul>
<li><code>groupId</code> - A unique base name of the company who creates and maintains the project.</li>
<li><code>artifactId</code> - A unique name for the project itself</li>
<li><code>version</code> - Version number for the project</li>
</ul>
<p>You’ll need to add the additional build to configure the project build. You’ll see it in the next steps.</p>
<h3 id="configuring-maven-build">Configuring Maven build</h3>
<p>In this section,  You’ll configure the build aspects of your maven build.</p>
<p>You can add <a href="https://maven.apache.org/plugins/">various plugins</a> inside the build section of your build plugin based on your requirement. Each will have different functions. These plugins will be executed during the build of your project.</p>
<p>In this tutorial, you’ll add the necessary plugin and configure it to export a jar during the build.</p>
<p>First you’ll add the <a href="https://maven.apache.org/plugins/maven-compiler-plugin/"><code>maven-compiler-plugin</code></a> as below.</p>
<pre><code>&lt;plugin&gt;  
    &lt;groupId&gt;org.apache.maven.plugins&lt;/groupId&gt;  
    &lt;artifactId&gt;maven-compiler-plugin&lt;/artifactId&gt;  
    &lt;configuration&gt;  
        &lt;source&gt;1.8&lt;/source&gt;  
        &lt;target&gt;1.8&lt;/target&gt;  
    &lt;/configuration&gt;  
&lt;/plugin&gt;
</code></pre>
<p>With this, you are adding a maven compiler for your projects and also mentioning the <code>java version</code> for compiling your sources. Here, you’re using <code>Java</code> version <code>1.8</code>.</p>
<p>Next, you’ll add the <a href="https://maven.apache.org/plugins/maven-assembly-plugin/"><code>maven-assembly-plugin</code></a>.</p>
<p>This assembly plugin is used to create a assembly of your Java project. The complete setup of the maven-assembly-plugin is given below. The sections are also explained separaterly.</p>
<pre><code>&lt;plugin&gt;  
&lt;artifactId&gt;maven-assembly-plugin&lt;/artifactId&gt;  
&lt;configuration&gt;  
    &lt;finalName&gt;Command_Line_Program&lt;/finalName&gt;  
    &lt;appendAssemblyId&gt;false&lt;/appendAssemblyId&gt;  
    &lt;archive&gt;  
        &lt;manifest&gt;  
            &lt;mainClass&gt;com.askvikram.commandlinedemo.PicoCliSamplePgm&lt;/mainClass&gt;  
        &lt;/manifest&gt;  
    &lt;/archive&gt;  
    &lt;descriptorRefs&gt;  
        &lt;descriptorRef&gt;jar-with-dependencies&lt;/descriptorRef&gt;  
    &lt;/descriptorRefs&gt;  
&lt;/configuration&gt;  
    &lt;executions&gt;  
        &lt;execution&gt;  
            &lt;id&gt;make-assembly&lt;/id&gt; 
  &lt;phase&gt;package&lt;/phase&gt; 
  &lt;goals&gt;  
                &lt;goal&gt;directory-single&lt;/goal&gt;  
            &lt;/goals&gt;  
        &lt;/execution&gt;  
    &lt;/executions&gt;  
&lt;/plugin&gt;
</code></pre>
<ul>
<li><code>artifactId</code> - Used to mention the name of the plugin itself.</li>
<li><code>configuration</code> - section used to configure the maven assembly.</li>
<li><code>finalName</code> - Final Name of the assembly to be created.</li>
<li><a href="https://maven.apache.org/plugins/maven-assembly-plugin/single-mojo.html#appendAssemblyId"><code>appendAssemblyId</code></a> - Flag to include or exclude the assembly name in the final name of the jar.</li>
<li><a href="https://maven.apache.org/plugins/maven-assembly-plugin/single-mojo.html#archive"><code>archive</code></a> - Section to add instructions to the archive builder. Inside the archive, the main class your java program is added which will be added in the manifest.</li>
<li><a href="https://maven.apache.org/plugins/maven-assembly-plugin/single-mojo.html#descriptorRefs"><code>descriptorRefs</code></a> - A list of references to assembly descriptors available on the plugin’s classpath.</li>
<li><code>execution</code> - Tag to specify the activities to be performed during execution.</li>
<li><code>goal</code> - directory-single is used to create a single file during the build.</li>
</ul>
<p>You’ve configured the maven settings for your project.</p>
<p>The whole pom file of the project should look like below.</p>
<pre><code>&lt;?xml version="1.0" encoding="UTF-8"?&gt;  
&lt;project xmlns="http://maven.apache.org/POM/4.0.0"  
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"&gt;  
    &lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;  
  
    &lt;groupId&gt;com.askvikram&lt;/groupId&gt;  
    &lt;artifactId&gt;commandline.demo&lt;/artifactId&gt;  
    &lt;version&gt;1.0&lt;/version&gt;  
    &lt;dependencies&gt;  
        &lt;dependency&gt;  
            &lt;groupId&gt;commons-cli&lt;/groupId&gt;  
            &lt;artifactId&gt;commons-cli&lt;/artifactId&gt;  
            &lt;version&gt;1.4&lt;/version&gt;  
        &lt;/dependency&gt;  
        &lt;!-- https://mvnrepository.com/artifact/info.picocli/picocli --&gt;  
  &lt;dependency&gt;  
            &lt;groupId&gt;info.picocli&lt;/groupId&gt;  
            &lt;artifactId&gt;picocli&lt;/artifactId&gt;  
            &lt;version&gt;4.5.2&lt;/version&gt;  
        &lt;/dependency&gt;  
  
    &lt;/dependencies&gt;  
    &lt;build&gt;  
        &lt;plugins&gt;  
            &lt;plugin&gt;  
                &lt;groupId&gt;org.apache.maven.plugins&lt;/groupId&gt;  
                &lt;artifactId&gt;maven-compiler-plugin&lt;/artifactId&gt;  
                &lt;configuration&gt;  
                    &lt;source&gt;1.8&lt;/source&gt;  
                    &lt;target&gt;1.8&lt;/target&gt;  
                &lt;/configuration&gt;  
            &lt;/plugin&gt;  
            &lt;plugin&gt;  
            &lt;artifactId&gt;maven-assembly-plugin&lt;/artifactId&gt;  
            &lt;configuration&gt;  
                &lt;finalName&gt;Command_Line_Program&lt;/finalName&gt;  
                &lt;appendAssemblyId&gt;false&lt;/appendAssemblyId&gt;  
                &lt;archive&gt;  
                    &lt;manifest&gt;  
                        &lt;mainClass&gt;com.askvikram.commandlinedemo.PicoCliSamplePgm&lt;/mainClass&gt;  
                    &lt;/manifest&gt;  
                &lt;/archive&gt;  
                &lt;descriptorRefs&gt;  
                    &lt;descriptorRef&gt;jar-with-dependencies&lt;/descriptorRef&gt;  
                &lt;/descriptorRefs&gt;  
            &lt;/configuration&gt;  
                &lt;executions&gt;  
                    &lt;execution&gt;  
                        &lt;id&gt;make-assembly&lt;/id&gt; 
  &lt;phase&gt;package&lt;/phase&gt;  
  &lt;goals&gt;  
                            &lt;goal&gt;directory-single&lt;/goal&gt;  
                        &lt;/goals&gt;  
                    &lt;/execution&gt;  
                &lt;/executions&gt;  
            &lt;/plugin&gt;  
        &lt;/plugins&gt;  
    &lt;/build&gt;  
  
  
&lt;/project&gt;
</code></pre>
<p>Now, you’ll see how to export a jar with the configured build.</p>
<h2 id="executing-maven-build">Executing Maven Build</h2>
<p>In this step, you’ll create a configuration for the maven build and execute it. It’ll export a jar for you in the specified directories.</p>
<p>Select menu <em>Run -&gt; Edit Configurations</em> as shown in the below image.</p>
<p><img src="https://imgur.com/Fafs8MS.jpg" alt="Create Configuration"></p>
<p>select the Maven option from the Add new configuration button as shown below.</p>
<p><img src="https://imgur.com/I9H5Vpo.jpg" alt="Create Maven Configuration"></p>
<p>It’ll create a Maven Run configuration for you as below.</p>
<p><img src="https://imgur.com/B45rOSP.jpg" alt="Create Maven Configuration"></p>
<p>Give the configuration a name. Add the project’s home directory as your home directory.<br>
In the command line option, add goals as <code>clean</code> <code>install</code>. This goals as used to denote the projects should be cleaned and the package should be installed in your appropriate directory.</p>
<p>Click Apply and Ok. It’ll create a Run configuration for you directly.</p>
<p>Select the menu Run and click Run <code>Configuration Name</code> as shown below.</p>
<p><img src="https://imgur.com/tP4ArrU.jpg" alt="Run Configuration"></p>
<p>You’ll see the build starting in your console. After few seconds, you’ll the message <code>BUILD SUCCESS</code>.</p>
<p>Now, you’ll have the Jar created in the target directory of your Project home directory.</p>
<p>This is how you can create a Jar using Maven. Since this project has the configurations in <code>Pom.xml</code> file, you can also configure the automatic build using Jenkins other scripts on any trigger.</p>
<h2 id="conclusion">Conclusion</h2>
<p>In this tutorial, you’ve created Jar in IntelliJ using the Project Artifacts option and also created a Jar using Maven. If you have any other suggestions or tips to create a Jar, please comment below.</p>
</div>
</body>

</html>
