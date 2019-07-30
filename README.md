# NIVA ROMS APPS

Currently available ROMS apps:
 * ##### Fjordos_dramsfjord - a high-resolution (50 m) model for Drammensfjorden based on FjordOS
 * ##### Hunnbunn - a high resolution (25-50 m) model for Hunnbunn
 
The structure of how ROMS apps are run has been forked from the metroms setup from met.no. The benefit of the system is that it is easy to compile, run, and update exsting apps. To setup a new app you will build on the exsiing setup and basically configure two files to make this work. The key files are: 
 
 * `Params.py` 
 * `runjob_fram.sh`

Before running any apps you need to compile ROMS for the appropriate app. First you have to load the required modules on Fram - run the script fram_modules.sh that contains:

```bash
module load Python/2.7.14-intel-2018a
module load CDO/1.9.3-intel-2018a
module load NCO/4.7.2-intel-2018a
module load netcdf4-python/1.4.0-intel-2018a-Python-2.7.14

#module restore system
module load OpenMPI/2.1.2-iccifort-2018.1.163-GCC-6.4.0-2.28
module load iomkl/2018a
module load netCDF/4.4.1.1-iomkl-2018a-HDF5-1.8.19
module load netCDF-Fortran/4.4.4-iomkl-2018a-HDF5-1.8.19
```

Second, you have to setup the environment variables to show metroms where the various components are found and where to save. Go to go to folder `$(METROMS_BASEDIR)/apps` and edit the variables stored in the file `myenv.bash:

```bash
export METROMS_MYHOST="fram" #$1
echo "loading $METROMS_MYHOST paths"
export LC_ALL=C

if [ "$METROMS_MYHOST" == "fram" ]; then
    export METROMS_BASEDIR=$HOME/metroms
    export METROMS_SRC=$HOME/ROMS_v37_26072019
    export METROMS_TMPDIR=/cluster/projects/nn9297k
	export METROMS_BLDDIR=$METROMS_TMPDIR
    export METROMS_APPDIR=$HOME/niva_apps
else
    echo "Undefined METROMS_MYHOST ", $METROMS_MYHOST
fi
export PYTHONPATH=$PYTHONPATH:$METROMS_BASEDIR/apps/common/python/
```
Run the file to make the environments variables active: `source myenv.bash`

Next, compile for the app you want (e.g. `fjordos_dramsfjord`):
```bash
./build_roms_standalone.sh fjordos_dramsfjord
```

To start a job for a particular niva_app go to the  `${METROMS_APPDIR} (${HOME}/niva_apps)` where all of the apps are stored. If you want to run  `fjordos_dramsfjord ` go into directory and execute command:

`sbatch runjob_fram.sh`

This will execute a series of commands which can be summarized as:

1. Setup the environment so that metroms understands where all of the files can be found, where the app should be run etc.A sample can look like this:

```bash
# define and create a unique scratch directory
export ROMS_APPLICATION="fjordos_dramsfjord"
export WORK_DIRECTORY=/cluster/projects/nn9297k/metroms/${ROMS_APPLICATION}
export SCRATCH_DIRECTORY=${WORK_DIRECTORY}/Run/${SLURM_JOBID}
mkdir -p ${SCRATCH_DIRECTORY}
cd ${SCRATCH_DIRECTORY}

export APP_DIR=${METROMS_APPDIR}/${ROMS_APPLICATION}
```
2. 
