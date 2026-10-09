## **프로젝트 주제**
- 공개 MS/MS 데이터베이스 간 동일 compound의 spectral reproducibility 및 변동 요인 분석

**프로젝트 배경 및 목적**
- 지난 랩 인턴 동안 LC-MS/MS 데이터를 다루며 GNPS molecular networking, library matching, MassQL 등을 이용한 성분 탐색을 경험하였다. 이 과정에서 동일하거나 유사한 화합물이라도 MS/MS spectrum과 cosine similarity가 항상 일정하게 나타나는 것은 아니며, 실험 조건이나 데이터 처리 조건에 따라 결과가 달라질 수 있다는 점에 관심을 가지게 되었다. 공개 MS/MS 데이터베이스에는 서로 다른 연구기관과 분석 조건에서 얻어진 spectrum이 축적되어 있으며, 동일 compound가 여러 데이터베이스에 등록된 경우도 존재한다. 따라서 동일 compound의 MS/MS spectrum이 데이터베이스 간 어느 정도 유사하게 재현되는지 정량적으로 확인하고, 그 차이에 영향을 미치는 요인을 분석하고자 한다. 최종적으로는 공개 spectral database를 이용한 library matching 및 MS/MS similarity 기반 분석에서 고려해야 할 변동 요인을 파악하는 것을 목적으로 한다.
 
**해결하고 싶은 문제**
- Spectral library search에서는 일반적으로 MS/MS spectrum의 유사도를 이용해 후보 화합물을 탐색한다. 그러나 동일 compound라도 장비 종류, collision energy, precursor/adduct 등의 조건에 따라 fragment pattern이나 intensity가 달라질 수 있다. 이러한 차이가 실제 database 간 spectrum similarity에 어느 정도 영향을 미치는지, 그리고 특정 실험 조건이나 분자 구조적 특성이 낮은 재현성과 관련되는지를 확인하고자 한다. 또한 단순히 전체 cosine similarity만 비교하는 것을 넘어, 어떤 fragment m/z 영역에서 차이가 크게 나타나는지도 탐색하고자 한다.

**핵심 분석 질문 2~3개 이상**
1) 동일 compound의 MS/MS spectrum은 서로 다른 공개 데이터베이스에서 어느 정도 유사하게 나타나는가?
   - 동일 compound spectrum pair의 cosine similarity 분포 분석
   - database 조합별 similarity 비교
2) 실험 조건 중 어떤 요인이 spectral reproducibility와 관련되는가?
   - instrument type, collision energy, precursor/adduct 등 확보 가능한 metadata를 이용하여 조건별 cosine similarity 비교
3) 어떤 fragment m/z 영역에서 database 간 차이가 크게 나타나는가?
   - fragment를 m/z 범위별로 나누어 각 범위의 matched fragment 비율을 비교하고, 데이터가 충분할 경우 상대 intensity의 변동도 추가로 확인한다.
4) 어떤 구조적 특성이 낮은 spectral reproducibility와 관련되는가?
   - molecular weight, ring count, rotatable bond 수, heteroatom 수 등의 molecular descriptor와 cosine similarity의 관계 탐색
* 데이터 확보 상황에 따라 1-2번을 우선적으로 분석으로 수행하고, 3-4번은 가능할 경우 진행하고자 한다.

**데이터 출처**
- 공개적으로 제공되는 MS/MS spectral database 중 GNPS와 MassBank 또는 MoNA를 우선적으로 사용할 예정이다.


각 데이터베이스에서 compound identifier, precursor 정보, experimental metadata 및 MS/MS peak list를 확보하고, 필요할 경우 PubChem 등의 공개 화학 데이터베이스를 이용해 SMILES(컴퓨터가 읽을 수 있는 분자구조식) 또는 분자 구조 정보를 추가한다.

**데이터 확보 방법**
- 각 데이터베이스에서 제공하는 bulk download, API 또는 공개 spectral library 파일을 이용해 spectrum과 metadata를 수집한다.


수집 후 다음과 같은 조건을 이용해 비교 가능한 데이터를 선별할 예정이다.
  - 동일 compound identifier(InChIKey 등)가 존재하는 화합물
  - MS/MS spectrum 보유
  - 동일 ionization mode
  - 가능한 경우 동일 precursor/adduct
  - 최소한의 experimental metadata가 확보된 spectrum
    Compound name은 synonym 및 표기 차이가 존재할 수 있으므로, 가능하면 이름이 아닌 InChIKey와 같은 구조 기반 identifier를 기준으로 데이터를 연결한다.

**예상 데이터 규모 및 주요 컬럼**
- 우선 전체 데이터베이스를 대상으로 하기보다 소규모 예비 분석을 통해 비교 가능한 compound 수를 확인한 뒤 범위를 결정할 예정이다.
초기 목표는 약 100–500개의 공통 compound로 하되, 실제 공통 데이터 규모와 metadata 품질에 따라 조정한다.
예상 주요 컬럼은 다음과 같다.

| 구분 | 주요 컬럼 |
|---|---|
| Compound 정보 | compound_id, compound_name, InChIKey, SMILES |
| Spectrum 정보 | spectrum_id, database, precursor_mz, ion_mode, adduct |
| 실험조건 | instrument_type, collision_energy, fragmentation_method |
| MS/MS | fragment_mz, fragment_intensity |
| 구조정보 | molecular_weight, ring_count, rotatable_bond, heteroatom_count |

**예상 분석 방법**
- 먼저 데이터 구조와 품질을 확인하고 identifier, 자료형, 결측치, 중복 및 metadata 표기 차이를 정리한다.
이후 동일 compound에 해당하는 spectrum pair를 생성하고 cosine similarity를 계산하여 spectral reproducibility의 기본 분포를 확인한다. DB별·instrument별·collision energy 조건별 similarity를 boxplot, histogram 등으로 비교하고, 조건에 따라 그룹 간 차이를 비교하는 적절한 통계 방법을 적용한다.
- Fragment 분석에서는 fragment m/z를 일정 구간 또는 precursor 대비 상대적인 m/z 범위로 나누어 matched fragment 비율과 intensity variation을 비교한다. 구조 정보가 충분히 확보될 경우 분자량, ring 수, rotatable bond 수 등과 cosine similarity의 관계를 확인한다.
- 결과의 안정성을 확인하기 위해 mass tolerance, minimum matched peaks 등 cosine 계산 조건을 일부 변경하고, 이에 따라 결과의 경향이 유지되는지 확인한다.

**생성형 AI 활용 계획**
- 생성형 AI는 분석 자체를 전적으로 수행하도록 하기보다 다음 작업의 보조 도구로 활용한다.
  - 데이터 구조 및 분석 절차 설계
  - Python/SQL 코드 작성 및 오류 수정
  - 데이터 전처리 방법 검토
  - 통계 분석 방법 후보 비교
  - 분석 결과에 대한 대안적 해석 탐색
  - 시각화 및 보고서 작성 보조
- 다만 AI가 생성한 코드나 해석을 그대로 사용하지 않고, 원본 데이터와 코드 실행 결과를 직접 확인하여 검증한다. 특히 compound matching, 중복 처리, 통계적 결과 등 분석 결론에 영향을 미치는 부분은 별도로 점검한다.


**향후 프로젝트 진행 계획**
- 초기에는 GNPS와 MassBank 또는 MoNA의 소규모 subset을 이용해 소규모 예비 분석을 수행한다. 먼저 두 database에 공통으로 존재하는 compound의 수와 experimental metadata의 결측률을 확인하여 프로젝트 실행 가능성을 평가한다.
- 이후 분석 가능한 데이터가 충분할 경우 전체 데이터 수집 및 전처리를 진행하고, 동일 compound spectrum pair 생성 → cosine similarity 계산 → 조건별 EDA 및 통계 분석 → fragment 및 구조 특성 분석 순서로 진행한다.
- 마지막으로 분석 parameter를 변경한 분석과 일부 spectrum의 수동 확인을 통해 결과를 검증하고, 실제 관찰 결과와 이에 대한 해석을 구분하여 작성하며, metadata 부족, database 간 수집 방식 차이 등의 한계와 추가적으로 확인해야 할 사항을 정리할 예정이다.
