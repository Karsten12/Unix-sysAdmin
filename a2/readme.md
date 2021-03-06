<!DOCTYPE html>
<meta charset="utf-8">

<meta http-equiv="Content-Type" content="text/html;charset=utf-8" >

<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/github-markdown-css/2.8.0/github-markdown.min.css">

<style>
	.markdown-body {
		box-sizing: border-box;
		min-width: 200px;
		max-width: 980px;
		margin: 0 auto;
		padding: 45px;
	}

	@media (max-width: 767px) {
		.markdown-body {
			padding: 15px;
		}
	}
</style>

<title>Lab 2 - Packages and Packaging and Troubleshooting - Hands-On UNIX System Administration DeCal</title>

<article class="markdown-body">
  <h1>Lab 2 - Packages and Packaging and Troubleshooting</h1>
  <h2 id="debian-an-introduction-to-apt-and-dpkg">Debian: An Introduction to <code class="highlighter-rouge">apt</code> and <code class="highlighter-rouge">dpkg</code></h2>

<p>In this class, we will be focused on using Debian. As noted before, Debian uses apt/dpkg as its package manager. Other distributions use different package managers.</p>

<p>The frontend package manager is <code class="highlighter-rouge">apt</code>. It will be what you’ll be using for 99.9% of the time. Before you do anything, it is good package to update the package list so your package manager can find and fetch the most up-to-date packages. To do that, run:</p>

<p><code class="highlighter-rouge">apt update</code></p>

<p>To find the package to install:</p>

<p><code class="highlighter-rouge">apt-cache search [package|description]</code></p>

<p>To install a package, run:</p>

<p><code class="highlighter-rouge">apt install package</code></p>

<p>To remove a package, run:</p>

<p><code class="highlighter-rouge">apt remove package</code></p>

<p>Easy? Sometimes, you want to update the packages that you have installed, run:</p>

<p><code class="highlighter-rouge">apt dist-upgrade</code></p>

<p>There are also other commands, such as removing unneeded dependencies and purging packages but that is what the man pages are for. Please note that you are going to have to use <code class="highlighter-rouge">sudo</code> for the above commands since you are actually modifying the system itself.</p>

<p>The backend package manager is <code class="highlighter-rouge">dpkg</code>. Traditionally, we use <code class="highlighter-rouge">dpkg</code> to install local packages. We also can inspect packages and fix broken installs. To install local packages, run:</p>

<p><code class="highlighter-rouge">dpkg -i packagefilename</code></p>

<p>To remove a system package:</p>

<p><code class="highlighter-rouge">dpkg --remove package</code></p>

<p>To inspect a package for more information about the package:</p>

<p><code class="highlighter-rouge">dpkg -I packagefilename</code></p>

<p>To fix/configure all unpacked but unfinished installs:</p>

<p><code class="highlighter-rouge">dpkg --configure -a</code></p>

<h2 id="getting-started">Getting Started</h2>

<p>We are going to use GCC to compile source code and a simple utility called <code class="highlighter-rouge">fpm</code> to create packages in this lab.</p>

<p>Using the commands above, install <code class="highlighter-rouge">gcc</code>, <code class="highlighter-rouge">ruby-dev</code>, and <code class="highlighter-rouge">ruby-ffi</code>.</p>

<p>Now check if GCC is installed by typing the followng:</p>

<p><code class="highlighter-rouge">gcc --version</code></p>

<p>Now install <code class="highlighter-rouge">fpm</code> using <code class="highlighter-rouge">gem</code>, Ruby’s own package manager:</p>

<p><code class="highlighter-rouge">sudo gem install fpm</code></p>

<p>Now check if <code class="highlighter-rouge">fpm</code> is installed:</p>

<p><code class="highlighter-rouge">fpm</code></p>

<p>Now clone the <code class="highlighter-rouge">decal-web</code> repository:</p>

<p><code class="highlighter-rouge">git clone https://github.com/0xcf/decal-labs.git</code></p>

<h2 id="exercise-1-compiling-and-packaging">Exercise 1: Compiling and Packaging</h2>

<p>Packaging manually for Debian can be very hard and frustrating, especially for first timers. That’s why for this class, we’ll be using a really cool Ruby package called fpm which simplifies the task of packaging a lot. Please note that if you were going to package for Debian’s own repositories, this is NOT the correct/formal way to go about packaging. It is merely a great way to backport or package your own applications extremely quickly.</p>

<p>Now we will create a very simple package using the hellopenguin executable that you made above. First, move into the <code class="highlighter-rouge">a2</code> folder in the repository that you cloned in the Getting Started section:</p>

<p><code class="highlighter-rouge">cd decal-labs/a2</code></p>

<p>Now we are going to create a folder to work in for this exercise:</p>

<p><code class="highlighter-rouge">mkdir ex1</code></p>

<p>And now move into the folder:</p>

<p><code class="highlighter-rouge">cd ex1</code></p>

<h3 id="compiling-the-program">Compiling the program</h3>

<p>Now, we will make a very simple application in C that prints “Hello Penguin!” named hellopenguin. Invoke:</p>

<p><code class="highlighter-rouge">nano hellopenguin.c</code></p>

<p>And then type in the following:</p>

<div class="highlighter-rouge"><div class="highlight"><pre class="highlight"><code>#include &lt;stdio.h&gt;

int main()

{

   printf("Hello Penguin!\n");

   return 0;

}
</code></pre></div></div>

<p>Now save and exit.</p>

<p>We will now compile the source file that you have just written:</p>

<p>gcc hellopenguin.c -o hellopenguin</p>

<p>What this does is to take in a source file hellopenguin.c and compile it to an executable named hellopenguin with the -o output flag.</p>

<h3 id="packaging-the-executable">Packaging the executable</h3>

<p>Now, we will create the folder structure of where the executable show reside in. In Debian, user-level packages usually reside in the folder <code class="highlighter-rouge">/usr/bin/</code>:</p>

<p><code class="highlighter-rouge">mkdir -p packpenguin/usr/bin</code></p>

<p>Now move your hellopenguin into the <code class="highlighter-rouge">packpenguin/usr/bin/</code> folder.</p>

<p><code class="highlighter-rouge">mv hellopenguin packpenguin/usr/bin/</code></p>

<p>Now we will create a package called hellopenguin. Move into the parent directory of the  hellopenguin folder and invoke the following:</p>

<p><code class="highlighter-rouge">fpm -s dir -t deb -n hellopenguin -v 1.0~ocf1 -C packpenguin</code></p>

<p>This specifies that you want to take in a directory, using the <code class="highlighter-rouge">-s</code> flag, and to output a <code class="highlighter-rouge">.deb</code> package using the <code class="highlighter-rouge">-t</code> flag. It takes in a directory called <code class="highlighter-rouge">packpenguin</code>, using the <code class="highlighter-rouge">-C</code> flag, and output a <code class="highlighter-rouge">.deb</code> file named <code class="highlighter-rouge">hellopenguin</code>, using the <code class="highlighter-rouge">-n</code>, with a version number of <code class="highlighter-rouge">1.0~ocf1</code>, using the <code class="highlighter-rouge">-v</code> flag.</p>

<p>Now test it by invoking apt and installing it:</p>

<p><code class="highlighter-rouge">sudo dpkg -i ./hellopenguin_1.0~ocf1_amd64.deb</code></p>

<p>Now you should be able to run hellopenguin by doing the following:</p>

<p><code class="highlighter-rouge">hellopenguin</code></p>

<h2 id="exercise-2-troubleshooting">Exercise 2: Troubleshooting</h2>

<p>Now we are going to try and troubleshoot a package. Move to the other folder. Use the following command if you are in the <code class="highlighter-rouge">ex1</code>:</p>

<p><code class="highlighter-rouge">cd ../ex2</code></p>

<p>Try installing the <code class="highlighter-rouge">ocfspy</code> package using <code class="highlighter-rouge">dpkg</code>. It should error. Take note what it is erroring on! Now try and fix it.</p>

<p>Hint: You should be missing a dependency. Inspect the package for more details. The file to create that application is in the folder. Try compiling and packaging it. Check exercise 1!</p>

<h2 id="conclusion">Conclusion:</h2>

<p>After you’re done, complete <a href="https://docs.google.com/forms/d/1meH6lUC77qta9kiH80dPmkEQJcQqNMV3PrqDN_f3DFg">this</a> submission form.</p>

<p>Note that you may want to clean up your VM by removing <code class="highlighter-rouge">hellopenguin</code>, <code class="highlighter-rouge">ocfdocs</code>, and <code class="highlighter-rouge">ocfspy</code> from /usr/bin.</p>


  <br>
<footer>
  <div class="row">
    <div class="col-lg-12 text-center">
      <p>
        <a href="https://www.digitalocean.com">
          <img src="/images/digitalocean.png" style="height: 34px; width: 200px;" />
        </a>
        With great appreciation to
        <a href="https://www.digitalocean.com">DigitalOcean</a> for sponsoring the
        VMs used in both tracks of the DeCal
      </p>

      <p>
        <a href="https://www.ocf.berkeley.edu">
          <img src="https://www.ocf.berkeley.edu/hosting-logos/ocf-hosted-penguin.svg"
               alt="Hosted by the OCF" style="border: 0;" />
        </a>
        <a href="https://creativecommons.org/licenses/by-nc-sa/4.0/">
          Copyright CC BY-NC-SA 4.0
        </a>
        &copy; Open Computing Facility, eXperimental Computing Facility
      </p>
    </div>
    <!-- /.col-lg-12 -->
  </div>
  <!-- /.row -->
</footer>

</article>

