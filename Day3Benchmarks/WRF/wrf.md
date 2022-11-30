# Setup Environment
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
source /opt/intel/impi/5.0.1.035/bin64/mpivars.sh intel64
source /opt/intel/bin/compilervars.sh intel64
source /opt/intel/mkl/bin/mklvars.sh intel64
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
export DIR=~/WRF/libs	
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