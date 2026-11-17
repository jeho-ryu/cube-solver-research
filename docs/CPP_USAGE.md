# Cube Solver

min2phase 계열 C++ 엔진에 Rob 유래 거리표, RAM 전개 조회, 작업량을 제한한 선행 탐색을 결합한 3×3×3 솔버입니다.
일반 사용자에게는 미리 빌드한 실행 파일을 제공하고, 개발자는 명령줄 또는 C++ API로 연결합니다.
최대 21 HTM 해법을 찾으며 최단해를 보장하지 않습니다. HTM에서 180도 회전도 한 수입니다.

Python에서 `from cube_solver import Solver`로 사용하려면 `python/README.md`를 보세요.
Windows x64 wheel에는 실행 파일이 포함되며 C++ 컴파일 없이 pip로 설치합니다.

## 실행 파일로 사용하기 — 컴파일 필요 없음

Windows x64 Release ZIP을 모두 풀고 `run.cmd`를 더블클릭하세요.
섞은 수순을 입력하면 JSON으로 해법과 회전 수, 탐색 시간을 출력합니다.

```text
입력: R U R' U' F2 D L2
출력 형식: {"solution":"...","length":7,"solve_ns":12345}
```

`QUIT` 또는 `q`로 종료합니다. 각 입력은 완성 상태에서 섞은 수순입니다.
섞은 수순을 모르면 54글자 상태를 입력합니다. URFDLB 면 순서, 각 면은 정면에서 본 행 순서 9칸입니다.

```powershell
.\bin\cube-solver.exe --scramble "R U R' U'"
.\bin\cube-solver.exe --facelets UUUUUUUUURRRRRRRRRFFFFFFFFFDDDDDDDDDLLLLLLLLLBBBBBBBBB
.\bin\cube-solver.exe --serve --memory masks
.\bin\cube-solver.exe --init --data-dir .\data
```

표가 없으면 `cube-tablegen.exe`를 자동으로 실행해 로컬에서 생성합니다. Python/Java/인터넷/컴파일러는 실행에 필요 없습니다.
처음에는 수십 초~수분 이상 걸릴 수 있습니다. 생성 후 거리표는 약 827MB이며, 생성 중에는 임시 파일 포함 약 1.6GB가 필요합니다.
기본 `data` 위치는 현재 작업 폴더입니다. 다른 위치는 `--data-dir`로 지정하세요. `run.cmd`는 패키지 폴더를 작업 폴더로 사용합니다.
`cube-tablegen.exe`는 실행 프로그램 옆에 둡니다. 이미 거리표가 모두 준비되어 있으면 다시 생성하지 않습니다.

## 다른 프로그램에서 호출하기

프로세스를 한 번 켜 두고 여러 큐브를 보내면 초기화 비용을 매번 내지 않습니다.

```text
실행 명령: cube-solver --serve --data-dir data
stdout 첫 줄: {"ready":true,"threads":1,"memory":"max 또는 masks","startup_ns":...}
stdin 한 줄: 54글자 URFDLB 상태
stdout 한 줄: {"solution":"R ...","length":20,"solve_ns":...}
stdin: QUIT
```

0.1.1부터 `SCRAMBLE R U R' U'` 형식의 수순 입력도 --serve에서 지원합니다.

stdout은 JSON Lines 응답, stderr는 준비 로그와 오류입니다. 잘못된 큐브 상태는 `length: -1`로 응답합니다.
단일 풀이 모드에서 잘못된 입력/준비 오류는 종료 코드 2, 성공은 0입니다.
`examples/python_pipe.py`에 표준 라이브러리만 사용하는 Python 연결 예제가 있습니다.
한 큐브마다 새 프로세스를 띄우면 표 초기화가 반복되므로 `--serve` 사용을 권장합니다.

## C++ 프로그램에서 사용하기

```cpp
#include <cube_solver/solver.hpp>

cube_solver::Options options;
options.data_directory = "data";
options.memory = cube_solver::Memory::Max; // 또는 Masks, Auto
cube_solver::Solver solver(options); // 한 번 준비하고 재사용

auto result = solver.solve_scramble("R U R' U'");
// 54글자 상태: solver.solve(facelets)
if (result.valid()) {
    // result.solution: 회전 수순
    // result.moves: HTM 회전 수
    // result.solve_ns: 엔진 내부 탐색 시간 (나노초)
}
```

처음 사용할 때 필요한 표는 자동으로 생성합니다. 기본 생성기 위치는 호출 프로그램 실행 파일 옆입니다.
생성기를 다른 곳에 두면 `options.table_generator`에 파일 경로를 지정하세요.
모든 Solver는 읽기 전용 표를 공유합니다. 한 프로세스 안에서는 같은 data 경로와 메모리 모드를 사용해야 합니다.
한 Solver에 동시에 호출하면 직렬 처리합니다. 병렬 요청은 호출 스레드마다 Solver를 하나씩 만드세요.
잘못된 수순은 `std::invalid_argument`, 초기화/파일 오류는 예외로 전달됩니다. 잘못된 큐브 상태는 유효하지 않은 Result를 반환합니다.
초기화에서 표를 읽고 준비하는 동안은 시간이 걸립니다. 재사용 중인 Solver의 풀이 경로는 기존 Tail 엔진과 같습니다.

## 메모리 모드

| 모드 | RAM | 동작 |
|---|---|---|
| `max` | 약 10.9GiB | 대칭을 펼친 표에서 직접 조회. 약 13GiB 이상의 사용 가능한 RAM 필요 |
| `masks` | 약 800MiB | 압축된 대칭 표를 조회. 같은 Tail 선행 탐색 사용 |
| `auto` | 자동 선택 | 사용 가능한 RAM이 14GiB 이상이면 max, 그 외에는 masks |

정확한 속도 비교에는 같은 모드를 명시하세요. auto는 PC마다 선택이 달라질 수 있습니다.
이번 배포의 풀이는 1스레드입니다. 표를 준비할 때는 여러 스레드를 사용합니다.

## 속도 재현

`benchmark.cmd`를 더블클릭하거나 다음 명령을 실행합니다.

```powershell
.\bin\cube-solver.exe --benchmark --memory max --cases benchmark/cases.txt --output results
```

공개한 합법 무작위 상태 2,000개, 워밍업 300회, 3라운드 × 2반복입니다.
결과는 `results/summary.json`과 `results/samples.csv`에 저장합니다.
입력별 2반복 중앙값, 이어서 3라운드 중앙값으로 요약한 값에서 평균/p50/p95/p99/최댓값을 계산합니다.
표 생성·적재, 프로세스 시작, 파일 쓰기, 콘솔 출력은 `solve_ns`에서 제외합니다.
Solver mutex를 얻기 위한 대기 시간도 포함하지 않습니다. 생성/초기화는 `startup_ns`에 따로 기록합니다.
순서는 고정된 순차 입력이므로 과거 무작위 순서 실험의 절대 수치와 직접 섞어 비교하지 마세요.
Rob 비교를 자동 실행하는 기능은 포함하지 않습니다. Rob과 비교할 때도 같은 입력·회전 수 제한·스레드 수·시간 범위를 맞춰야 합니다.

## 소스 빌드

Windows에서 테스트한 컴파일러는 TDM-GCC x64의 g++입니다.

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File .\build_windows.ps1
.\build\bin\cube-solver.exe --scramble "R U R' U'"
.\build\bin\cube-example.exe
```

출력: `build/bin` 실행 파일 3개, `build/lib/libcube_solver.a` C++ 정적 라이브러리.
외부 프로젝트에서 같은 GCC 계열로 헤더와 라이브러리를 연결할 수 있습니다.

```powershell
g++ -std=c++17 -O3 -flto -pthread -Iinclude examples/simple.cpp build/lib/libcube_solver.a -o build/bin/my-app.exe -static-libgcc -static-libstdc++
```

표준 CMake 빌드 및 외부 프로젝트 연결도 제공합니다.

```text
cmake -S . -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build --parallel
cmake --install build --prefix install
```

Windows에서 CMake를 쓰면 GCC/MinGW용 generator를 선택하세요. MSVC는 현재 지원하지 않습니다.
GCC/Clang 및 Linux용 코드 경로는 제공하지만 이 배포에서는 Windows GCC 실행만 검증했습니다.

외부 CMake 프로젝트에서 소스를 포함:

```cmake
add_subdirectory(path/to/cube_solver_public cube_solver_build)
target_link_libraries(my_app PRIVATE CubeSolver::cube_solver)
```

설치된 패키지를 포함:

```cmake
find_package(CubeSolver CONFIG REQUIRED)
target_link_libraries(my_app PRIVATE CubeSolver::cube_solver)
```

## 폴더

| 폴더/파일 | 역할 |
|---|---|
| `include/cube_solver/solver.hpp` | 사용자가 포함할 공개 C++ API |
| `examples/` | C++ 직접 호출, Python 프로세스 연결 예제 |
| `tools/cli.cpp` | 명령줄/JSONL 인터페이스와 벤치마크 |
| `tools/tablegen_main.cpp` | 오프라인 거리표 생성기 |
| `src/public_api.cpp` | 공개 API와 기존 엔진 연결 |
| `src/public_setup.cpp` | 자동 표 생성, 생성 중 중복 실행 방지 |
| `src/core/` | Tail 개선을 반영한 min2phase 파생 탐색 엔진 |
| `src/*pdb*`, `src/*tables*` | 표 적재와 RAM 조회 |
| `vendor/` | 원본 소스, 출처, 라이선스 |
| `benchmark/cases.txt` | 재현용 고정 입력 2,000개 |
| `data/` | 실행 때 만드는 표. 저장소/배포 ZIP에는 포함하지 않음 |

탐색 엔진과 표의 출처 및 GPL-3.0-or-later 조건은 `NOTICE.md`와 `COPYING`을 확인하세요.
`src/core`는 이미 생성된 대응 소스입니다. 이 독립 프로젝트는 `prepare.py` 없이 빌드됩니다.
이번 포장 작업은 새 탐색 알고리즘이나 Rob 전체 기능의 대체를 의미하지 않습니다.
