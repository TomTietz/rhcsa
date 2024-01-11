# Containers

### Question
Login to the registry.
Download image of a web server. 
Run the web server in a container as a user-service on port 8080, sharing files from /home/user/webfiles.
Ensure the web server is available across reboots.

***
(scroll down for an answer)

<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

### Answer (RHEL 9)

1. Switch to user account the container is supposed to be run as `su - USER`
2. Authenticate if you have a private registry with images to work with. To let anyone do the exercise public repository is used through the rest of step
```
podman login
```
3. Pull webserver from repository
```
podman image list                              # list local images
podman [image] search nginx                    # search repositories for images
podman [image] pull docker.io/library/nginx    # pull image from repo
```
4. Run webserver
```
# create server container
podman create -v /home/USER/webfiles:/usr/share/nginx/html \
-p 8080:80 --name user_nginx docker.io/library/nginx

podman start user_nginx                 # start server
podman ps                               # check server status
```
5. Create systemd service from container [DEPRECATED]
```
podman generate systemd user_nginx > ~/.config/systemd/USER/user_nginx.service   # create service from container
systemctl --user enable --now user_nginx.service                                 # start and enable service
loginctl enable-linger                                                           # ensuire user logs in automatically at boot
```
6. Configure firewalld and SELinux to allow webserver/container access  
```
firewall-cmd --permanent --add-service http --add-port=8080/tcp           # add firewall rule
firewall-cmd --reload                                                     # reload firewall
semanage fcontext --add -t container_file_t "/home/USER/webfiles(/.*)?"   # allow container access to files
restorecon -vR /home/user/webfiles                                        # reload file contexts
```

## Additioonal comment
`podman generate systemd` is now deprecated but still part of the course material which is why it's used here. The correct way to create a systemd container is a Quadlet. For more details see quadlet(5) or podman-systemd.unit(5)