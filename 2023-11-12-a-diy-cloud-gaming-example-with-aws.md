# A DIY Cloud Gaming Example with AWS

## Motivation

Ready-made cloud gaming solutions are sparse.
Expectedly, these solutions are designed to be user-friendly, and therefore suffer in customizability, so they offer only a limited selection of games.

The alternative is a custom "DIY" set-up, offering much more flexibility --- conceivably, you could play run any PC game.
Naturally, this DIY route requires more technical know-how; but as cloud services continue to grow, cloud resources have and should continue to become easier to use.

## Methods

1. Create an AWS EC2 instance using the "NVIDIA Gaming PC - Windows Server 2019" AMI with type g4dn.xlarge
    - The formal ID for this AMI is "nv-windows-server-2019-cloudGaming-512.59-v202204250311-offer-f403759c-b30f-494e-a101-87e3957bba3d"; it creates an instance with a pre-installed NVIDIA T4 GPU driver
    - g4dn.xlarge was sufficiently performant for me and reasonably priced at about 1 USD per hour, but more powerful g4dn instance types should work too
    - At the time of writing, in November 2023, each AWS account must apply to create GPU-enabled EC2 instance types (including g4dn types): I was prompted to apply after the EC2 instance creation wizard, and the request was easily made and usually approved within the hour
2. Connect to the instance remotely via RDP and set-up at least Parsec and VB-Cable
    - You may need to edit the instance's security group to allow incoming TCP 3389 for RDP
    - Parsec is the remote desktop software which streams key inputs from client to server, and video output from server to client
    - VB-Cable creates a virtual audio device essential for the server to transmit sound to the Parsec client
3. Configure the instance as you like: install and log into Steam, change the Adminstrator password, etc.; then create an AMI from the instance
    - 1 USD per hour is a reasonble price for cloud gaming (it is less than my local LAN shopes), but costs can add up if you leave an instance running while not using it (1 USD hourly for 30 days is 720 USD)
    - So, EC2 instances should be terminated when you are done gaming
    - But it is inconvenient to start from the first step every time you want to game
    - So, it is advisable to save the state of the instance by creating an AMI from the fully-configured instance: in the future, instances can be spawned from that AMI, which is akin to loading the "saved state"
    - The Adminstrator password cannot be changed from the graphical control panel; instead, you must use the `net user` command on the Windows Command Prompt: `net user Adminstrator "new password"`
    - You can iteratively update AMIs as you go: save an AMI from an instance spawned from a previous AMI, and so on
4. Create an EBS volume, and attach that volume to the instance: use it to hold game installs
    - This separate EBS volume makes it so you don't have to reinstall games for each new instance spawned
  
After this initial set-up, the steps to start cloud gaming will be:

1. Create a new EC2 instance from the AMI saved in step (3)
2. Attach the volume holding game installs in step (4)
3. Connect to the instance using Parsec

The steps to finish cloud gaming will be:

1. Terminate the EC2 instance (though, you may want to verify cloud saves have finished syncing first)
