---
title:  Separable convolution and Group Convolution
parent: Posts
---
# Normall convolution
> approximate complexity = `out_dim` x `kernel` x `conv` = 4 x 9 x `conv`. (`conv` = `in_dim` x `in_pixels`)

![image](https://user-images.githubusercontent.com/42603768/166188612-a891d975-751b-468b-a13d-3c444886d64d.png)

# Separable convolution
> Separable conv = Depth-wise conv + Point-wise conv

> approximate complexity = `kenel` x `conv` + `out_dim` x `conv` = (9 + 4) x `conv`. (`conv` = `in_dim` x `in_pixels`(

![image](https://user-images.githubusercontent.com/42603768/166188714-ed4ee442-eac7-4f18-9a02-a3050fad6808.png)
![image](https://user-images.githubusercontent.com/42603768/166188723-55e732d3-76f0-4183-9f09-23762bfab2c0.png)
