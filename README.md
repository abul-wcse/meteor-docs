<p><font size="7">Meteor</font></p>

<p><div class="toc">
<ul>
<li><a href="#meteor">Meteor</a><ul>
<li><a href="#adding-meteor-to-your-project">Adding Meteor to your project</a></li>
<li><a href="#initialising-meteor-into-your-project">Initialising Meteor into your project</a><ul>
<li><a href="#building-up-the-buildxml">Building up the build.xml</a></li>
<li><a href="#building-up-meteorjson">Building up Meteor.json</a></li>
<li><a href="#standards-of-naming-migration-tables">Standards of naming migration tables</a></li>
</ul>
</li>
<li><a href="#baseline">Baseline</a><ul>
<li><a href="#what-is-baseline">What is Baseline?</a></li>
<li><a href="#how-to-create-a-baseline">How  to create a baseline?</a></li>
</ul>
</li>
<li><a href="#creating-a-package">Creating a Package</a><ul>
<li><a href="#full-package">Full package</a><ul>
<li><a href="#how-do-i-create-a-full-package">How do I create a full package</a></li>
</ul>
</li>
</ul>
</li>
<li><a href="#baselined-package">#Baselined package</a></li>
<li><a href="#meteor-for-deployment-tasks">Meteor for deployment tasks</a><ul>
<li><a href="#patching">Patching</a></li>
<li><a href="#remove-meteor-lock">Remove Meteor lock</a></li>
<li><a href="#set-permission-to-patch-files">Set permission to patch files</a></li>
<li><a href="#reset-permission-of-files-on-the-server">Reset permission of files on the server</a></li>
<li><a href="#file-migration-command">File Migration Command</a></li>
<li><a href="#commands-related-to-migrations-imported-from-doctrine">Commands related to migrations imported from Doctrine</a></li>
</ul>
</li>
<li><a href="#faqs">FAQs</a></li>
</ul>
</li>
<li><a href="#common-issues-encounter-while-running-meteor">Common issues encounter while running Meteor</a><ul>
<li><ul>
<li><a href="#error-apc-error-cannot-redeclare-class">error: [apc-error] Cannot redeclare class</a></li>
<li><a href="#suhosin-enabled-on-server">Suhosin  enabled on server</a></li>
<li><a href="#phar-extension-disabled">phar extension disabled</a></li>
<li><a href="#mysql-timeout">MySQL Timeout</a></li>
</ul>
</li>
<li><a href="#other-ant-tasks">Other Ant Tasks</a></li>
</ul>
</li>
</ul>
</div>
</p>



<h1 id="meteor"><strong>Meteor</strong></h1>

<p>Meteor, a package builder and product deployment command line PHP tool, specifically designed for deploying products of Jadu and Spacecraft agency to the customer environment. <br>
The product such as</p>

<ol>
<li>Jadu CMS</li>
<li>Jadu XFP</li>
<li>Spacecraft customer packages </li>
</ol>

<p>Meteor is single tool that take care of both building package and deployments. The part of meteor that performs  building packages are developed using <strong>ANT</strong> scripts and the other part that take care of deployments are developed using PHP script over the symphony framework as a console application.</p>



<h2 id="adding-meteor-to-your-project"><strong>Adding Meteor to your project</strong></h2>

<p>Using composer is the recommended way to invoke Meteor to your project.</p>

<ul>
<li>Add <strong><em>jadu/meteor</em></strong> as a dependency in your project’s composer.json <br>
file. As of now, Meteor is not in the registered list of composer <br>
package. Hence include the repository in which Meteor resides, to <br>
your project’s composer.json file,</li>
</ul>



<pre class="prettyprint"><code class=" hljs json">{
 "<span class="hljs-attribute">repositories</span>": <span class="hljs-value">[
    {
      "<span class="hljs-attribute">type</span>": <span class="hljs-value"><span class="hljs-string">"git"</span></span>,
      "<span class="hljs-attribute">url</span>": <span class="hljs-value"><span class="hljs-string">"https://Domain/meteor.git"</span>
        </span>}
    ]</span>,
    "<span class="hljs-attribute">require</span>": <span class="hljs-value">{
        "<span class="hljs-attribute">php</span>": <span class="hljs-value"><span class="hljs-string">"&gt;=5.3.2"</span></span>,
        "<span class="hljs-attribute">jadu/meteor</span>": <span class="hljs-value"><span class="hljs-string">"1.0.5"</span>
    </span>}
</span>}</code></pre>

<ul>
<li>Download and install Composer.</li>
</ul>



<pre class="prettyprint"><code class=" hljs lasso">curl <span class="hljs-attribute">-sS</span> https:<span class="hljs-comment">//getcomposer.org/installer | php</span></code></pre>

<ul>
<li>Install your dependencies.</li>
</ul>



<pre class="prettyprint"><code class=" hljs cmake">php composer.phar <span class="hljs-keyword">install</span></code></pre>



<h2 id="initialising-meteor-into-your-project"><strong>Initialising Meteor into your project</strong></h2>

<p>To be able to use Meteor you need add two 2 config files into your project</p>

<ol>
<li>build.xml that loads in the other scripts and sets some properties</li>
<li><p>meteor.json file that provides settings for Doctrine Migrations</p>

<p>To get template copies of these files run:</p>

<pre class="prettyprint"><code class=" hljs lasso">ant <span class="hljs-attribute">-f</span> vendor/jadu/meteor/res/build/ant/setup<span class="hljs-built_in">.</span><span class="hljs-built_in">xml</span> setup<span class="hljs-attribute">-client</span></code></pre></li>
</ol>



<h3 id="building-up-the-buildxml"><strong>Building up the build.xml</strong></h3>

<hr>

<p>build.xml is the configuration file that is used while creating a package.  <br>
There are two important parts on this configuration file. </p>

<ol>
<li>Fileset , lists the files and directories that are to included or excluded in creation on package. </li>
<li>Targets, calls the tasks for necessary packaging. To find the list of task provided by meteor by default <a href="#ant-tasks">click here</a></li>
</ol>

<p>The default fileset </p>



<pre class="prettyprint"><code class=" hljs applescript">&lt;<span class="hljs-keyword">property</span> <span class="hljs-property">name</span>=<span class="hljs-string">"version-file"</span> value=<span class="hljs-string">"${basedir}/CLIENT_VERSION"</span> /&gt;
&lt;include <span class="hljs-property">name</span>=<span class="hljs-string">"jadu/**"</span> /&gt;
&lt;include <span class="hljs-property">name</span>=<span class="hljs-string">"public_html/**"</span> /&gt;
&lt;include <span class="hljs-property">name</span>=<span class="hljs-string">"var/**"</span> /&gt;
&lt;include <span class="hljs-property">name</span>=<span class="hljs-string">"CLIENT_VERSION"</span> /&gt; 
&lt;exclude <span class="hljs-property">name</span>=<span class="hljs-string">"public_html/site/styles/widget/**"</span> /&gt;</code></pre>

<p>To make  a custom fileset, uncomment the default fileset from the built.xml  update the fileset id to some <em>custom_fileset_id</em>. Update the list of files and directories that should be included and excluded in the package. Finally update the <strong>package-fileset</strong> property </p>

<p>Example: </p>



<pre class="prettyprint"><code class=" hljs applescript">&lt;<span class="hljs-keyword">property</span> <span class="hljs-property">name</span>=<span class="hljs-string">"version-file"</span> value=<span class="hljs-string">"${basedir}/CLIENT_VERSION"</span> /&gt;
&lt;fileset <span class="hljs-property">id</span>=<span class="hljs-string">"fileset.files"</span> dir=<span class="hljs-string">"${basedir}"</span>&gt;
    &lt;include <span class="hljs-property">name</span>=<span class="hljs-string">"jadu/**"</span> /&gt;
    &lt;include <span class="hljs-property">name</span>=<span class="hljs-string">"public_html/**"</span> /&gt;
    &lt;include <span class="hljs-property">name</span>=<span class="hljs-string">"var/**"</span> /&gt;
    &lt;include <span class="hljs-property">name</span>=<span class="hljs-string">"microsites/**"</span> /&gt;
    &lt;include <span class="hljs-property">name</span>=<span class="hljs-string">"CLIENT_VERSION"</span> /&gt;
    &lt;exclude <span class="hljs-property">name</span>=<span class="hljs-string">"public_html/site/styles/widget/**"</span> /&gt;
&lt;/fileset&gt;
&lt;<span class="hljs-keyword">property</span> <span class="hljs-property">name</span>=<span class="hljs-string">"package-fileset"</span> value=<span class="hljs-string">"fileset.files"</span> /&gt;</code></pre>

<blockquote>
  <p><strong>Note:</strong> <br>
  It is alway recommended to include the version file of the package/module into the file set and also update the property version-file</p>
</blockquote>



<h3 id="building-up-meteorjson"><strong>Building up Meteor.json</strong></h3>

<hr>

<p>meteor.json is a configuration file that withholds the  details required for running a migration</p>



<pre class="prettyprint"><code class=" hljs json">{
   "<span class="hljs-attribute">migrations</span>": <span class="hljs-value">{
        "<span class="hljs-attribute">name</span>": <span class="hljs-value"><span class="hljs-string">"Migration Name"</span></span>,
        "<span class="hljs-attribute">table</span>": <span class="hljs-value"><span class="hljs-string">"Migration Table"</span></span>,
        "<span class="hljs-attribute">namespace</span>": <span class="hljs-value"><span class="hljs-string">"DoctrineMigrations"</span></span>,
        "<span class="hljs-attribute">directory</span>": <span class="hljs-value"><span class="hljs-string">"upgrades/migrations"</span>
   </span>}</span>,
   "<span class="hljs-attribute">patch</span>": <span class="hljs-value">{
        "<span class="hljs-attribute">includeHiddenFiles</span>" : <span class="hljs-value"><span class="hljs-literal">false</span>
   </span>}
</span>}</code></pre>

<blockquote>
  <p><strong>NOTE.</strong></p>
  
  <ul>
  <li>With the above example, Meteor will search the <strong>upgrades/migrations</strong> recursively for files that begin with Version followed one to 255 characters and a .php suffix. Version.{1,255}.php is the regular expression that’s used. </li>
  <li>Everything after Version will be treated as the actual version in the database</li>
  <li>Take the file name Version<u><i>20150505120000</i></u>.php, <strong>20150505120000</strong> would be the version number stored in the migrations database table. </li>
  <li>Meteor uses Doctrine component for executing migrations, Doctrine creates a migration table inside the database and tracks which migrations have been executed there.</li>
  <li><em>includeHiddenFiles</em> is a boolean option, based on which Meteor decides wether the hidden file like .htaccess files. It is a replacement of the option   </li>
  </ul>
</blockquote>



<h3 id="standards-of-naming-migration-tables"><strong>Standards of naming migration tables</strong></h3>

<hr>

<blockquote>
  <ul>
  <li>The standards of naming a migration table to work efficiently with Meteor while patch and rollback is that, the name of the table should be prefixed by <font color="red"><strong>JaduMigrations</strong> </font> suffixed by the product/module name <font color="red"><strong>Core</strong> </font> (or) <font color="red"><strong>Xfp</strong> </font> (or) <font color="red"><strong>Epayments</strong> </font> <br>
  <strong>Be aware of casing, the prefix JaduMigrations should alway look  same and the name of product/module should be in a casing structure that only its first letter can be capitalised</strong></li>
  </ul>
</blockquote>



<h2 id="baseline"><strong>Baseline</strong></h2>



<h3 id="what-is-baseline"><strong>What is Baseline?</strong></h3>

<p>The baseline is nothing else than a frozen picture of your project at a particular point time. <br>
Baseline is the feature that store the state of each file on the package into a single file called <em>baseline.properties</em> as a particular point time.  <br>
<em>baseline.properties</em> file consist of all file in the package and its relevant MD5. <br>
If any file is altered the MD5 of the file changed and hence its easy to file the file that are altered after the creation of baseline. <br>
This feature help to reduce the size of the package</p>



<h3 id="how-to-create-a-baseline"><strong>How  to create a baseline?</strong></h3>

<p>Once you have pulled the meteor dependency into your project use composer, run</p>



<pre class="prettyprint"><code class=" hljs lua">ant <span class="hljs-built_in">package</span>.create-baseline</code></pre>

<p>or look for the relevant  task on build.xml, to see all task and its description. Meteor comes with a number of re-usable Ant scripts to help standardise the build process between projects. Tasks are run using ant <code>&lt;taskname&gt;</code>. To see the list of available tasks:</p>



<pre class="prettyprint"><code class=" hljs lasso">ant <span class="hljs-attribute">-p</span></code></pre>

<p>Running the above command results in a file <strong>baseline.properties</strong> in <em>output</em> directory <br>
Copy the file <strong>baseline.properties</strong> to your project’s root directory and commit it.</p>



<h2 id="creating-a-package"><strong>Creating a Package</strong></h2>

<hr>

<p>The important task of any software development company is delivering their new product to the customer properly. The deliverable package is normally referred as binary package. In Meteor, packages are generated from the version control repository using a build language, <a href="http://ant.apache.org/">Apache ANT</a>. This package is deployed to client using the meteor console application. </p>

<p>Creation of package is a ant tasks and it depends upon the <code>build.xml</code>. There are two types of packages that can be created</p>

<ol>
<li>Full package</li>
<li>Baselined package</li>
</ol>



<h3 id="full-package"><strong>Full package</strong></h3>

<p>Full package is a binary package that contains all script in the repository  that matches the file set defined on the <code>build.xml</code>. The first patch after a installation to a customer should be a <code>full-package</code>. </p>



<h4 id="how-do-i-create-a-full-package"><strong>How do I create a full package</strong></h4>

<p>The base task used to create a full package is defined in the package.xml within Meteor.  The package requires two property, <br>
1. Package name <br>
2. Fileset</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">target</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"package"</span> <span class="hljs-attribute">depends</span>=<span class="hljs-value">"package.load-version-number"</span> <span class="hljs-attribute">description</span>=<span class="hljs-value">"Create a package "</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">fileset</span> <span class="hljs-attribute">id</span>=<span class="hljs-value">"fileset.files"</span> <span class="hljs-attribute">dir</span>=<span class="hljs-value">"${basedir}"</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">include</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"jadu/**"</span> /&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">include</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"public_html/**"</span> /&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">include</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"var/**"</span> /&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">include</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"CLIENT_VERSION"</span> /&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">exclude</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"public_html/site/styles/widget/**"</span> /&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">fileset</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"package-name"</span> <span class="hljs-attribute">value</span>=<span class="hljs-value">"package.zip"</span> /&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"package-fileset"</span> <span class="hljs-attribute">value</span>=<span class="hljs-value">"fileset.files"</span> /&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">antcall</span> <span class="hljs-attribute">target</span>=<span class="hljs-value">"package.package"</span> /&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">target</span>&gt;</span></code></pre>



<h2 id="baselined-package">#<strong>Baselined package</strong></h2>

<hr>



<h2 id="meteor-for-deployment-tasks"><strong>Meteor for deployment tasks</strong></h2>

<hr>



<h3 id="patching"><strong>Patching</strong></h3>

<hr>

<blockquote>
  <ul>
  <li>When you run the command during deployment, Doctrine will know exactly which migrations it hasn’t run yet by looking at the migration table.</li>
  </ul>
</blockquote>



<h3 id="remove-meteor-lock"><strong>Remove Meteor lock</strong></h3>

<hr>

<p>This command is used when you see the following error while performing patch or rollback, </p>



<pre class="prettyprint"><code class=" hljs lasso">Creating lock file



  <span class="hljs-preprocessor">[</span>RuntimeException<span class="hljs-preprocessor">]</span><span class="hljs-markup">
  Unable to create lock file. This may be due to failure during a previous attempt to apply this package.



patch:apply </span><span class="hljs-preprocessor">[</span><span class="hljs-subst">--</span>path<span class="hljs-subst">=</span><span class="hljs-string">"..."</span><span class="hljs-preprocessor">]</span><span class="hljs-markup"> </span><span class="hljs-preprocessor">[</span><span class="hljs-subst">--</span>patch<span class="hljs-attribute">-path</span><span class="hljs-subst">=</span><span class="hljs-string">"..."</span><span class="hljs-preprocessor">]</span><span class="hljs-markup"> </span><span class="hljs-preprocessor">[</span><span class="hljs-subst">--</span><span class="hljs-keyword">skip</span><span class="hljs-attribute">-lock</span><span class="hljs-preprocessor">]</span><span class="hljs-markup"> </span><span class="hljs-preprocessor">[</span><span class="hljs-subst">--</span>post<span class="hljs-attribute">-slack</span><span class="hljs-preprocessor">]</span><span class="hljs-markup"> </span><span class="hljs-preprocessor">[</span><span class="hljs-subst">--</span><span class="hljs-keyword">skip</span><span class="hljs-attribute">-migrations</span><span class="hljs-preprocessor">]</span><span class="hljs-markup"> </span><span class="hljs-preprocessor">[</span><span class="hljs-subst">--</span><span class="hljs-keyword">skip</span><span class="hljs-attribute">-file</span><span class="hljs-attribute">-migrations</span><span class="hljs-preprocessor">]</span><span class="hljs-markup"> </span><span class="hljs-preprocessor">[</span><span class="hljs-subst">--</span>db<span class="hljs-attribute">-name</span><span class="hljs-subst">=</span><span class="hljs-string">"..."</span><span class="hljs-preprocessor">]</span><span class="hljs-markup"> </span><span class="hljs-preprocessor">[</span><span class="hljs-subst">--</span>db<span class="hljs-attribute">-user</span><span class="hljs-subst">=</span><span class="hljs-string">"..."</span><span class="hljs-preprocessor">]</span><span class="hljs-markup"> </span><span class="hljs-preprocessor">[</span><span class="hljs-subst">--</span>db<span class="hljs-attribute">-password</span><span class="hljs-subst">=</span><span class="hljs-string">"..."</span><span class="hljs-preprocessor">]</span><span class="hljs-markup"> </span><span class="hljs-preprocessor">[</span><span class="hljs-subst">--</span>db<span class="hljs-attribute">-host</span><span class="hljs-subst">=</span><span class="hljs-string">"..."</span><span class="hljs-preprocessor">]</span><span class="hljs-markup"> </span><span class="hljs-preprocessor">[</span><span class="hljs-subst">--</span>db<span class="hljs-attribute">-driver</span><span class="hljs-subst">=</span><span class="hljs-string">"..."</span><span class="hljs-preprocessor">]</span><span class="hljs-markup"></span></code></pre>

<p>This is common error that occurs when someone else is performing patch or rollback action on the server or there was something wrong went in previous meteor execution. If you are sure that the state of server is perfect then you can remove the lock use the following command.</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">php meteor.phar patch:<span class="hljs-built_in">clear</span>-lock <span class="hljs-comment">--path={JADU_HOME}</span></code></pre>



<h3 id="set-permission-to-patch-files"><strong>Set permission to patch files</strong></h3>

<p>If you want to set permission to the current patched files, then you can use this command. </p>

<p>The command to invoke the above function in meteor is</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">php meteor.phar patch:<span class="hljs-built_in">set</span>-permissions <span class="hljs-comment">--path={JADU_HOME}</span></code></pre>



<h3 id="reset-permission-of-files-on-the-server">Reset permission of files on the server</h3>

<p>If you want to reset the permission of the files that resides in the server, you can use this command. </p>

<blockquote>
  <p><strong>Note:</strong> <br>
  It does not reset all permissions to all files in the Jadu Home, it tries to reset only group permissions to those list of files that are listed in config folder of Jadu.  </p>
</blockquote>

<p>The command to invoke the above function in meteor is</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">php meteor.phar <span class="hljs-built_in">set</span>-permissions <span class="hljs-comment">--path={JADU_HOME}</span></code></pre>



<h3 id="file-migration-command"><strong>File Migration Command</strong></h3>

<hr>

<p>The a command to execute file migrations separately was introduced in Meteor 0.5.  <br>
<strong>file-migrations:migrate</strong></p>

<p>To run migrations from x version to y, then you can use <strong>file-migrations:migrate</strong> command</p>



<h3 id="commands-related-to-migrations-imported-from-doctrine"><strong>Commands related to migrations imported from Doctrine</strong></h3>

<hr>



<h2 id="faqs"><strong><i class="icon-pencil">FAQs</i></strong></h2>

<hr>

<p> <strong>Release Notes of Meteor</strong></p>



<pre class="prettyprint"><code class=" hljs mathematica">Meteor <span class="hljs-number">0.1</span>
 <span class="hljs-number">1.</span> <span class="hljs-keyword">Skip</span> Lock file</code></pre>



<pre class="prettyprint"><code class=" hljs livecodeserver">Meteor <span class="hljs-number">0.2</span>

 <span class="hljs-number">1.</span> Skip database migration 
 <span class="hljs-number">2.</span> Logging <span class="hljs-operator">in</span> Slack 
 <span class="hljs-number">3.</span> Disk <span class="hljs-constant">space</span> validation 
 <span class="hljs-number">4.</span> Reading <span class="hljs-keyword">system</span>.xml <span class="hljs-built_in">to</span> retrieve <span class="hljs-operator">the</span> database details</code></pre>



<pre class="prettyprint"><code class=" hljs livecodeserver">Meteor <span class="hljs-number">0.3</span>

 <span class="hljs-number">1.</span> File Migrations <span class="hljs-keyword">as</span> separate module 
 <span class="hljs-number">2.</span> Skip File Migration 
 <span class="hljs-number">3.</span> Maintaining all previous backups 
 <span class="hljs-number">4.</span> Rollback <span class="hljs-built_in">to</span> <span class="hljs-keyword">any</span> <span class="hljs-operator">of</span> <span class="hljs-operator">the</span> preferred backup 
 <span class="hljs-number">5.</span> Undo Rollback 
 <span class="hljs-number">6.</span> Verifying <span class="hljs-operator">the</span> <span class="hljs-built_in">files</span> that are copied <span class="hljs-built_in">to</span> backup <span class="hljs-operator">and</span> live <span class="hljs-built_in">directory</span> 
 <span class="hljs-number">7.</span> Creation <span class="hljs-operator">of</span> <span class="hljs-operator">an</span> <span class="hljs-constant">empty</span> migration <span class="hljs-built_in">file</span> <span class="hljs-keyword">without</span> <span class="hljs-keyword">any</span> config details</code></pre>



<pre class="prettyprint"><code class=" hljs ">Meteor 0.4

</code></pre>

<p>Meteor 0.5</p>

<ol>
<li>Automatic detection of database driver.</li>
<li>New command for executing file migration alone</li>
<li>New way to handle dependancies</li>
<li>Skip Doctrine’s migration conformation question if no migrations are to be executed</li>
<li>Including a option to patch or rollback without checking the previous state (version) of the sever</li>
<li>Patch dot file like .htaccess</li>
<li>Added the addition exception message on request from support team</li>
<li>Terminate the support of –quiet option</li>
<li>Terminate meteor support if the database is DSN configured</li>
<li>Setting Permissions on Window platform</li>
<li>Show readable backup version on list on rollback</li>
<li>Enables XFP Rollback by fixing table not found exception</li>
<li>Fixed issue where file system migrations get skipped in WISP.</li>
</ol>

<p><strong>What are file migrations? Why are they needed?</strong></p>

<p>File migrations are a custom type of migration that Meteor supports, which are not a part of Doctrine migrations. File migrations are migrations that involve changes in files alone, not to the database. The main reason this feature is needed is to execute such migrations on all servers on which the Jadu CMS application is installed. If a client has multiple web nodes and the file system is not a Network File System (NFS) then the file changes need to be applied to each web node.</p>

<p>If a Doctrine migration is used then the first node on which the patch or rollback is executed will get the appropriate changes and on the other web nodes the changes would have to be manually deployed. So the basic concept of file migration is to extract or separate the file changes from the database migrations.</p>

<p><strong>How does Meteor differentiate between file migrations and database migrations?</strong> <br>
    Any migration class file within ‘upgrades/migrations/filesystem’ is considered to be a file migration and the migration class files in ‘upgrades/migrations’ are considered to be database migration files</p>

<p><strong>Is the usage of migrations:migrate command when MySQL goes away is same as migrations executed as a part of patch:apply command?</strong> <br>
    No, they aren’t same, migrations:migrate is only one of the steps. After the migrations:migrate command is executed, the current status of the migration should be determined and the status file that exists in the Jadu path should be updated. The current status of the migration can be determined using the migration:status command.</p>



<h1 id="common-issues-encounter-while-running-meteor"><strong>Common issues encounter while running Meteor</strong></h1>

<hr>



<h3 id="error-apc-error-cannot-redeclare-class"><strong>error: [apc-error] Cannot redeclare class</strong></h3>

<p>If you get error: [apc-error] Cannot redeclare class… There is an issue with composer and apc Run the above script by overriding the php.ini value for apc.enable_cli in order to switch it off.</p>

<p>The command to do so:</p>



<pre class="prettyprint"><code class=" hljs lasso">php <span class="hljs-attribute">-d</span> apc<span class="hljs-built_in">.</span>enable_cli<span class="hljs-subst">=</span><span class="hljs-number">0</span> meteor<span class="hljs-built_in">.</span>phar patch:apply <span class="hljs-subst">--</span>path<span class="hljs-subst">=</span>/<span class="hljs-built_in">var</span>/www/jadu <span class="hljs-subst">--</span>patch<span class="hljs-attribute">-path</span><span class="hljs-subst">=</span>/home/jadu_patch</code></pre>



<h3 id="suhosin-enabled-on-server"><strong>Suhosin  enabled on server</strong></h3>

<p>If the server is Suhosin  enabled. Run the following command:</p>



<pre class="prettyprint"><code class=" hljs avrasm">php -d suhosin<span class="hljs-preprocessor">.executor</span><span class="hljs-preprocessor">.include</span><span class="hljs-preprocessor">.whitelist</span>=phar meteor<span class="hljs-preprocessor">.phar</span> patch:apply --path=/var/www/jadu --patch-path=/home/jadu_patch</code></pre>



<h3 id="phar-extension-disabled"><strong>phar extension disabled</strong></h3>

<p>If phar extension is disabled and the version of PHP on the server is greater than 5.3. </p>



<pre class="prettyprint"><code class=" hljs lasso">php <span class="hljs-attribute">-d</span> extension<span class="hljs-subst">=</span>phar<span class="hljs-built_in">.</span>so  meteor<span class="hljs-built_in">.</span>phar patch:apply <span class="hljs-subst">--</span>path<span class="hljs-subst">=</span>/<span class="hljs-built_in">var</span>/www/jadu <span class="hljs-subst">--</span>patch<span class="hljs-attribute">-path</span><span class="hljs-subst">=</span>/home/jadu_patch</code></pre>



<h3 id="mysql-timeout"><strong>MySQL Timeout</strong></h3>

<p>If you get error: [PDOException] SQLSTATE[HY000]: General error: 2006 MySQL server has gone away</p>

<p>The issue is due to the connection timeout.</p>

<p><strong>Immediate step to be taken is to Rollback the failure patch</strong></p>

<p>There are 3 steps that needs to be carried for a proper and successful patch</p>

<p><strong>Step 1:</strong> Run the patch without any migration using the option –skip-migration option</p>

<p><code>php meteor.phar patch:apply --path={JADU_PATH} --skip-migration --skip-file-migrations</code></p>

<p><strong>Step 2:</strong> Run the both database and file migrations ,</p>

<p>To execute database migrations use <br>
<code>php meteor.phar migrations:migrate --path={JADU_PATH}</code></p>

<p>To run the file migrations [To run file migration you need meteor 0.5 or greater version ] <br>
<code>php meteor.phar file-migrations:migrate --path={JADU_PATH}</code></p>

<p><strong>Step 3:</strong> Update the migrations version on the respective status file.</p>

<blockquote>
  <p><strong>Note</strong> <br>
  The version just is executed at the end of each process is their respective current migration version. Update the respective file with the proper version.  If this step is missed the rollback might become a night mare.</p>
  
  <blockquote>
    <p><strong>Example:</strong>  <br>
    CORE_MIGARTION_NUMBER –&gt; holds the current version of the database migration of Jadu CMS.</p>
    
    <p>CORE_FILE_MIGARTION_NUMBER –&gt; holds the current version of the file migration of Jadu CMS .</p>
    
    <p>XFP_MIGARTION_NUMBER –&gt; holds the current version of the database migration of XFP module.</p>
    
    <p>XFP_FILE_MIGARTION_NUMBER –&gt; holds the current version of the file migration of JXFP module.</p>
  </blockquote>
</blockquote>

<p><strong>Another method</strong></p>

<p>If the system administrator are ok with increasing the  MySql time out , increase the value of wait_timeout from</p>

<p><code>wait_timeout= 60</code> to <code>wait_timeout= 200</code></p>



<h2 id="other-ant-tasks">Other Ant Tasks</h2>