## Deploying Ray cluster of HAL through Jupyter Notebook


## Introduction
This is the instruction for setting up a Ray cluster with autoscaling capability on HAL cluster through a Jupyter Notebook. With the autoscaling functionality, the Ray cluster tries to add more "nodes" to the existing Ray cluster by submitting new SLURM jobs, and disconnect "nodes" by cancelling SLURM jobs when idle. 


## Private Environment and Requirements


## Instructions

1. Download the autoscaler code and deployment script from https://github.com/TingkaiLiu/Ray-SLURM-autoscaler by running:
```
git clone https://github.com/TingkaiLiu/Ray-SLURM-autoscaler.git
```


2. Download the script that automatically launches the Ray cluster for you from https://github.com/wtegge2/Ray_cluster_automation by running:
```
git clone https://github.com/wtegge2/Ray_cluster_automation.git
```

It is important to note that you should download this in the same directory that you performed step 1 in. 


3. Launch a Jupyter Notebook through the OnDemand site. 


4. Move your desired project notebook into the same directory that you performed steps 1 and 2 in, or just create a new empty notebook. 


5. Create a new code cell in the notebook. Enter the below code to in order to launch the automation script. You should change the arguments to fit what requirements you want the cluster to have. A description of the arguments can be found below. 
```
import os
os.system("python auto_script.py NetID Environment HEAD_NODE_CPUS HEAD_NODE_GPUS WORKER_NODE_CPUS WORKER_NODE_GPUS")
```

Arguments:
NetID - User's UIUC NetID
Environment - User's conda environment
HEAD_NODE_CPUS - Number of CPUs you would like on the head node
HEAD_NODE_GPUS - Number of GPUs you would like on the head node
WORKER_NODE_CPUS - Number of CPUs you would like on each worker node
WORKER_NODE_GPUS - Number of GPUs you would like on each worker node

To run this cell in the notebook, hold down the shift key and press enter. 


**Note**: This will print out a big message. Look for a line in the output that contains the 'Local Node IP'. This corresponds to the HAL node that your Jupyter Notebook is running on.  This IP is important. You need to take note of this IP as it will be used in the next step. 

It should look something like: "Local node IP: 192.168.20.8"


6. Create a new code cell below. Copy and paste the below code. This block of code initiates Ray and connects you to the Ray cluster that you just launched in the previous cell using the Local Node IP you found in the output. 
```
import ray
ray.init(address='ray://<Local_Node_IP_HERE>:10001')
```

You will need to replace "<Local_Node_IP_HERE>" with the Local Node IP you found in the output of step 5.

Run this cell, and wait for an output. If the output looks something like the message below, you have successfully connected to the Ray Cluster. 
```
OUTPUT:
ClientContext(dashboard_url='127.0.0.1:8265', python_version='3.9.12', ray_version='1.11.1', ray_commit='{{RAY_COMMIT_SHA}}', protocol_version='2021-12-07', _num_clients=1, _context_to_restore=<ray.util.client._ClientContext object at 0x7fff35162a00>)
```


You can also run the command below to double check that the HAL node you are on is connected to the Ray cluster you launched. 
```
ray.nodes()
```

7. Feel free to create new cells below and input any code you wish to run on the cluster. 


8. When you are finished working, always remember to tear down you Ray cluster! 

To tear down the Ray cluster from inside the Jupyter Notebook, create a new cell and run the following code:
```
import os
cwd = os.getcwd()
os.chdir(cwd + '/Ray-SLURM-autoscaler')
os.system("ray down ray-slurm.yaml --yes")
```