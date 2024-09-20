
# 🔐 Linux PAM을 이용한 비밀번호 규칙 설정

## 📌 목차
1. [새로운 VM 생성](#1-새로운-vm-생성)
2. [IP 주소 설정](#2-ip-주소-설정)
3. [PAM 설정](#3-pam-설정)
4. [결과 확인](#4-결과-확인)

---

## 1. 새로운 VM 생성 🖥️

기존 `myserver01`을 복제하여 새로운 `myserver03` VM을 생성합니다.

![VM 생성](https://github.com/user-attachments/assets/614c8f3c-4a60-4a51-9805-7b081fc0e0ef)

---

## 2. IP 주소 설정 🌐

새로 생성한 VM의 IP 주소를 다음과 같이 변경합니다:
- **기존 IP주소**: `10.0.2.15`
- **새로운 IP주소**: `10.0.2.21`

### 2.1 Netplan 설정 파일 수정

```bash
sudo vi /etc/netplan/00-installer-config.yaml
```

![Netplan 설정](https://github.com/user-attachments/assets/d217953f-4fa0-46fa-a498-73b32d2439ed)

### 2.2 포트 설정

`myserver03`에 대한 포트 설정:

![YAML 설정](https://github.com/user-attachments/assets/0b8aa7c0-7fcc-4234-b07b-fa11e9a99692)

### 2.3 IP 설정 완료

![포트 설정](https://github.com/user-attachments/assets/9f03d6fa-ece8-40c0-9a7a-7d4624042f82)

---

## 3. PAM 설정 🛠️

### 3.1 PAM 소개

**PAM(Pluggable Authentication Module)**은 사용자를 인증하고 서비스에 대한 액세스를 제어하는 모듈화된 방식입니다. 관리자는 PAM을 사용해 응용프로그램의 인증 방법을 선택할 수 있으며, 재컴파일 없이도 인증 방법을 쉽게 변경할 수 있습니다.

- PAM의 주요 설정 파일 위치:
  - 설정 파일: `/etc/pam.d` 또는 `/etc/pam.conf`
  - 모듈 파일: `/lib/security` 또는 `/usr/lib/security`

### 3.2 PAM 설치

```bash
sudo apt install libpam0g-dev
sudo apt install libpam-pwquality
```

![PAM 설치 1](https://github.com/user-attachments/assets/5a494d9d-7e48-4456-bb7d-e815f449ae26)  
![PAM 설치 2](https://github.com/user-attachments/assets/e8484c60-978c-4a17-9eb4-472dec4c946b)

### 3.3 `pwquality.conf` 설정

`/etc/security/pwquality.conf` 파일을 열어 아래 설정을 추가합니다:

```bash
minlen = 8
dcredit = -1
ucredit = -1
lcredit = -1
ocredit = 0
```

![pwquality.conf 설정](https://github.com/user-attachments/assets/82e93c99-c9e8-40e8-88e2-5b3f7135a6e1)

#### 각 설정의 의미:
- **`minlen = 8`**: 비밀번호 최소 길이를 8자리로 설정.
- **`dcredit = -1`**: 최소 1개의 숫자가 포함되어야 함.
- **`ucredit = -1`**: 최소 1개의 대문자가 포함되어야 함.
- **`lcredit = -1`**: 최소 1개의 소문자가 포함되어야 함.
- **`ocredit = 0`**: 특수 문자는 필수가 아님.

### 3.4 `common-password` 파일 설정

`/etc/pam.d/common-password` 파일을 수정하여 아래 내용을 추가합니다:

```bash
password requisite pam_pwquality.so retry=3 minlen=8
```

![common-password 설정](https://github.com/user-attachments/assets/11c29ade-cb48-4bee-b3bc-0ea08d63192a)

#### 각 항목의 의미:
1. **`password`**: 비밀번호 관련 인증 과정을 관리.
2. **`requisite`**: 이 모듈이 실패하면 PAM 스택에서 즉시 실패 처리.
3. **`pam_pwquality.so`**: 비밀번호 품질(길이, 복잡성 등)을 검사.
4. **`retry=3`**: 비밀번호 입력 시 3번까지 시도 가능.
5. **`minlen=8`**: 비밀번호는 최소 8자리를 요구.

---

## 4. 결과 확인 ✅

비밀번호 설정을 시도할때, 8자 미만의 비밀번호를 설정하려고 하면 시스템에서 거부

![결과 확인](https://github.com/user-attachments/assets/27cf3bc7-06cf-45e2-b8f6-22d02c3b794b)

---

🎉PAM을 사용한 비밀번호 규칙 설정을 통해 시스템 보안을 강화할 수 있습니다!
