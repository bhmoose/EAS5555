# EAS5555

Ben Moose
Derecho / WRF How-To Guide

**Run in WSL Ubuntu**

(1) Log in to Derecho
  Open a terminal / command prompt window and connect to Derecho via the following command:
ssh -XY bmoose@derecho.hpc.ucar.edu
  Enter password for NCAR account when prompted and complete two-factor authentication.

(2) Copy the WRF and WPS executables to your home directory
  Once within Derecho, change directories to the location of the WRF and WPS executables:
cd /glade/work/wrfhelp/derecho_pre_compiled_code
  Copy the files contained in the wrfv4.1 and wpsv4.1 directories to the home directory:
cp -rp [wrfv4.1 or wpsv4.1] /~
		This command copies the relevant directories named in the command to
/glade/u/home/bmoose
  Move data from Derecho to local system using:
scp -r bmoose@derecho.hpc.ucar.edu:/glade/u/home/bmoose/[wrfv4.1 or wpsv4.1] C:/users/benja/WRF
	Enter password and 2FA when prompted.
Now the executables are both within the home directory on Derecho and on my local computer

(3) Download data for the WPS preprocessing system
  Created directory called DATA both in /glade/u/home/bmoose and my local laptop.
  Download matthew_1deg.tar.gz (or any other data file) and copy to the DATA directory via the command cp [source_path] [destination_path]. 
  If doing locally in Windows, used MobaXTerm and accessed local file system through /mnt/c/users/….
  Run tar -xf [data_file_path] to take the .tar.gz data file (if the data is in this format) and unzip it into a directory containing data files (in this case, GRIB2 format).
  NOTE: error in g2print.exe: ./g2print.exe: error while loading shared libraries: libpmi.so.0: cannot open shared object file: No such file or directory and ./g2print: cannot execute binary file: Exec format error. This prevented me from reading the files beforehand
  In the …/benja/WRF directory, create a link to the vtable for the appropriate model. This vtable specifies the fields to be read from the input data, and depends on the model from which the input data is taken (GFS, NAM, NARR reanalysis, SREF, etc.)
  Existing vtables are stored at …/wpsv4.1/ungrib/Variable_Tables
  Linking to a vtable is done using the command:
ln -sf [path_to_vtable] [name_of_link (in this case, Vtable)]
  Originally ran into an error since c shell was not installed. Used chatGPT to suggest sudo apt-get install csh and was able to successfully install c shell and run the following to create standard names (GRIBFILE.AAA, GRIBFILE.AAB, etc.) and link the GRIB files from the DATA directory (ran command from within wpsv4.1 directory):
./link_grib.csh [path_to_data]/matthew/fnl ← shared prefix of data files

(4) Modify namelist.wps and namelist.input
  Use vim namelist.wps to enter text editor within namelist.wps file, edit by typing I to enter insert mode and then :wq to save changes and leave file.
  Guide to namelist vars is here:
https://www2.mmm.ucar.edu/wrf/users/namelist_best_prac_wps.html#max_dom
  Edit max_dom to change total number of domains 
  Edit start and end dates of model run (format specifications in above link)
  If multiple domains, can include a list of dates with single quotes and commas in between delineating start and end dates for each domain
  Edit interval_seconds to change interval in seconds at which model takes in data from existing data files (here, the GFS data) [double-check this]
