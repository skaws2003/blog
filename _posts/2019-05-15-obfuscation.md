---
layout: post
title: 'Image Obfuscation'
author: Jinwoo Nam
date: 2019-05-15 22:00
tags: [Image Security]
image: /files/covers/obfus.jpg
---


> 현재 실제로 기업에서 사용 중인 프로젝트입니다. 많은 디테일이 생략되었습니다

> [디테일한 내용을 생략하고 후기로 넘어가려면 여기를 클릭](#후기)

# Image Obfuscation

> [Image Obfuscation에 대한 내용은  Deeping Source Inc.의 공식 문서를 참조](https://www.deepingsource.io/privacy-protection?lang=ko)

## Objective

Image obfuscation은 이미지 데이터셋을 처리하여 이미지가 다음의 두 가지 성질을 만족하도록 하는 것입니다.
* 익명성: 이미지 내부의 개인정보(얼굴 등)은 사람이 알아볼 수 없는 형태로 가공되어야 하며, 제 3자가 원본을 복원할 수도 없어야 합니다.
* 데이터 활용도: 어떤 특정 Task에 대하여 (e.g. Facial landmark detection, gaze estimation), 익명화된 이미지로 해당 task를 다루는 기계학습 모델을 학습시키더라도 원본 이미지로 학습한 것과 비슷한 성능을 내어야 합니다.

## Previous Works

기존의 Image obfuscation 방식에는 다음과 같은 것들이 있습니다:
* 프라이버시와 관련된 부분 (예, 얼굴, 주소)을 찾아낸 뒤 해당 부분을 지우거나[1] GAN을 활용하여 변조하는 방식[2]
    * 프라이버시를 탐지하는 모델을 학습하기 위해 많은 데이터를 필요로 합니다
    * 프라이버시와 관련된 부분을 제대로 탐지하지 못할 위험이 있습니다. (익명성 보장의 문제)
    * 지우거나 변조된 부분에 관한 Task는 학습할 수 없습니다. 예를 들어, 얼굴을 지울 경우 Facial Landmark detection은 학습하지 못합니다. (데이터 활용성의 문제)
    * 이미지로 어떤 Task를 학습하더라도 좋은 결과를 기대할 수 없습니다 (데이터 활용성의 문제)
* 아예 이미지를 초저해상도로 만든 뒤 초저해상도에서도 작동하는 모델을 학습하는 방식[3]
    * 초저해상도 이미지를 사용하므로 학습된 모델의 성능을 기대할 수 없습니다 (데이터 활용성의 문제)
    * 데이터 활용성을 위해 해상도를 높이면 익명화가 제대로 되지 않을 수 있습니다 (익명성 보장의 문제)

위에서 설명한 것처럼, 기존의 방법은 이미지의 활용도를 떨어뜨릴 뿐더러, 개인정보 탐지 과정에서 개인정보를 놓칠 경우 익명성이 보장되지 않을 수도 있다는 치명적인 문제가 있습니다.


# Approach

## Idea

* GAN기반의 Generative model을 사용하여 obfuscated image를 생성
    * 이미지 전체를 익명화할 수 있으므로 익명성이 제대로 보장된다
    * 이미지 전체를 익명화하면서도 데이터 활용성을 보장하려면, Image generation과정에 직접 supervision을 줄 수 있는 GAN이 적절하다
* Target Task에 대한 데이터 활용성을 보장하기 위해 Original Image에 Pre-train된 target task network를 사용
    * Pre-trained target task network로부터의 피드백을 통해 이미지에서 task와 관련된 부분은 남기고 task와 관련 없는 부분은 지워질 수 있도록 한다.
    * 위와 같은 방식으로, Obfuscator는 Target task performance를 보존하면서도 익명화는 제대로 하는 Image mapping function을 학습하게 된다.


## Overall Usage Scenario
![Usage Scenario](/files/images/obfus/scenario.jpg)

위의 그림은 우리 Image Obfuscation이 어떤 방식으로 이용될 수 있는 지 보여줍니다.

Image obfuscation에 관련된 사람은 프라이버시 걱정 없이 이미지를 배포하려는 사람(_이미지 배포자_), 배포된 이미지로 모델을 학습하려는 사람(_이미지 사용자_)의 두 가지 분류로 나눌 수 있습니다.

위의 그림에서, 이미지 배포자는 아래와 같은 일을 합니다:
1. Obfuscator를 통해 이미지를 비식별화
2. 비식별화된 이미지를 배포
3. Obfuscator에 Distillation된 모델(Distillated Obfuscator)을 배포

이미지를 이용하려는 이미지 사용자는, 배포된 자료를 가지고 다음과 같은 일을 합니다:
1. 비식별화된 이미지를 사용해 Target Task 모델을 학습
2. 학습된 모델을 이용해 Inference할 때는, Distillated Obfuscator를 이용해 인풋 이미지를 Obfuscated Image와 비슷하게 만들어서 Inference 수행


## Obfuscator 학습

![Obfuscation Training Procedure](/files/images/obfus/training.jpg)

Obfuscator 모델(__O__)을 학습하기 위해 Adversarial Training 및 Multi-task learning기법을 사용하였습니다.
* 먼저 이미지를 익명화하기 위해 Reference Obfuscation(Noise, Jittering 등의 합성)된 이미지를 만들고, 이것을 Generation Target으로 하여 obfuscated image를만드는 Adversarial Training을 합니다.
    * 이 과정에서 Obfuscator(__O__)는 주어진 이미지로부터 reference obfuscation과 최대한 비슷하게 생긴 이미지를 만드는 방법을 학습하게 됩니다.
* 위에서 나오는 Obfuscated Image(Generator output)를 Pre-train된 Target task network(__F__)에 input으로 넣어 prediction이 제대로 되는지 확인합니다.
    * 이 과정에서 Obfuscator(__O__)는 인풋 이미지에서 Target Task에 필요한 정보는 남기고 필요없는 정보는 지워서 이미지를 사람이 인식하지 못하는 형태로 바꾸는 법을 학습합니다.

## Obfuscated Image 이용

![Usage](/files/images/obfus/inference.jpg)

Obfuscated Image를 배포할 때, 사용자에는 다음의 두 가지 자료가 주어집니다:
* Obfuscated Image
    * 원본 이미지는 주어지지 않습니다
    * 비식별화된 이미지만이 주어지므로 프라이버시를 효과적으로 보호할 수 있습니다
* Diltillated Obfuscator
    * 학습된 Task model로 Inference할 때 필요합니다
    * Obfuscator의 Inverse mapping을 학습하는 것을 막기 위해 원본 Obfuscator는 주어지지 않습니다

위의 두 자료를 가지고, 사용자는 다음과 같이 Obfuscated Image를 이용할 수 있습니다:
1. 비식별화된 이미지를 사용해 Target Task 모델을 학습
2. 학습된 모델을 이용해 Inference할 때는, Distillated Obfuscator를 이용해 인풋 이미지를 Obfuscated Image와 비슷하게 만들어서 Inference 수행
    * 사용자가 학습된 모델을 배포하고 싶다면, 단순히 Diltillated Obfuscator와 학습된 Target Task Model을 이어붙여 배포하면 됩니다


# How it Works?
![Obfuscation Mapping](/files/images/obfus/scheme.jpg)

위 그림은 Obfuscator 모델이 어떻게 동작하는 지 설명하는 그림입니다. 그림에서 __O__는 Obfuscator 매핑, F는 원본 이미지에 학습된 Target Task 모델의 Decision Boundary, F0는 Obfuscated Image에 학습된 Target Task 모델의 Decision Boundary입니다.

그림에서 Obfuscator(__O__)는 회색 영역의 원본 이미지를 노란색 영역으로 매핑하는 역할을 합니다. 그런데 이 Obfuscator는 Target Task의 Decision Boundary를 최대한 유지하도록 학습되었기 때문에 이미지의 형태는 다르게 하면서도(__O__ 매핑) 원본 이미지에 대한 Target Task 모델의 Decision Boundary는 비슷하게 (회색 영역에서 F와 F0 비교) 유지할 수 있습니다.


## Obfuscation for Bigger Image

![Obfuscation for big image fails](/files/images/obfus/big-fail.jpg)

앒에 설명한 것과 같은 Image Obfuscation은 데이터의 익명성과 활용성이라는 두 가지 목표를 모두 만족시킬 수 있으나, 커다란 이미지에서는 Obfuscator의 학습이 Target Task loss에 좌우되어 Obfuscator가 계속 원본 이미지과 같은 이미지를 만드는 현상이 발생합니다(위의 그림, (a)). 이는 커다란 이미지에서는 가능한 이미지의 경우의 수가 크게 늘어 Adversarial Learning Objective가 크게 어려워지기 때문입니다.

![obfuscation for big image](/files/images/obfus/bigobfus.jpg)

커다란 이미지에서 Obfuscation이 제대로 되지 않는 현상을 완화하기 위해, 여기서는 Pretrained Autoencoder를 사용햐여 Adversarial Learning의 난이도를 낮추도록 하였습니다. 위의 그림과 같이, 여기서는 먼저 (a) 이미지의 크기를 줄이기 위한 autoencoder를 학습시킨 뒤 (b) 앞에서 설명한 obfuscator 학습 과정을 이용하였습니다. 이러한 과정을 통해, 커다란 이미지도 obfuscator 입장에서는 작은 이미지와 똑같이 다룰 수 있게 됩니다.

큰 이미지에서 Inference시에는 학습된 Autoencoder와 obfuscator, target task model을 순차적으로 적용합니다. 


# Results
>많은 디테일이 생략되었으며 웹 상에 공개된 내용만을 적은 점 양해 부탁드립니다.

### Quantitative Results
제안된 방식의 효율성을 알아보기 위해 Image Classification, Object Detection, Facial Landmar Detection, Face Varification 등의 다양한 문제에 실험을 진행하였습니다.
모든 문제에서 Original Image로 학습한 모델에 비해 0%-1% 이내의 작은 성능 차이만을 보였습니다.

### Qualitative Results
![Obfuscation Results](/files/images/obfus/celeba-results.jpg)
위의 그림은 제안된 Obfuscation 모델의 결과를 보여줍니다. 위에서부터 차례대로 원본 이미지, Reference Obfuscation, 우리 모델의 Obfuscated Image입니다.
우리 모델의 아웃풋은 원본 이미지와 크게 다르며, 변형 강도는 Reference Obfuscation과도 견줄 수 있습니다.

### Reversibility
![Obfuscation Results](/files/images/obfus/celeba-reverse.jpg)
제안된 모델이 Task와 관련없는 정보를 효과적으로 없앤다는 것을 보이기 위해, Obfuscator를 학습할 때 사용된 원본 데이터가 유출된 상황을 가정한 뒤 Obfuscated Image를 Original Image로 매핑하는 신경망 모델을 학습해 보았습니다. 

그 결과는 위의 그림과 같습니다. 그림은 CelebA Dataset의 Facial Landmark Detection Task에 학습된 Obfuscator 모델을 보여줍니다. 위에서부터 차례대로 원본 이미지, Obfuscated Image, 그리고 복원된 이미지입니다. 설령 원본 트레이닝 데이터가 유출되었다 하더라도, Obfuscator의 역방향 매핑을 학습한 모델은 Obfuscated Image를 잘 복원하지 못하는 모습을 볼 수 있습니다.


# 후기

* 프로젝트가 한창 진행되던 와중에 중간에 끼어들어 협업에 문제가 있었다
    * 처음 해보는 팀 작업이었기에 프로젝트 코드의 디자인 패턴을 배우는 데 시간이 걸렸다. 처음 몇 번은 유지 보수가 힘든 코드를 짰으나, 곧 익숙해졌다. 

* 처음 하는 협업이었기에 커뮤니케이션이 중요했다
    * 처음에는 협업 과정에서 아이디어를 전달하는 데 어려움이 있었다. 그러나 차차 당연하지만 중요한 사실들(말보다 그림이 효과적이다, 내가 당연하게 아는 것을 꼭 상대방이 알고 있지는 않다 ...)을 잊고 있었음을 새삼 깨닫게 되어 원만한 아이디어 교환이 가능해졌다.
    * 내가 참여하기 전까지 obfuscation은 1인 프로젝트였고, 항상 인력이 부족하던 상태였기에 새로이 할 실험이 정말 많았다. 이 때문에 처음 프로젝트에 참여하고 며칠 정도는 아예 실험을 전부 멈추고 실험에 대한 검토를 했다.