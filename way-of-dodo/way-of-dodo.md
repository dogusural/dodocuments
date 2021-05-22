# Way of a Riddle & Code Engineer

# Getting Started

Below you can find a complete stack of our best practices &  basic setup of the tools we are using.

Here are some of the gadgets we utilize in order to build cool stuff :

- We use **Github** as our git server
- We use **Clubhouse** for the project management
- We use **Notion** for documentation
- We use **Slack** for daily conversations and syncing
- We use **Keybase** or **PGP** encrypted emails for confidential data transfers
- You are free to choose your operating system, IDE, text editor, debugger. But keep in mind that most of the guides are written for Linux .

Let's dive into it.

[http://gph.is/YBpwJd](http://gph.is/YBpwJd)

## 1) Setting a your Github account

### 1.1) Setting up Configs

To identify yourself to the git client, call the following commands with your github email.

```jsx
$git config --global user.name "YOUR NAME"
$git config --global user.email "YOUR EMAIL ADDRESS"

```

### 1.2) Setting up SSH keys

In Riddle and Code we use ssh protocol to access github to avoid having to enter credentials every time we do push/pull/fetch .

Before everything else, you need to generate a ssh keypair on your machine.

Run the following command with your email address as an argument to **-C** option to act as a memo to differentiate different keys with different purposes .

```jsx
$ssh-keygen -t rsa -o -C my-github@email.com
```

First it confirms where you want to save the key (.ssh/id_rsa), and then it asks twice for a passphrase, which you can leave empty if you don’t want to type a password when you use the key. However, if you do use a password, make sure to add the -o option; it saves the private key in a format that is more resistant to brute-force password cracking than is the default format.

After generation, simply run the command below and copy the output.

```jsx
$cat ~/.ssh/id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAklOUpkDHrfHY17Sb
rmTIpNLTGK9Tjom/BWDSU
GPl+nafzlHDTYW7hdI4yZ5ew18JH4JW9jbhUFrviQzM7xlELEVf4h9lFX5QVkbPppSwg0cda3
Pbv7kOdJ/MTyBlWXFCR+HAo3FXRitBqxiX1nKhXpHAZsMciLq8V6RjsNAQwdsdMFvSlVK/7XA
t3FaoJoAsncM1Q9x5+3V0Ww68/eIFmb1zuUFljQJKprrX88XypNDvjYNby6vw/Pb0rwert/En
mZ+AW4OZPnTPI89ZPmVMLuayrD2cE86Z/il8b+gw3r3+1nKatmIkjn2so1d01QraTlMqVSsbx
NrRFi9wrf+M7Q== my-github@email.com
```

Copy the output and paste under Github/settings/SSH and GPG Keys

 

Start the ssh agent and add your ssh key to the ssh agent.

```jsx
$eval "$(ssh-agent -s)"
$ssh-add ~/.ssh/id_rsa
```

![Way%20of%20a%20Riddle%20&%20Code%20Engineer%2070792caf1b024a6485e9412cebf53aa4/Untitled.png](Way%20of%20a%20Riddle%20&%20Code%20Engineer%2070792caf1b024a6485e9412cebf53aa4/Untitled.png)

### 1.3) Security

Setting up two factor authentication and yubikey are essential. 

2fa can be enabled under Setting/Account Security.

Instructions about yubikey setup can be found under our github yubikey-helper repository.

[](https://github.com/RiddleAndCode/yubikey-helper)

### 1.4) Tips

- This goes without saying but i have to say it. Never push to master/ develop. If you are the admin of the repository, disable direct pushes to the master branch and include admins.
- Always merge the master branch back to your branch before doing any pull requests and solve the potential conflicts in your branch.
- Pull regularly and don't fall behind other people may be developing on the same project.
- Commits should be atomic and consecutive commits should be cohesive to enable commit reverts without any issue.
- Keep your commit messages spicy, rather than writing "it works" explain what you have changed to make it work and what was causing the error.
- Use *git stash* before changing branches to save your progress.
- *git log* is your friend in case of an error, learn to use it to traverse between commits effectively.
- If your project is dependent on other projects , dont copy them. Include them as submodule to the project. It makes the maintenance more tolerable.
- Every project must be on [**github.com/RiddleAndCode](https://github.com/RiddleAndCode) ,** no matter the size.
- Code transfers on mediums other than Github is not optimal, always stick to commits and pushes and say no to emails and flash disks ⛔.
- Keep your repository organised and easy to maintain. Adding a readme file always helps . Here are some nicely formatted repository examples : 

 - [**RiddleAndCode/rwel**](https://github.com/RiddleAndCode/rwel)
 - **[RiddleAndCode/seal-edge](https://github.com/RiddleAndCode/seal-edge)**

---

This concludes our github section. To cheer you up, here is a cat wearing glasses hissing at a laptop 😾. 

![https://media.giphy.com/media/VbnUQpnihPSIgIXuZv/giphy.gif](https://media.giphy.com/media/VbnUQpnihPSIgIXuZv/giphy.gif)

## 2) Development Processes

### 2.1) Summary

In Riddle and Code we follow Agile Software Development methods. We run short daily meetings every morning to discuss shortcomings , achievements , blockers and plan the day.  

- We run weekly sprints and run retrospective / planning sessions at the end of each epoch.
- For Kanban board and project management, we chose Clubhouse interface.
- For continuous integration / continuous delivery environment we chose  Jenkins - Artifactory - Kubernetes/Docker-compose.

Let's get down to what each of this items mean

![https://media.giphy.com/media/Ol2yHMEFJdYEo/giphy.gif](https://media.giphy.com/media/Ol2yHMEFJdYEo/giphy.gif)

### 2.2) Sprints

Weekly sprints

We should have three different meetings at the end of the sprint:

- Review
    - Talk about what's been accomplished
    - Talk about what we've missed
- Retrospective
    - Talk about what went well
    - Talk about what didn't go well
    - Talk about why we didn't get things done
    - Tasks to improve
- Sprint Planning
    - Talk about tasks to accomplish in the next sprint
    - Talk about what needs to be planned for the sprint after next

### 2.3) Clubhouse

Planning:

- New Stories get added to "Needs Requirements" if requirements / acceptance criteria are needed
- Once stories get detailed, then they get moved to "Needs Triage"
- During Product Planning, these stories get estimates attached to them and they get put into "Ready for Development" in priority order

Sprint:

- To start a Sprint, create a new Sprint in Clubhouse
- Add stories from the top of the "Ready for Development" list into the Sprint so that that we match our velocity
- Add Review Requirements to the Sprint description

Development:

- Pick things from the top of the "Ready for Development" list and put into "In Development"
- Create a matching branch name with your task or let clubhouse do it for you as shown below.

![Way%20of%20a%20Riddle%20&%20Code%20Engineer%2070792caf1b024a6485e9412cebf53aa4/Untitled%201.png](Way%20of%20a%20Riddle%20&%20Code%20Engineer%2070792caf1b024a6485e9412cebf53aa4/Untitled%201.png)

- Stick to the scope of the task at hand, if unexpected tasks comes up  create the issues and place them under the backlog , if it is a blocker, share your concerns in the next daily.
- Create a "Pull Request" once the development is done.
- Move the ticket to "Ready for Review" and assign an Owner to do a PR review
    - If the person needs to make changes move the ticket back to "In Development"
    - Rinse and repeat
- Once the ticket is merged put into "Done"

### 2.3) CI

Please refer to Drafts/ CI Scenario in the meantime.

[Drafts](https://www.notion.so/Drafts-8e36166c155a4ad28f71ec33e79f846f)

## 3) Documentation

![https://media.giphy.com/media/6bjWIzKKkTvnG/giphy.gif](https://media.giphy.com/media/6bjWIzKKkTvnG/giphy.gif)

We believe all flow of information should happen on *notion* under its respected category. Documentation has been divided under three sections, namely :

### 3.1) Hardware

This place is the sacred grove of an embedded systems engineer. It should contain the schematics, BOM, Gerber files and the detailed descriptions of hardware modifications.

If there is more than one version of the hardware each version should be documented under its respected place in the shelf.

### 3.2) Developer Journey

Think of it as a journal containing the chain of decisions that led up to an end product. All important decision stages through the lifetime of the project should exist within this space. To give you an idea of what this space should contain, check example cases below.

- How did you plan to approach the project in the first place .
- Have you experienced any technical problems.
- How customer came up with new requirements .
- How you have had to change some hardware / software components of the project because of various limitations.

### 3.3) Knowledge Base

If you believe you posses a specific skill / knowledge from which others can benefit you are encouraged to share it in this section.

It might also be the case that you might be the only one who has the ability to use a specific tool whether it be hardware or software,  please contribute under this page.

![https://media.giphy.com/media/aQUGAeZ1fBWpy/giphy.gif](https://media.giphy.com/media/aQUGAeZ1fBWpy/giphy.gif)

And last but not least , please don't forget that sharing is caring  😻.

## 4) Hardware & Software Tools

As rule of thumb , you should have develop and master branches. Once the development on the branch has been done a pull request should be created. 

Branches are always protected on PR merge. They need code review and automated tests to pass before merging.

### 4.1 ) Creating Automated Tests on Jenkins

- Create Jenkins item/project. (One can copy the project struct from aother one)

![Way%20of%20a%20Riddle%20&%20Code%20Engineer%2070792caf1b024a6485e9412cebf53aa4/Untitled%202.png](Way%20of%20a%20Riddle%20&%20Code%20Engineer%2070792caf1b024a6485e9412cebf53aa4/Untitled%202.png)

- Jenkinsfile
    1. Usually ran under a docker image [https://github.com/RiddleAndCode/STM32L475-microecc/blob/1ed5247b181042845c0dcbc6be323d99a6cd57cd/Jenkinsfile#L7](https://github.com/RiddleAndCode/STM32L475-microecc/blob/1ed5247b181042845c0dcbc6be323d99a6cd57cd/Jenkinsfile#L7)
        
        1. This docker image should have the tools necessary to lint/build/test. That way we can reuse the same jenkins node for multiple builds for multiple version dependencies etc etc
    2. Lint / Coding guidelines
        1. C: astyle 
        2. Python: autopep
    3. Build (firmware/dockerimages/etc)
        1. Build for test
        2. Build for release (test should be the same as release if possible)
    3. Stash if doing hardware firmware
    
    ![Way%20of%20a%20Riddle%20&%20Code%20Engineer%2070792caf1b024a6485e9412cebf53aa4/Untitled%203.png](Way%20of%20a%20Riddle%20&%20Code%20Engineer%2070792caf1b024a6485e9412cebf53aa4/Untitled%203.png)
    
    4. Test
        1. Software
            1. C: Unity/CMake
            2. Python: pytest
            3. Rust: cargo test
        2. Hardware (eg. [https://github.com/RiddleAndCode/STM32L475-microecc/blob/master/Jenkinsfile](https://github.com/RiddleAndCode/STM32L475-microecc/blob/1ed5247b181042845c0dcbc6be323d99a6cd57cd/Jenkinsfile#L37))
            1. Requires NUC or UPSquared (check Jenkins node [https://jenkins.r3c.network/computer/hw-node/](https://jenkins.r3c.network/computer/hw-node/) )
            2. Jenkins will connect through ssh to the physical node
            3. unstash
            1. 
    
            ![Way%20of%20a%20Riddle%20&%20Code%20Engineer%2070792caf1b024a6485e9412cebf53aa4/Untitled%204.png](Way%20of%20a%20Riddle%20&%20Code%20Engineer%2070792caf1b024a6485e9412cebf53aa4/Untitled%204.png)
    
            4. Run the tests
            5. ???
        6. profit
    
    5. Publish
        1. Artifactory [https://github.com/RiddleAndCode/STM32L475-microecc/blob/1ed5247b181042845c0dcbc6be323d99a6cd57cd/Jenkinsfile#L60](https://github.com/RiddleAndCode/STM32L475-microecc/blob/1ed5247b181042845c0dcbc6be323d99a6cd57cd/Jenkinsfile#L60)
        2. Dockerhub [https://github.com/RiddleAndCode/custodian-solution/blob/fce734b439a31f09e5995a3216c5e4ca868289ad/Jenkinsfile#L134](https://github.com/RiddleAndCode/custodian-solution/blob/fce734b439a31f09e5995a3216c5e4ca868289ad/Jenkinsfile#L134)

CustodianSolution has a more complex system which involves multiple submodules and interactions, but in the end they follow the same structure

### 4.2 ) Hardware Debugging

For hardware debugging we use Segger Jlink with [Segger Embedded Studio](https://www.segger.com/downloads/embedded-studio/) IDE or VS code's [Cortex-Debug](https://marketplace.visualstudio.com/items?itemName=marus25.cortex-debug) add on.

**4.2.1 )** **Segger Studio Debugging**

Setting up Segger Studio for debugging might take up some time but it definitely worth the effort. It has a nice memory view and once you got the basics it is less hassle than the Cortex Debug since adjustments for each MCU is done automatically. 

Note that Segger Studio Debugging documents support only Jlink. Any other interfaces needs to be converted to Jlink. 

A tutorial on how to convert ST-link to Jlink can be found [here](https://www.segger.com/products/debug-probes/j-link/models/other-j-links/st-link-on-board/) .

An example of how we debug the wallet project can be found under Knowledge Base/Guides/Overview/[Wallet2 build and debug tutorial](https://www.notion.so/Wallet2-build-and-debug-tutorial-de002b8bb21e4f718c6ef1cf166d55a2) . 

**4.2.1 ) VS Code Cortex Debug** 

VS code is preferable since you can code and debug on the same interface. It supports al the debugging interfaces given that you provide the appropriate configuration file, namely .vscode/*launch.json*

An example launch.json file for STM32F429 MCU with Jlink interface :

```jsx
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Cortex Debug",
            "cwd": "${workspaceRoot}",
            "executable": "./build/se050-wallet-dev.elf",
            "request": "launch",
            "type": "cortex-debug",
            "device": "STM32F429ZI",
            "servertype": "jlink",
        }
    ]
}
```

Rest is pretty straight forward, just click run and start debugging 🥳 

![https://media.giphy.com/media/P95T23EGT6eSQ/giphy.gif](https://media.giphy.com/media/P95T23EGT6eSQ/giphy.gif)

This concludes our tooling part.