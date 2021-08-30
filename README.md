# Bruteforcing-SSH
This is a demo which will exploit SSH on a linux host. Once SSH access is gained, privilege escalation is used to become root. 

# First download a list of common users & weak passwords
curl https://download.weakpass.com/wordlists/90/rockyou.txt.gz | gunzip > /tmp/passwords.txt
curl https://raw.githubusercontent.com/danielmiessler/SecLists/master/Usernames/top-usernames-shortlist.txt > /tmp/users.txt

# Just so you are familiar, take at the content and format of the files 
`head /tmp/passwords.txt`
`head /tmp/users.txt`

# Download the latest version of Metasploit Framework (OSS)
`curl https://raw.githubusercontent.com/rapid7/metasploit-omnibus/master/config/templates/metasploit-framework-wrappers/msfupdate.erb > msfinstall`   

# set correct Permissions 
`chmod 755 msfinstall`

# Run the install script
`./msfinstall`

# By default metasploit framework installs it's self to `/opt/` let's take a look and add that to our path:
``ls /opt/
google  metasploit-framework``

`echo $PATH`
/home/mhillis/.local/bin:/home/mhillis/bin:/usr/share/Modules/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/var/lib/snapd/snap/bin

# Add the /opt/metasploit-framework/bin/ directory to our path
`PATH=/opt/metasploit-framework/bin/:$PATH`

# Add Metasploit repo to our systems and make sure we have the latest version of metasploit
`msfupdate`

# Launch the metasploit framework console
`msfconsole`

# List the available exploits modules for metasploit
`show exploits`

# Because there are over 2000 exploits it may make more sense to search for one we're looking for
`search ssh login`

# use the auxiliary/scanner/ssh/ssh_login module
`use auxiliary/scanner/ssh/ssh_login'

# List the options available for this module
`options`

# Define some of these module options to launch a brute force attack against a target host
`set pass_file /tmp/password.txt`
