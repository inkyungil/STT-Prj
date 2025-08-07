# 📘 AI 기반 콜센터 음성 분석 자동화 시스템 기획서

> **구성 요소:**  
> ✅ Google Drive (mp3 업로드)  
> ✅ n8n (자동화 워크플로우: Whisper + 파싱)  
> ✅ PHP CMS (VOC 대시보드 및 관리)  
> ✅ MariaDB (분석 데이터 저장)

---

## 🔁 시스템 전체 흐름

```plaintext
[사용자]
 └─▶ mp3 파일 Google Drive 업로드
       ↓
[n8n Google Drive Trigger]
       ↓
[Whisper API 호출 (OpenAI)]
       ↓
[텍스트 + 메타데이터 파싱]
       ↓
[PHP API 호출 → MariaDB 저장]
       ↓
[PHP CMS 관리자 페이지에서 확인 및 관리]


---

1️⃣ Google Drive + n8n 자동화 워크플로우

구성	설명

Google Drive Trigger	지정 폴더에 mp3 업로드 시 감지
Whisper API (STT)	mp3 → 텍스트 변환 (한국어 지원)
OpenAI GPT (선택)	텍스트를 항목별 JSON으로 분류
HTTP POST	결과를 PHP API(/api/stt/upload)로 전송
데이터 예시	{"file_name": "...", "transcript": "...", "uploaded_at": "...", ...}



---

2️⃣ PHP 기반 관리자 CMS (VOC 관리)

메뉴	기능

📊 대시보드	분석 건수, 문제 유형별 분포 등 시각화
📁 분석 리스트	변환된 음성 텍스트 확인
▶️ 다시듣기	HTML5 <audio>로 mp3 재생
📝 항목 수정	품목명, 수량, 원인, 후속처리 등 수동 수정
🔍 고급 검색	날짜, 키워드, 가맹점 등 필터 검색
📥 엑셀 다운로드	필터링 후 CSV/XLS 내보내기


## 💰 저비용 운영 전략

### ✅ PHP + Google Drive 구조의 장점

| 항목 | 설명 |
|------|------|
| 💸 **저렴한 호스팅 비용** | Cafe24 등 공유호스팅 사용 시 월 500원~2,000원 수준 |
| 🖥 **PHP 기반 CMS 운영** | 별도 서버 없이 파일 업로드, 대시보드 구현 가능 |
| ☁️ **Google Drive 스토리지 사용** | 대용량 mp3 파일도 무료 또는 적은 비용으로 저장 가능 |
| 🔄 **n8n 자동화 연동** | Google Drive 트리거 기반 자동화 가능 (별도 서버 가능 or n8n 클라우드 활용) |
| 🔐 **Google 보안 기반 스토리지** | 접근 권한 제어, OAuth 인증 등 지원 |
| 📈 **확장성 유지** | 필요 시 Google Workspace, Google Cloud Storage 연계 확장 가능 |

---

### ✅ Google Drive로 스토리지 비용 커버

| 비교 항목 | 자체 서버 스토리지 | Google Drive 스토리지 |
|-----------|-------------------|------------------------|
| 저장 용량 | 제한적 (호스팅 한도 있음) | 무료 15GB, 유료 요금제 확장 가능 |
| 확장성 | 하드웨어 증설 필요 | Google One 플랜으로 확장 용이 |
| 안정성 | 트래픽 및 백업 위험 있음 | Google 인프라 기반 안정성 보장 |
| 관리 편의성 | FTP 필요, 관리 복잡 | 웹 업로드/연동 API로 간단 |

> ✅ 예시: Google One 요금제  
> - 무료 15GB  
> - 100GB: 월 2,400원  
> - 200GB: 월 3,700원 (가족 공유 가능)

📌 **mp3 파일의 스토리지 압박을 Google Drive가 해결**하고,  
📌 **PHP + Cafe24 조합으로 초기 인프라 비용 최소화**가 가능합니다.

---

## 🧠 n8n AI 워크플로우 서버 운영 전략

### ✅ 공통 AI 워크플로우 서버 운영 (서버 호스팅 기반)

n8n을 프로젝트마다 나눠서 운영하는 대신, **공통 AI 워크플로우 서버**로 통합하면 다음과 같은 장점이 있습니다:

| 항목 | 설명 |
|------|------|
| 💡 **중앙 집중형 관리** | 하나의 서버에서 모든 워크플로우 관리 가능 (Whisper, GPT, Google API 등) |
| 🔁 **재사용 가능한 플로우** | 다양한 프로젝트에서 공통 STT, 요약, 분류 워크플로우 공유 가능 |
| 📡 **웹훅 호출로 연동 가능** | PHP, 노코드, 외부 시스템에서 `HTTP Request`만으로 쉽게 호출 |
| 💾 **서버 리소스 최적화** | Whisper, GPT 등 무거운 작업을 한 곳에서 처리해 비용 절감 |
| 🔐 **보안 관리 용이** | API Key, OAuth2 등 민감 정보 한 서버에서 안전하게 관리 |
| 📦 **버전 관리 및 백업 간편** | n8n 자체 백업 + Git 연동 가능 |

---

### ☁️ 예시 구성

```plaintext
[PHP CMS] or [Google Drive Trigger]
        ↓ (Webhook 호출)
[공통 n8n AI 워크플로우 서버]
        ↓
[STT 변환 → GPT 요약/분류 → PHP API로 전송]


---

3️⃣ MariaDB 테이블 구조

CREATE TABLE voc_records (
  id INT AUTO_INCREMENT PRIMARY KEY,
  file_name VARCHAR(255),
  transcript TEXT,
  uploaded_at DATETIME,
  parsed_data JSON,
  audio_url VARCHAR(500),
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

parsed_data: 품목, 수량, 접수유형, 세부사유 등 항목별 정보(JSON)



---

📤 엑셀 다운로드 기능 (PHP)

use PhpOffice\\PhpSpreadsheet\\Spreadsheet;
use PhpOffice\\PhpSpreadsheet\\Writer\\Xlsx;

$spreadsheet = new Spreadsheet();
$sheet = $spreadsheet->getActiveSheet();
// 데이터 입력...
$writer = new Xlsx($spreadsheet);
$writer->save('voc_export.xlsx');


---

🔐 보안 요소

Google OAuth2 인증 (Drive/Sheets 연동)

OpenAI API Key 보안 설정

PHP API에 Token 기반 인증 적용

음성 파일 저장소 보호 (GDrive/S3 등)



---

🌱 확장 가능성

항목	설명

🔍 감정 분석	고객 불만/부정 감정 자동 탐지
📈 통계 대시보드	문제 유형별 발생 건수 시각화
🔔 알림 연동	Slack/메일/Kakao 알림 전송
📦 CRM 연동	고객별 VOC 이력 통합 관리



---

✅ 기술 요약

항목	도구/기술

업로드 감지	Google Drive + n8n Trigger
음성 인식	OpenAI Whisper API
데이터 저장	MariaDB
관리자 페이지	PHP CMS + Bootstrap
자동화 처리	n8n 워크플로우
다운로드	PhpSpreadsheet 라이브러리



---

📌 Google Drive 사용 이유

항목	설명

🔁 자동화 연동 용이	n8n, Zapier 등 자동화 툴과 쉽게 연동 가능
📂 공유 및 접근 관리	조직 내 Google 계정 기반 폴더 권한 설정
☁️ 클라우드 저장소	별도 서버 없이 mp3 저장 가능
🖥 PC 동기화 지원	로컬 폴더에 저장 시 자동 업로드
🔐 보안성	Google 보안 체계 기반, 접근 제어 가능



---

📌 Google Sheets 사용 이유

항목	설명

📊 구조화 저장	텍스트 결과를 행/열 기반으로 저장
🔍 검색 및 필터링	관리자 편의성 우수
🤝 실시간 협업	동시에 열람/편집 가능
📤 엑셀 내보내기	보고용 자료 손쉽게 추출
🔄 자동화 가능	n8n에서 행 추가/수정 
