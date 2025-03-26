# sai-urdfreader

This is a standalone port of the ROS urdf reader for use with the SAI libraries.

## Dependencies

It depends on tinyxml2. 
On Ubuntu :

```
sudo apt-get install libtinyxml2-dev
```

On MAC :

```
brew install tinyxml2
```

## Build instructions 

from the base directory

```
mkdir build
cd build
cmake .. && make -j8
```

## License

This software is distributed under the terms of the Stanford Academic Software License Agreement For SAI
