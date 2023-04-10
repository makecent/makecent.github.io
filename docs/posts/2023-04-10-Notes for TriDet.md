TriDet has two main contribution: the **Trident Head** and **SGP** layers

![image](https://user-images.githubusercontent.com/42603768/230882506-9e2a9193-f541-46c3-b970-2794e8b28911.png)

# Trident Head
Let's first start with FCOS head and BSN head. The FCOS head predicst **offsets** while the BSN head predicts boundary probablilites on each time-point.
Combine their ideas, we can compute the **expected offset**. The trident head output a response on each time-potions and compute the offset by computing
the **offset expectation using the boundaries probablity of a set of neibouring time-points**:

![image](https://user-images.githubusercontent.com/42603768/230883996-bf8d0a1f-729e-4165-a25a-a0af7a99cea4.png)
![image](https://user-images.githubusercontent.com/42603768/230884293-cd4dfec8-d78a-4c01-8016-308d3053bfc1.png)


This make the predicted offsets more stable and reliable. Note that in this work the boundary probabilities is **relative** since it's computed the with softmax function
on a set of consective time-points, thus each time-point have different boundaries probabilties depends on its position in the interval.

The author futher introduce a **center-offset head** in addition ot the starting and ending prediction head.
The center-offset simply predicts the boundary probablities of the neibouring time-points for each time-potions. And the predicted boundary response will be added to the
boundary response on starting and ending heads.
