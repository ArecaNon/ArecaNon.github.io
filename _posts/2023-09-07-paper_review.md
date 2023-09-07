---
layout : single
categories : Paper Review
title : "A Simple Baseline for Video Restoration with Grouped Spatial-temporal Shift Review"
---

# Video Restoration

작성자가 아직 공부 중에 있어 논문리뷰가 매우 난잡하거나 일부분 생략되었을 수 있음


## Instroduction

video restoration은 프레임 간의 관계를 아는 것이 중요하다. 몇몇 방법들은 명시적인 정렬이 없는 다중 프레임 합성을 위해 Conv-Net을 사용하였으나 성능은 최선이 아니였다.
대부분의 optical flow, deformable convolution, self attention을 이용한 방법들은 시간적인 대응을 확립하기 위한 명시적인 정렬에 의존하게 된다.

그러나 이러한 방법들은 복잡하거나 큰 비용의 네트워크를 필요로 하며 큰 변위, 프레임 노이즈, 흐린 영역이 포함될 때 성능이 나쁠 수 있다.
최신에는 트랜스포머를 이용한 VRT도 나왔지만 계산이 까다롭다.
그 외에 Swin Transformer에 영감을 받아 Large Kernel Conv를 사용하는 솔루션들이 나오지만, 반드시 성능이 향상되는 것은 아니다

본 논문에서는 시간적 correspondence에 대한 크고 효과적인 receptive field를 달성하기 위해서 간단하고, 효과적인 시공간적 변환 블럭을 제안한다.
또한 비디오 디블러링 및 비디오 노이즈 제거 작업 모두에서 좋은 성능을 보였다.

기본 2D-UNet에 제안하는 시공간적 변환 블럭을 적용한다.
그리고 이는 시간적 공간적 차원 모두에서 입력 클립 특징들의 개별적인 변환을 포함하고, 이를 2D Conv-Net을 사용하여 합친다.

## Method

### Group Shift-Net

분 논문에서 제안하는 구조는 three-stage로 되어있다.

1. Feature extract
2. Multi-frame feature fusion with grouped spatial-temporal shift
3. final restoration

1번 과정은 시간에 따른 열화(블러, 노이즈)를 최대한 줄이기위해서 " Investigating tradeoffs in real-world video super-resolution"에서 영감을 받아 2D U-Net을 사용한다.
2번 과정에서는 제안한 시공간적 변환 블럭을 이용한다. 이를 통해 이전 프레임과 주변 특징맵의 정보를 합친다.
3번 과정은 U-Net-like 구조에서 낮은 퀄리티의 인풋에서 최종 결과물을 출력해내고 이를 통해서 Loss를 구한다.

### Grouped temporal shift

Grouped temporal shift는 FTS와 BTS로 구성된다
FTS는 Forward Temporal Shift로 이전 프레임에서 구한 특징맵을 앞 단을 가져와서 현재 프레임의 뒷 단과 합쳐서 사용한다.
BTS는 Backward Temporal Shift로 FTS와 Grouped Spatial shift를 거치고 나온 특징맵을 이번에는 현재프레임의 앞 단과 이후 프레임의 뒷 단을 합쳐서 사용한다.
이때 앞단과 뒷단의 채널수는 기존 특징맵의 절반이다.

## Grouped Spatial shift
Grouped Spatial shift의 경우 FTS에서는 이전 프레임의 앞단, BTS에서는 이후 프레임의 뒷단에 적용되며 현재 프레임의 특징맵과 합쳐진 것과는 별개로 사용된다. 
이때 각 단을 동일한 크기의 채널을 가지게 m개로 분리한 뒤, 각각을 특정한 규칙에 맞추어서 width와 height 방향으로 몇 픽셀 이동시킨다. 
이때 특정한 규칙은 정사각형의 root(m) x root(m) 모양의 필터와 같으며 본 논문에서는 m = 25로 각각의 방향으로 {−9, −5, 0, 5, 9}을 적용하였다.

# Fusion layer
Grouped Spatial shift를 거친 특징맵, 현재프레임의 특징맵 그리고 이전 혹은 이후 프레임의 특징맵, 이렇게 3가지를 Fusion layer에 넣어서 사용한다.
이때 Fusion layer에는 두개의 lightweight convolution block인 NAFNet와 Super Kernels 사이의 조합을 선택해서 사용한다.
이를 통해 무거운 연산을 피한다.
