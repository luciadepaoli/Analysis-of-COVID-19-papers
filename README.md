# Analysis of COVID-19 papers on distributed machine using DASK
## Project for Management and Analysis of Physics Datasets course at University of Padua.

**Authors**: Lucia Depaoli, Alessandro Fella, Simone Mistrali

**Project Summary**: This distributed computing project focuses on the analysis of $1000$ papers about COVID-19, SARS-CoV-2 and related corona viruses. The dataset is a sub-sample of $1000$ items taken from the original dataset that is composed of more than $75000$ papers. This dataset is a part of real-world research on COVID-19 named COVID-19 Open Research Dataset Challenge (CORD-19). The research and related challenges are available on the dedicated [page](https://www.kaggle.com/allen-institute-for-ai/CORD-19-research-challenge) on Kaggle.

### Introduction

For the purpose of this assignment, we have relied on CloudVeneto.
We have created a cluster using the $3$ machine available on the server. One machine is the `dask-scheduler`, the other $2$ are the `dask-worker`. 
In order to make the cluster working, the same Dask and Distributed version should be installed in every machine. Also, the Python's packages used should be the same.

We access the machine via `ssh`:

``` bash
ssh -t -L port:127.0.0.1:port username@gate.cloudveneto.it ssh -L port:127.0.0.1:port root@ip-machine
```

`username`, `ip-machine`, `port` need to be changed. In our case, we have the following ip-address:
- `10.67.22.115` first machine
- `10.67.22.202` second machine
- `10.67.22.125` third machine

In order to create the cluster the three machine need to communicate and "see" each other without password request.

The three machines see each other out-of-the-box because they are in the same network (we need to open a gate to Cloud Veneto server in order to see them).

The main problem is the `ssh` connection, because this type of connection always ask for a password and Dask can not automatically satisfy this request. To overcome this we make all the machines "SSH known host", making the login password-less.
For each machine we create a SSH key using the command `ssh-keygen`, and copy it to all the other machines (include the machine itself) using the following command:

```bash
ssh-copy-id 10.67.22.{115,202,125}
```

So all the machines now can communicate each other without asking the password.

### Cluster creation

We access the first machine using `port-7000`:

``` bash
ssh -t -L 7000:127.0.0.1:7000 username@gate.cloudveneto.it ssh -L 7000:127.0.0.1:7000 root@10.67.22.115
```


Then we use `dask-ssh` to create the cluster. After the command we have to specify the ip-address of the machines used. The first one is used for the scheduler, the following for the workers. 
The output will show the port of the scheduler and the workers. In this case we have created a cluster with 3 workers.

``` bash
dask-ssh 10.67.22.115 10.67.22.202 10.67.22.125
```

Next step is to create a ssh tunnel for the dask dashboard, which usually is by default in the `port-8787`. This will make the dask dashboard visible at `localhost:8787`:

``` bash
ssh -t -L 8787:127.0.0.1:8787 username@gate.cloudveneto.it ssh -L 8787:127.0.0.1:8787 root@10.67.22.115
```


Then we need to connect to the cluster via Jupyter Notebook.

``` bash
jupyter notebook --ip 127.0.0.1 --port 7000 --no-browser --allow-root
```

Inside Jupyer Notebook, we start the cluster (specifying the scheduler address).

``` bash
Client("tcp://10.67.22.115:8786")
```
