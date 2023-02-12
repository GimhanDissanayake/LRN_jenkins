# LRN_jenkins

## Intro
1. Jenkins is an automation server.
2. often associated with building source code and deploying the results
3. ability to distribute jobs across multiple nodes
4. Jenkins controller sends jobs to the appropriate agent based on the job requirements and the resources available at the time.
5. possible to run jobs on the controller, Best practice is to always create at least one agent and run jobs there

### Importance of Not building on the built-in node
1. Any builds running on the built-in node have the same level of access to the controller file system as the Jenkins process.
2. To block: Manage Jenkins » Manage Nodes and Clouds >> Select Built-In Node in the list, then select Configure in the menu. Set the number of executors to 0 and save
3. Alternatively, use a plugin such as Job Restrictions Plugin to limit which jobs can be run on certain nodes (like the built-in node), independent of what your less trusted users may use as label expression in their jobs' configurations.
4. If you do not have any other computers to run agents on, you can also run an agent process as a different operating system user on the same system to achieve a similar isolation effect.

Terms:
### CI(Continous Integration)
- Automatically testing and building the code once the developer push the code to the code repo

### CD(Continous Delivery/Deployment)
- Delivery:
1. May involve a manual step to approve a deployment
2. is an extension of continuous integration since it automatically deploys all code changes to a testing and/or production environment after the build stage. 
- Deployment:
1. No manual approvals
2. Every change that passes all stages of your production pipeline is released to your customers