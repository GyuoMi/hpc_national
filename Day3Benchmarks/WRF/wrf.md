# Resources
[hpc advisory council](https://hpcadvisorycouncil.atlassian.net/wiki/spaces/HPCWORKS/pages/1827438600/WRF+with+Single+Domain+-+Practice+case+for+ISC21+SCC)
# Setup Environment
Doing everything from root and ssh-ing to each node
```
    mkdir WRF
    cd WRF
    mkdir libs
    mkdir tars
```
# .bashrc
```
vi bashrc
```
add:
```
source /home/intel/impi/5.0.1.035/bin64/mpivars.sh intel64
source /home/intel/bin/compilervars.sh intel64
source /home/intel/mkl/bin/mklvars.sh intel64
```
setvars should already be in bashrc??

```
export CC=icc
export CXX=icpc
export CFLAGS='-O3 -xHost -ip -no-prec-div -static-intel'
export CXXFLAGS='-O3 -xHost -ip -no-prec-div -static-intel'
export F77=ifort
export FC=ifort
export F90=ifort
export FFLAGS='-O3 -xHost -ip -no-prec-div -static-intel'
export CPP='icc -E'
export CXXCPP='icpc -E'
export DIR=/home/admin/WRF/libs	
export PATH=$DIR/netcdf/bin:$PATH
export NETCDF=$DIR/netcdf
export LDFLAGS=-L$DIR/grib2/lib
export LD_LIBRARY_PATH=$DIR/grib2/lib:$LD_LIBRARY_PATH
export CPPFLAGS=-I$DIR/grib2/include
export JASPERLIB=$DIR/grib2/lib
export JASPERINC=$DIR/grib2/include
export WRFIO_NCD_LARGE_FILE_SUPPORT=1
export PATH=~/WRF/WPS:$PATH
export PATH=~/WRF/WRFV3/run:$PATH
```
write and quit
```
source bashrc
```
# Dependencies
### Jasper
```
cd /home/admin/WRF/tars
wget http://www2.mmm.ucar.edu/wrf/OnLineTutorial/compile_tutorial/tar_files/jasper-1.900.1.tar.gz
tar -xvf jasper-1.900.1.tar.gz
cd jasper-1.900.1
./configure --prefix=/home/admin/WRF/libs/grib2
make 
make install
```
### NetCDF
```
cd /home/admin/WRF/tars
wget http://www2.mmm.ucar.edu/wrf/OnLineTutorial/compile_tutorial/tar_files/netcdf-4.1.3.tar.gz
tar -xvf netcdf-4.1.3.tar.gz
cd netcdf-4.1.3
./configure --prefix=/home/admin/WRF/libs/netcdf --disable-dap --disable-netcdf-4 --disable-shared
make -j12
make check
make install
```
# Actual Compilation
### WRF
```
cd /home/admin/WRF/tars
wget http://www2.mmm.ucar.edu/wrf/src/WRFV3.7.TAR.gz
tar -xf WRFV3.7.TAR.gz 
mv WRFV3 ..
cd ../WRFV3
./configure
```
You will be prompted to select config options for WRF. NetCDF should be found from environment variables set above.

for Linux x86_64 options: select 20,
then option 1 for compile nesting
After the configure step is complete, build the code with:
```
./compile em_real
```
### WPS
``` 
cd /home/admin/WRF/tars
wget http://www2.mmm.ucar.edu/wrf/src/WPSV3.7.TAR.gz
tar -xf WPSV3.7.TAR.gz
mv WPS ..
cd ../WPS
```
```
./configure 
```
Select option 17: Linux, with Intel, serial /w grib2
```
./compile
```
```
ln -sf ungrib/Variable_Tables/Vtable.GFS Vtable 
```

### Benchmark
```
cd /home/admin/WRF/tars
```
Download the input data for the benchmark here: [GEOG.tar.gz](http://www.ace.chpc.ac.za/tars/GEOG.tar.gz)
```
tar -xf GEOG.tar.gz
mv GEOG ..
cd ../GEOG
cp namelists.wps ../WPS
cd /home/admin/WRF/tars
```
And here : DATA.tar.gz
```
tar -xf DATA.tar.gz
mv DATA ..
cd ../WPS
./link_grib.csh ../DATA/  
```
hpc advisory councilhomehomehome
```
```
setvars should already be in bashrc??[]()(Build the model from the geographical data: cp -t /home/admin/WRF/tars)
```
./geogrid.exe
./ungrib.exe
./metgrid.exe
```
Setup the benchmark run:
```
cd /home/admin/WRF
cp GEOG/namelist.input WRFV3/run
cd WRFV3/run
ln -sf ../../WPS/met_em.d01.2015* .
```
Final setup step:
```
./real.exe
```
Start the benchmark with:
```
mpirun -np <N> -hostfile <HF> ./wrf.exe
