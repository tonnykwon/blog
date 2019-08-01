---
title: "앱 내 이동 로그 데이터 분석"
date: 2019-07-31
categories: portfolio
mathjax: true
---

### 목적 및 업무명

카메라 필터앱 기능 사용 패턴 분석 및 각 기능 사용과 총 사용량 관계 분석



### 회사

텐디



### 기간

2019.03.27 ~ 2019.04.29



### 주요 역할

- 데이터 전처리
- 분성 방향 설계
- 분석 진행
- 분석 결과 해석 및 모델 향상



### 세부 방법

#### 1. 회귀분석

카메라 필터 앱에서 사용자의 기능 사용 로그 데이터를 사용하여 각 기능과 전체 사용량의 관계를 분석해보았다. 대략 41만여개의 로그 데이터를 사용하였고 총 3만5천여명의 사용자가 있었다. 기본 변수 혹은 기능 30여가지를 먼저 전체 사용량에 대해 회귀분석을 실시한 후에 Cook's distance를 통해 회귀선으로부터 잔차가 크고 influential한 이상치들을 제거해주었다. 이후 AIC를 기반으로 단계별 선택법을 통해 변수를 추가 제거하여 최적의 모델을 만들었다. 이를 기반으로 각 기능과 총 사용량에 대해 어떤 관계가 있는 지 나타내보았다.



#### 2. 패턴 마이닝

대표적으로 사용자들이 앱 내부에서 어디서 어디로 이동하는 지 파악하기 위해 PrefixSpan을 사용하여 sequential pattern을 추출하였다. 전체에서 20%이상 나타난 패턴만을 파악하였다.



### 결과

카메라 앱 내부 광고 화면이나 설정 기능 사용 등은 음의 계수를 나타냈고, 그외 실제 필터 기능 사용이나 영상 촬영 등은 양의 계수를 나타냈다. 생각했던 거와 비슷하게 진부한 결과가 나왔다.



### 개선점

비슷한 앱 사용 패턴을 가진 사용자들끼리 묶으면 사용자들 파악이 더 쉽지 않을까라는 생각에서 sequential pattern mining에 관해 찾아보았다. Similarity Measure for Sequential Patterns(S2MP)라는 방법을 통해 패턴끼리의 거리를 구할 수 있었다. 이 방법에서는 두 패턴 내 아이템 기반 유사도(각 패턴의 아이템끼리 매칭) Mapping Score와 두 패턴 내 아이템의 순서와 위치의 유사도 Order Score을 통해 패턴끼리의 유사도를 계산했다.

안타깝게도 S2MP방법은 시간복잡도가 높은 계산방법이라 군집화에 적용하는 데 한계가 있어 분석을 완성하지 못했다.



### 개발환경 및 언어

R

Python: pyspark



### 참조

- Saneifar, H., Bringay, S., Laurent, A., & Teisseire, M. (2008, November). S 2 MP: similarity measure for sequential patterns. In *Proceedings of the 7th Australasian Data Mining Conference-Volume 87* (pp. 95-104). Australian Computer Society, Inc..