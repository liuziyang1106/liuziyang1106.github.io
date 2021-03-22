今天利用scp -r .conda/envs/torch从a服务器转移torch环境到b服务器，但是import torch的时候报错：/libstdc++.so.6: version `CXXABI_1.3.11' not found
cd .conda/envs/torch/lib 然后 ls | grep libstdc++ 发现存在 libstdc++.so.6.0.26，怀疑是系统找不到libstdc++.so.6
参考https://blog.csdn.net/qingdu007/article/details/81515984，通过ln -s libstdc++.so.6.0.26 libstdc++.so.6 建立软链接解决问题

