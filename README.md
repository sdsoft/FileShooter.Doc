# FileShooter3 API

**setSize** `setSize(int width, int height)`

메인화면의 창 크기를 변경합니다. `width`와 `height`의 단위는 `px`입니다.
```javascript
shooter.setSize(800, 600);
```
---

**getSize** `getSize(): int[]`

메인화면의 창 크기를 배열로 반환합니다.
```javascript
shooter.getSize();
=> [800, 600]
```
---

**setTopMost** `setTopMost(bool value)`


메인화면의 항상위 세팅을 변경합니다.
```javascript
shooter.setTopMost(true);   // 항상위
shooter.setTopMost(false);  // 보통
```
---

**getTopMost** `getTopMost(): bool`

메인화면의 항상위 세팅을 반환합니다.
```javascript
shooter.getTopMost();
=> false
```
---
**getAppTypes** `getAppTypes(): string(json)`

슈터에서 점검기능을 지원하는 프로그램 목록을 JSON 형식 문자열로 반환합니다. `JSON.parse`를 활용하여 사용하면 됩니다.
```javascript
JSON.parse(shooter.getAppTypes());
=> [
     {"appType":"corelDRAW","version":9,"name":"CorelDRAW 9","progId":"CorelDRAW.Application.9","compatibility":"x86"},
     {"appType":"corelDRAW","version":10,"name":"CorelDRAW 10","progId":"CorelDRAW.Application.10","compatibility":"x86"},
     {"appType":"corelDRAW","version":11,"name":"CorelDRAW 11","progId":"CorelDRAW.Application.11","compatibility":"x86"},
     ...
   ]
```
---

**getLastApp** `getLastApp(): string`

슈터에서 마지막으로 점검할 때 사용한 프로그램의 이름을 반환합니다. 처음 사용시에는 빈문자열을 반환합니다.
```javascript
shooter.getLastApp();
=> "CorelDRAW 15"
```
---

**getAutomationConfig** `getAutomationConfig(): string(json)`

슈터의 현재 상세점검 설정을 JSON형식 문자열로 반환합니다.
```javascript
JSON.parse(shooter.getAutomationConfig());
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

**setAutomationConfig** `setAutomationConfig(string configJson)`

슈터의 상세점검 설정을 업데이트 합니다. configJson은 JSON형식 문자열입니다.
```javascript
// 설정을 가져온다.
var config = JSON.parse(shooter.getAutomationConfig());
// 코렐에서 점검시 비트맵 최저해상도를 250dpi로 변경
config['corel-bitmap-resolution-min'] = 250;
// 슈터에 적용
shooter.setAutomationConfig(JSON.stringify(config));
```
---

**getProgressItems** `getProgressItems(bool details, bool save): string`

슈터에서 점검할 작업목록을 JSON 형식 문자열로 반환합니다. `details`는 상세점검이 포함될지를 나타내고, `save`는 원본파일 및 PDF파일을 저장할지를 나타냅니다.
```javascript
JSON.parse(shooter.getProgressItems(true, true));
=> [
     {"key":"CheckAppStart","value":"애플리케이션을 연결합니다."},
     {"key":"CheckDocument","value":"문서를 확인합니다."},
     {"key":"CheckSideAndCount","value":"도수 및 건수를 확인합니다."},
     ...
   ]
```
---

**fileCheck** `fileCheck(string appName, int width, int height, int side, int count, bool details, bool save, bool resumeOnError, string callbackName)`

파일점검을 시작합니다. `resumeOnError`는 점검도중 오류가 발생해도 중간에 멈추지 않고 점검을 끝까지 진행하는지 세팅할 수 있습니다. `callbackName`은 점검 진행상황을 알려주는 콜백함수의 이름입니다. 같은 이름으로 함수를 정의하면 점검 진행상황을 확인할 수 있습니다.
```javascript
shooter.fileCkeck('CorelDRAW 14', 92, 52, 1, 1, true, true, true, '_filecheck_callback_';
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

**fileUpload** `fileUpload(string pdfFilename, string originFilename, string callbackName)`

파일을 업로드 합니다. 점검이 끝나고 저장된 `pdfFilename`과 `originFilename`은 각각 `shooter.getPdfFileName`과 `shooter.getOriginFileName` 함수로 얻을 수 있습니다. `callbackName` 은 업로드 진행상황을 알려주는 콜백함수의 이름입니다. 같은 이름으로 함수를 정의하면 업로드 진행상황을 확인할 수 있습니다.
```javascript
var pdfFilename = shooter.getPdfFileName();
var originFilename = shooter.getOriginFileName();
shooter.fileUpload(pdfFileName, originFileName, '_fileupload_callback_');
window._fileupload_callback_ = function(result) {
  // result.status: ['progress', 'completed', 'error'] 업로드상
  // result.key: status가 'progress'일때 파일이름 (예: 2f038ba3-f4f1-44a0-a319-d2f841508fdf_pdf)
  // result.progress: status가 'progress'일때 해당 파일의 업로드 진행 퍼센트 1~100
  // result.message: status가 'error'일때 에러메세지
  // result.result: status가 'completed'일때 서버에서 리턴한 값
};
```
---

**selectObject** `selectObject(int shapeIndex)`

파일점검중 객체에 오류가 있을때 얻은 `shapeIndex`를 사용하여 그래픽 애플리케이션에서 해당 객체만 선택된 상태로 만듭니다.

---

**selectAllObject** `selectAllObject(string shapeIndexes)`

파일점검중 객체에 오류가 있을때 얻은 `shapeIndex`들의 배열을 사용하여 그래픽 애플리케이션에서 넘겨받은 객체들을 모두 선택된 상태로 만듭니다. `shapeIndexes` 배열은 `JSON.stringify` 함수를 이용하여 `string`으로 넘겨야합니다.
```javascript
var shapeIndexes = [0,1,3,6];
shooter.selectAllObject(JSON.stringify(shapeIndexes));
```
---

**getOriginFileName** `getOriginFileName(): string`

마지막으로 저장된 원본파일이름을 가져올 수 있습니다. `shooter.fileCheck` 사용시 `save` 파라미터가 `true`일때만 사용해야합니다.
```javascript
shooter.getOriginFileName();
=> '2f038ba3-f4f1-44a0-a319-d2f841508fdf.ai'
```
---
**getPdfFileName** `getPdfFileName(): string`

마지막으로 저장된 Pdf파일이름을 가져올 수 있습니다. `shooter.fileCheck` 사용시 `save` 파라미터가 `true`일때만 사용해야합니다.
```javascript
shooter.getPdfFileName();
=> '2f038ba3-f4f1-44a0-a319-d2f841508fdf.pdf'
```
---

**cancel** `cancel()`

파일점검도중 호출하면 파일점검이 중지됩니다.

---

**isRunningInUSB** `isRunningInUSB(): bool`

프로그램이 USB드라이브상에서 실행중인지를 반환합니다.
```javascript
shooter.isRunningInUSB();
=> false
```
---

**login** `login(string userid)`

로그인했을 때 호출해주세요.
```javascript
shooter.login(member_id);
```
---

**transaction** `transaction(string transactionJson)`

주문이 완료되었을 때 호출해주세요.`transactionJson`은 JSON형식 문자열입니다.

```javascript
var transaction_obj = {
    transactionId: "pt1402080624",		// 주문번호
    revenue: 30000,						// 주문총액
    affiliation: "printcity",			// 가맹점 (선택)
    shipping: 2500,						// 배송비 (선택)
    tax: 3000,							// 세금 (선택)
    currencyCode: "krw",				// 통화
    items: [
    	{
            sku: "pdk01",					// 제품코드
            name: "일반명함",				// 제품이름
            price: 5000,					// 제품가격
            quantity: 1,					// 건수
            category: "200SW / 4도 / 92*52" // 세부내역(선택)
        },
    	{
            sku: "pdk03",					// 제품코드
            name: "수입지명함",				// 제품이름
            price: 5000,					// 제품가격
            quantity: 5,					// 건수
            category: "누브지 / 8도 / 92*52" // 세부내역(선택)
        }
    ]
};
shooter.transaction(JSON.stringify(transaction_obj));
```

