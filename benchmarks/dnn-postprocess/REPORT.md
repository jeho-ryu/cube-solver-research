# min2phase + AlphaCube 실제 실행 보고서

실행 시각(UTC): 2026-09-10T08:04:23.835388+00:00

고정 시드 20260910로 생성한 스크램블 30개. 길이는 10/20/25수이며 같은 면 연속 회전을 제외했습니다. 스크램블 길이는 최적 풀이 난도가 아니며, 균등 무작위 큐브 상태 표본도 아닙니다.

AlphaCube small 사전학습 모델, cpu, PyTorch 스레드 4개, 빔 폭 1024, 각 후처리의 요청 시간 예산 3.0초. 새 DNN 학습은 수행하지 않았습니다.

## 측정 결과

| 방식 | 검증 성공 | 평균 HTM 회전 수 | 평균 시간(s) | 중앙 시간(s) | P95 시간(s) | 첫 해보다 개선 | 총 절약 수 |
|---|---:|---:|---:|---:|---:|---:|---:|
| min2phase 첫 해 | 30/30 | 17.900 | 0.0011 | 0.0006 | 0.0039 | 0 | 0 |
| min2phase + AlphaCube 전체 재탐색 | 30/30 | 16.100 | 1.2067 | 1.2634 | 2.0342 | 14 | 54 |
| min2phase + AlphaCube 구간 교체 | 30/30 | 17.667 | 2.7369 | 3.0147 | 3.1190 | 2 | 7 |
| min2phase 추가 탐색 | 30/30 | 15.167 | 2.0829 | 3.0009 | 3.0066 | 23 | 82 |

## 해석

- min2phase + AlphaCube 전체 재탐색: min2phase 첫 해 대비 14건에서 총 54수를 줄였습니다. 개선 실패 시 검증된 기존 해법을 반환합니다.
- min2phase + AlphaCube 구간 교체: min2phase 첫 해 대비 2건에서 총 7수를 줄였습니다. 개선 실패 시 검증된 기존 해법을 반환합니다.
- 구간 교체와 min2phase 추가 탐색의 공통 30건 비교: 구간 교체가 더 짧음 0건, 동일 9건, 더 김 21건.
- 첫 해보다 개선되는 것과 동일한 추가 시간의 min2phase를 이기는 것은 별개입니다. 위 비교는 작은 CPU 파일럿이며 GPU, 큰 빔, 재학습 모델에 대한 일반적인 우열을 입증하지 않습니다.

## 스크램블 길이별 평균 회전 수

| 스크램블 길이 | 첫 해 | 전체 재탐색 | 구간 교체 | min2phase 추가 탐색 |
|---|---:|---:|---:|---:|
| 10 | 13.000 | 9.500 | 12.300 | 9.500 |
| 20 | 20.000 | 18.900 | 20.000 | 17.700 |
| 25 | 20.700 | 19.900 | 20.700 | 18.300 |

## 측정 조건과 한계

- HTM: 18개 일반 면 회전만 허용하며 R2도 1수입니다. 모든 방식에 같은 인접 동일 면 정규화를 적용합니다.
- 전체 재탐색: 원래 스크램블에서 첫 해 길이보다 짧은 깊이만 탐색하는 독립 재탐색입니다. min2phase 경로 주변을 직접 이용하지 않습니다.
- 구간 교체: min2phase 수순의 길이 8/12/6/16 구간을 선택합니다. W의 역수순으로 만든 상태를 DNN+빔서치로 풀어 W와 같은 변환의 더 짧은 Q를 얻습니다. 짧은 해법에서는 교체 가능한 구간 수가 적습니다.
- 모델은 고정됩니다. 기본 AlphaCube의 로그 확률, 회전 가지치기, 상위 B개 선택, 동일 상태 제거를 재사용했습니다. 이 실험용 외부 루프에 깊이 및 시간 제한만 추가했습니다.
- 독립 검증: AlphaCube의 상태 배열이 아닌 Java min2phase Tools의 회전 엔진으로 원래 스크램블+최종 수순이 완성 상태인지 재검사했습니다. 잘못된 후보는 오류로 기록하며 개선으로 세지 않습니다.
- 시간은 초기화 후 warm 실행 기준입니다. 하이브리드는 min2phase 첫 풀이 시간 + Python 후처리 실제 경과 시간이며, 후처리 중 후보 검증 비용은 포함됩니다. 최종 보고용 검증/파일 저장은 제외됩니다. Java 조건은 JVM 내부 풀이 시간으로 통신 시간을 제외합니다.
- 시간 제한은 협력적입니다. AlphaCube는 깊이 단계 사이, min2phase는 20-probe 묶음 사이에서 확인하므로 한 작업만큼 초과할 수 있습니다. 실측 시간과 초과량을 CSV에 기록했습니다. 같은 요청 예산이지 정확히 같은 실행 시간이 아닙니다.
- min2phase 추가 탐색은 같은 초기 호출 후 Search.next로 이어서 탐색합니다. 더 긴 수순은 채택하지 않습니다. 단순히 약한 첫 해만 비교 대상으로 쓰는 오류를 피하기 위한 대조군입니다.
- 성공률은 최종 출력 기준입니다. 하이브리드 100%는 AlphaCube 단독 성공률을 뜻하지 않습니다. 개선 시도 상태와 실패는 traces.jsonl에 따로 남깁니다.
- 프로세스 최대 메모리는 측정하지 않았습니다. DNN/Java 초기화 시간은 metadata.json에 별도 기록했습니다. 컴파일 및 Python import 시간은 풀이 비교에서 제외했습니다.
- 소규모 단일 실행이므로 통계적 유의성/전역 최단성을 주장하지 않습니다. 설정 변경 후에는 이 표본을 개발용으로 취급하고 새 seed의 보류 표본으로 재평가하세요.

## 실제 개선 사례

### case-001 / min2phase + AlphaCube 전체 재탐색

- 스크램블: `D L' U' D2 F2 R' D2 B2 D2 B`
- 기존 15수: `F' U2 B' F' R2 U2 F2 U2 R2 D2 L' B2 U' L D'`
- 개선 10수: `B' D2 B2 D2 R F2 U D2 L D'`

### case-001 / min2phase + AlphaCube 구간 교체

- 스크램블: `D L' U' D2 F2 R' D2 B2 D2 B`
- 기존 15수: `F' U2 B' F' R2 U2 F2 U2 R2 D2 L' B2 U' L D'`
- 개선 10수: `B' D2 B2 D2 R F2 U D2 L D'`

### case-003 / min2phase + AlphaCube 전체 재탐색

- 스크램블: `D' B' D' U' L D2 R D F L' B' L2 F2 L' B L2 B R' U2 R' B' F R D' L2`
- 기존 21수: `U R' B D F2 R2 D L' U2 F2 B2 U' F2 U B2 D' R2 U R2 F2 L'`
- 개선 20수: `F2 R' U' B R' D' B2 U2 B2 R' U' B2 R2 U F D' R' D' F D2`

### case-004 / min2phase + AlphaCube 전체 재탐색

- 스크램블: `U D R B2 R' F' D' F' L D`
- 기존 18수: `R2 D R2 D' B2 U F2 D B2 D2 F2 U' L' F D F D' U'`
- 개선 10수: `D' L' F D F R B2 R' U' D'`

### case-005 / min2phase + AlphaCube 전체 재탐색

- 스크램블: `D' R' U' R2 F' D2 L R2 L' R2 B' F R D F2 L2 F2 B' F L2`
- 기존 21수: `R' F2 B' R' L U2 B' U' L2 U2 R2 B2 R2 D R2 F2 D' R2 D2 R U'`
- 개선 14수: `B2 D2 F' B' D R' B F' D2 F R2 U R D`

### case-008 / min2phase + AlphaCube 전체 재탐색

- 스크램블: `L2 D' U2 D L' D L D2 U' B' L' R2 B2 F' L' B2 F2 D2 L2 U2`
- 기존 21수: `F B2 D2 R' F2 L U2 R D2 B2 D2 L' F2 B R' U L R2 D' F' U'`
- 개선 18수: `U2 L2 D2 B2 F2 L B2 F L R2 B U D2 L' D' L U2 L2`

### case-009 / min2phase + AlphaCube 전체 재탐색

- 스크램블: `F U R2 L2 F2 L2 R2 B2 L2 F' B2 U' D B' D2 U B2 R' D2 L D L2 F2 R2 L'`
- 기존 21수: `R' B' U' L2 B' D2 R' U2 D2 B R2 D B2 L2 D B2 U' F2 D B2 U'`
- 개선 20수: `R' U' R2 B' F' U' F' L' B R2 B' R' B2 L2 B' L D2 L' B L'`

### case-018 / min2phase + AlphaCube 전체 재탐색

- 스크램블: `R' U2 R2 B R2 F' R' B2 F2 B2 R L R U2 F2 U F2 B2 F R' F2 L R' F U`
- 기존 19수: `D' L2 U' R L' F D2 R D2 F2 R2 U2 L2 B' L2 B2 U2 B D2`
- 개선 18수: `D' L2 U B2 L U2 B' D2 L' F2 R2 F B' L2 R2 F D2 L2`

### case-019 / min2phase + AlphaCube 전체 재탐색

- 스크램블: `D2 R2 F2 R' D' L2 D U F2 L`
- 기존 12수: `L' U2 F2 U R2 D2 L2 D2 R' F2 R2 D2`
- 개선 10수: `L' F2 D' U' L2 D R F2 R2 D2`

### case-019 / min2phase + AlphaCube 구간 교체

- 스크램블: `D2 R2 F2 R' D' L2 D U F2 L`
- 기존 12수: `L' U2 F2 U R2 D2 L2 D2 R' F2 R2 D2`
- 개선 10수: `L' F2 D' U' L2 D R F2 R2 D2`


## 재현 파일

- cases.json: 입력 목록
- results.csv: 모든 방식의 실제 수순, HTM 길이, 시간, 검증 결과
- traces.jsonl: 깊이별 후보 수/점수, 구간 교체 시도, 종료 이유
- metadata.json: 장비, 설정, 패키지 버전, 모델/소스 SHA-256
- errors.json: 실행 오류 (빈 배열이면 없음)

기록된 오류 수: 0
