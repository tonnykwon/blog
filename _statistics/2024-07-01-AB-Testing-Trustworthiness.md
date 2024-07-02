---
title: "AB Testing 실험 신뢰도"
date: 2024-07-01
categories: statistics
mathjax: true
tags: [AB, hypothesis, p-value, z-score]
---


`A/B 테스트 - 3장 트위먼의 법치과 실험의 신뢰도`를 읽고 내용 정리.

## 실험 신뢰도

AB 테스트 실험과 실험 결과가 얼마나 신뢰할만한가 확인해야 할 사항들에 대해 설명한다.
* 내적 타당성
	* 실험 결과가 과연 독립 변수에 영향을 받았는지
	* 독립 변수 외 영향을 미칠 수 있는 사항
		* SUTVA(Stable Unit Treatment Value Assumption)
			* 실험군과 대조군 unit이 서로 영향을 주는 경우
			* 예) 플랫폼 시장에서 유저에게 특정 조치를 취하면 판매자에게도 영향을 미침
			* 예) 소셜 네트워크에서 특정 유저 그룹군에만 조치를 취하더라도 서비스 특성 상 다른 유저에게 영향을 줄 수 있음
		* 생존 편향
			* 일정 기간 활동한 사용자 혹은 특정 조건의 사용자를 분석하면 편향이 발생
    	* 실험 의도 분석
        	* 최종의 할당이 아니라 최초 할당에 의해 분석.
        	* 예) 배너 광고 노출을 A/B 테스트할 시 처음 분할된 AB 그룹으로 분석하지 않고, 각 AB 그룹 내 전환된 유저를 분석하면 선택 편향이 발생
		* SRM(Sample Ratio Mismatch)
			* 


## Reference

- Kohavi, R., Tang, D., & Xu, Y. (2020). _Trustworthy online controlled experiments: A practical guide to a_