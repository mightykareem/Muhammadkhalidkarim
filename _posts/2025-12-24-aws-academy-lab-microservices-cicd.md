---
layout: post
title: "AWS Academy Lab: Microservices & CI/CD"
date: 2025-12-24
tags: [Cloud, AWS, DevOps]
permalink: /posts/awslab-cicd/
---

## Approach

![Solution architecture diagram showing microservices and cloud infrastructure](images/media/image3.jpeg)

## Phase 2: Analyzing the Infrastructure of the Monolithic Application

In this phase, I will analyze the current application infrastructure and then test the web application.

### Task 2.1: Verify that the Monolithic Application is Available

- Go to AWS Console
- Find the instance named `MonolithicApp`  
- Copy the public IPv4 address

![EC2 instance management console showing MonolithicApp instance](images/media/image4.jpeg)
>
My MonolithicApp is already running with the public IPv4 address shown above.
>
Copy the IPv4 address and open a new browser tab, entering it as the URL. In this example: `35.171.28.188`
>
**Expected Behavior:**
- Page loads using HTTP (not HTTPS)
- Browser might show a security warning - ignore it

![Web browser showing secure connection warning message](images/media/image5.jpeg)

Home page of website is visible.

### Task 2.2: Test the Monolithic Web Application

In this task, we will add data to the web application, test the functionality, and observe the different URL paths that are used to display the different pages. These URL paths are important to understand for when you divide this application into microservices later.

1. **Choose Suppliers**

![Coffee suppliers page interface showing list of suppliers](images/media/image6.jpeg)

Notice that the URL path includes `/suppliers`

2. **Add a New Supplier**

![Add supplier form with fields for company information](images/media/image7.jpeg)

Fill the form with any values and submit. Notice that the URL path includes `/supplier-add`.

3. **Edit a Supplier**

Select a supplier's edit button:

![Suppliers list showing edit button for each entry](images/media/image8.jpeg)

Select the edit button.
>
![Edit supplier form for updating company details](images/media/image9.jpeg)
>
Notice that the URL path now includes `supplier-update/1`.
>
Change any field and save:

![Supplier update form with modified address field](images/media/image10.jpeg)

The address was changed.
>
![Confirmation message showing supplier changes saved successfully](images/media/image11.jpeg)
>
Changes saved successfully.

# Task 2.3: Analyze how the monolithic application runs

After Task 2.2 works, continue:

1.  **Use EC2 Instance Connect to Connect to the Instance**

Open EC2 Instance Connect, Select **MonolithicAppServer → Connect → EC2 Instance Connect** → "Connect"

![EC2 instance management console showing MonolithicApp instance](images/media/image12.jpeg)

click on connect.

![Image 13](images/media/image13.jpeg)

Keep everything as default and click on connect.
>
![Image 14](images/media/image14.jpeg)
>
A new tab will open with a terminal.

2.  Analyze how the application is running.

In the terminal session, run the following command: sudo lsof -i :80

![Image 15](images/media/image15.jpeg)

This shows important information about what's running on port 80 of
the EC2 instance:
>
Q: What did you notice in the command output? What port and protocol
is the *node* daemon using?
>
Answer: The Node.js application is running on port 80 using the TCP
protocol. In Next, run the following command: ps -ef | head -1; ps
-ef | grep node
>
![Image 16](images/media/image16.jpeg)
>
This command shows process information.
>
Q: What did you notice in the command output? Which user on this EC2
instance is running a *node* process? Does the node process ID (PID)
match any of the PIDs from the output of the command that you ran
before the last one?
>
The root user runs the Node process. The PID 15099 matches the PID
from the previous lsof
>
command.

3.  To analyze the application structure, run the following commands:

~/resources/codebase_partner

![Image 17](images/media/image17.jpeg)

This is where the index.js file exists. It contains the base
application logic.
>
**Questions for thought:** Based on what you have observed, what can
you determine about how and where this node application is running?
How do you think it was installed? What prerequisite libraries, if
any, were required to make it run? Where does the application store
data?
>
Answer:
>
How/Where: Node.js app runs directly on EC2 (port 80) as root via node
index.js. Installation: Likely cloned from Git and installed via npm
install.
>
Prerequisites: Node.js runtime and dependencies in node_modules. Data
Storage: External RDS MySQL database (not local).

... (content continues; images under images/media/)
