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
