---
title: "Explain the Mean Average Precision (mAP) in 20 lines of psude code"
parent: Posts
---
<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>


```python
for cls_id in all_classes:
    det_cls = dets[cls_id]
    gdt_cls = gdts[cls_id]
    det_cls = sorted(det_cls, key=lambda det: det.score)
    for sample_id in all_samples:
        det_cls_sample = det_cls[sample_id]
        gdt_cls_sample = gdt_cls[sample_id]
        for det in det_cls_sample:
            for gdt in gdt_cls_sample:
                if gdt.assigned:  # default False
                    continue
                if compute_IoU(det, gdt) >= iou_threshold:
                    det.tpfp = True     # default False
                    gdt.assigned = True
                    break
                    
    tpfp = [det.tpfp for det in det_cls]
    cum_tpfp = np.cumsum(tpfp) 
    
    precision_cls = cum_tpfp / np.arange(1, len(det_cls) + 1)
    
    # area style 
    precision[cls_id] = sum(precision_cls * tpfp) / len(gdt_cls)
    
    ## 11-potions style
    # recall_cls =  cum_tpfp / len(gdt_cls)
    # for thr in np.arange(0, 1 + 1e-3, 0.1):
    #     precs = precision_cls[recall_cls >= thr]
    #     prec = precs.max() if precs.size > 0 else 0
    #     precision_cls += prec
    # precision[cls_id] = precision_cls / 11
    
mAP = mean([precision[cls_id] for cls_id in all_classes])
````
- `det.tpfp` tells if this detection is **true positive** or **false positive**, which is set as `False` by default (false positive).
- `gdt.assigned` tells if this ground truth has already been assigned to one detection, which is set as `False` by default (not assigned).
- There are two styles for averaging the precisions, named the `area` and the `11-points`. `area` is adopted in most of the current work.
- **average precision (AP)** represents averaing precisions of different number of top detections. Can be `area` style of `11-potions` style;
- **mean average precision (mAP)** refers to the averaing AP of different **classes** in most cases; but sometimes refers to the averaging AP of different **IoU thresholds**, in which case the AP in it is already the mAP in the previous case.
