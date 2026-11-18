# 출처와 자료 범위

[전체 요약으로 돌아가기](../README.md)

## 자료 읽는 법

README와 상세 글에서 인용한 숫자는 아래 실험 보고서에서 확인할 수 있습니다. 각 보고서에는 측정에 쓴 큐브와 비교 조건, 당시 얻은 결과가 들어 있습니다.

보고서에 나오는 코드와 원시 측정값의 경로는 실험할 때 쓴 작업 폴더를 기준으로 합니다. Tail 보고서에서는 실행 안내 링크 한 곳을 이 저장소의 C++ 글에 맞췄습니다. 각 보고서 옆의 `SOURCE.json`에 원본과 게시본의 SHA-256 값을 적었습니다.

`docs/CPP_USAGE.md`는 기존 공개용 C++ 프로젝트의 README 사본입니다. 그 안의 빌드·실행 명령은 해당 소스나 실행 패키지에서 사용하는 명령입니다.

## 어떤 프로젝트를 바탕으로 만들었나

| 외부 프로젝트 | 기존에 제공한 것 | 이번 작업에서 추가한 것 |
|---|---|---|
| [min2phaseCXX](https://github.com/lilborgo/min2phaseCXX) | C++ two-phase 엔진 | 거리표 연결, 조회 방식·탐색 순서 변경, 공개용 인터페이스 |
| [min2phase](https://github.com/cs0x7f/min2phase) | Java two-phase 엔진 | 교사 데이터, 후보·방향 선택 실험, 비교·검증 도구 |
| [Rob two-phase](https://github.com/efrantar/rob-twophase) | 거리표·좌표·대칭 정의와 원본 탐색 | min2phase 좌표로 표 변환, 이동 마스크·하한 연결, 상주 풀 비교 |
| [AlphaCube](https://github.com/kyo-takano/alphacube) | 신경망·사전학습 모델·탐색 기반 | 깊이·시간 제한 후처리, 구간 교체, 교사 추가 학습·평가 |

독립 Policy+Value 실험에서는 모델을 직접 학습시키고 탐색 코드도 작성했습니다. 이때 min2phase로 학습용 풀이를 만들고, 완성한 모델이 같은 큐브를 얼마나 푸는지 min2phase와 비교했습니다.

C++ 출처 기록의 기준 커밋은 min2phaseCXX `db0ee5f431178f2153f9aa60581bd6ca59e89391`, Rob `d245031257d52b2663c5790c5410ef30aefd775f`입니다. C++ 파생 패키지에는 GPL-3.0-or-later를 적용했으며, 각 원본의 저작권 표시는 패키지의 `NOTICE.md`, `COPYING`, `vendor` 폴더에 있습니다. AlphaCube 원본은 MIT 라이선스를 따릅니다.

## 실험별 보고서

왼쪽 문서명을 누르면 해당 보고서를 읽을 수 있습니다.

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

## 문서를 정리할 때 참고한 저장소

실험 수치 옆에 비교 조건을 붙이는 방식은 Cube-Solver-Dev를 참고했습니다. 프로젝트를 시작한 이유와 개발하면서 부딪힌 문제를 글에 담을 때는 위니브 예시를 참고했습니다. [Cube-Solver-Dev](https://github.com/P0doDang/PodoDang-suGar/tree/main/Cube-Solver-Dev) · [위니브 프로젝트 샘플](https://github.com/weniv/project_sample_repo)
