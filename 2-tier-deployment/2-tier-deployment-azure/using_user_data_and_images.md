# Tech 258 - Using User Data & Images

## User Data
To speed up our deployment even more, instead of needing to SSH into our instance to run our scripts we can bootstrap our virtual machines with the script that will execute once the virtual machine resource is ready.

To do this we make use of the `User data` component of our setup.

Refer to the README for a general overview of how to deploy your virtual machines [here.](https://github.com/ShafiqueMahen2/tech258_cloud/blob/main/2-tier-deployment/2-tier-deployment-azure/readme.md)

When reaching the `Advanced` tab, look for the `User data` section and tick `Enable user data`.Paste your corresponding script there. Example configuration: <br>
![azure_vm_user_data_demo.png](images/azure_vm_user_data_demo.png)

## Images


