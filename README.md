## DownloadsClassify-RPA

다운로드 폴더에 들어오는 파일들을 확장자→카테고리 규칙에 따라 자동으로 분류·이동하는 UiPath 프로젝트입니다.
메인 워크플로우는 Main.xaml, 보조 유틸리티로 EnsureDirectory.xaml을 사용합니다. 프로젝트 메타/의존성은 project.json에 정의되어 있습니다. 

### 빠른 소개
확장자별 규칙(JSON) 기준으로 파일을 이미지/문서/비디오/음악/압축/프로그램/기타 등 하위 폴더로 자동 이동하는 RPA프로세스입니다.
다운로드 폴더를 간편하게 정리할 수 있고 fileMapping.json만 편집하면 새 확장자/카테고리를 (로봇 수정 없이) 추가하여 관리할 수 있습니다.

### 실행 환경
UiPath Studio/Robot (Windows, C# 식 표현 사용) — 프로젝트 타겟: Windows, Entry: Main.xaml 
주요 패키지(예시): System, UIAutomation, WebAPI, Mail, Excel 액티비티 등 (버전은 project.json 참조) 

### 폴더 구조(요약)
```bash
DownloadsClassify-RPA/
├─ Main.xaml                # 진입점
├─ EnsureDirectory.xaml     # 대상 폴더 보장(없으면 생성)
├─ fileMapping.json         # 확장자→카테고리 매핑 + 로깅 옵션
└─ project.json             # UiPath 프로젝트 메타/의존성
```
### 설정 (fileMapping.json)
fileMapping.json은 확장자별 카테고리를 정의합니다. 비어있는 확장자 키("")는 기타로 분류하는 기본값입니다. enableLogging이 있으면 간단한 로그를 켤 수 있도록 설계되어 있습니다. 

예시 일부:
{
  "categoryMapping": {
    ".jpg": "이미지",
    ".pdf": "문서",
    ".mp4": "비디오",
    ".mp3": "음악",
    ".zip": "압축파일",
    ".exe": "프로그램",
    "": "기타"
  },
  "enableLogging": true
}

✔ 확장자 키는 소문자 기준으로 맞추는 걸 권장합니다.
✔ 필요 시 이미지/문서/비디오/... 폴더 이름은 자유롭게 변경해도 됩니다.

### 동작 개요

대상 폴더 스캔 → 파일 목록 수집
확장자 파싱 → fileMapping.json에서 카테고리 조회 
대상 경로 보장 → EnsureDirectory.xaml로 카테고리 폴더 생성
파일 이동 → 동일 파일명 존재 시 중복 처리 규칙(예: 접미사 부여) 적용
로깅(옵션) → enableLogging이 true면 이동 결과를 출력/파일 기록

### 사용 방법

UiPath Studio로 폴더 열기 (DownloadsClassify-RPA 루트).
fileMapping.json에서 필요한 확장자/카테고리 추가/수정. 
Main.xaml 실행(디버그/런). 

첫 실행 시 각 카테고리 폴더가 자동으로 만들어지고, 파일들이 규칙에 따라 이동됩니다.
Tip) 테스트를 위해 빈 텍스트 파일(확장자 여러 개)과 예제 이미지/문서를 다운로드 폴더에 미리 넣어두면 동작이 바로 보입니다.

### 자주 하는 변경 시나리오

새 확장자 추가: ".heic": "이미지"처럼 fileMapping.json에 한 줄 추가 → 저장 → 재실행. 
폴더 이름 바꾸기: "문서"를 "Docs"로 바꾸면 다음 실행부터 새 폴더에 이동.
로깅 끄기: "enableLogging": false로 변경. 

### 향후 개선 아이디어

규칙 고급화: 파일 크기/이름 패턴/수정일자 기반 라우팅
충돌 처리 옵션: 덮어쓰기/버전링/스킵 정책 선택
스케줄링: Orchestrator/Windows 작업 스케줄러를 통한 주기 실행
알림: 정리 결과를 이메일/슬랙 Webhook으로 전송
