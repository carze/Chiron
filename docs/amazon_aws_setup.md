# Amazon AWS EC2 Setup 
<a name="top"></a>
***

## Contents

* [Logging into Amazon AWS](#login)
* [Regions](#regions)
* [EC2 (Elastic Compute Cloud) Environment](#ec2)
* [Key Pairs](#keypairs)
* [Security Groups](#security_groups)
* [Volumes](#volumes)
* [Launching an AMI (Amazon Machine Image](#launch)
* [Attaching Volume to Instance](#attach_volume)
* [Connecting to Instance](#instance_connect)
* [Using SSH instead of Secure Shell (non-Windows users)](#using_ssh)
* [Final Steps](#final_steps)
* [Tearing Down](#tearing_down)
* [Related Links](#related)

Here we go through the various steps to quickly create a Docker enabled virtual machine running on Amazon with sufficient storage to execute the various tools and containers for the Cloud Workshop.

## <a name="login"></a> Logging into Amazon AWS

* You should already have an Amazon AWS account if you followed with workshop preparations
* If not, browse to [aws.amazon.com)(https://aws.amazon.com), click the button at the upper right, and follow the directions

![alt text](https://github.com/IGS/Chiron/raw/master/docs/images/login.png "")

## <a name="regions"></a> Regions

* Amazon organizes resources and datacenters into “regions”.
* There are numerous “regions” dedicated to serving different parts of the world.
* In this workshop, we will work exclusively with the **US-East (N. Virginia)** region.
* Please switch to it if you find yourself in a different region.

![alt text](https://github.com/IGS/Chiron/raw/master/docs/images/regions.png "")

## <a name="ec2"></a> EC2 (Elastic Compute Cloud) Environment

Back on the AWS dashboard, click on the "**EC2**"" link under the “**Compute**” section.

![alt text](https://github.com/IGS/Chiron/raw/master/docs/images/ec2.png "")

## <a name="keypairs"></a>Key Pairs

Click on the "**Key Pairs**" link

![alt text](https://github.com/IGS/Chiron/raw/master/docs/images/keys1.png "keys1")

Most interactions with Amazon AWS resources will involve key-based encryption for security. Amazon EC2 will work with SSH generated keys, but for this workshop, we will use a key pair created via Amazon AWS's web console.

1. Click “**Create Key Pair**”, and use “cloud_workshop” for the name.

2. Accept the download and save the .pem file.

![alt text](https://github.com/IGS/Chiron/raw/master/docs/images/keys2.png "keys2")

### Make copies of the the .pem file for use by "Secure Shell"

* Make two copies of your PEM file:
    * 1st copy to be called “**cloud_workshop**” (no extension)
    * 2nd copy to be called “**cloud_workshop.pub**” (.pub extension)

* Make a note of where these files are located on your laptop’s filesystem

* This is necessary because the “Secure Shell” Chrome app doesn’t work well, with just a PEM file, and is looking for a pair of files.

* OSX/Linux users intending to use SSH can probably skip this step because SSH's "-i" flag can work with .pem files.

You should now have a "**cloud_workshop**" key pair listed in the "Key Pairs" section

![alt text](https://github.com/IGS/Chiron/raw/master/docs/images/keys3.png "keys3")

## <a name="security_groups"></a> Security Groups

Amazon locks everything down tightly. We have to create rules to allow traffic to our resources. These rules are expressed with “Security Groups”.

1. Navigate to “**Security Groups**” under “**Network and Security**”

2. Click the blue “**Create Security Group**” button at the upper left.

3. Name the group “**cloud_workshop_sg**”

4. Enter a description.

5. Add two rules as shown:
    * For SSH
    * For “Custom TCP” port **8888**. Sources should be set to “**Anywhere**”.

![alt text](https://github.com/IGS/Chiron/raw/master/docs/images/security1.png "security1")

## <a name="volumes"></a> Volumes

This workshop will require a considerable amount of storage, which exceeds the amount of storage that is commonly available to virtual machines on their own.

To address this, we will create a “Volume” to greatly expand the amount of diskspace we can use. The type of volume is provided by a service that Amazon calls "EBS", for Elastic Block Store.

1. Click the “**Volumes**” link on the left of the screen under “Elastic Block Store”

2. Click on the “**Create Volume**” button

![alt text](https://github.com/IGS/Chiron/raw/master/docs/images/volumes1.png "volumes1")

3. Make the following selections: “**Throughput optimized**”, **500** GB in size, and “**us-east-1d**” for the Availability Zone.

4. After creating the volume, there will be a brief time before it becomes “available” according to the web console.

5. After creating the volume, an entry should appear in the volumes listing.

6. Verify that the volume is in the correct availability zone and that it has the correct size.

![alt text](https://github.com/IGS/Chiron/raw/master/docs/images/volumes2.png "volumes2")

## <a name="launch"></a> Launching an AMI (Amazon Machine Image)

Now we will launch an Amazon Machine Image (AMI)

1. Under “**Images**” on the left, click “**AMIs**”.

2. Type “**chiron**” into the search box at the top of the dashboard.

![alt text](https://github.com/IGS/Chiron/raw/master/docs/images/launch1.png "launch1")

3. After entering “**chiron**” into the search box, a single AMI result should appear.

4. The AMI that is found should have an ID of: **ami-21530437**.

![alt text](https://github.com/IGS/Chiron/raw/master/docs/images/launch2.png "launch2")

The **chiron** AMI is a custom image created for the workshop and has Docker pre-installed on it for your use.

5. Ensure the **chiron** AMI row is selected

6. Click the “**Launch**” button at the upper left

![alt text](https://github.com/IGS/Chiron/raw/master/docs/images/launch3.png "launch3")

7. Click the “**Edit security groups**” link on the right side of the screen.

8. Choose “**Select an existing security group**” and check the previously created “**cloud_workshop_sg**” row as shown.

![alt text](https://github.com/IGS/Chiron/raw/master/docs/images/launch4.png "launch4")

9. Click “**Review and Launch**”.

On the “Step 2” screen, after clicking “**Launch**”, one must select an instance type. Instance types describe the features of the virtual machine that will be instantiated, such as how much RAM (memory) and how many CPUs it will have. The more resources you assign, the greater the expense will be per hour of use.

10. Scroll down and select “**m3.xlarge**”.

11. Click the “**Review and Launch**” button.

![alt text](https://github.com/IGS/Chiron/raw/master/docs/images/launch5.png "launch5")

12. Click the “**Edit security groups**” link on the right side of the screen.

13. Choose “**Select an existing security group**” and check the previously created “**cloud_workshop_sg**”.

14. Click “**Review and Launch**”.

![alt text](https://github.com/IGS/Chiron/raw/master/docs/images/launch6.png "launch6")

15. Select “**Choose an existing key pair**”. It should be the default.

16. Choose the previously created “**cloud_workshop**” key pair.

17. Acknowledge that you have the private key file (which you downloaded earlier) by checking the checkbox.

![alt text](https://github.com/IGS/Chiron/raw/master/docs/images/launch7.png "launch7")

18. Verify the details of the running instance match up with the selections you have made.

19. Take special note of the “**IPv4 Public IP**” internet address. We will need this later in order to connect to our cloud machine.

![alt text](https://github.com/IGS/Chiron/raw/master/docs/images/launch8.png "launch8")

## <a name="attach_volume"></a> Attaching Volume to Instance

1. Navigate to the volumes section, and right-click on the previously created 500GB volume.

2. Select “**Attach volume**”, then, under “**Instance**”, select our newly launched virtual machine instance, which should be “running”.

3. Make a note of the device. It will probably be **/dev/sdf**, or something similar.

4. Click “**Attach**”. After a brief period, the volume state will change to indicate that it is “in-use”.

![alt text](https://github.com/IGS/Chiron/raw/master/docs/images/attach_volume.png "attach_volume")

## <a name="instance_connect"></a> Connecting to Instance

1. Launch your “Secure Shell” Google Chrome application. The app launcher is typically at the upper right of Chrome's browser window.

2. For the top input box, enter “**cloud_workshop**”, or another description of your choice.

3. For the username, use “**ubuntu**”.

4. For the hostname, enter the IPv4 address you previously made a note of.

5. For identity, click the “**Import...**” button and browse to the “**cloud_workshop**” file you recently copied from your .pem file. The "Identity" field should take on the value of "**cloud_workshop**".

![alt text](https://github.com/IGS/Chiron/raw/master/docs/images/connect1.png "connect1")

6. After configuring the connection in “*Secure Shell*”, click the “**Connect**” button at the lower right.

7. The connection should be established. Once established, you will have a working terminal that looks similar to:

![alt text](https://github.com/IGS/Chiron/raw/master/docs/images/connect2.png "connect2")

[top](#top)

## <a name="using_ssh"> Using SSH instead of Secure Shell (non-Windows users)

For Linux and Mac OSX users that would like to use SSH to login, simply open a terminal and issue the following command:

```
$ ssh –i /path/to/cloud_workshop.pem ubuntu@XXX.XXX.XXX.XXX
```

* Be sure to replace “/path/to/cloud_workshop.pem”, with the actual path to where you saved your PEM file.
* Replace “XXX.XXX.XXX.XXX” with the public IPv4 address that you previously made a note of.

## <a name="final_steps"></a> Final Steps

After you have a working terminal, there a few steps left to perform to make the storage volume usable to our machine.

1. Issue the following command to change to “root”, which is a user with elevated permissions:

```
$ sudo su -
```

Your prompt should change to a “#” character.

2. Create a filesystem on the volume that we attached to this machine. Use the device name you noted when the volume was attached. You’ll probably need to replace the “s” in /dev/sdf, with “xv” like so:

```
# mkfs.ext4 /dev/xvdf
```

3. Stop the docker process. 

```
# /etc/init.d/docker stop
```

You should see an “ok” message if you do so successfully..

4. Mount the volume and verify you have approximately 500GB available

```
# mount –t ext4 /dev/xvdf /opt
# df -h /opt
Filesystem Size Used Avail Use% Mounted on
/dev/xvdf 493G 70M 467G 1% /opt
```

5. Restart the docker process.

```
# /etc/init.d/docker start
```

## <a name="tearing_down"></a> Tearing Down

When the workshop is concluded, we recommend that you “terminate” the running **chiron** instance to avoid unnecessary charges to your credit card.

1. Navigate to the instances portion of the EC2 control panel

2. Right-click on the running “**chiron**” instance

3. From “**Instance State**” and choose “**Terminate**”.

Remove the 500GB volume that was created.

1. Navigate to the "Volumes" portion of the EC2 control panel.

2. Right-click on the volume.

3. Choose “**Delete Volume**” (if it’s disabled, you may have to detach, or force detach, first)

## <a name="related"></a> Related Links:

- [Official Docker Installation](https://docs.docker.com/engine/installation/)

[top](#top)