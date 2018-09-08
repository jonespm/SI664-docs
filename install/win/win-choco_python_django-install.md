# Windows 10: Setting up a Python 3.x / Django 2.x Development Environment Using Chocolatey

I do my development work on a Mac. But I also run Windows 10 using 
[Parallels Desktop](https://www.parallels.com/).  What follows are the steps I took to set up a 
Python 3.x/Django 2.x dev environment in Windows, based in part on Lisa Tagliaferri's excellent 
[guide](#tagliaferri).  I'll discuss how to add MySQL and connect it to Django in a another install 
guide.
 
Outside the Parallels virtual machine (VM) I use [Homebrew](https://brew.sh/), a macOS package 
manager, to acquire and maintain a good deal of the software I use on a daily basis. Within Windows 
I turned to [Chocolatey](https://chocolatey.org/) to manage my installs of nano, Python and Git.

I installed Chocolatey using Windows [PowerShell](https://docs.microsoft.com/en-us/powershell/scripting/getting-started/getting-started-with-windows-powershell?view=powershell-6). 
Chocolatey can also be installed using `cmd.exe`.  See the Chocolatey [install page]
(https://chocolatey.org/docs/installation) for directions.

<a name="introduction"></a>

## 1.0  Configuring Windows PowerShell

Windows PowerShell is a command line shell for system administrators built on top of .Net.  
Administrative tasks are performed by running `cmdlets` (pronounced "command-lets").

### 1.1 Open PowerShell
Click the start menu icon (lower left corner).  Type "PowerShell" in the search box.  Then 
*right-click* on the "Windows PowerShell Desktop app" option and select "Run as administrator".  
If prompted, click "Yes" to allow PowerShell to make changes to your device.  The PowerShell command 
line shell will then open displaying a prefix prompt of "PS".

__WARNING__: Be sure that you are running PowerShell _as an administrator_ before executing the 
following commands.

```
Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

PS C:\Windows\system32>
```

Enter the following command in order to change directories to your user directory:

```
PS C:\Windows\system32> cd ~
PS C:\Users\arwhyte>
```

When done with PowerShell (though not now), type "exit" and click enter.

```
PS C:\Users\arwhyte> exit
```

### 1.2 Change PowerShell's Execution Policy
"Restricted" is the default execution policy.  It prevents you from running scripts.  Change 
PowerShell's execution policy to "RemoteSigned".  "RemoteSigned" will let you run scripts and 
configuration files downloaded from the Internet and signed by trusted publishers.

__WARNING__: Note that a "trusted" script could still include malicious code so consider carefully 
what scripts you choose to execute when running under the new execution policy.

First, set the scope of the new execution policy to the current user (i.e., you).

```
PS C:\Users\arwhyte> Set-ExecutionPolicy -Scope CurrentUser
```

PowerShell will then prompt you to select an Execution Policy.  Type "RemoteSigned" and then 
press `Enter`. PowerShell will then ask if you to change the current execution policy.  Type 
"y" (yes) and the "RemoteSigned" execution policy will be instituted.  To confirm the policy 
change enter the following command:

```
PS C:\Users\arwhyte> Get-ExecutionPolicy -List
```

PowerShell's response should resemble the following output:

```
        Scope ExecutionPolicy
        ----- ---------------
MachinePolicy       Undefined
   UserPolicy       Undefined
      Process       Undefined
  CurrentUser    RemoteSigned
 LocalMachine       Undefined
```

## 2.0 Install Chocolatey
[Chocolatey](https://chocolatey.org/) is a package manager for Windows. Like [Homebrew](https://brew.sh/) 
it simplifies installing, configuring, updating, and removing Windows software. Before 
downloading and running the Chocolatey install script, create a WebClient object called `$script` 
in order to share the Internet connection settings with Internet Explorer:

```
PS C:\Users\arwhyte> $script = New-Object Net.WebClient
```

Then review the available properties and methods of the `$script` object by piping it to the 
`Get-Member` class:

```
$script | Get-Member
```

A long list of methods and properties will be outputed to the screen. The `DownloadString` method
 is what we will use to download the Chocolatey install script.

```
...
DownloadString Method string DownloadString(string address), string DownloadString(uri address)
...
```

Implement the method:

```
PS C:\Users\arwhyte> $script.DownloadString("https://chocolatey.org/install.ps1")
```

Then install Chocolatey:

```
PS C:\Users\arwhyte> iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
```

The `iwr`("Invoke-WebRequest") cmdlet will download and parse the Chocolatey install script before 
piping it on to the `iex` (Invoke-Expression) cmdlet which will execute install script.

Allow PowerShell to install Chocolatey.

## 3.0 Chocolatey Package Installs
Now let's install nano, Python and Git.

### 3.1 Install nano
nano is a text editor with a command line interface that can be invoked within PowerShell to write 
programs. Issue the following `choco` command to install the nano [package](https://chocolatey
.org/packages/nano):

```
PS C:\Users\arwhyte> choco install -y nano
```
_Note_: the `-y` flag tells Chocolatey to execute the script without a formal confirmation prompt.

### 3.2 Install Python 3.7.x
Issue the following `choco` command to install the latest version of Python 3.7.x (currently 3.7.0)
 using the Chocolatey Python [package](https://chocolatey.org/packages/python):

```
PS C:\Users\arwhyte> choco install -y python
```

_Note_: The default location of the Chocolatey Python 3.7.x install is:

```
C:\Python37
```

If you want to install Python in another location set the `/InstallDir` parameter to the location of 
your choice.

```
PS C:\Users\arwhyte> choco install python3 --params "/InstallDir:C:\your\install\path"
```

The Windows `Path` environment variable is also updated as is indicated in the install output:

```
Chocolatey v0.10.11
Installing the following packages:
python
By installing you accept licenses for the packages.
Progress: Downloading python3 3.7.0... 100%
Progress: Downloading python 3.7.0... 100%

python3 v3.7.0 [Approved]
python3 package files install completed. Performing other installation steps.
Installing 64-bit python3...
python3 has been installed.
Installed to: 'C:\Python37'
  python3 can be automatically uninstalled.
Environment Vars (like PATH) have changed. Close/reopen your shell to
 see the changes (or in powershell/cmd.exe just type `refreshenv`).
 The install of python3 was successful.
  Software installed as 'exe', install location is likely default.

python v3.7.0 [Approved]
python package files install completed. Performing other installation steps.
 The install of python was successful.
  Software install location not explicitly set, could be in package or
  default install location if installer.

Chocolatey installed 2/2 packages.
 See the log for details (C:\ProgramData\chocolatey\logs\chocolatey.log).
 ```
You can check the Python installation location by starting Python in the shell and returning the 
path to the system executable:

```
PS C:\> python
Python 3.7.0 (v3.7.0:1bf9cc5093, Jun 27 2018, 04:59:51) [MSC v.1914 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> import os
>>> import sys
>>> os.path.dirname(sys.executable)
'C:\\Python37'
>>> exit()
```

You can also confirm if the `PATH` environment variable has been updated by clicking the start menu 
icon (lower left corner) and searching for the "SystemPropertiesAdvanced" run command.  Then 
*right-click* on the "SystemPropertiesAdvanced" option and select "Run as administrator" to open 
the System Properties Advanced tab.  Click "Environment Variables . . ." and check the System 
variables `PATH` variable.  It should include paths to your Python 3.7.x directory:

```
C:\Python37\Scripts;
C:\Python37\;
```

Confirm that Python has been successfully installed by typing the command `refreshenv` to 
close/reopen PowerShell.

```
PS C:\Users\arwhyte> refreshenv
Refreshing environment variables from registry for cmd.exe. Please wait...Finished..
PS C:\Users\arwhyte> python -V
Python 3.7.0
```

__WARNING__: running `refreshenv` did not work for me.  I had to exit PowerShell and then restart it
 (as administrator) in order to get it to recognize the addition of Python in `PATH`.

### 3.3 Install Git
Issue the following `choco` command to install the Git [package](https://chocolatey.org/packages/git):

```
PS C:\Users\arwhyte> choco install git -params "/GitAndUnixToolsOnPath"
```

Chocolatey will install git, the BASH tools and add each to your 'PATH'.

### 3.4 Confirm Chocolatey package installs
Let's check what packages we've installed so far:

```
PS C:\Users\arwhyte> choco list --local-only
Chocolatey v0.10.11
chocolatey 0.10.11
chocolatey-core.extension 1.3.3
git 2.18.0
git.install 2.18.0
nano 2.5.3
python 3.7.0
python3 3.7.0
7 packages installed.
```

Looks good.

By the way, upgrading Chocolatey itself is easy:

```
PS C:\Users\arwhyte> choco upgrade chocolatey
```

### 4.0 Git Working directory for the Django Project
I use [Git](https://git-scm.com/) as my distributed version control system and [Github]
(https://github.com/) to store and share my work.  If you don't have a Github account create one 
(it's free). I organize my development work locally by service (e.g., Bitbucket, Github) and by 
the organization or user account whose repos I choose to fork.  I offer this approach merely as an 
example; choose a directory structure for organizing your 
project work that makes 
sense to you.

```
Development\
    repos\
        bitbucket\
          ...
        github\
          apereo\
          arwhyte\
          csev\
          IMSGlobal\
          tsugiproject\
          ...
```

Since I'm not forking a repo from which my django project work will be based (initially) I'll create
 my project in the arwhyte\ folder:

```
PS C:\Users\arwhyte> mkdir Development/repos/github/arwhyte/django_tutorial
```

Next, I initialize the empty django_tutorial directory as a Git repo:

```
PS C:\Users\arwhyte\Development\repos\github\arwhyte\django_tutorial> git init
Initialized empty Git repository in C:/Users/arwhyte/Development/repos/github/arwhyte/django_tutorial/.git/
```

You do the same.  Decide on a directory location for the Django project work.  Then initialize the 
directory as an empty Git repo.

That's enough Git for now.  We will do more with Git and Github later.  For a useful Git primer 
read Roger Dudler's [git - the simple guide](http://rogerdudler.github.io/git-guide/).

## 5.0 Django Project Virtual Environment
Next, create a virtual environment isolate the Django development work from your other 
Python projects using the `virtualenv` package. If you are fuzzy on the purpose of virtual 
environments review Real Python's 
[Python Virtual Environments: a Primer](https://realpython.com/python-virtual-environments-a-primer/)

### 5.1 Upgrade pip
However, before we install `virtualenv` make sure that the latest version of `pip`, Python's own 
package manager, is installed locally:

```
PS C:\Users\arwhyte> python -m pip install --upgrade pip
Collecting pip
  Using cached https://files.pythonhosted.org/packages/5f/25/e52d3f31441505a5f3af41213346e5b6c221c9e086a166f3703d2ddaf940/pip-18.0-py2.py3-none-any.whl
Installing collected packages: pip
  Found existing installation: pip 10.0.1
    Uninstalling pip-10.0.1:
      Successfully uninstalled pip-10.0.1
Successfully installed pip-18.0
```

### 5.2 Install virtualenv
Once `pip` is updated, use it to install the `virtualenv` package.

```
PS PS C:\Users\arwhyte> pip install virtualenv
```

### 5.3 Create the Virtual Environment
Now create a virtual environment for your django project.  Create it from within the project root 
directory:

```
PS C:\Users\arwhyte> cd Development\repos\github\arwhyte\django_tutorial
PS C:\Users\arwhyte\Development\repos\github\arwhyte\django_tutorial> virtualenv venv
Using base prefix 'c:\\python37'
New python executable in C:\Users\arwhyte\Development\repos\github\arwhyte\django_tutorial\venv\Scripts\python.exe
Installing setuptools, pip, wheel...done.
```

### 5.4 Activate the Virtual Environment
You __must__ activate the virtual environment before adding project-specific Python packages such
 as Django.

```
PS C:\Users\arwhyte\Development\repos\github\arwhyte\django_tutorial> venv\Scripts\activate
(venv) PS C:\Users\arwhyte\Development\repos\github\arwhyte\django_tutorial>
```

When activated the prompt is prefixed with the name of the virtual environment (e.g., "(venv)").

To deactivate the virtual environment run:

```
(venv) PS C:\Users\arwhyte\Development\repos\github\arwhyte\django_tutorial> deactivate
PS C:\Users\arwhyte\Development\repos\github\arwhyte\django_tutorial>
```

### 5.5 Install Django

After activating the django_tutorial virtual environment, install Django:

```
(venv) PS C:\Users\arwhyte\Development\repos\github\arwhyte\django_tutorial> pip install Django
```

### 5.6 Confirm Virtual Environment Installed Packages
Check the installed packages in your django project virtual environment:

```
(venv) PS C:\Users\arwhyte\Development\repos\github\arwhyte\django_tutorial> pip list
Package    Version
---------- -------
Django     2.1.1
pip        18.0
pytz       2018.5
setuptools 40.2.0
wheel      0.31.1
```

You can also confirm that Django is installed this way:

```
(venv) PS C:\Users\arwhyte\Development\repos\github\arwhyte\django_tutorial> python -m django --version
2.1.1
```

## 6.0 Django mysite project
From within your Django project root directory, create the "mysite" project by issuing the 
django-admin "startproject" command. Note the inclusion of a trailing dot ('.') following "mysite":

```
(venv) PS C:\Users\arwhyte\Development\repos\github\arwhyte\django_tutorial> django-admin startproject mysite .
```

__WARNING__: make sure you include the trailing dot ('.') in the command.  The dot creates the new 
project with a directory structure that simplifies deployment to a server.  If you neglect to 
include the dot, delete the directories and files that were created (except 'venv') and run the 
command again along with the trailing doc ('.').

Generating the `mysite` results in the following project layout:

```
django-tutorial/
    manage.py
    mysite/
        __init__.py
        settings.py
        urls.py
        wsgi.py
```

### 6.1 Start the Development Server
Start up the development server by issuing the `runserver` command:

```
(venv) PS C:\Users\arwhyte\Development\repos\github\arwhyte\django_tutorial> python manage.py runserver
Performing system checks...

System check identified no issues (0 silenced).

You have 15 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.
September 05, 2018 - 23:14:55
Django version 2.1.1, using settings 'mysite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CTRL-BREAK.
[05/Sep/2018 23:15:07] "GET / HTTP/1.1" 200 16348
[05/Sep/2018 23:15:07] "GET /static/admin/css/fonts.css HTTP/1.1" 200 423
[05/Sep/2018 23:15:07] "GET /static/admin/fonts/Roboto-Regular-webfont.woff HTTP/1.1" 200 80304
[05/Sep/2018 23:15:07] "GET /static/admin/fonts/Roboto-Bold-webfont.woff HTTP/1.1" 200 82564
[05/Sep/2018 23:15:07] "GET /static/admin/fonts/Roboto-Light-webfont.woff HTTP/1.1" 200 81348
```

__INFO__: ignore the database migration warnings; you will address them momentarily.

Open a web browser and point to `http://127.0.0.1:8000/`.  Confirm that the Django "The install 
worked successfully! Congratulations!" page successfully loads.

Once confirmed, shut down the development server by typing `Control` - `c`.

### 6.2 Create the Django tutorial polls app
With Django installed, "mysite" created

```
(venv) PS C:\Users\arwhyte\Development1\repos\github\arwhyte\django_tutorial> python manage.py startapp polls
```

The skeletal polls app possesses the following structure:

```
polls/
    __init__.py
    admin.py
    apps.py
    migrations/
        __init__.py
    models.py
    tests.py
    views.py
```

### TODO Start Tutorial

ASFASFASFASFASFASFDASFASFD

## Chocolatey Commands Reference
For other `choco` commands see the [Chocolatey Wiki](https://github.com/chocolatey/choco/wiki/CommandsReference) on Github:


## Sources
<a name="tagliaferri">Tagliaferri, L.</a> Digital Ocean: 
[How to Install Python 3 and Set Up a Local Programming Environment in Windows 10](https://www.digitalocean.com/community/tutorials/how-to-install-python-3-and-set-up-a-local-programming-environment-on-windows-10). 24 October 2016.
