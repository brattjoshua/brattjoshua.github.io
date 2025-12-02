1) I first created a Digital Ocean account.  I then created a droplet. I have had some issues with this project, I will omit my errors and only include the final project, as my errors were due to unnecessary work.  I will highlight the steps necessary and if not done, will cause the rework that I encountered.  
	1) I created a Digital Ocean droplet, Linux Ubuntu 22.04 LTS 2Gb ram, 1 Intel vCPU, 70 GB disk, ATL1 data region.  
2) I then started the droplet and went into terminal and updated the version using **sudo apt-get update**
	1) after that I followed commands necessary to install docker for Ubuntu, per the website listed   https://docs.docker.com/engine/install/ubuntu/
	2) I went as far as running the 'hello-world' test container to verify docker functionality.
3) After installing Docker, I followed the steps listed for installed the Docker Compose Plugin, for Ubuntu, using the commands from https://docs.docker.com/compose/install/linux/#install-using-the-repository
	1) Docker Compose commands are 
		1) sudo apt-get update
		2) sudo apt-get install docker-compose-plugin
4) After installing the Compose Plug-in, I began working on Wireguard.  
	1) I first created the necessary directories using 
		1) **mkdir wireguard && cd wireguard&&
	2) I then created the docker-compose file and wrote to it using 
		1) **touch docker-compose.yml and nano docker-compose.yml**
			I copy pasted onto my .yml file in nano
	3) I ran **docker exec -it wireguard /app/show-peer 1** **  This showed a QR code for my phone to use in the wireguard app to create a new VPN tunnel. 
		1) I downloaded the Wireguard app for my phone. I used the Wireguard app to pull the info from the QR code the Wireguard container generated.  I collected a screenshot of my IP using IPleak before and after activating the VPN, the IP addresses showed a change, confirming the VPN is working. 
	4) I then installed the wireguard app on both my laptop. 
		1) I collected a IP and screenshot from IPleak.net before configuring the VPN on my laptop.  
		2) I navigated to /wireguard/config/peer2/ on my wireguard ubuntu droplet, then used **cat peer2.conf** to read the configuration information of peer2, which is the config information I need for wireguard on my laptop.  
		3) I copied the peer2.conf information onto the subsection **add tunnel>empty tunnel**  I cleared out the text in the box, pasted the peer2.conf info into the empty tunnel box, named the tunnel, and saved it.  
		4) I then activated the tunnel, went to IPleak.net, the IP had shown a change, demonstrating that the VPN tunnel is working.  
5) Docker-compose.yml will be attached in the github page along with screenshots. 