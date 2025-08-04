# 하나로 풀스택 배포 프로젝트 - Backend

## 기술 스택

- Spring Boot (Java 21)
- Gradle
- AWS EC2 (Ubuntu)
- GitHub Actions (CI/CD)

---

## ☁️ 배포 인프라 개요

### 1. AWS EC2란?

EC2(Elastic Compute Cloud)는 AWS에서 제공하는 **가상 서버 서비스**
EC2 위에 Java 백엔드 애플리케이션을 실행하여 API 서버를 구성함

### 2. 왜 EC2를 사용했나?

- Spring Boot 백엔드는 정적 파일이 아닌 **서버 프로세스**가 필요
- EC2는 SSH로 직접 접속하여 `.jar` 실행, 로그 확인, 수동 제어가 가능
- 비용이 저렴하고, 퍼블릭 IP/도메인을 부여해 외부 접속 가능
- GitHub Actions를 통해 `.jar` 파일을 자동 배포할 수 있음

---

## 🚀 배포 흐름 요약

1. GitHub Actions에서 `./gradlew build -x test`로 빌드
2. 빌드된 `.jar` 파일을 EC2로 전송
3. EC2에서 기존 프로세스를 종료 후 새 버전 실행 (`nohup java -jar`)
4. 백엔드 API가 `http://<EC2_IP>:8080` 에서 구동됨

---

## 🔐 GitHub Secrets

| 이름           | 설명                             |
|----------------|----------------------------------|
| `EC2_HOST`     | EC2 퍼블릭 DNS 또는 IP           |
| `EC2_USER`     | EC2 사용자 (`ubuntu` 등)          |
| `EC2_SSH_KEY`  | `.pem` 키 파일의 전체 내용       |

---

## 🔄 GitHub Actions 구성

- 위치: `.github/workflows/back-deploy.yml`
- 주요 Step:
  - Gradle 빌드
  - `.jar` 파일 EC2에 업로드
  - 기존 서버 종료 & 새 서버 실행 (`nohup`, `lsof`, `kill` 등 사용)

---

## 💡 참고 사항

- EC2 보안 그룹에서 8080 포트가 열려 있어야 외부 접근 가능
- 로그는 EC2에서 `tail -f app.log` 로 확인 가능
- CORS 설정이 잘못되면 프론트에서 API 요청이 막힐 수 있음

---

## 배포 결과
![Image](https://github.com/user-attachments/assets/5deea251-95cc-4f30-9473-a67dc2d511a6)
