# deployingASG
### how i deploy an auto scaling project on AWS

I'm going to deploy an  [express api application][api] on AWS with an auto scaling group, it's a simple curd api made with typescript express and a file to read data from, takes the port from the environment if it doesn't exist it'll default to port 3000.

there's a `/health` endpoint to tell the AWS load balancer that the instance is running without problems.

app is managed through systemctl with a [service](https://github.com/xsharawi/expressBook/blob/master/app.service) and a [timer](https://github.com/xsharawi/expressBook/blob/master/app.timer) (refreshing it periodically).

it's managed through github [releases](https://github.com/xsharawi/expressBook/releases/latest) where the complied app is packaged into  server.tar.gz compressed file.


any and all suggestions are welcomed.


## prerequisites
AWS account, taking a look at the project we're deploying [here.][api]

## template for instances
### [this script]('./script.sh') was used to make the instances:
- Updates the current system
- Installs node and a few other tools
- Downloads the latest server.tar.gz
- Installs all of the dependancies needed downloads
- Activates the service and timer 


## making the auto scaling group:
### step 1:
- go to the auto scaling group tab inside Amazon EC2
- create auto scaling group
- give it a name and a launch template( if you don't have one make it here with the script as your `user data` input) 

### it shoudl look like this after you're done

![NameAndTemplate](./imgs/1)


### step 2:
- ## Network:
  - leave the VPC as default
  - add all free availability zones
- ## Instance type requirements
  -  leave as default from the template

### step 3:
- ## Load balancing
    -  either make a new load balancer or select one you had before, i made one before and configured it so im just selecting mine
- ## Health checks
  - Turn on Elastic Load Balancing health checks and give it 300s 
- ## Additional settings
  - enable them all and also give 300s
  
### step 4:
- ## Group size
    - for this app we'd like it to have a Desired capacity of 2 min capacity of 2 and a maximum capacity of 6
  - we'd like our app to be always online but also prevent DDOS attacks so our bill stays within reach
- ## Scaling policies:
  - turn it on and make it set on average cpu utilization of 60 and leave the instance warm up as is

![gruopSize](./imgs/2)
![scaling policies](./imgs/3)


### step 5:
- ## notifications 
  - leave them as is.
- ## tags
  - leave them as is.

### after all that you should see a review tab with everything set to your liking if not go back and change things around (don't worry all of your values would stay in place)

### and just like that vola you have an auto scaling app running on amazon servers with your instances behind an auto balancer!
![vola](./imgs/4)

### here the instances running within the target group being healthy

![instances](./imgs/5)

## operation scaling is a success!!!



## possible upgrades:

- making sure the instances are not reachable unless it's from the load balancer
- changing the method of making new instances to add instances from an image of an instance instead of a new ubuntu instance and configuring it from 0

[api]:[https://github.com/xsharawi/expressBook]