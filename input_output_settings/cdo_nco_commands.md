This file contains examples of cdo and nco commands (in no specific order) that can be used to produce FUSE input files.

* extract specific variables from a file containing multiple variables:

```
for ((y=1979;y<=1989;y++)); do
    ncks -v t_mean erai_gard_$y.nc  wrf50_erai_metsim_tas_$y.nc
    ncks -v pcp erai_gard_$y.nc  wrf50_erai_metsim_pr_$y.nc
done
```

* change _FillValue to -9999 - the 4th argument must be either f or d (float or double) depending on the kind of the variable:

```
ncatted -O -a _FillValue,pet,o,d,-9999. wrf50_erai_metsim_pet.nc
ncatted -O -a missing_value,pet,o,d,-9999. iwrf50_erai_allvars_$y.nc
```


* find period covered

```
cdo showyear file.nc

```

* extract data and create files for specific years
```
for ((y=1979;y<=1989;y++)); do
    cdo selyear,$y cesm1-cam5_rcp85_r1i1p1_pet.nc cesm1-cam5_rcp85_r1i1p1_$y.nc;
done
```

* combine files for different variables (already on the same grid)

```
for ((y=1979;y<=1989;y++)); do
  cdo merge wrf50_erai_metsim_pet_$y.nc wrf50_erai_metsim_tas_$y.nc wrf50_erai_metsim_pr_$y.nc wrf50_erai_metsim_allvars_$y.nc;
done
```

* change metadata

```
for ((y=1979;y<=1989;y++)); do

  my_file=wrf50_erai_metsim_allvars_$y.nc

  # rename variables
  ncrename -O -v t_mean,temp $my_file $my_file
  ncrename -O -v pcp,pr $my_file $my_file
  ncrename -O -v lon,longitude $my_file $my_file
  ncrename -O -v lat,latitude $my_file $my_file
  #ncrename -O -d lon,longitude $my_file $my_file
  #ncrename -O -d lat,latitude $my_file $my_file

  # change units
  ncatted -O -a units,pr,m,c,"mm/day” $my_file $my_file
  ncatted -O -a units,pet,m,c,"mm/day” $my_file $my_file

  # change missing value
  #ncatted -O -a _FillValue,pr,o,f,-9999. $my_file
  #ncatted -O -a missing_value,pr,o,f,-9999. $my_file

done
```