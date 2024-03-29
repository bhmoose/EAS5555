(1) cd to the directory that contains your DATA, wpsv4.1, and wrfv4.1 directories.\
(2) run `vim .bash_profile`\
(3) edit the blank file that opens and add the following lines:\
![image](https://github.com/bhmoose/EAS5555/assets/143351355/f14eca26-6dad-4637-9d27-5e441e87df13)
\
(4) save the file with `:wq`\
(5) cd into getope directory, do the two line command to add PATH variable from Carlos' github. Adjust dates to desired ones in `dates.loop.5dy.Oct.2016.txt`\
(6) **Make sure format is YYYY-MM-DD_HH:MM:SS**\
(7) run `get.loop.ope.csh`. This should work WITHOUT a `./` in front (if it doesn't, something is wrong)\
(8) Verify that the data you requested exists in scratch directory.\
(9) cd into gowrf directory, do the two line command to add PATH variable from Carlos' github.\
(10) Adjust the `dates.loop.5dy.Oct.2016.txt` file to exactly match the contents of its counterpart in the getope directory.\
(11) If doing a single domain simulation, change other details of simulation (NOT date/time, this is done automatically) in `namelist.FRM-CA.wps` and   `namelist.FRM-CA.input`. If you just want to get something to run, just keep it how it is for now.\
(12) If doing a multiple domain simulation, change the CA-MX input files to include domain 2 info and the CA-MX-CO files to include domain 3 info.\
(13) Run `go.loop.csh`. As with `get.loop.ope.csh`, this should work without the `./`. Otherwise, something is probably wrong (double check PATH set and exported).\
(14) Wait for the script to complete and job to run. If your qstat is blank almost immediately, something is probably wrong\
(15) WPS-related files (GRIBFILE.AAA, met_em.d01.___, geo_em.___ and all the other ones from the tutorial) should be in `/glade/derecho/scratch/<username>/wrf_model/Hurr_Patr-2015_D3/PRE_COMPILED_CODE/wpsv4.0.1/<your_case_date>`\
(16) WRFout files and error/log files should be in `/glade/derecho/scratch/<username>/wrf_model/Hurr_Patr-2015_D3/PRE_COMPILED_CODE/wrfv4.0.1/<your_case_date>/test/em_real`\
NOTE: The "Hurr_Patr-2015_D3" is the case name and can be changed near the top of the `go.loop.csh` file\
