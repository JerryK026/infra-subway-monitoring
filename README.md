<p align="center">
    <img width="200px;" src="https://raw.githubusercontent.com/woowacourse/atdd-subway-admin-frontend/master/images/main_logo.png"/>
</p>
<p align="center">
  <img alt="npm" src="https://img.shields.io/badge/npm-%3E%3D%205.5.0-blue">
  <img alt="node" src="https://img.shields.io/badge/node-%3E%3D%209.3.0-blue">
  <a href="https://edu.nextstep.camp/c/R89PYi5H" alt="nextstep atdd">
    <img alt="Website" src="https://img.shields.io/website?url=https%3A%2F%2Fedu.nextstep.camp%2Fc%2FR89PYi5H">
  </a>
  <img alt="GitHub" src="https://img.shields.io/github/license/next-step/atdd-subway-service">
</p>

<br>

# 인프라공방 샘플 서비스 - 지하철 노선도

<br>

## 🚀 Getting Started

### Install

#### npm 설치

```
cd frontend
npm install
```

> `frontend` 디렉토리에서 수행해야 합니다.

### Usage

#### webpack server 구동

```
npm run dev
```

#### application 구동

```
./gradlew clean build
```

<br>

### 1단계 - 웹 성능 테스트

1. 웹 성능예산은 어느정도가 적당하다고 생각하시나요

**성능 측정**

Desktop

| 경쟁사\지표  | FCP(s) | TTI(s) | Speed Index(s) | TBT(ms) | LCP(s) | CLS(s) | 성능 |
| ------------ | ------ | ------ | -------------- | ------- | ------ | ------ | ---- |
| 서울교통공사 | 1.6    | 2.4    | 4.5            | 710     | 3.7    | 0      | 39   |
| 네이버지도   | 1.2    | 1.8    | 3.2            | 60      | 2.2    | 0.006  | 78   |
| 카카오맵     | 0.5    | 0.7    | 3.0            | 0.0     | 1.3    | 0.003  | 89   |
| infra-subway | 2.8    | 2.9    | 3.7            | 50      | 2.9    | 0.004  | 65   |

Mobile

| 경쟁사\지표  | FCP(s) | TTI(s) | Speed Index(s) | TBT(ms) | LCP(s) | CLS(s) | 성능 |
| ------------ | ------ | ------ | -------------- | ------- | ------ | ------ | ---- |
| 서울교통공사 | 6.5    | 9.6    | 13.3           | 2,350   | 6.9    | 0      | 21   |
| 네이버지도   | 2.4    | 6.6    | 6.6            | 410     | 8.3    | 0.025  | 52   |
| 카카오맵     | 3.3    | 4.2    | 8.2            | 60      | 5.1    | 0.005  | 71   |
| infra-subway | 15.2   | 15.8   | 15.4           | 580     | 15.8   | 0.042  | 31   |

**용어 정리**

`FCP` (First Contentful Paint) : 화면에서 첫번째 컨텐츠를 볼 수 있기까지의 시간 (우수 : 1.8 이하)
`TTI` (Time to Interactive) : 페이지 로드 후부터 주요 하위 리소스가 보여지고 사용자 입력에 대해 안정적인 응답을 제공하기까지의 시간 (우수 : 5 이하)
`Speed Index` : 페이지 컨텐츠가 얼마나 빨리 표시되는지 (우수 : 3.4 이하)
`TBT` (Total Blocking Time) : 페이지 응답 전까지 사용자 입력이 차단된 시간 (우수 : 200 이하)
`LCP` (Largest Contentful Paint) : 페이지의 메인 컨텐츠가 보여질 때의 시점 (우수 : 2.5 이하)
`CLS` (Cumulative Layout Shift) : 예기치 않게 레이아웃이 이동했을 때 가장 많이 이동한 정도 (우수 : 0.1 이하)

참고 : https://web.dev/

**중요 페이지**

2. 웹 성능예산을 바탕으로 현재 지하철 노선도 서비스는 어떤 부분을 개선하면 좋을까요

**개선 사항 분석**
PageSpeed Insight에서 제안한 개선 사항들은 다음과 같습니다.

- 텍스트 압축 사용
  - 현재 전달하는 총 전송 크기는 2497Kib이며 가능한 총 절감 효과는 1860.1Kib입니다. 경쟁사 중 전송 크기가 가장 작은 `네이버지도`는 807Kib를 제공하기 때문에, 상당한 차이를 보이고 있습니다.
- 사용하지 않는 자바스크립트 줄이기
- 초기 서버 응답 시간 단축
  - TTFB를 개선할 필요가 있습니다. 앱 로직 / DB 쿼리 최적화를 수행할 필요가 있습니다.
- 캐시 제공
- CSS 요소 수정
  - 정적인 레이아웃 크기를 제공해 레이아웃 변경 수를 제한합니다.
  - 웹폰트가 유지되는 동안 텍스트가 표시되도록 하는 것이 권장됩니다.

추가적으로 제안하는 사항은 다음과 같습니다.

- 모바일에서의 FCP 개선
  - 3초의 법칙에 의거하여 모바일 환경에서의 FCP 환경을 1.8초 이내로 개선할 필요가 있습니다. 텍스트 압축에 9.3초를 소모하므로 최우선 사항이 될 것입니다.
  - 경쟁사 중 가장 FCP가 빠른 네이버 지도의 경우 2.4초를 제공하고 있습니다. 적어도 이보다 20% 이내의 시간인 1.9초 이내의 2.9초 이내가 권장되어 보입니다.

---

### 2단계 - 부하 테스트

1. 부하테스트 전제조건은 어느정도로 설정하셨나요

2. Smoke, Load, Stress 테스트 스크립트와 결과를 공유해주세요

---

### 3단계 - 로깅, 모니터링

1. 각 서버내 로깅 경로를 알려주세요

2. Cloudwatch 대시보드 URL을 알려주세요
