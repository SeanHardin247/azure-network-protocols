<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

<h1>Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines</h1>
In this tutorial, we observe various network traffic to and from Azure Virtual Machines with Wireshark as well as experiment with Network Security Groups. <br />


<h2>Video Demonstration</h2>

- ### [YouTube: Azure Virtual Machines, Wireshark, and Network Security Groups](https://www.youtube.com)

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Various Command-Line Tools
- Various Network Protocols (SSH, RDH, DNS, HTTP/S, ICMP)
- Wireshark (Protocol Analyzer)

<h2>Operating Systems Used </h2>

- Windows 10 (21H2)
- Ubuntu Server 20.04

Both of these operating systems are connected to the same Virtual Network

<h2>Steps</h2>

- Connect to the windows 10 VM with remote desktop using the VMs public ip address and the username and password you set when creating it in azure
  
- Install wireshark on the VM with all the default settings in the setup wizard. Wireshark can be found at https://www.wireshark.org/
  
- Open Wireshark when it is finished installing, clcik on ethernet and near the top left of Wireshark there should be a blue fin buttton to start capturing packets, click that.
  
- In the display filter above all the moving text type "icmp" and press enter. There shouldnt be any traffic popping up.
  
- Retrive the Ubuntu server VMs private ip address from azure, open command prompt within VM1 and type "ping (private ip adress without brackets)" observe some traffic now popping up in wireshark.
  
  
you can try pinging a public ip address like www.google.com and also notice traffic popping up on Wireshark  

- Start a perpetual ping from the windows 10 VM to the Ubuntu VM by typing "ping -t (private ip address)"
  
- Go back to azure from your computer and search for Network security groups, click on it and find the security group for your Ubuntu VM, click on it and on the left click the drop down menu for settings and select inbound security rules. Click add.
  
- in the security rule options, keep source, source port, destination as default settings. Change protocol to ICMP which should also change Destination port ranges to just a "*" symbol, change action to deny. Set the priority to 200 (lower numbers are executed first) change the name to something appropiate like "DENY_ICMP_PING_FROM_ANYWHERE" and wait for the rule to be created.

command prompt should then keep saying "Request timed out" and new traffic in Wireshark should only come from one source IP since we are no longer being replied back too. although ive had an issue once were it didnt stop or i stopped receiving messages alltogether, i was able to fix this by pressing control + c (this stops perpetual pinging) and try to ping it again, which that time i got "request timed out" so if you get this error try pinging again to confirm if you did it correctly.

- Go back in Wireshark and change the filter from icmp to ssh. There shouldnt be any traffic.
  
- In command prompt type "ssh (the username of Unbuntu VM)@(ubuntu private ip address)" then press enter, some traffic should pop up in Wireshark. It will ask if you want to continute connecting, type yes and hit enter. If you get "permission denied" you likely didnt set up the Ubuntu VM the right way in azure.
  
- Input the password of the Ubuntu VM, you wont be able to see it while typing.
  
- Now you should be connected to the Ubuntu VM, notice whenever you type something there is traffic appearing in Wireshark. Type things like username or pwd and hit enter to see more traffic being spammed in Wireshark.

So you (hopefully) understand traffic between VMs a bit now, by paying attention to the source and destination tab you can see where things are coming from and where they are meant to go. Heres some other protocols you can observe

- Change the Wireshark filter from ssh to dhcp and hit enter, type ipconfig /renew in command prompt and hit enter, you can see a request and a reply, the reply ip address should be from a microsoft building, but you wont know that unless you look it up, you dont have to.
  
- Change filter to dns. there might be a bit of traffic, type nslookup www.google.com youll get a bunch of traffic in Wireshark, and youll get a bunch of googles ip addresses in command prompt.
  
- If you change the filter to tcp.port == 3389 you can see a lot of traffic coming in, this is because its the stream of traffic happening between our computer and the VM

And were done here, thats all i have to show off in this tutorial. I hope it was interesting.
