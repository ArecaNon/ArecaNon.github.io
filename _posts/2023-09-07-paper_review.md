---
layout : single
categories : Paper Review
title : "A Simple Baseline for Video Restoration with Grouped Spatial-temporal Shift Review"
---

# Video Restoration

## Instroduction

video restoration은 프레임 간의 관계를 아는 것이 중요하다. 몇몇 방법들은 명시적인 정렬이 없는 다중 프레임 합성을 위해 Conv-Net을 사용하였으나 성능은 최선이 아니였다.
대부분의 optical flow, deformable convolution, self attention을 이용한 방법들은 시간적인 대응을 확립하기 위한 명시적인 정렬에 의존하게 된다.

그러나 이러한 방법들은 복잡하거나 큰 비용의 네트워크를 필요로 하며 큰 변위, 프레임 노이즈, 흐린 영역이 포함될 때 성능이 나쁠 수 있다.
최신에는 트랜스포머를 이용한 VRT도 나왔지만 계산이 까다롭다.
그 외에 Swin Transformer에 영감을 받아 Large Kernel Conv를 사용하는 솔루션들이 나오지만, 반드시 성능이 향상되는 것은 아니다

본 논문에서는 시간적 correspondence에 대한 크고 효과적인 receptive field를 달성하기 위해서 간단하고, 효과적인 시공간적 변환 블럭을 제안한다.
이는 또한 


## 