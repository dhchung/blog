---
layout: post
category: etc
---

# Installing GTSAM

clone the gtsam github [repo](https://github.com/borglab/gtsam)

open terminal in root folder of gtsam (i.e. cd ~/something/gtsam-4.x.x)

```shell
mkdir build
cd build
cmake ..
make check
sudo make install
```

run some examples