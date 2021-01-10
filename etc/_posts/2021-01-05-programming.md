---
layout: post
category: etc
---

# Segmentation Fault with Eigen Matrix

A segmentation fault error was occured when I tried to copy a eigen matrix to a elemenet of a vector of eigne matrices:

```cpp

std::vector<Eigen::Matrix4f> foo;
foo.resize(X.size());

for(int i = 0; i < X.size(); ++i) {
    foo[i] = someFunction(X[i]);
}

```

Not really sure what caused the problem, but here's the solution for this:

```cpp
std::vector<Eigen::Matrix4f> foo;
foo.resize(X.size());

for(int i = 0; i < X.size(); ++i) {
    foo[i].block(0,0,4,4) = someFunction(X[i]);
}

```

