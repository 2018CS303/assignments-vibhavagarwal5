# **Docker Case Study** - Automate Infra allocation for L&D

### **Objective**:-
- Dynamic Allocation of Linux systems for users
- Each user should have independent Linux System
- Specific training environment should be created in Container
- User should not allow to access other containers/images
- User should not allow to access docker command
- Monitor participants containers
- Debug/live demo for the participants if they have any doubts/bug in running applications. 
- Automate container creation and deletion.

## Create training environment for L&D
The `ubuntu` or any other OS image might not be sufficient since it might not contain all necessary programs for the training. We can create a new image upon the original `ubuntu` image with the necessary programs and files and use that for each user.

- First build a new `ubuntu` container and use it's shell by running `docker run --name train -it ubuntu /bin/bash`. The container's shell should now be available.
- Make necessary changes there like installing new packages or adding new files.
- We can create a new image from the `train` container by running `docker commit train trainContainer:v1` (`trainContainer` is image name and `v1` is its tag indicating the version1 of the container) from the bash of your machine and not from your container bash.
- Now the `trainContainer` image will be used to create containers for each user.

## Allocate Linux systems for users
1.  To dynamically allocate the users a linux system create a shell script `create_userContainers.sh` to automatically create docker containers using a specific environment docker image for every user.

    - `users.txt`
        ```
        alpha
        beta
        gamma
        theta
        lambda
        ```
    - `create_userContainers.sh`
        ```sh
        echo -n "Enter user list file: "
        read file

        while read user
            do 
                docker create -it --name $user <Training Image> /bin/bash
            done < $file
        ```
2.  Run the shell script `create_userContainers.sh` and enter the `users.txt`. This creates a docker container corresponding to each username from that file.
3.  The user can then use the container allocated to him/her using `useContainer.sh` script.
    - `useContainer.sh`
        ```sh
        echo -n "Enter your username: "
        read name
        docker start $name
        docker attach $name
        ```
4.  This allows user to enter to his/her allocated Linux system and has only access to the bash of that system.

## Isolation of the containers
Since the above container shell is a process within a container it is isolated from the host system and other containers. Any change from the command or program will affect only the container and not the host system. The commands mentioned here isolate each container completely. The user will also not be able to access the docker command from within the container.

## Monitoring the container
- One can monitor the participants container using the `monitorContainer.sh` script.

    - `monitorContainer.sh`
        ```sh
        echo -n "Enter container name to be monitored: "
        read name
        docker logs -f $name
        ```
- This shows the real time display of their bash which helps the participants if they have any doubts/bug in running applications.

- Incase the administrator wants to access the participants container bash for debugging purposes, he can use `docker exec -it <Container> bin/bash`

## Automating deletion of the containers
- Automate the deletion using the `deleteContainers.sh` script.

    - `deleteContainers.sh`
        ```sh
        echo -n "Enter 'all' to delete all user containers or enter 'user' to delete a specific user container: "
        read typ
        if [ "$typ" == "all" ]
        then
            echo -n "Enter the user list file: "
            read file
            while read user
                do
                    docker rm $user
                done < $file
        else
            echo -n "Enter the username: "
            read name
            docker rm $name
        fi
        ```
- This gives two options ie. to either delete all users containers at once or delete a specific user container.

**Note:** To run any shell script in the terminal use the following command: 
```
sh <shell script> 
```
or  
```
bash <shell script> 
```
