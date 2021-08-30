# Bruteforcing-SSH
This is a demo which will exploit SSH on a linux host. Once SSH access is gained, privilege escalation is used to become root. 

# Download a wordlist

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
msfupdate

