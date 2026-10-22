# Keithly-Id-Vd-converter

[최신 릴리스 및 첨부 파일](https://github.com/LWJ0604/Keithly-Id-Vd-converter/releases/latest)

배포 파일은 릴리스의 **Assets**에서 내려받으세요.

# Keithley I-V Reshaper 사용법

v1.1은 Python 3.7 이상을 지원합니다. Python 버전에 맞춰 pandas/numpy/openpyxl 버전을 자동 선택합니다.

Keithley에서 저장한 Excel 파일의 반복 열 구조를 자동으로 분석해 **Origin에서 바로 그리기 쉬운 형태**로 바꿔주는 도구입니다.

예를 들어 원본이 다음처럼 되어 있어도:

```text
DrainI(1) | DrainV(1) | GateV(1) | DrainI(2) | DrainV(2) | GateV(2) | ...
```

자동으로 다음과 같이 정리합니다.

```text
Vds_V | Id_Vg_m20V_A | Id_Vg_m19V_A | ... | Id_Vg_0V_A | ... | Id_Vg_p20V_A
```

Origin에서는 **첫 번째 열을 X, 나머지 열을 Y**로 지정하면 constant-Vg Id–Vds family plot을 바로 그릴 수 있습니다.

## 지원 기능

- `.xls`, `.xlsx`, `.xlsm`
- 여러 파일 한 번에 변환
- `DrainV sweep + constant GateV` → **Id–Vds 자동 판별**
- `GateV sweep + constant DrainV` → **Id–Vg 자동 판별**
- `GateI`가 존재하면 Ig 표도 함께 생성
- 동일 bias 조건의 반복 측정 → `Run01`, `Run02`, ... 자동 분리
- `Long_Format` 생성
- 원본 sheet 보존 옵션

> `dark`, `light`, `before`, `after` 같은 실험 의미는 파일 구조만으로 임의 판단하지 않고 Run 번호만 붙입니다.

## 최초 1회 설치

1. ZIP 파일을 원하는 폴더에 압축 해제합니다.
2. `setup_converter.bat`을 더블클릭합니다.
3. 설치가 완료되면 이후에는 `run_converter.bat`만 실행하면 됩니다.

`setup_converter.bat`은 변환기 폴더 안에 독립적인 `.venv`를 만들고 필요한 Python 패키지를 설치합니다.

## 사용 방법

### GUI 사용

1. `run_converter.bat` 더블클릭
2. **Add files...** 클릭
3. Keithley Excel 파일 선택
4. 필요하면 output folder 지정
5. **Convert** 클릭

Output folder를 비워두면 원본 파일 옆에 아래 이름으로 저장됩니다.

```text
원본파일명_reshaped.xlsx
```

### Drag & Drop

Excel 파일을 `run_converter.bat` 위에 드래그해도 바로 변환할 수 있습니다.

## 생성되는 주요 Sheet

Id–Vds를 두 번 반복 측정한 경우 예:

```text
README
Run01_Id_vs_Vds
Run02_Id_vs_Vds
Long_Format
Raw_Data
Raw_Settings
```

Id–Vg 측정이면:

```text
Run01_Id_vs_Vg
Run02_Id_vs_Vg
```

GateI가 원본에 있으면:

```text
Run01_Ig_vs_Vds
Run01_Ig_vs_Vg
```

형태의 sheet도 자동 생성됩니다.

## Long_Format

추후 Python 분석이나 parameter extraction에 쓰기 좋은 형태입니다.

```text
Run
Block
SweepType
ConstantBias
ConstantBias_V
Point
DrainV_V
GateV_V
DrainI_A
GateI_A
```

원본 measurement point 순서를 유지하기 때문에 특이한 sweep을 분석할 때는 wide sheet보다 `Long_Format`을 기준으로 보는 것이 안전합니다.

## 현재 folded ReS2 분석에 추천하는 사용법

1. Keithley 원본 `.xls`는 그대로 보관
2. 이 변환기로 `_reshaped.xlsx` 생성
3. Origin에서는 `RunXX_Id_vs_Vds` 또는 `RunXX_Id_vs_Vg`만 불러오기
4. 추가 분석(RR, conductance, differential conductance, photoresponse 등)은 `Long_Format` 또는 reshaped 파일을 기반으로 진행

이렇게 하면 원본 데이터는 건드리지 않고 분석용 데이터만 일관된 포맷으로 유지할 수 있습니다.
