# 러프 스케치 정리를 위한 벤치마크

이 코드 저장소는 SIGGRAPH Asia 2020에서 Chuan Yan, David Vanderhaeghe, 그리고 Yotam Gingold가 발표한 <a href="https://cragl.cs.gmu.edu/sketchbench/">*A Benchmark for Rough Sketch Cleanup*</a>논문과 관련된 코드 저장소입니다.

이 코드는 논문에서 설명한 메트릭을 계산하고, 다양한 스케치 정리 알고리즘의 출력물을 비교하기 위한 벤치마크 웹사이트를 생성합니다.



## 디렉토리 구조

데이터 디렉토리는 `cfg.yaml` 파일에 정의되어 있습니다:

- `dataset_dir`: 사용자가 이곳에 데이터셋을 넣습니다. 웹사이트에서 필요합니다.
- `alg_dir`: 사용자가 여기에 자동 결과물을 넣습니다. 웹사이트에서 필요합니다.
- `web_dir`: 웹사이트를 여기에 생성합니다. 이미지 경로는 `../{alg_dir}/rest/of/path.svg`와 같이 보입니다.
- `table_dir`: 벤치마크에서 계산된 메트릭을 여기에 생성합니다. 웹사이트 생성에 필요하지만 웹사이트를 호스팅할 때는 필요하지 않습니다. (테스트한 알고리즘의 미리 계산된 버전이 아래에 제공됩니다.)
- `test_dir`: 여기에 알고리즘 테스트용 리사이즈된 이미지 파일을 생성합니다. 메트릭을 계산할 때 필요하지만 웹사이트에서는 필요하지 않습니다. (미리 계산된 버전이 아래에 제공됩니다.)

기본값은 다음과 같습니다.

```python
vbnetCopy code
dataset_dir: './data/Benchmark_Dataset'
alg_dir: './data/Automatic_Results'
web_dir: './data/web'
table_dir: './data/Evaluation_Data'
test_dir: './data/Benchmark_Testset'
```

`test_dir` 데이터를 직접 생성하는 경우, Inkscape와 ImageMagick이 필요합니다. `run_benchmark.py`는 사용자의 운영 체제에 따라 이들을 찾으려고 시도합니다. `inkscape_path`와 `magick_path`를 변경하여 `cfg.yaml`에서 직접 경로를 설정할 수 있습니다.



## 코드 종속성 설치

이 저장소를 복제하거나 다운로드하세요. 이 코드는 Python으로 작성되었습니다. 

다음 모듈에 의존합니다: `aabbtree`, `CairoSVG`, `cssutils`, `matplotlib`, `numpy`, `opencv-python`, `pandas`, `Pillow`, `PyYAML`, `scipy`, `svglib`, `svgpathtools`, `tqdm`

다음 명령어를 사용하여 이러한 모듈을 설치할 수 있습니다:

```bash
pip3 install -r requirements.txt
```

또는 더 재현 가능한 환경을 위해 [Poetry](https://python-poetry.org/docs/#installation) (`brew install poetry` 또는 `pip install poetry`)를 사용할 수 있습니다:

```bash
poetry install --no-root
poetry shell
```

또는 Pipenv (`pip install pipenv`):

```bash
pipenv install
pipenv shell
```

`shell` 명령은 가상 환경을 활성화합니다. 스크립트를 실행하기 전에 한 번 실행해야 합니다.

미리 계산된 테스트 이미지를 다운로드하지 않는 경우, 시스템에 다음 외부 소프트웨어가 설치되어 있는지 확인하십시오:

1. [Inkscape 1.x](https://inkscape.org/). 최신 Inkscape를 설치하세요. 1.0 이전 버전은 호환되지 않는 명령줄 매개변수를 가지고 있습니다. `brew cask install inkscape` 또는 `apt-get install inkscape`로 설치할 수 있습니다.
2. [ImageMagick](https://imagemagick.org/script/download.php). `brew install imagemagick` 또는 `apt-get install imagemagick`으로 설치할 수 있습니다.



## 데이터셋 및 미리 계산된 출력

스케치 데이터셋, 미리 계산된 알고리즘 출력 및 계산된 메트릭을 다음에서 다운로드할 수 있습니다: [`Benchmark_Dataset.zip` (900 MB)](https://cragl.cs.gmu.edu/sketchbench/Benchmark_Dataset.zip), [`Automatic_Results.zip` (440 MB)](https://cragl.cs.gmu.edu/sketchbench/Automatic_Results.zip), [`Evaluation_Data.zip` (20 MB)](https://cragl.cs.gmu.edu/sketchbench/Evaluation_Data.zip). `cfg.yaml`에서 경로를 변경하지 않은 경우 `./data/`에 압축을 풉니다:

```bash
unzip Benchmark_Dataset.zip
unzip Automatic_Results.zip
unzip Evaluation_Data.zip
```

벡터화된 데이터는 균일한 선 너비를 갖도록 정규화되었습니다. 작가들이 기존 이미지의 선 너비에 맞추는 것이 너무 귀찮아서 이를 요구하지 않았으며, 그 후 데이터를 정규화했습니다.



## 실행

### 테스트셋 생성 또는 다운로드

(이미 계산된 출력과 계산된 메트릭을 사용하여 논문에서 웹사이트를 다시 생성하려는 경우 테스트셋은 필요하지 않습니다. 웹사이트 자체를 제외한 모든 것을 변경하려는 경우 필요합니다.)

테스트셋은 데이터셋에서 유도된 파일로 구성됩니다: 벡터 이미지의 비트맵화된 버전 및 축소된 이미지입니다. 다음을 실행하여 다시 생성하거나 [`Benchmark_Testset.zip` (780 MB)](https://cragl.cs.gmu.edu/sketchbench/Benchmark_Testset.zip)을 다운로드하고 `./data/`에 압축을 풉니다(경로를 `cfg.yaml`에서 변경하지 않은 경우):

```bash
unzip Benchmark_Testset.zip
```

테스트셋을 다시 생성할 수 있습니다(데이터셋 자체를 변경하는 경우 필요):

```bash
python3 run_benchmark.py --normalize   # SVG의 정규화된 버전 생성
python3 run_benchmark.py --generate-test # 데이터셋의 비트맵 버전을 다양한 해상도로 생성
```

이렇게 하면 `dataset_dir` 및 `test_dir`를 스캔하여 필요한 정규화된 및 비트맵 이미지를 생성합니다.

전체 테스트셋 생성에는 약 20~30분 정도 소요됩니다.



### 벤치마크에 알고리즘 추가

테스트셋의 모든 이미지에서 알고리즘을 실행합니다. 알고리즘이 래스터 입력을 사용하는 경우 `./data/Benchmark_Testset/rough/pixel`의 모든 이미지에서 실행합니다. 벡터 입력을 사용하는 경우 `./data/Benchmark_Testset/rough/vector`의 모든 이미지에서 실행합니다. 각 입력에 대해 해당 출력 이미지를 파일로 저장하고 디렉토리에 동일한 이름으로 저장합니다: `./data/Automatic_Results/{name_of_your_method}{input_type}/{parameter}/`

알고리즘 폴더 이름은 두 부분으로 구성되어야 합니다: `name_of_your_method`에 `input_type` 접미사가 있어야 합니다. `input_type` 접미사는 `-png` 또는 `-svg` 중 하나여야 합니다. 

매개변수 하위 디렉토리는 임의의 문자열이 될 수 있으며, 웹사이트를 생성할 때 `none` 문자열은 빈 문자열로 대체됩니다. 맨 앞에 `.`이 있는 폴더는 무시됩니다.

`./Automatic_Results`에 미리 계산된 알고리즘 출력 및 `./Evaluation_Data`에 평가 결과가 있는 예제를 참조하세요.

알고리즘이 `alg path/to/input.svg path/to/output.png`을 통해 실행되는 경우 전체 벤치마크에서 알고리즘을 배치로 실행하는 두 가지 예제 명령어는 다음과 같습니다.

`find` 및 `parallel`을 사용한 방법:

```bash
find ./data/Benchmark_Testset/rough/pixel -name '*.png' -print0 | parallel -0 alg '{}' './data/Automatic_Results/MyAlgorithm-png/none/{/.}.svg'
```

`fd`를 사용한 방법:

```bash
fd ./data/Benchmark_Testset/rough/pixel -e png -x alg '{}' './data/Automatic_Results/MyAlgorithm-png/none/{/.}.svg'
```



### 메트릭 계산

다음 명령을 사용하여 평가를 실행합니다.

```bash
python3 run_benchmark.py --evaluation
```

이 명령은 `./data/Evaluation_Data`에 CSV 파일을 생성합니다. 기존 CSV 파일을 덮어쓰지 않습니다.

미리 계산된 데이터를 다운로드했다면 파일을 제거하여 재생성해야 합니다.



### 평가 결과를 보기 위한 웹사이트 생성

위의 평가 단계를 호출하여 메트릭을 계산한 후, 다음 명령을 사용하여 웹사이트를 생성합니다.

```bash
python3 run_benchmark.py --website
```

또한 썸네일을 한 번 생성해야 합니다.

```bash
python3 run_benchmark.py --thumbs
```

내부적으로 `--thumbs` 명령은 `find`, `convert`, 및 `parallel`을 호출하는 셸을 생성합니다.

웹사이트를 보려면 수동으로 `web_dir` 내의 `help.html` 또는 `index.html`을 열거나 다음 명령을 호출하십시오.

```bash
python3 run_benchmark.py --show
```

웹사이트는 모든 알고리즘의 출력물을 시각화하고 메트릭을 플롯합니다.



### 모두 함께 실행

각 단계를 개별적으로 호출하지 않으려면 다음 명령만 실행하면 됩니다.

```bash
python3 run_benchmark.py --all
```



## 단일 스케치의 메트릭 계산

### 유사성 메트릭

유사성 메트릭을 수동으로 실행하려면 `tools/metric_multiple.py`를 사용합니다. 도움말을 보려면 다음 명령을 실행하십시오.

```bash
python3 tools/metric_multiple.py --help
```

두 파일을 비교하려면 다음을 실행하십시오.

```bash
python3 tools/metric_multiple.py -gt "example/simple-single-dot.png" -i "example/simple-single-dot-horizontal1.png" -d 0 --f-measure --chamfer --hausdorff
```



### 벡터 메트릭

접선 품질을 평가하려면 다음을 실행하십시오.

```bash
python3 tools/junction_quality.py --help
```

호도르프 통계량을 계산하려면 다음을 실행하십시오.

```bash
python3 tools/svg_arclengths_statistics.py --help
```



### 비트맵화

SVG 파일을 PNG로 변환해야 하는 경우 출력 파일명을 지정하여 다음을 수행할 수 있습니다.

```bash
inkscape my_file.svg --export-filename="output-WIDTH.png" --export-width=WIDTH --export-height=HEIGHT
```

또는 출력 유형을 지정할 수 있습니다(입력 파일명의 확장자가 바뀝니다).

```bash
inkscape my_file.svg --export-type=png --export-width=WIDTH --export-height=HEIGHT
```

