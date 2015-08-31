# RAPIDpy
Python scripting interface for the RAPID progam.
More information about installation and the input parameters can be found at http://rapid-hub.org.
The source code for RAPID is located at https://github.com/c-h-david/rapid/.

#Installation

##Step 1: Install RAPID
**For Ubuntu:**
```
$ apt-get install gfortran g++
```
Follow the instructions on page 10-14: http://rapid-hub.org/docs/RAPID_Azure.pdf.

Here is a script to download prereqs: http://rapid-hub.org/data/rapid_install_prereqs.sh.gz

##Step 2: Install netCDF4-python
###Install on Ubuntu:
```
$ apt-get install python-dev zlib1g-dev libhdf5-serial-dev libnetcdf-dev
$ sudo su
$ pip install numpy netCDF4
$ exit
```
###Install on Redhat:
*Note: this tool was desgined and tested in Ubuntu*
```
$ yum install netcdf4-python hdf5-devel netcdf-devel
$ sudo su
$ pip install numpy netCDF4
$ exit
```

#How to use

## Step 1: Initialize the RAPID manager class. 
- First, add the path to you rapid executable location. 
- Next, you need to either tell it how many processors to use using the *num_processors* input variable or to use all available processors set *use_all_processors* to true.
- After that, add any other parameters you would like to use that would normally be in the rapid namelist file (this is case sensitive).


Example:
```python
   rapid_manager = RAPID(rapid_executable_location='~/work/rapid/run/rapid'
                         use_all_processors=True,                          
                         ZS_TauR = 24*3600, #duration of routing procedure (time step of runoff data)
                         ZS_dtR = 15*60, #internal routing time step
                         ZS_TauM = len(era_interim_file_list)*24*3600, #total simulation time 
                         ZS_dtM = 24*3600 #input time step 
                         )
```

## Step 2 (optional): Add/update additional parameters later
You can add or update parameters using the *update_parameters* function by using the name of the variable in the rapid namelist file (this is case sensitive).


Example:
```python
    rapid_manager.update_parameters(rapid_connect_file='../rapid_input_directory/rapid_connect.csv',
                                    Vlat_file='../rapid_input_directory/m3_riv.nc',
                                    riv_bas_id_file='../rapid_input_directory/riv_bas_id.csv,
                                    k_file='../rapid_input_directory/k.csv',
                                    x_file='../rapid_input_directory/x.csv',
                                    Qout_file='../OUTPUT/Qout.nc'
                                    )
```
## Step 4 (optional): Update reach number data
If you don't want to manually count the numbers for the rapid_connect or riv_bas_id files, use the *update_reach_number_data* function.


Example:
```python
rapid_manager.update_reach_number_data()
```

## Step 5: Run RAPID
This will generate your rapid_namelist file and run RAPID from wherever you call this script (your working directory).

Example:
```python
rapid_manager.run()
```

##Step 6 (optional): Convert RAPID output to be CF Compliant
This will convert the RAPID output to be CF compliant. This will require a comid_lat_lon_z file.
Additionally, it prepends time zero to you simulation. If no qinit file is given, a value of zero is added.

Example:
```python
    rapid_manager.make_output_CF_compliant(simulation_start_datetime=datetime.datetime(1980, 1, 1),
                                           comid_lat_lon_z_file='../rapid_input_directory/comid_lat_lon_z.csv',
                                           project_name="ERA Interim Historical flows by US Army ERDC") 
```

