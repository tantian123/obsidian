```
# docker pull jenkins/jenkins:lts
lts: Pulling from jenkins/jenkins
a492eee5e559: Pull complete 
cfe6e8049b20: Pull complete 
a36abae8f559: Pull complete 
aaad395e4e83: Pull complete 
15c4963bfd8e: Pull complete 
2b2f9df68344: Pull complete 
0be2444c0a64: Pull complete 
bba1a160bb54: Pull complete 
695ab411b701: Pull complete 
098e6cd28a3d: Pull complete 
187d98c68c0a: Pull complete 
2101411f4e14: Pull complete 
Digest: sha256:01ccf53ac68037a1dc5aff50bf2336e6a672532cbe2536dcb91ccb86447894ad
Status: Downloaded newer image for jenkins/jenkins:lts
docker.io/jenkins/jenkins:lts
# docker run -p 8180:8080 -p 50000:5000 --name jenkins \
-u root \
-v /mydata/jenkins_home:/var/jenkins_home \
-d jenkins/jenkins:lts> > > 
42ea81909d15422e90706b81085a139acf5cc394fe32d0980428ed90de88e5d4
# sudo docker exec -it jenkins cat /var/jenkins_home/secrets/initialAdminPassword
0c4d69b1cffe445585707a04eada8ba5
# ^C
# 
```