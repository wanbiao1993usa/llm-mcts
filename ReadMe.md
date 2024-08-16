# OverView

This project is a guideline for [llm-mcts project](https://github.com/1989Ryan/llm-mcts.git)

Something you need to know:
- This project need to use [watch_and_help project](https://github.com/xavierpuigf/watch_and_help.git) to generate data
- [watch_and_help project](https://github.com/xavierpuigf/watch_and_help.git) need to use [virtualhome project](https://github.com/xavierpuigf/virtualhome.git) to simulate data

# Usage Guidelines

## First:Preparation for virtualhome

1. Download a VirutalHome excutable file


```shell
# Another alternative source:http://virtual-home.org/documentation/master/

wget http://wancloud.ddns.net:20080/remote.php/dav/files/wanbiao/wanbiao-raid1/All-Platform-Share/linux_exec.zip
```

2. Test the above executable to see if it works

Running the executable file
```shell
# unzip linux_exec.zip
unzip linux_exec.zip -d linux_exec
# run 
./linux_exec/linux_exec.v2.2.4.x86_64 -screen-fullscreen 0 -screen-quality 4
```
Open a new terminal and clone [virtualhome project](https://github.com/xavierpuigf/virtualhome.git) 
```shell
git clone https://github.com/xavierpuigf/virtualhome.git
cd virtualhome/
## we must checkout to this branch
git checkout wah 

## create a new python enviroment which must use python 3.8
conda create -n VirtualHome python==3.8
conda activate VirtualHome

## install requirements
pip install -r requirements.txt
pip install virtualhome
```

Run the file demo/unity_demo.ipynb to see if virutalhome enviroment works

### note: Change the following code as required

Original code:
```python
comm.reset(4)
```
Modified code:
```python
success = comm.reset(4)
print("enviroment is ok!!" if success else "enviroment failed!!!")
```
If everything is ok ,you will see "enviroment is ok!!"

## Second:Preparatioin for watch_and_help

1. Clone project
```shell
## Exit to virtualhome sibling directory
cd ..

git clone https://github.com/1989Ryan/watch_and_help.git
## we must checkout the 8894699 node
cd watch_and_help/
git reset --hard 8894699
```

2. bug fix
Remove all text beginning with @file from file requirements.txt。They are on lines 51 and 61，and then install requirements
```shell
## Don't worry about the errors
pip install -r requirements.txt
```

3. Testing to see if watch_and_help can work

The above, which was done on the wah branch of virtualhome, now needs to be switched to the master branch
```shell
cd ../virtualhome/
## restore all the modified code
git checkout .
## checkout to master branch
git checkout master
## cd to wathc_and_help root direcoty
cd ../watch_and_help/
```

buf fix:
```shell
mkdir -p vh/vh_sim
cp -a ../virtualhome/virtualhome/* vh/vh_sim/
```

Run the following command to see if the environment is OK
```shell
## download checkpoint
sh scripts/download_ckpts.sh

pip install torch torchvision tensorboard

## download dataset
wget http://wancloud.ddns.net:20080/remote.php/dav/files/wanbiao/wanbiao-raid1/All-Platform-Share/watch_data.zip

unzip watch_data.zip -d watch_data

cp -a  watch_data/watch_data ./dataset/

## now ,run the following command to see if the environment is OK
python watch/predicate-train.py --gpu_id 0 --model_lr_rate 3e-4 --batch_size 8 --demo_hidden 512 --model_type lstmavg --inputtype graphinput --dropout 0 --single 1 --checkpoint checkpoints/test
```

If it runs for a while and there are no problems, then the environment is ok. you can end the program.

## Third:Preparation for llm-mcts

1. clone
```shell
cd ..
git clone https://github.com/1989Ryan/llm-mcts.git
cd llm-mcts
pip install -r requirement.txt
```

2. prepare submodule
```shell
##virtual home
wget https://uc81ae3ea647ffe3c757dd43200b.dl.dropboxusercontent.com/cd/0/get/CYz9i5nWhO22MS_zSIskw99GS2yYHUH8BXt_PpNUj61egJf2Hkx45joGBu65ZG2fnVRltRmzz67dsbL-NlzJgPs3bRvS818dcv3PEwLsE-KDH6tvLHN9ELaH_iu-fkk8rTwAa5V9vmRmYvc305CnCY7SI41tuLGnNQaSH94iuEumyg/file?_download_id=05186566518576074575940085815366475913658207802078655021087579077&_notify_domain=www.dropbox.com&dl=1

unzip virtualhome.zip -d virtualhome
mv virtualhome/virtualhome/assets/ vh/vh_sim/
mv virtualhome/virtualhome/dataset vh/vh_sim/
mv virtualhome/virtualhome/dataset_utils vh/vh_sim/
mv virtualhome/virtualhome/demo vh/vh_sim/
mv virtualhome/virtualhome/docker vh/vh_sim/
mv virtualhome/virtualhome/helper_scripts vh/vh_sim/
mv virtualhome/virtualhome/resources vh/vh_sim/
mv virtualhome/virtualhome/simulation vh/vh_sim/
mv virtualhome/virtualhome/.gitignore vh/
mv virtualhome/virtualhome/LICENSE.md vh/
mv virtualhome/virtualhome/requirements.txt vh/
mv virtualhome/virtualhome/setup.py vh/

cp -a ../watch_and_help/* ./vh/data_gene/
```

3. Run the command to verify the environment

```shell
mkdir ./vh/dataset
python vh/data_gene/gen_data/vh_init.py --task put_fridge --mode full --use-editor --num-per-apartment 10

export PYTHONPATH=$PYTHONPATH:./vh
pip install scipy anytree ray

python vh/data_gene/testing_agents/gene_data.py --mode full  --dataset_path ./vh/dataset/env_task_set_1_full.pik --use-editor --base-port 8080
```

that's all!!