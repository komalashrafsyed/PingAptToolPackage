# Ping Apt Tool Package  Creation
This walkthrough takes you step by step on how to create an apt package from your .NET Core 3.0 application code so that the same apt package can be easily installed on user's client machine using a simple apt-install command.

# Deployment Steps
<b>Step 1:</b> You need to <b>'Download Code'</b> for your .NET Core Application from <a href="https://dev.azure.com/airband/_git/Komal">here</a> </br>

use the <b>git clone codeurl</b> command to download the code to your VM </br>

<b>Step 2:</b> You need a Linux OS based virtual machine, you can use the following command to create one, or you can even use an existing VM </br>

<b> az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --admin-username azureuser --generate-ssh-keys </b>

<b>Step 3:</b> After you ssh into the VM, you can type the following to install the relevant required packages</br>
<b>
$ sudo wget -q https://packages.microsoft.com/config/ubuntu/18.04/packages-microsoft-prod.deb</br>
$ sudo dpkg -i packages-microsoft-prod.deb</br>
$ sudo apt-get install apt-transport-https</br>
$ sudo apt-get update</br>
$ sudo apt-get install dotnet-sdk-3.0</br>
$ sudo dotnet --version</br>


$ git clone https://airband@dev.azure.com/airband/Komal/_git/Komal</br>
#cd into the code folder at PingAsync level</br>
$ sudo dotnet build </br>
$ sudo dotnet publish -c Release --self-contained -r ubuntu.18.04-x64</br>
</b>

#run this 
</br>
$ sudo dotnet tool install -g dotnet-symbol</br>
#it is ok if the following command doesnt run as it if for .NET SDK 2.1
</br>
$ sudo dotnet-symbol --symbols bin/Release/netcoreapp3.0/ubuntu.18.04-x64/publish/</br>


#run the following
$ sudo dotnet tool install -g dotnet-symbol</br>

#skip this if it doesnt run that is fine as well
</br>
$ sudo dotnet-symbol --symbols bin/Release/netcoreapp3.0/ubuntu.18.04-x64/publish/</br>

#RUN THIS!! 
</br>
$ sudo apt install gnupg pbuilder ubuntu-dev-tools apt-file dh-make bzr-builddeb</br>

$ export DEBEMAIL="******syed@microsoft.com"</br>

$ export DEBFULLNAME="PingAsync Tool"</br>

$ sudo dotnet clean</br>

$ sudo rm -rf bin obj</br>

$ cd ..</br>

$ sudo mv PingAsync pingasync-2.0</br>

$ sudo tar cvzf pingasync-2.0.tar.gz pingasync-2.0</br>

$ sudo cd pingasync-2.0</br>

$ sudo dh_make -f ../pingasync-2.0.tar.gz -s -c mit -n</br>

$ cd debian </br>
$ sudo rm *ex *EX </br>
$ sudo rm README README.Debian README.source pingasync-docs.docs </br>

Files Changes

--------------------------------------
<b>changelog</b> file changed </br>
--------------------------------------

pingasync (5.0-0ubuntu1) bionic; urgency=medium

  * Initial Release.

 -- PingAsync Tool <*****syed@microsoft.com>  Wed, 27 Nov 2019 22:37:13 +0000

<img src="https://komalsandboxdiag.blob.core.windows.net/pingarmtemplatereadmefiles/changelog.png" >

--------------------------------------
<b>control</b> file changed </br>
--------------------------------------

Source: pingasync </br>
Section: unknown </br>
Priority: optional</br>
Maintainer: root <******syed@microsoft.com></br>
Build-Depends: debhelper (>= 10)</br>
Standards-Version: 4.1.2</br>
Homepage: <insert the upstream URL, if relevant></br>
#Vcs-Git: https://anonscm.debian.org/git/collab-maint/pingasync.git
</br>
#Vcs-Browser: https://anonscm.debian.org/cgit/collab-maint/pingasync.git
</br>

Package: pingasync</br>
Architecture: any</br>
Depends: ${shlibs:Depends}, ${misc:Depends}</br>
Description: <insert up to 60 chars description></br>
 <insert long description, indented with spaces></br>

<img src="https://komalsandboxdiag.blob.core.windows.net/pingarmtemplatereadmefiles/control.png" >

--------------------------------------
<b>rules</b> file changed </br>
---------------------------------------

#!/usr/bin/make -f
</br>
#export DH_VERBOSE = 1
</br>
%: </br>
	dh $@ --with=systemd </br>


override_dh_auto_build: </br>
	dotnet publish -c Release --self-contained -r ubuntu.18.04-x64 </br>
	# auto-build disabled </br>
override_dh_auto_install: </br>
	# install application 
	</br>
	mkdir -p debian/pingasync/opt/ksyed/pingtool </br>
	install -D -m 755 bin/Release/netcoreapp3.0/ubuntu.18.04-x64/publish/* debian/pingasync/opt/ksyed/pingtool </br>
	rm debian/pingasync/opt/ksyed/pingtool/*.pdb #delete pdb
	</br>

<img src="https://komalsandboxdiag.blob.core.windows.net/pingarmtemplatereadmefiles/rules.png" >

--------------------------------------
<b> pingasync.service </b> file changed </br>
$ sudo vim pingasync.service </br>
--------------------------------------



[Unit] </br>
Description=Ping Async </br>
After=network.target </br>

[Service] </br>
Type=simple </br>
ExecStart=/opt/ksyed/pingtool/pingtool </br>
ExecReload=/bin/kill -HUP $MAINPID </br>

[Install] </br>
WantedBy=multi-user.target </br>


<img src="https://komalsandboxdiag.blob.core.windows.net/pingarmtemplatereadmefiles/pingasyncservice.png" >

</br>

after the files are changed then go up level in folder heirarchy </br>

$ cd ..</br>

$ sudo dpkg-buildpackage -b --no-sign</br>

#if the above exits with error code 2 and does not go through run the following 3 commands marked with a *
</br>
*
$ sudo apt-get install lttng-tools </br> 
*
$ sudo apt-get install lttng-modules-dkms </br>
*
$ apt-get install liblttng-ust-dev </br>

------
</b>
$ cd ..</br>
$ sudo dpkg -i pingasync_2.0-0ubuntu1_amd64.deb </br>
$ sudo apt-get install gnupg rng-tools </br>
$ gpg --gen-key </br>
#copy the key above should be in the form below
</br>
------
</br>
pub   rsa3072 2019-11-28 [SC] [expires: 2021-11-27] </br>
      DDDXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX315E </br>
uid                      PingAsync Tool <******syed@microsoft.com> </br>
sub   rsa3072 2019-11-28 [E] [expires: 2021-11-27] </br>
</br>
<img src="https://komalsandboxdiag.blob.core.windows.net/pingarmtemplatereadmefiles/gpg%20gen%20key.png" >


#position in directory '/Komal/Asyn-r-code/repo'
</br>
$ sudo apt-get install reprepro </br>
$ mkdir repo && cd repo </br>
$ mkdir conf && cd conf </br>
$ touch distributions </br>

$ sudo vim distributions</br>

--------
<b>distributions </b> file changed </br>
--------

#change your file name and put the key SignWith below 
</br>
#this is the pub key id given earlier step 
</br>

Origin: PingAsync Tool </br>
Label: pingasync </br>
Codename: bionic </br>
Architectures: amd64 </br>
Components: main </br>
Description: Personal repository </br>
SignWith: B501DE17DA19A16F  </br>



 #give path of repo and also of the deb file if you in the folder where both
 </br>
 #the repo folder and the deb file are on same level then dont need to give it a path
 </br>
$ sudo reprepro --basedir repo includedeb bionic pingasync_4.0-0ubuntu1_amd64.deb </br>
$ sudo reprepro --basedir repo includedeb bionic pingasync*.deb </br>
$ sudo reprepro --basedir repo list bionic </br>

$ sudo gpg --output PUBLIC.KEY --armor --export ******syed@microsoft.com </br>
$ sudo mv PUBLIC.KEY repo </br>

$ cd repo </br>
$ sudo mv PUBLIC.KEY </br>
$ sudo mv db </br>
$ sudo mv conf </br>
$ sudo mv pool </br>


$ git clone ---- distination url of an online empty repo </br>
$ git add --all </br>
$ git commit -m "Package repository" </br>
$ git push </br>
 
#enter username and password for your git repo
</br>



<b> At this stage the Ping Utilty is installed in your system ready to be used! </b></br>
<img src="https://komalsandboxdiag.blob.core.windows.net/pingarmtemplatereadmefiles/26.png" >





