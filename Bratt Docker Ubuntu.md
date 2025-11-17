	1)Attempted to created a docker container using the Linux Mint image on the Proxmox vm's Mint is not compatable.  This required manual configuration of the Network settings prior to installing Mint, Mint requires more on the install then Ubuntu.
2) Defaulted to my existing Ubuntu VM on the Proxmox server
3) I followed the Instructions listed on the Docker Install page for Ubuntu. 
	1) first ran the commands to uninstall any pre-existing Docker versions
	2) I installed Docker using the CLI apt repo using the commands given in the install page. 
	3) I checked the Docker running status with 'sudo systemctl status docker' docker is running
	4) I performed a test run, per the install page with 'sudo docker run hello-world' docker ran the test and reported successful operation of the docker container.  
4) I did some familiarization on docker compose using the "docker primer" video provided.
5) I created a directory used "mkdir -p" to create the directory in my host machine 'DkrComp/Bratt_Plex'  for creating the necessary files from github.  I intend to use a Plex compose image. 
6) I created a ".env" file and a "compose.yaml" file in 'DkrComp/Bratt_Plex'.  
	1) per the instructions on the Plex github, I wrote "PLEX_MEDIA_PATH=/media/your/plex/path" to the .env file.
	2) I copied from the github, the code on the compose.yml file to the 'compse.yaml' in my directory.
7) I started the docker container and copied the files from the host machine to the container with 'sudo docker run-di -v /home/josh//DkrComp/Bratt_Plex debian'
8) I then navigated to the /Bratt_Plex dir, entered "sudo docker compose up -d"
9) Per the instruction on the Plex github, I opened an instance of firefox, and entered 'http://localhose:32400/web'   This opened a Plex homepage.


