# 출처와 자료 범위

[전체 요약으로 돌아가기](../README.md)

## 1. 이 자료의 범위

이 문서 묶음은 기존 큐브 프로젝트의 C++·DNN 작업을 GitHub용으로 요약한 것입니다. 실행 소스·모델·대형 거리표·전체 원시 측정값은 포함하지 않습니다. 보고서에 기록된 실험을 새로 실행하거나 학습한 결과도 아닙니다.

`benchmarks/*/REPORT.md`는 기존 보고서를 복사한 근거 자료입니다. 원래 프로젝트를 기준으로 적힌 코드 경로·원시 결과 경로는 설명용으로 보존했습니다. Tail 보고서의 실행 안내 링크 한 곳은 이 묶음의 C++ 상세 문서로 연결되도록 조정했습니다. 각 `SOURCE.json`에 원본과 복사본의 SHA-256을 기록했습니다.

`docs/CPP_USAGE.md`는 기존 공개용 C++ 프로젝트의 README 사본입니다. 그 안의 빌드·실행 명령은 해당 소스나 실행 패키지에서 사용하는 명령입니다.

## 2. 외부 프로젝트와 이번 작업

| 외부 프로젝트 | 기존에 제공한 것 | 이번 작업에서 추가한 것 |
|---|---|---|
| [min2phaseCXX](https://github.com/lilborgo/min2phaseCXX) | C++ two-phase 엔진 | 거리표 연결, 조회 방식·탐색 순서 변경, 공개용 인터페이스 |
| [min2phase](https://github.com/cs0x7f/min2phase) | Java two-phase 엔진 | 교사 데이터, 후보·방향 선택 실험, 비교·검증 도구 |
| [Rob two-phase](https://github.com/efrantar/rob-twophase) | 거리표·좌표·대칭 정의와 원본 탐색 | min2phase 좌표로 표 변환, 이동 마스크·하한 연결, 상주 풀 비교 |
| [AlphaCube](https://github.com/kyo-takano/alphacube) | 신경망·사전학습 모델·탐색 기반 | 깊이·시간 제한 후처리, 구간 교체, 교사 추가 학습·평가 |

AlphaCube 없는 독립 Policy+Value 실험은 자체 모델과 탐색 코드를 사용했습니다. min2phase는 이 실험의 데이터 생성·기준선·독립 검증에 사용했습니다.

C++ 출처 기록의 기준 커밋은 min2phaseCXX `db0ee5f431178f2153f9aa60581bd6ca59e89391`, Rob `d245031257d52b2663c5790c5410ef30aefd775f`입니다. 이 묶음에는 해당 외부 코드를 재배포하지 않습니다. 실제 코드 공개 시에는 기존 `NOTICE.md`, `COPYING`, vendor의 라이선스·저작권 고지를 함께 유지해야 합니다. 기존 C++ 파생 배포는 GPL-3.0-or-later 조건으로 구성됐으며, AlphaCube 원본은 MIT입니다. 문서만으로 모든 소스에 일괄 MIT 라이선스를 선언하지 않습니다.

## 3. 근거 보고서와 원래 위치

아래 원래 위치는 작업 폴더 루트 기준입니다. GitHub에 이 요약본만 올렸을 때 접근 가능한 소스 링크를 뜻하지 않습니다.

| 포함 문서 | 원래 보고서 경로 |
|---|---|
| [거리표 최종 비교](../benchmarks/cpp-max/REPORT.md) | `cube_solver_cpp/results/max-final-20261101/REPORT.md` |
| [Tail 최종 비교](../benchmarks/cpp-tail/REPORT.md) | `cube_solver_cpp/results/tail-holdout-20261102/REPORT.md` |
| [Tail 대 Rob 원본](../benchmarks/cpp-tail-vs-rob/REPORT.md) | `cube_solver_cpp/results/tail-vs-rob-20261102/REPORT.md` |
| [AlphaCube 후처리](../benchmarks/dnn-postprocess/REPORT.md) | `hybrid_cube_lab/results/pilot-30-b1024/REPORT.md` |
| [교사 추가 학습](../benchmarks/dnn-teacher/REPORT.md) | `hybrid_cube_lab/teacher_training/runs/pilot-3000/evaluation/REPORT.md` |
| [독립 Policy+Value](../benchmarks/dnn-standalone/REPORT.md) | `hybrid_cube_lab/standalone_v2/runs/improved-v1/final/REPORT.md` |
| [Phase 2 후보 정렬](../benchmarks/dnn-ranking/REPORT.md) | `hybrid_cube_lab/ranking_lab/runs/pdb-screened/evaluation/REPORT.md` |
| [고정 시간 품질·메모리](../benchmarks/dnn-quality/REPORT.md) | `hybrid_cube_lab/quality_memory_lab/runs/pilot-100/REPORT.md` |
| [초소형 DNN 학습량 비교](../benchmarks/dnn-scale/REPORT.md) | `hybrid_cube_lab/tiny_scale_lab/runs/scale-1/REPORT.md` |

요약 작성에는 기존 `output/큐브_거리표_개선_노션.md`, `output/큐브_DNN_연구_노션.md`, `cube_solver_cpp/MAX_PDB.md`, `cube_solver_cpp/TAIL_OPT.md`도 참고했습니다. 실험 조건과 해석이 충돌할 때는 해당 실험의 최종 보고서를 우선했습니다.

## 4. 문서 형식 참고

- [Cube-Solver-Dev](https://github.com/P0doDang/PodoDang-suGar/tree/main/Cube-Solver-Dev): 소개, 구조, 동작 원리, 조건을 포함한 결과, 문제 해결, 한계, 출처의 구성 참고.
- [위니브 프로젝트 샘플](https://github.com/weniv/project_sample_repo): 목표, 개발 환경, 기능, 구조, 아키텍처, 문제 해결, 회고의 구성 참고.

웹 서비스용 로그인·ERD·화면 설계 등은 이 연구에 맞지 않아 포함하지 않았습니다. 참고 프로젝트의 성과나 작성자의 역할을 이번 작업의 결과로 가져오지 않았습니다.
