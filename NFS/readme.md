### 250718
# NFS (network file system) 세팅 방법
### 스토리지 서버가 있다면 해당 스토리지를 쓰면 되고, 나는 A 라는 서버에 있는 디스크를 다른 서버에 mount 해서 쓰려고 한다.
### 그리고 /data 라는 곳이 할당되어 있다고 하더라도 /data/nfs와 같이 하위 경로로 mount를 할 수 있다.
```
mkdir /data/nfs
```
### <br/>

### 디스크 쿼터 설정
### /etc/fstab를 열어서 다음과 같이 usrquota,grpquota를 추가하자.
```
/dev/sdb1  /data  ext4  defaults,usrquota,grpquota  0  2
```

| 옵션         | 설명                           |
| ---------- | ---------------------------- |
| `defaults` | 일반적인 기본 옵션 (rw, suid, dev 등) |
| `usrquota` | 사용자 단위 디스크 사용 제한 기능 사용       |
| `grpquota` | 그룹 단위 디스크 사용 제한 기능 사용        |

#### <br/>

| 항목         | 예시 값 | 의미                                   |
| ---------- | ---- | ------------------------------------ |
| **6번째 필드** | `0`  | **`dump` 백업 여부** (보통 0으로 둠)          |
| **7번째 필드** | `2`  | **부팅 시 `fsck` 검사 순서** (파일 시스템 검사 순서) |

#### <br/>

### 계정 당 quota를 할당할 수도 있는데 그럴려면 setquota를 이용해야 한다. 
#### * NFS 구성에 setquota는 필수가 아니다.
### 아래와 같이 하면 무제한이다.
```
sudo apt update
sudo apt install quota -y
sudo setquota -u jhshin 0 0 0 0 /data
```
### <br/>

### NFS 설정
### 아래 파일을 열어서 
```
sudo vi /etc/exports
```
### 다음과 같이 추가한다.
```
/data/nfs  *(rw,sync,no_subtree_check,no_root_squash)
```
### <br/>

### 그리고 nfs-kernel-server로 적용하면 끝이다. 이제 NFS 설정은 끝났고, 각 클라이언트 서버에서 mount만 하면 된다.
```
sudo apt install -y nfs-kernel-server
sudo exportfs -a
sudo systemctl restart nfs-kernel-server
```
### <br/>

### client server에서 NFS mount 하기
```
sudo apt update
sudo apt install -y nfs-common
sudo mount -t nfs [IP]:/data/nfs /data/nfs
```
### <br/>

### df로 확인해보자.
```
df -h
```
#### <img width="525" height="19" alt="image" src="https://github.com/user-attachments/assets/31b0dd73-6837-4525-a579-3c7859e99653" />
