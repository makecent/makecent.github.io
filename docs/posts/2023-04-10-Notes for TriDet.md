TriDet has two main contribution: the **Trident Head** and **SGP (Scalable-Granularity Perception)** layers

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

# SGP (Scalable-Granularity Perception) Head
The SGP is proposed to replace the Trasformer layer to tackle some probelm of the transformer layer: There are theorarical and experimental proof show that the transformer layer
will make the features on different time-point to be similar as training goes, which is harmful for temporal action detection, especially these methods that is based on features that are densely extractly (thus feature on adjacent time-points are already very similar).

![image](https://user-images.githubusercontent.com/42603768/230888636-59534591-9ab7-4dd3-8326-12fb210eab01.png)

The SGP has two branch: Instant-level and window-level. The instant-level is used to enlarge the feature distance between action instant and non-action instance,
by multiply the feature with the video-level avearge feature. The window-level try to enlarge the feature receptive field by learning information from the feature extracted from a convolutional layer of larger kernel size (ConV_kw). Details can be found in the above figure.

*"The resultant SGP-based feature pyramid can achieve better performance than the transformer-based feature pyramid while being much more efficient."*

# Some result
Ablation study show that SGP improve the perforamance no matter compared with SA (used by ActionFormer) or baseline (two 1D ConV). The trident head improve the performance a few, gap is more obvious on high IoU thrshold, but introduce more computation cost though.
![image](https://user-images.githubusercontent.com/42603768/230891481-1af3e59a-4fdc-4124-b427-6f6dc2f19a6d.png)
![image](https://user-images.githubusercontent.com/42603768/230892364-30a176c0-c061-4111-82b9-29f43704c0ce.png)

# Conclusion
The **SGP (Scalable-Granularity Perception)** layers could be a alternative for the Transformer Layer used in ActionFormer, while the Trident head not only brings more computation but also new hyper-parameter to tune (size of bin), the improvement is minor thereby not recommended.
