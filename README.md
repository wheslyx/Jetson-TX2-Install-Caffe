
# Jetson-TX2-Install-Caffe


Installing Caffe v1 official release from BVLC in embedded device Nvidia Jetson TX2 8GB with JetPack L4T 4.2 as updated as August 2019 rellease.

Official Caffe: https://caffe.berkeleyvision.org/

## Getting Started: For JetPack L4T v4.2 as of latest August 2019 JetPack release Ubuntu 18.04 comes with OpenCV v3.3 (No CUDA support), c++ (Ubuntu/Linaro 7.4.0-1ubuntu1~18.04.1) 7.4.0, Python 3.6 and CUDA 9.0 with 7.x cuDNN

### Prerequisites

Dependencies for installation:
sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev \
                       libhdf5-dev libhdf5-serial-dev protobuf-compiler
sudo apt-get install --no-install-recommends libboost-all-dev
sudo apt-get install libgflags-dev libgoogle-glog-dev liblmdb-dev
sudo apt-get install libatlas-base-dev libopenblas-dev


### Installing

In your terminal type
git clone https://github.com/BVLC/caffe
cd caffe
cp Makefile.config Makefile.config.example (download Makefile.config and modify paths according to device and user name)
make -j6 all
make -j6 test
make runtest 
mkdir -p ~/src #As of recommendation from jkjung in https://jkjung-avt.github.io/caffe-on-tx2/ I installed 
               # updated package leveldb-0.20
ldconfig # distribute Caffe in the system
cd ~/src
wget https://pypi.python.org/packages/03/98/1521e7274cfbcc678e9640e242a62cbcd18743f9c5761179da165c940eac/leveldb-0.20.tar.gz
tar xzvf leveldb-0.20.tar.gz
cd leveldb-0.20
python3 setup.py build
sudo python3 setup.py install
pkgs=`sed 's/[>=<].*$//' ~/caffe/python/requirements.txt`
for pkg in $pkgs; do sudo pip3 install $pkg; done
cd ~/caffe #make caffev wrapper for Python3 --> pycaffe
make pycaffe


to check that caffe is installed type
.caffe/build/tools/caffe # Shows Caffe options to do train, test and time a actions


## Running the tests

./caffe/build/tools/caffe time --gpu 0 --model caffe/examples/mnist/lenet.prototxt #Measure execution time of 1 iteration, you can also add number of iterations with "--iteration"
./caffe/build/tools/caffe train --solver  --gpu 0 # Train your model
./caffe/build/tools/caffe train --gpu 0 --solver path_to_solver/solver.prototxt --snapshot path_to_snapshot/caffe_net_1_iter_1000.solverstate #Resume your training
./caffe/build/tools/caffe test —model path/to/deploy/deploy.prototxt —-weights path/to/trained/weights.caffemodel —-gpu 0 —iterations 100 # Benchmark of trained file for 100 iterations


To run automated tests of Caffe for classification go to 
https://caffe.berkeleyvision.org/gathered/examples/cpp_classification.html
For more documentation visit:
https://caffe.berkeleyvision.org

## Deployment
To let Caffe use your Python3 scripts it is required to export PYTHONPATH and add the following line to your ".bashrc"
export PYTHONPATH=/path/to/python3/script/:$PYTHONPATH
Caffe package is imported in python scripts using:
import caffe # Rememeber to install scikit-image v0.15 or higher and numpy v1.17 to avoid "no module named 
             # skimage.io"

To use your C++ scripts with Caffe it is required a Cmake installation (2nd part of this guide coming soon) with CmakeLists.txt
With this kind of installation Caffe package is imported in C++ scripts using:
#include "caffe.hpp"

## Built With

* Caffe dependencies
* scikit-image v0.15
* leveldb-0.20

## Contributing

https://jkjung-avt.github.io/caffe-on-tx2/ for installation of Caffe in Jetson TX2 with JetPack 3.3

## Versioning

Version 1.0 Alpha

## Authors

* Wheslyx

## License

Use this information at your discretion.

## Acknowledgments

BVLC at http://caffe.berkeleyvision.org/ for developing this great framework "Caffe"
