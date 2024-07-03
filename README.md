# flutterproj

A new Flutter project.

## Getting Started

This project is a starting point for a Flutter application.

A few resources to get you started if this is your first Flutter project:

- [Lab: Write your first Flutter app](https://docs.flutter.dev/get-started/codelab)
- [Cookbook: Useful Flutter samples](https://docs.flutter.dev/cookbook)

For help getting started with Flutter development, view the
[online documentation](https://docs.flutter.dev/), which offers tutorials,
samples, guidance on mobile development, and a full API reference.
### Details

---

> **Intro**
> 
- 앱을 처음 실행했을 때 MBook 마스코트 ‘엠부기’ 가 등장합니다.
- `TapBar` 를 통해 탭 1,2,3 으로 이동할 수 있습니다.

> **Tab 1 : Contacts**
> 
- **개요**: 작가들의 목록이 나열된 탭이며 데이터는 SQLite DB에 저장하였습니다.
- **작가 목록**: 작가의 사진, 이름, 대표작을 `ListView` 를 통해 보여지도록 구현했습니다.
- **작가 검색**: 탭 상단에 검색창을 표시하였으며 영문, 한글 검색이 가능합니다.
- **작가 상세 페이지**: 리스트 중 하나를 누르면 해당 작가의 상세 페이지로 넘어갑니다. `Navigator`를 통해 상세 페이지로 클릭된 item의 정보가 함께 넘어가도록 구현했습니다. `Sliver`를 통해 상단의 bar 와 이미지 리스트를 작성했습니다. 또한 작가 이미지를 누르면 이미지가 전체 화면으로 확대됩니다.

> **Tab 2 : Gallery**
> 
- **개요**: 책들이 나열되어 있는 갤러리입니다. 검색창, 갤러리, 추가 Asset들로 구현되어 있습니다. 갤러리 탭 입장 시 자동으로 DB의 데이터를 받아온 json파일을 불러오며, 내부 화면을 구성할 준비를 합니다.
- **검색창**: 제목 또는 작가를 기준으로 검색하여 책을 찾을 수 있도록 구현했습니다. 검색창은`TextEditingController`, 검색 기준설정은 `DropdownButton` 으로 구현했습니다.
- **책 갤러리**: 로컬 SQLiteDB에 저장된 데이터를 받아와, `Gridview`를 사용하여 저장된 책들의 이미지를 띄웁니다. 이미지 경로를 분석하여 로컬 이미지인지, 앱 기본 이미지인지 인식하여 띄웁니다. `GestureDectector`로 터치를 감지합니다.
    - 짧은 탭: 새 창 `ZoomableImage`를 Navigator에 push하여 이미지 상세정보를 보여줍니다.
    - 긴 탭: 해당 책 정보를 수정하는 창 `BookAdjust`를 Navigator에 push합니다.
- **이미지 상세정보**: 갤러리에서 사진을 누르면 뜨는 창으로, 이미지를 자유롭게 확대, 축소하여 자세한 책의 이미지를 볼 수 있게 합니다. `GestureDectector`로 여러 동작을 감지하고, `InteractiveViewer`로 자유자재로 이미지를 보여줍니다. `Hero`로 화면전환 애니메이션을 매끄럽게 만들어주었습니다.
    - 짧은 탭: 해당 창을 Navigator에서 pop합니다.
    - 더블 탭: `TransformationController`를 사용, 더블 탭한 곳을 중심으로 2.5배 확대합니다. 이미 2.5배 이상 확대되어 있다면 원본크기로 되돌립니다.
    - 스크롤: 이미지를 이동합니다. 만약 가장 밑에서 아래에서 위로 스크롤했다면 그것을 `ScrollListener`로 감지하여 `showModalBottomSheet`를 사용, 책의 제목, 작가, 그리고 줄거리를 보여줍니다.
- **More Actions 버튼**: 추가로 여러 동작을 가능케 하는 버튼 2개를 `AnimatedBuilder`를 사용하여 나선형 애니메이션으로 띄웁니다. 버튼 위 아이콘을 점 3개에서 X표시로 변경하며, 다시 누르면 나선형 애니메이션이 반대로 작동하며 다시 점 3개로 돌아갑니다. `ColorFiltered`를 사용하여 누르는 즉시 갤러리가 어두워지도록 변경하였습니다. `AnimationController`의 값에 따라 More Actions버튼을 중심으로 각은 일정하게 변화하며, 반지름은 부드러운 변화를 위해 이차함수와 같이 변화하도록 설정하였습니다. b를 최댓값의 위치라고 할 때, 해당 이차함수는 다음과 같습니다. 저희는 b=0.85로 설정하여 애니메이션이 85% 완료되었을 때 반지름이 감소하기 시작하도록 설정했습니다.
    - $y=\frac{1}{1 - 2b}(x-b)^2-\frac{b^2}{1-2b}$
    - 해당 애니메이션으로 새롭게 등장하는 버튼 2개는 아래에서 설명할 책 추가버튼과 책 삭제 버튼입니다.
- **책 추가ㆍ수정**: 새로운 책을 삽입하거나(More Actions 버튼을 눌러서 ‘+’버튼을 클릭) 책을 수정하는(이미지를 길게 눌러 진입) 기능입니다. 책 저자, 제목, 줄거리, 소개를 넣는 `Textfield`가 있고, 이미지를 추가하는 버튼, 제출하는 버튼으로 구성되어 있습니다. 이미지를 먼저 추가한다면 Google Vision AI가 이미지에서 자동으로 이미지의 텍스트를 인식, ChatGPT 3.5-Turbo가 그 텍스트에서 다시 책의 제목과 저자를 추출해내는 OCR 기술을 접목시켰습니다. 제출한다면 자동으로 DB를 업데이트하고, json파일을 수정하며 화면을 reload합니다.
- **책 삭제**: More Actions 버튼을 눌러서 ‘-’버튼을 클릭한다면 삭제기능이 활성화됩니다. 누르는 즉시 화면이 다시 밝아지며, 각 책에 체크박스가 띄워지며(`Stack`), 검색기준 드롭다운 목록 옆에 쓰레기통 아이콘 모양의 버튼이 생깁니다. 지우고 싶은 책 위의 체크박스를 눌렀다면, 쓰레기통 모양의 아이콘을 눌러 지울 수 있습니다. 지우는 즉시 DB와 json파일을 업데이트, 반영합니다. 삭제화면에서 나가려면, ‘-’버튼을 다시 누르면 삭제기능이 취소, 종료됩니다.

> **Tab 3 : Today’s Book**
> 
- **개요**: 오늘의 책을 고르는 탭입니다.
- **오늘의 책 페이지**: 시작 전 물음표가 떠있습니다. Start 버튼을 누르면 책 이미지가 슬라이드쇼로 빠르게 지나갑니다. DB에서 파일 경로를 받아와 `List<String>` 을 만들고 인덱스를 계속해서 변화시켜 구현했습니다. Stop 버튼을 누르면 오늘의 책 팝업 창이 뜹니다.
- **오늘의 책 팝업 창**: 팝업 창에는 마스코트 ‘엠부기’, 오늘의 책 이미지, 책선반 오브젝트, 오늘의 책 멘트가 적혀있습니다.
    - 오늘의 책 이미지: `AnimatedCrossFade`를 통해 `secondChild`인 ‘tadaramzi’ 와 `firstChild`인 오늘의 책 이미지가 차례로 나타나게 구현했습니다.
    - 책 선반 오브젝트: `CustomPaint` 를 이용해 사다리꼴을 만들고 `Container`를 이용해 직사각형을 만든 것을 합쳐 구현했습니다.
    - 오늘의 책 멘트: DB에서 책 별 멘트를  가져왔습니다.
- **경고 문구**: Start 버튼을 누르지 않고 Stop 버튼을 먼저 누른 경우 `AlertDialog`를 통해 경고 문구가 나타나도록 했습니다.
