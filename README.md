Download Link: https://assignmentchef.com/product/solved-operating-systems-laboratory-cs39002-assignment-2
<br>
<strong>Assignment 2: </strong>Implementation of a rudimentary command-line interpreter running on the Linux operating system

Implement a shell that will run as an application program on top of the Linux kernel. The shell will accept user commands (one line at a time), and execute the same. The following features must be implemented:

<ol>

 <li><strong>Run an external command:</strong> The external commands refer to executables that are stored as files. They have to be executed by spawning a child process and invoking <strong>execlp()</strong> or some similar system calls. Example user commands:</li>

</ol>

<strong>  ./a.out myprog.c   cc –o myprog myprog.c   ls -l</strong>

<ol>

 <li><strong>Run an external command by redirecting standard input from a file: </strong>The symbol “<strong>&lt;</strong>” is used for input redirection, where the input will be read from the specified file and not from the keyboard. You need to use a system call like “<strong>dup()</strong>” or “<strong>dup2()</strong>” to carry out the redirection. Example user command:</li>

</ol>

<strong>  ./a.out &lt; infile.txt   sort &lt; somefile.txt</strong>

<ol>

 <li><strong>Run an external command by redirecting standard output to a file: </strong>The symbol “<strong>&gt;</strong>” is used for output redirection, where the output will be written to the specified file and not to the screen. You need to use a system call like “<strong>dup()</strong>” or “<strong>dup2()</strong>” to carry out the redirection. Example user commands:</li>

</ol>

<strong>  ./a.out &gt; outfile.txt   ls &gt; abc</strong>

<ol>

 <li><strong>Combination of input and output redirection:</strong> Here we use both “&lt;” and “&gt;” to specify both types of redirection. Example user command:</li>

</ol>

<strong>  ./a.out &lt; infile.txt &gt; outfile.txt</strong>

<ol>

 <li><strong>Run an external command in the background with possible input and output redirections: </strong>We use the symbol “<strong>&amp;</strong>” to specify running in the background. The shell prompt will appear and the next command can be typed while the command is being executed in the background. Example user commands:</li>

</ol>

<strong>  ./a.out &amp;   ./myprog &lt; in.txt &gt; out.txt &amp;</strong>

<ol>

 <li><strong>Run several external commands in the pipe mode: </strong>The symbol “<strong>|</strong>” is used to indicate pipe mode of execution. Here, the standard output of one commands will be redirected to the standard input of the next command, in sequence. You</li>

</ol>

need to use the “<strong>pipe()</strong>” system call to implement this feature. Example user commands: <strong>  ls | more   cat abc.c | sort | more</strong>

<ol>

 <li><strong>Run several external commands in the pipe mode and with possible input and output redirection in background: </strong>This is a combination of the earlier modes. Example user commands: <strong>ls | cat &gt; outfile.txt &amp;</strong></li>

</ol>

<strong>  cat &lt; abc.c | sort | cat &gt; outfile.txt &amp;</strong>




<strong>Implementation Guideline:</strong>

For redirecting the standard input or output, you can refer to the book: “<em>Design of the Unix</em>

<em>Operating System</em>” by Maurice Bach. Actually, the kernel maintains a file descriptor table or FDT (one per process), where the first three entries (index 0, 1 and 2) correspond to standard input (<strong>stdin</strong>), standard output (<strong>stdout</strong>), and standard error (<strong>stderr</strong>). When files are opened, new entries are created in the table. When a file is closed, the corresponding entry is logically deleted. There is a system call <strong>dup(xyz)</strong>, which takes a file descriptor <strong>xyz</strong> as its input and copies it to the first empty location in FDT. So if we write the two statements: <strong>close(stdin); dup(xyz);</strong> the file descriptor <strong>xyz</strong> will be copied into the FDT entry corresponding to <strong>stdin</strong>. If the program wants to read something from the keyboard, it will actually get read from the file corresponding to <strong>xyz</strong>. Similarly, to redirect the standard output, we have to use the two statements: <strong>close(stdout); dup(xyz);</strong>

Normally, when the parent forks a child that executes a command using <strong>execlp()</strong> or <strong>execvp()</strong>, the parent calls the function <strong>wait()</strong>, thereby waiting for the child to terminate. Only after that, it will ask the user for the next command. However, if we want to run a program in the background, we do not give the <strong>wait()</strong>, and so the parent asks for the next command even while the child is in execution.

A pipe between two processes can be created using the <strong>pipe()</strong> system call, followed by input and output redirection. Consider the command: <strong>ls | more</strong>. The parent process finds out there is a pipe between two programs, creates a pipe, and forks two child processes (say, X and Y). X will redirect its standard output to the output end of the pipe (using <strong>dup()</strong>), and then call <strong>execlp() </strong>or <strong>execvp() </strong>to execute <strong>ls</strong>. Similarly, Y will redirect its standard input to the input end of the pipe (again using <strong>dup()</strong>), and then call <strong>execlp() </strong>or <strong>execvp() </strong>to execute <strong>more</strong>. If there is a pipe command involving N commands in general, then you need to create N-1 pipes, create N child processes, and connect each pair of consecutive child processes by a pipe.




<strong>Submission Guideline:</strong>

<ul>

 <li>Create a single program for the assignment, and name it Ass2_&lt;groupno&gt;.c or .cpp (replace &lt;groupno&gt; by your group number), and upload it.</li>

 <li>Submit an interim version within January 21, 2019 for intermediate evaluation.</li>

 <li>You must show the running version of the program(s) to your assigned TA during the lab hours.</li>

</ul>

<strong>Evaluation Guidelines: </strong>

While entering marks, the partwise break up should also be entered according to the marking guidelines given below. There is a separate component for individual assessment, based on how the student answers questions.




<table width="435">

 <tbody>

  <tr>

   <td width="26"><strong>Sl</strong></td>

   <td width="363"><strong>Items</strong></td>

   <td width="46"><strong>Marks</strong></td>

  </tr>

  <tr>

   <td width="26"><strong>(a)</strong></td>

   <td width="363">Process creation and running an external command</td>

   <td width="46">8</td>

  </tr>

  <tr>

   <td width="26"><strong>(b)</strong></td>

   <td width="363">Redirection of stdin</td>

   <td width="46">8</td>

  </tr>

  <tr>

   <td width="26"><strong>(c)</strong></td>

   <td width="363">Redirection of stdout</td>

   <td width="46">5</td>

  </tr>

  <tr>

   <td width="26"><strong>(d)</strong></td>

   <td width="363">Redirection of stdin and stdout together</td>

   <td width="46">3</td>

  </tr>

  <tr>

   <td width="26"><strong>(e)</strong></td>

   <td width="363">Running an external command in background</td>

   <td width="46">8</td>

  </tr>

  <tr>

   <td width="26"><strong>(f)</strong></td>

   <td width="363">Running an external command in background with I/O redirection</td>

   <td width="46">8</td>

  </tr>

  <tr>

   <td width="26"><strong>(g)</strong></td>

   <td width="363">Running several external commands in pipe mode</td>

   <td width="46">12</td>

  </tr>

  <tr>

   <td width="26"><strong>(h)</strong></td>

   <td width="363">Running several external commands in pipe mode with possible I/O redirection</td>

   <td width="46">8</td>

  </tr>

  <tr>

   <td width="26"><strong>(i)</strong></td>

   <td width="363">Avoiding simultaneous redirection to pipe and file and other programming aspects</td>

   <td width="46">5</td>

  </tr>

  <tr>

   <td width="26"> </td>

   <td width="363"><strong>Total</strong></td>

   <td width="46">65</td>

  </tr>

 </tbody>

</table>


