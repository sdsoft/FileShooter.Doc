# FileShooter3 API

**SetSize** `SetSize(int width, int height)`

메인화면의 창 크기를 변경합니다. `width`와 `height`의 단위는 `px`입니다.

    shooter.SetSize(800, 600);

---

**GetSize** `GetSize(): int[]`

메인화면의 창 크기를 배열로 반환합니다.

    shooter.GetSize();
    => [800, 600]

---

**SetTopMost** `SetTopMost(bool value)`

메인화면의 항상위 세팅을 변경합니다.

    shooter.SetTopMost(true);   // 항상위
    shooter.SetTopMost(false);  // 보통

---

**GetTopMost** `GetTopMost(): bool`

메인화면의 항상위 세팅을 반환합니다.

    shooter.GetTopMost();
    => false

---
**GetAppTypes** `GetAppTypes(): string(json)`

슈터에서 점검기능을 지원하는 프로그램 목록을 JSON 형식 문자열로 불러옵니다. `JSON.stringify`를 활용하여 사용하면 됩니다.

    JSON.stringify(shooter.GetAppTypes());
    => [{"appType":"corelDRAW","version":9,"name":"CorelDRAW 9","progId":"CorelDRAW.Application.9","compatibility":"x86"},
        {"appType":"corelDRAW","version":10,"name":"CorelDRAW 10","progId":"CorelDRAW.Application.10","compatibility":"x86"},
        {"appType":"corelDRAW","version":11,"name":"CorelDRAW 11","progId":"CorelDRAW.Application.11","compatibility":"x86"},
        ...]

---

**GetLastApp** `GetLastApp(): string`

슈터에서 마지막에 점검할 때 사용한 프로그램의 이름을 반환합니다. 처음 사용시에는 빈문자열을 반환합니다.

    shooter.GetLastApp();
    => "CorelDRAW 15"

---

