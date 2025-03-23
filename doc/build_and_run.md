# 1. Build
On orion/hercules/gaea, you will need to run `module load git-lfs` first.
```
GIT_LFS_SKIP_SMUDGE=1 git clone --recursive https://github.com/NOAA-GSL/GRFS.git
cd GRFS/sorc
./build.all
```
If you run cold start forecasts only and don't need data assimilation, you can `vi build.all` and comment out this line `./build.rdas &> ./log.build.rdas 2>&1 &` before running `./build.all`

# 2. Setup and run experiments:
### 2.1. cat/copy and modify exp.setup
```
cd workflow
# find the target exp setup template file, copy it. Here we use exp.conus12km as an example:
cp exp/exp.conus12km exp.conus12km
vi exp.conus12km # modify as needed; usually need to change OPSROOT, ACCOUNT, QUEUE, PARTITION
```
In retro runs, for simplicity, `OPSROOT` provides a top directory for `COMROOT`, `DATAROOT` and `EXPDIR`. But this is NOT a must and you may set them separately without a shared top directory.
    
Refer to [this guide](https://github.com/NOAA-EMC/rrfs-workflow/wiki/deploy-a-realtime-run-in-Jet) for setting up realtime runs. Note: realtime runs under role accounts should be coordinated with the POC of each realtime run.

### 2.2 setup_rocoto.py
```
# Here we use exp.conus12km as an example:
./setup_rocoto.py exp.conus12km
```   
    
This Python script creates an experiment directory (i.e. `EXPDIR`), writes out a runtime version of `exp.setup` under EXPDIR, and  then copies runtime config files from `HOMErrfs/parm` to `EXPDIR`.
       
### 2.3 run and monitor experiments using rocoto commands

Go to `EXPDIR`, open `rrfs.xml`, and make sure it has all the required tasks and settings.
    
Use `./run_rocoto.sh` to run the experiment. Add an entry to your crontab similar to the following to run the experiment continuously.
```
*/5 * * * * /home/role.rtrr/RRFS/1.0.1/conus3km/run_rocoto.sh
```
Check the first few tasks/cycles to make sure everything works well. You may use [this handy rocoto tool](https://github.com/rrfsx/qrocoto/wiki/qrocoto) to check the workflow running status.

### note
The workflow depends on the environmental variables. If your environment defines and exports rrfs-workflow-specific environmental variables in an unexpected way or your environment is corrupt, the setup step may fail or generate unexpected results. Check the `rrfs.xml` file before `run_rocoto.sh`. Starting from a fresh terminal or `module purge` usually solves the above problem.


