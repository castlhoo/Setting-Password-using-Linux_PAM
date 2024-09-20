# 🔐 Linux PAM을 이용한 비밀번호 규칙 설정 가이드

## 📌 목차
1. [새로운 VM 생성](#1-새로운-vm-생성)
2. [IP 주소 설정](#2-ip-주소-설정)
3. [PAM 설정](#3-pam-설정)
4. [결과 확인](#4-결과-확인)

## 1. 새로운 VM 생성 🖥️

기존 myserver01을 이용하여 복제 후 myserver03 생성

![VM 생성](https://github.com/user-attachments/assets/614c8f3c-4a60-4a51-9805-7b081fc0e0ef)

## 2. IP 주소 설정 🌐

새롭게 생성한 VM의 IP주소를 변경:
- 기존 IP주소: 10.0.2.15
- 새로운 IP주소: 10.0.2.21

### 2.1 Netplan 설정 파일 수정

```bash
sudo vi /etc/netplan/00-installer-config.yaml
```

![Netplan 설정](https://github.com/user-attachments/assets/d217953f-4fa0-46fa-a498-73b32d2439ed)

### 2.2 포트 설정

myserver03에 대한 포트 설정:

![YAML 설정](https://github.com/user-attachments/assets/0b8aa7c0-7fcc-4234-b07b-fa11e9a99692)

### 2.3 IP 설정 완료
![포트 설정](https://github.com/user-attachments/assets/9f03d6fa-ece8-40c0-9a7a-7d4624042f82)

## 3. PAM 설정 🛠️

### 3.1 PAM 소개

PAM(Pluggable Authentication Module: 착탈형 인증 모듈)은 사용자를 인증하고 서비스에 대한 액세스를 제어하는 모듈화된 방법 

- 관리자가 응용프로그램의 사용자 인증 방법을 선택 가능
- 재컴파일 없이 인증 방법 변경 가능

#### PAM의 목적과 동작
- 권한 부여 소프트웨어와 인증 개발의 분리
- `/etc/pam.d` 또는 `/etc/pam.conf`에서 시스템 설정
- 모듈은 `/lib/security` 또는 `/usr/lib/security`에 위치

### 3.2 PAM 설치

```bash
sudo apt install libpam0g-dev
sudo apt install libpam-pwequality
```

![PAM 설치 1](https://github.com/user-attachments/assets/5a494d9d-7e48-4456-bb7d-e815f449ae26)
![PAM 설치 2](https://github.com/user-attachments/assets/e8484c60-978c-4a17-9eb4-472dec4c946b)

### 3.3 pwquality.conf 설정

`/etc/security/pwquality.conf` 파일 수정:

![pwquality.conf 설정](https://github.com/user-attachments/assets/82e93c99-c9e8-40e8-88e2-5b3f7135a6e1)

설정 내용: `minlen = 8` (비밀번호 최소 8자 설정)
minlen = 8:

최소 비밀번호 길이를 8자리로 설정한 것으로, 
이는 비밀번호가 최소 8자 이상이어야 함을 의미
dcredit = -1:

비밀번호에 포함되어야 하는 최소 숫자(digit) 개수를 정의
dcredit = -1은 최소 하나의 숫자가 비밀번호에 포함되어야 하는 의미로, 
숫자를 최소 1개 이상 포함해야 한다는 규칙
ucredit = -1:

비밀번호에 포함되어야 하는 최소 대문자(uppercase character) 개수를 정의
ucredit = -1은 최소 하나의 대문자가 비밀번호에 포함되어야 함을 의미
lcredit = -1:

비밀번호에 포함되어야 하는 최소 소문자(lowercase character) 개수를 정의
lcredit = -1은 최소 하나의 소문자가 비밀번호에 포함되어야 함을 의미
ocredit = 0:

비밀번호에 포함되어야 하는 특수 문자(other character) 개수를 정의
ocredit = 0은 특수 문자가 포함되지 않아도 된다는 의미

### 3.4 common-password 파일 설정

`/etc/pam.d/common-password` 파일 수정:

![common-password 설정](https://github.com/user-attachments/assets/11c29ade-cb48-4bee-b3bc-0ea08d63192a)

수정 내용: `password requisite pam_pwquality.so retry=3 minlen=8`
1. password:
**PAM 모듈의 "타입"**을 지정합니다. password는 이 줄이 비밀번호 변경이나 설정과 관련된 인증 과정을 관리한다는 것을 의미

2. requisite:
이 모듈에서 비밀번호가 정책을 따르지 않으면 PAM 스택은 즉시 실패하고, 이후의 모듈은 실행되지 않음을 의미

4. pam_pwquality.so:
PAM 모듈 중 하나인 pam_pwquality 모듈을 사용, 이 모듈은 비밀번호의 품질(복잡성, 길이 등)을 검사하는 역할
이 모듈은 비밀번호의 길이, 숫자, 대문자/소문자, 특수 문자 등을 요구하여 보안 수준을 높이는 데 사용
.
6. retry=3:
사용자가 비밀번호를 설정할 때 최대 3번까지 시도할 수 있도록 허용하는 옵션

8. minlen=8:
비밀번호의 최소 길이를 지정, 최소 8자리의 비밀번호가 요구됨을 의미
minlen=8은 사용자가 입력하는 비밀번호가 최소 8자 이상이어야만 허용

## 4. 결과 확인 ✅

비밀번호 설정 시도:

![결과 확인](https://github.com/user-attachments/assets/27cf3bc7-06cf-45e2-b8f6-22d02c3b794b)

📢 8자 미만으로 비밀번호를 설정하면 거부됩니다.

---

🎉 이제 Linux PAM을 이용한 기본적인 비밀번호 정책 설정으로 시스템의 보안이 한층 강화되었습니다.
