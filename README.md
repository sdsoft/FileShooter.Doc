# FileShooter3 API

**SetSize** `SetSize(int width, int height)`

메인화면의 창 크기를 변경합니다. `width`와 `height`의 단위는 `px`입니다.
```javascript
shooter.SetSize(800, 600);
```
---

**GetSize** `GetSize(): int[]`

메인화면의 창 크기를 배열로 반환합니다.
```javascript
shooter.GetSize();
=> [800, 600]
```
---

**SetTopMost** `SetTopMost(bool value)`


메인화면의 항상위 세팅을 변경합니다.
```javascript
shooter.SetTopMost(true);   // 항상위
shooter.SetTopMost(false);  // 보통
```
---

**GetTopMost** `GetTopMost(): bool`

메인화면의 항상위 세팅을 반환합니다.
```javascript
shooter.GetTopMost();
=> false
```
---
**GetAppTypes** `GetAppTypes(): string(json)`

슈터에서 점검기능을 지원하는 프로그램 목록을 JSON 형식 문자열로 반환합니다. `JSON.parse`를 활용하여 사용하면 됩니다.
```javascript
JSON.parse(shooter.GetAppTypes());
=> [
     {"appType":"corelDRAW","version":9,"name":"CorelDRAW 9","progId":"CorelDRAW.Application.9","compatibility":"x86"},
     {"appType":"corelDRAW","version":10,"name":"CorelDRAW 10","progId":"CorelDRAW.Application.10","compatibility":"x86"},
     {"appType":"corelDRAW","version":11,"name":"CorelDRAW 11","progId":"CorelDRAW.Application.11","compatibility":"x86"},
     ...
   ]
```
---

**GetLastApp** `GetLastApp(): string`

슈터에서 마지막으로 점검할 때 사용한 프로그램의 이름을 반환합니다. 처음 사용시에는 빈문자열을 반환합니다.
```javascript
shooter.GetLastApp();
=> "CorelDRAW 15"
```
---

**GetAutomationConfig** `GetAutomationConfig(): string(json)`

슈터의 현재 상세점검 설정을 JSON형식 문자열로 반환합니다.
```javascript
JSON.parse(shooter.GetAutomationConfig());
=> {
     "corel-object-lock":"true",
     "corel-object-ole":"true",
     ...,
     "illust-bitmap-resolution-min":"290",
     "illust-bitmap-link":"true",
     ...
   }
```
---

**SetAutomationConfig** `SetAutomationConfig(string configJson)`

슈터의 상세점검 설정을 업데이트 합니다. configJson은 JSON형식 문자열입니다.
```javascript
// 설정을 가져온다.
var config = JSON.parse(shooter.GetAutomationConfig());
// 코렐에서 점검시 비트맵 최저해상도를 250dpi로 변경
config['corel-bitmap-resolution-min'] = 250;
// 슈터에 적용
shooter.SetAutomationConfig(JSON.stringify(config));
```
---

**GetProgressItems** `GetProgressItems(bool details, bool save): string`

슈터에서 점검할 작업목록을 JSON 형식 문자열로 반환합니다. `details`는 상세점검이 포함될지를 나타내고, `save`는 원본파일 및 PDF파일을 저장할지를 나타냅니다.
```javascript
JSON.parse(shooter.GetProgressItems(true, true));
=> [
     {"key":"CheckAppStart","value":"애플리케이션을 연결합니다."},
     {"key":"CheckDocument","value":"문서를 확인합니다."},
     {"key":"CheckSideAndCount","value":"도수 및 건수를 확인합니다."},
     ...
   ]
```
---

**FileCheck** `FileCheck(string appName, int width, int height, int side, int count, bool details, bool save, bool resumeOnError, string callbackName)`

파일점검을 시작합니다. `resumeOnError`는 점검도중 오류가 발생해도 중간에 멈추지 않고 점검을 끝까지 진행하는지 세팅할 수 있습니다. `callbackName`은 점검 진행상황을 알려주는 콜백함수의 이름입니다. 같은 이름으로 함수를 정의하면 점검 진행상황을 확인할 수 있습니다.
```javascript
shooter.FileCkeck('CorelDRAW 14', 92, 52, 1, 1, true, true, true, '_filecheck_callback_';
window._filecheck_callback_ = function(result) {
  // result.context: ['CheckAppStart', 'CheckDocument', ...] 
  // 어떤 항목을 점검하고 있는지 (GetProgressItems 에서 반환된 값들의 키)
  //
  // result.status: ['Start', 'Progress', 'Completed', 'Warning', 'Fail']
  // 현재 context의 세부상황
  //
  // result.current, result.total: status가 'Progress'일때의 세부진행상황
  // result.errorCode: status가 'Warning'이나 'Fail'일때 에러코드
  // result.errorMessage: status가 'Warning'이나 'Fail'일때 에러메세지
  // result.shapeIndex: status가 'Warning'이나 'Fail'일때 문제객체의 id
  //
  // 
};
```
---

**FileUpload** `FileUpload(string pdfFilename, string originFilename, string callbackName)`

파일을 업로드 합니다. 점검이 끝나고 저장된 `pdfFilename`과 `originFilename`은 각각 `shooter.GetPdfFileName`과 `shooter.GetOriginFileName` 함수로 얻을 수 있습니다. `callbackName` 은 업로드 진행상황을 알려주는 콜백함수의 이름입니다. 같은 이름으로 함수를 정의하면 업로드 진행상황을 확인할 수 있습니다.
```javascript
var pdfFilename = shooter.GetPdfFileName();
var originFilename = shooter.GetOriginFileName();
shooter.FileUpload(pdfFileName, originFileName, '_fileupload_callback_');
window._fileupload_callback_ = function(result) {
  // result.status: ['progress', 'completed', 'error'] 업로드상
  // result.key: status가 'progress'일때 파일이름 (예: 2f038ba3-f4f1-44a0-a319-d2f841508fdf_pdf)
  // result.progress: status가 'progress'일때 해당 파일의 업로드 진행 퍼센트 1~100
  // result.message: status가 'error'일때 에러메세지
  // result.result: status가 'completed'일때 서버에서 리턴한 값
};
```
---

**SelectObject** `SelectObject(int shapeIndex)`

파일점검중 객체에 오류가 있을때 얻은 `shapeIndex`를 사용하여 그래픽 애플리케이션에서 해당 객체만 선택된 상태로 만듭니다.

---

**GetOriginFileName** `GetOriginFileName(): string`

마지막으로 저장된 원본파일이름을 가져올 수 있습니다. `shooter.FileCheck` 사용시 `save` 파라미터가 `true`일때만 사용해야합니다.
```javascript
shooter.GetOriginFileName();
=> '2f038ba3-f4f1-44a0-a319-d2f841508fdf.ai'
```
---
**GetPdfFileName** `GetPdfFileName(): string`

마지막으로 저장된 Pdf파일이름을 가져올 수 있습니다. `shooter.FileCheck` 사용시 `save` 파라미터가 `true`일때만 사용해야합니다.
```javascript
shooter.GetOriginFileName();
=> '2f038ba3-f4f1-44a0-a319-d2f841508fdf.pdf'
```
---

**Cancel** `Cancel()`

파일점검도중 호출하면 파일점검이 중지됩니다.

---
