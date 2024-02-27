# EAS5555

Ben Moose
Derecho / WRF How-To Guide

**Run in WSL Ubuntu**

(1) Log in to Derecho
* Open a terminal / command prompt window and connect to Derecho via the following command:
* ssh -XY bmoose@derecho.hpc.ucar.edu
  * Enter password for NCAR account when prompted and complete two-factor authentication.

(2) Copy the WRF and WPS executables to your home directory
* Once within Derecho, change directories to the location of the WRF and WPS executables:
	* cd /glade/work/wrfhelp/derecho_pre_compiled_code
* Copy the files contained in the wrfv4.1 and wpsv4.1 directories to the home directory:
	* cp -rp [wrfv4.1 or wpsv4.1] /~
* This command copies the relevant directories named in the command to
/glade/u/home/bmoose

(3) Download data for the WPS - WRF preprocessing system
* Created directory called DATA in /glade/u/home/bmoose.
* Download matthew_1deg.tar.gz (or any other data file) and copy to the DATA directory via the command scp [local_source_path] bmoose@derecho.hpc.ucar.edu:/glade/u/home/bmoose/EAS5555/DATA
  	* scp command must be run from local terminal / command prompt, not from within derecho
* Run tar -xf [data_file_path] to take the .tar.gz data file (if the data is in this format) and unzip it into a directory containing data files (in this case, GRIB2 format).
* In the wpsv4.1 directory, create a link to the vtable for the appropriate model. This vtable specifies the fields to be read from the input data, and depends on the model from which the input data is taken (GFS, NAM, NARR reanalysis, SREF, etc.)
* Existing vtables are stored at …/wpsv4.1/ungrib/Variable_Tables
* Linking to a vtable is done using the command:
	* ln -sf [path_to_vtable] [name_of_link (in this case, Vtable)]
* Run the following to create standard names (GRIBFILE.AAA, GRIBFILE.AAB, etc.) and link the GRIB files from the DATA directory (ran command from within wpsv4.1 directory):
	* ./link_grib.csh [path_to_data]/matthew/fnl ← shared prefix of data files. No need for a wild card character (*) as noted in tutorial.

(4) Modify namelist.wps
* Use vim namelist.wps to enter text editor within namelist.wps file, edit by typing I to enter insert mode and then :wq to save changes and leave file.
	* Guide to namelist vars is here:
https://www2.mmm.ucar.edu/wrf/users/namelist_best_prac_wps.html#max_dom
	* Edit max_dom to change total number of domains 
	* Edit start and end dates of model run (format specifications in above link)
	* If multiple domains, can include a list of dates with single quotes and commas in between delineating start and end dates for each domain
	* Edit interval_seconds to change interval in seconds at which model takes in data from existing data files (here, the GFS data). Should equal the temporal resolution of the boundary condition input data.
   	* Can also change map projection and other things related to nesting in namelist.wps
   	* Also can adjust the grid resolution (units depend on projection, meters in tutorial) of the domain(s)
   	* GEOG_DATA_PATH specifies path to source of geographic data. Keep it as the default (/glade/work/wrfhelp/WPS_GEOG)
 
(5) Run ungrib, geogrid, and metgrid
* After namelist.wps is edited and saved, run ungrib.exe. This outputs a series of input / boundary data files with variables from the Vtables saved in a way compatible with future steps of the process of running WRF.
* Then run geogrid.exe. This program creates the required geographic data files at the model resolution specified by namelist.wps for input into WRF. Output file has name "geo_em.[domain_number].nc.
	* It is a good idea to inspect this file using ncview (first, use module load ncview) to ensure that the geographic domain is correct.
* Last, run metgrid. This takes in the output from ungrib and geogrid and combines them into gridded data useful for input into WRF. Output files are names "met_em.d[domain_number].[datetime].nc"

 (6) Run real.exe and wrf.exe on login node
* Change directories to the wrfv4.1 directory within EAS5555 directory.
* Change directories to either wrfv4.1/run or wrfv4.1/test/em_real, both of which can be used to run WRF.
* Run the following command to create a link to the met_em files from metgrid output within the wrfv4.1/run or wrfv4.1/test/em_real directories:
 	* ln -sf /glade/u/home/bmoose/EAS5555/wpsv4.1/met_em.d01.2016-10*
* Edit namelist.input to change a variety of model configurations
  	* These include start and end times (for our purposes, ensure these match start and end times of input data), time step (keep to below 6 * horizontal resolution in km according to namelist info document; may have to reduce time step for model to run correctly and not crash), and dynamics-related parameters + options.
  	* Much more namelist info provided here: https://www2.mmm.ucar.edu/wrf/users/docs/user_guide_v4/v4.4/users_guide_chap5.html#Namelist
* Run real.exe to take the input data and put it in final format useful for WRF itself. Outputs wrfinput_[domain_number] and wrfbdy_d01 containing the intialization time gridded data for each domain (wrfinput) and the boundary conditions (wrfbdy)
	* Only ever a single wrfbdy file because only one set of externally-specified boundary conditions is used (others derived from WRF itself for smaller internal domains), as noted in tutorial.
 * Run wrf.exe in the login node by simply typing ./wrf.exe. Use only for small cases where the resources required do not take up most of the login node's compute power (generally better to run as a job, see next section)
	* Outputs netCDF files with format "wrfout_[domain_number]_[datetime]" containing model output data. One output file exists for every history_interval (specified in namelist.input).
 	* Use ncview (module load ncview, then ncview [file_path]) to check if output looks ok
  	* For more detailed analysis of output, copy the wrfout files to local machine
		* First, rename the wrfout files to remove the colons from filename (they confuse scp) using cp [old_filename] [new_filename] and by removing the old files with rm -r [old_filenames]
    		* Then, use scp bmoose@derecho.hpc.ucar.edu:[path_to_wrfout_file_within_glade] [destination_path_on_local_machine] to transfer the files. Run from a local terminal (not on derecho).
	* Can then use xarray to view the output files and analyze them within Python.

(7) Run wrf.exe as a job
Use the following script to run wrf.exe, saved as a .sh file in wrfv4.1/test/em_real directory:


				#!/bin/csh
				#PBS -N matthew_case
				#PBS -A UCOR0073
				#PBS -q main
				#PBS -l walltime=00:30:00
				#PBS -j oe
				#PBS -k eod
				#PBS -m abe
				#PBS -M bhm43@cornell.edu
				#PBS -l select=1:ncpus=8:mpiprocs=8:ompthreads=4
				
				limit stacksize unlimited
				
				setenv MPI_SHEPHERD true
				cd ~/EAS5555/wrfv4.1/test/em_real/
				mpiexec -n 8 -ppn 8 ./wrf.exe

				exit

Adjust the following when changing the case to their appropriate values:
-N (name_of_case)\
-A (project_number) - keep the same for this class\
-l (walltime) - limit to wall time of job to enforce\
-l (select_[#_of_nodes]:ncpus=[number_of_cpus_per_node]:mpiprocs=[keep_same_as_ncpus]:ompthreads=1 - do not change ompthreads unless trying to run in parallel\
mpiexec -n [ncpus] -ppn [ncpus_per_node]\
\
\
Submit job using qsub [submit_script_name.sh], check status with qstat -u $USER.

