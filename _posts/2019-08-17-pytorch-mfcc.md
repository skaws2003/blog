---
layout: post
title: 'PyTorch-MFCC'
author: Jinwoo Nam
date: 2019-08-17 22:00
tags: [Audio Processing]
image: /files/covers/mfcc.jpg
---

# Project Description: MFCC for PyTorch
MFCC는 현재 가장 인기 있는 오디오 특징값 추출 알고리즘입니다 (알고리즘에 대한 자세한 설명은 [여기](https://brightwon.tistory.com/11)를 참조).
특히 이 알고리즘은 최근 음성인식/장르인식 등을 위한 기계학습 모델에서 오디오 데이터의 전처리 기법으로 널리 사용되고 있습니다.
그러나 2019년 6월 현재 PyTorch에서는 이 알고리즘을 지원하지 않고 있습니다.
현재 MFCC는 [python_speech_features](https://github.com/jameslyons/python_speech_features)와 같은 라이브러리를 통해 추출되고 있으나, 이들은 PyTorch와의 호환성이 없어 PyTorch의 강력한 Autograd를 사용할 수 없다는 치명적인 약점이 있습니다. 
이 때문에 PyTorch에서 MFCC를 사용할 때는 Data Augmentation과 같이 인풋 데이터가 수시로 바뀌는 알고리즘은 사용하기가 매우 까다로웠습니다.

위와 같은 이유로 이 프로젝트에서는 PyTorch Autograd와 잘 연계되는 MFCC 추출 라이브러리를 작성하는 것을 목표로 하였습니다. 이를 위해, 여기서는 MFCC 추출 과정을 처음부터 끝까지 직접 PyTorch로 구현하였습니다.

[디테일한 부분을 생략하고 후기로 넘어가고 싶다면 여기로](#후기)

# 결과
## Achievements
* 기존 라이브러리인 [python_speech_features](https://github.com/jameslyons/python_speech_features)와 1e-5 미만의 작은 값 차이만을 보임
* Fully Differentiable with PyTorch!
* 2020년 연말 기준 Github Star 21개를 가지고 있음
    * 이전에는 좀 더 많이 있었으나, TorchAudio에서 MFCC를 지원하기 시작한 이후로 몇 개 빠짐


## Drawbacks
* 2020년 연말 기준으로는 [TorchAudio](https://pytorch.org/audio/stable/index.html)에서 공식 지원하는 기능임. 따라서 현재는 repo를 더 이상 관리하지 않고 있음
* Numerically unstable할 가능성이 있음
    * Issue로 지적된 부분으로, 중간의 logarithm 부분에 epsilon을 더하지 않은 것이 unstablity를 유발할 가능성이 있음
    * MFCC를 수식 그대로 구현하였기 때문에 생긴 문제로 보임


# 후기
* 짧기는 하지만 많은 사람에게 공개될 코드이기 때문에 코멘팅에 대해 많은 고민을 했다. PyTorch 및 다른 유명한 라이브러리 다수에서 어떤 식으로 코멘팅을 하는 지, 어떤 식으로 문서화에 대비하는지를 배우는 데 시간을 많이 사용해야 했다. 
* 돌이켜 보자면, 이 프로젝트에서 코멘팅에 대해 많은 고민을 한 덕분에 이후 코딩 협업에서 큰 문제가 없었던 것 같다.