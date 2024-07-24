# wsl-utilities
Collection of various utilities, scripts, and files that have been useful to me on my journey using WSL2

## Useful Paths and files

#### General
`/home/<user>` -> User directory. I prefer to work here. <br>
`/home/<user>/.bashrc`  ->  Configuration file for the bash shell on unix systems <br>
`/var/log` -> Log files for various programs and system services in Linux. <br>
`/mnt/<disk>` -> Access point to windows disks from wsl. <br>


#### Apache
`/var/www` -> Default Apache path for storing server files. <br>
`/etc/apache2` -> Apache configuration path. <br>
`/etc/apache2/apache2.conf` -> Apache main configuration file. <br>


## Commands

#### System
`sudo` -> Executes a command with superuser privileges. <br>
`cd` -> Changes the current directory to the specified directory.<br>
`pwd` -> Prints the current working directory.<br>
`cp` -> Copies files or directories from one location to another.<br>
`mv` -> Moves or renames files or directories.<br>
`rm` -> Removes (deletes) files or directories.<br>
`mkdir` -> Creates a new directory.<br>
`rmdir` -> Removes an empty directory.<br>
`touch` -> Creates an empty file or updates the timestamp of an existing file.<br>
`cat` -> Concatenates and displays the contents of files.<br>
`grep` -> Searches for a specified pattern within files.<br>
`chmod` -> Changes the permissions of files or directories.<br>
`chown` -> Changes the owner and/or group of files or directories.<br>
`ps` -> Displays information about currently running processes.<br>
`kill` -> Sends a signal to terminate a process.<br>
`df` -> Displays information about disk space usage.<br>
`du` -> Displays the disk usage of files and directories.<br>
`tar` -> Archives and compresses files into a tarball.<br>
`wget` -> Downloads files from the web.<br>
`curl` -> Transfers data to or from a server using various protocols.<br>
`echo`-> Displays a line of text or a variable's value.<br>


## Script - Firewall and Ports rules
```
$remoteport = bash.exe -c "ifconfig eth0 | grep 'inet '"
$found = $remoteport -match '\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}';

if( $found ){
  $remoteport = $matches[0];
} else{
  echo "The Script Exited, the ip address of WSL 2 cannot be found";
  exit;
}

#[Ports]

#All the ports you want to forward separated by coma
$ports=@(80,443,10000,3000,5000);


#[Static ip]
#You can change the addr to your ip config to listen to a specific address
$addr='0.0.0.0';
$ports_a = $ports -join ",";


#Remove Firewall Exception Rules
iex "Remove-NetFireWallRule -DisplayName 'WSL 2 Firewall Unlock' ";

#adding Exception Rules for inbound and outbound Rules
iex "New-NetFireWallRule -DisplayName 'WSL 2 Firewall Unlock' -Direction Outbound -LocalPort $ports_a -Action Allow -Protocol TCP";
iex "New-NetFireWallRule -DisplayName 'WSL 2 Firewall Unlock' -Direction Inbound -LocalPort $ports_a -Action Allow -Protocol TCP";

for( $i = 0; $i -lt $ports.length; $i++ ){
  $port = $ports[$i];
  iex "netsh interface portproxy delete v4tov4 listenport=$port listenaddress=$addr";
  iex "netsh interface portproxy add v4tov4 listenport=$port listenaddress=$addr connectport=$port connectaddress=$remoteport";
}
```
