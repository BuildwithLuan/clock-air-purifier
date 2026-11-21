# Clock × 공기청정기

집에서 쓰는 시계 화면에 샤오미 Mi Air Purifier 3/3H의 상태 조회, 전원, 운전 모드 조작을 붙였습니다. 이 저장소에는 개념도와 실제 코드의 일부만 공개합니다. 실행 가능한 전체 프로젝트나 기기 연결 키트는 아닙니다.

[설명 글: 집 안의 시계에서 공기청정기를 켜는 법](https://luans47.wordpress.com/2026/11/21/clock-xiaomi-air-purifier/)

## 연결 구조

```text
최초 등록: Mi Home → 공기청정기 등록 → 기기 인증 정보는 집 서버에만 보관
평소 조작: Clock 집 아이콘 → 같은 출처의 API → 내부망 nginx
          → Python 제어 서비스 → 샤오미 어댑터 → 공기청정기 (로컬 miIO)
```

## 실제 코드 발췌

### 브라우저: 요청 후 화면 갱신

```js
await api(`devices/${encodeURIComponent(id)}/${action}`, {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ value }),
});
await refresh(false, true);
```

### 어댑터: 기기 속성으로 매핑

```python
siid, piid = self.MIOT[action]
caster = self.WRITABLE[action]
return self.device.set_property(siid, piid, caster(value))
```

Mi Air Purifier 3/3H의 이 구현에서 전원은 `(2, 2)`, 운전 모드는 `(2, 5)`입니다. 평소 조작 요청은 Mi Home 클라우드를 거치지 않습니다.

기기 토큰, 실제 주소, 서버 설정, 개인 데이터는 포함하지 않았습니다. 내부망 제한은 사용자별 인증을 대신하지 않고, 코드 발췌의 형변환은 입력 검증이 아닙니다. 이 코드를 그대로 인터넷에 노출하지 마세요.
