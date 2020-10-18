# checkLocationServicesStatus() 메소드


## description 

- LocationManager 의 GPS_PRIVIDER 또는 NETWORK_PROVIDER 가 동작 중인지 확인하여 결과 값을 리턴한다.

- LocationManager는 getSystemSerivce()를 이용하여 얻는다.
            
## parameter

없음
    
## return value

- type: boolean

- value: GPS_PRIVIDER 또는 NETWORK_PROVIDER 가 Enable 이면 True, 아니면 False 값.

    
## Dependence function
- getSystemService(): 주어진 파라미터에 대응되는 안드로이드가 제공하는 시스템 서비스를 요청한다. 
  - https://developer.android.com/reference/android/content/Context#getSystemService(java.lang.String)

- locationManager.isProviderEnabled():지정된 Provider의 Enabled/Disabled 상태를 Return합니다. 
  - https://developer.android.com/reference/android/location/LocationManager#isProviderEnabled(java.lang.String)
    
## Source code    
    
    ```
     public boolean checkLocationServicesStatus() {
        LocationManager locationManager = (LocationManager) getSystemService(LOCATION_SERVICE);

        return locationManager.isProviderEnabled(LocationManager.GPS_PROVIDER)
                || locationManager.isProviderEnabled(LocationManager.NETWORK_PROVIDER);
    }
    ```
    

# showDialogForLocationServiceSetting메소드
    
## description 

- 사용자에게 대화박스를 띄워줍니다.

- 대화박스의 OK버튼을 누르면 위치 서비스를 사용할 수 있게 됩니다.
            
## parameter

없음
    
## return value   

- type: void

- value:사용자에게 대화박스를 띄워줍니다 
    
## Dependence function

- builder.setTitle: 대화상자의 제목
  - https://developer.android.com/reference/android/app/AlertDialog.Builder#setTitle(java.lang.CharSequence)
  
- builder.setMessage: 대화상자의 메세지
  - https://developer.android.com/reference/android/app/AlertDialog.Builder#setMessage(int)
  
- builder.setCancelable: 사용자가 대화상자를 없앨 수 있는지에 대한 여부 설정
  - https://developer.android.com/reference/android/app/AlertDialog.Builder#setCancelable(boolean)
  
- builder.setPositiveButton: 대화상자의 허용버튼을 눌렀을때 일어나는 함수 onClick메소드를 오버라이드 한다
- builder.setPositiveButton: 대화상자의 취소버튼을 눌렀을때 일어나는 함수 onClick메소드를 오버라이드 한다
  - https://developer.android.com/reference/android/app/AlertDialog.Builder#setPositiveButton(int,%20android.content.DialogInterface.OnClickListener)
  
- startActivityForResult: onActivityResult메소드와 콜백에서 액티비티의 결과를 별도의 Intent객체로 수신하는 함수
  - https://developer.android.com/reference/android/app/Activity?hl=ko#startActivityForResult(android.content.Intent,%20int)
                        
    
## Source code
```
    private void showDialogForLocationServiceSetting() {

    AlertDialog.Builder builder = new AlertDialog.Builder(MainActivity.this);
    builder.setTitle("위치 서비스 비활성화");
    builder.setMessage("앱을 사용하기 위해서는 위치 서비스가 필요합니다.\n"
            + "위치 설정을 수정하실래요?");
    builder.setCancelable(true);
    builder.setPositiveButton("설정", new DialogInterface.OnClickListener() {
        @Override
        public void onClick(DialogInterface dialog, int id) {
            Intent callGPSSettingIntent
                    = new Intent(android.provider.Settings.ACTION_LOCATION_SOURCE_SETTINGS);
            startActivityForResult(callGPSSettingIntent, GPS_ENABLE_REQUEST_CODE);
        }
    });
    builder.setNegativeButton("취소", new DialogInterface.OnClickListener() {
        @Override
        public void onClick(DialogInterface dialog, int id) {
            dialog.cancel();
        }
    });
    builder.create().show();
}
```
# OnActivityResult메소드


## description 

- startActivityForResult메소드의 콜백 메소드
  - https://developer.android.com/reference/android/app/Activity?hl=ko#onActivityResult(int,%20int,%20android.content.Intent)
            
## parameter

- requestCode: 대화상자 액티비티랑 MainActivity를 구별하기 위한 코드

- resultCode: 어떠한 결과코드를 주었는지에 대한 변수

- Intent data: 액티비티에서 보낸 결과 데이터가 들어가있는 부분
  - https://developer.android.com/reference/android/app/Activity?hl=ko#onActivityResult(int,%20int,%20android.content.Intent)
    
## return value

- type: void

- value:없음

    
## Dependence function

- checkRunTimePermission()메소드

## Case

- requestCode가 일치할때
  - GPS_PRIVIDER 또는 NETWORK_PROVIDER 가 Enable 이면 checkRunTimePermission()메소드를 호출한다.
- requestCode가 일치하지 않을때   
  - break;

## Source code

```
 @Override
   protected void onActivityResult(int requestCode, int resultCode, Intent data) {
   super.onActivityResult(requestCode, resultCode, data);

   switch (requestCode) {

       case GPS_ENABLE_REQUEST_CODE:


           if (checkLocationServicesStatus()) {
               if (checkLocationServicesStatus()) {

                   Log.d("@@@", "onActivityResult : GPS 활성화 되있음");
                   checkRunTimePermission();
                   return;
               }
           }

           break;
   }
```
     
# checkRunTimePermission메소드

## description 

- 위치 퍼미션을 가지고 있는지 체크합니다.
            
## parameter

없음
    
## return value

- type: void

- value: 없음
    
## Dependence function

- ContextCompat.CheckSelfPermission
  - 특정권한이 이미 획득됐는지 확인합니다.  
  - 권한이 없으면 shouldShowRequestPermissionRationale을 호출합니다
  - https://developer.android.com/reference/androidx/core/content/ContextCompat?hl=ko#checkSelfPermission(android.content.Context,%20java.lang.String)
  
- ActivityCompat.shouldShowRequestPermissionRationale
  - 권한을 요청하기 전에 근거가있는 UI를 표시해야하는지 여부를 가져옵니다.
  - 사용자가 권한 요청을 한 번 거절하면 ActivityCompat의 shouldShowRequestPermissionRationale 메서드 반환 값이 true가 됩니다.
  - https://developer.android.com/reference/androidx/core/app/ActivityCompat?hl=ko#shouldShowRequestPermissionRationale(android.app.Activity,%20java.lang.String)
  - https://academy.realm.io/kr/posts/android-marshmellow-permission/
  
- ActivityCompat.requestPermissions: 사용자에게 퍼미션을 요청합니다, 이 메소드의 요청 결과는 onRequestPermissionResult에서 콜백됩니다
  - https://developer.android.com/reference/androidx/core/app/ActivityCompat?hl=ko#requestPermissions(android.app.Activity,%20java.lang.String%5B%5D,%20int)
    
## Source code    
    
 ```
    void checkRunTimePermission() {

    //런타임 퍼미션 처리
    // 1. 위치 퍼미션을 가지고 있는지 체크합니다.
    int hasFineLocationPermission = ContextCompat.checkSelfPermission(MainActivity.this,
            Manifest.permission.ACCESS_FINE_LOCATION);
    int hasCoarseLocationPermission = ContextCompat.checkSelfPermission(MainActivity.this,
            Manifest.permission.ACCESS_COARSE_LOCATION);


    if (hasFineLocationPermission == PackageManager.PERMISSION_GRANTED &&
            hasCoarseLocationPermission == PackageManager.PERMISSION_GRANTED) {

        // 2. 이미 퍼미션을 가지고 있다면
        // ( 안드로이드 6.0 이하 버전은 런타임 퍼미션이 필요없기 때문에 이미 허용된 걸로 인식합니다.)


        // 3.  위치 값을 가져올 수 있음


    } else {  //2. 퍼미션 요청을 허용한 적이 없다면 퍼미션 요청이 필요합니다. 2가지 경우(3-1, 4-1)가 있습니다.

        // 3-1. 사용자가 퍼미션 거부를 한 적이 있는 경우에는
        if(ActivityCompat.shouldShowRequestPermissionRationale(MainActivity.this,shouldShowRequestPermissionRationale(MainActivity.this, REQUIRED_PERMISSIONS[0]))) {

            // 3-2. 요청을 진행하기 전에 사용자가에게 퍼미션이 필요한 이유를 설명해줄 필요가 있습니다.
            Toast.makeText(MainActivity.this, "이 앱을 실행하려면 위치 접근 권한이 필요합니다.", Toast.LENGTH_LONG).show();
            // 3-3. 사용자게에 퍼미션 요청을 합니다. 요청 결과는 onRequestPermissionResult에서 수신됩니다.
            ActivityCompat.requestPermissions(MainActivity.this, REQUIRED_PERMISSIONS,
                    PERMISSIONS_REQUEST_CODE);


        } else {
            // 4-1. 사용자가 퍼미션 거부를 한 적이 없는 경우에는 퍼미션 요청을 바로 합니다.
            // 요청 결과는 onRequestPermissionResult에서 수신됩니다.
            ActivityCompat.requestPermissions(MainActivity.this, REQUIRED_PERMISSIONS,
                    PERMISSIONS_REQUEST_CODE);
        }

    }
}

```
# onRequestPermissionResult

## description 

 - requestPermissions메소드의 권한 요청 결과에 대한 콜백메소드
            
## parameter

- int permsRequestCode 
  - 전달된 요청 코드

- @NonNull String[] permissions
  - 요청 된 권한

- @NonNull int[] grandResults
  - 해당 권한에 대한 부여 결과
  - https://developer.android.com/reference/androidx/core/app/ActivityCompat.OnRequestPermissionsResultCallback?hl=ko

## return value 

- type
  - void

- value
  - 없음
    
## Dependence function

- ActivityCompat.shouldShowRequestPermissionRationale
  - 권한을 요청하기 전에 근거가있는 UI를 표시해야하는지 여부를 가져옵니다.
  - 권한 요청을 한 번 거절하면 메서드 반환 값이 true가 됩니다.
  - https://developer.android.com/reference/androidx/core/app/ActivityCompat?hl=ko#shouldShowRequestPermissionRationale(android.app.Activity,%20java.lang.String)
  - https://academy.realm.io/kr/posts/android-marshmellow-permission/
    
## Source code

```
public void onRequestPermissionsResult(int permsRequestCode,
                                    @NonNull String[] permissions,
                                    @NonNull int[] grandResults) {

 if ( permsRequestCode == PERMISSIONS_REQUEST_CODE && grandResults.length == REQUIRED_PERMISSIONS.length) {

      // 요청 코드가 PERMISSIONS_REQUEST_CODE 이고, 요청한 퍼미션 개수만큼 수신되었다면
     boolean check_result = true;


     // 모든 퍼미션을 허용했는지 체크합니다. 
     for (int result : grandResults) {
         if (result != PackageManager.PERMISSION_GRANTED) {
             check_result = false;
             break;
         }
     }


     if ( check_result ) {


     }
     
     else {
 // 거부한 퍼미션이 있다면 앱을 사용할 수 없는 이유를 설명해주고 앱을 종료합니다.2 가지 경우가 있습니다
         if (ActivityCompat.shouldShowRequestPermissionRationale(this, REQUIRED_PERMISSIONS[0])
                 || ActivityCompat.shouldShowRequestPermissionRationale(this, REQUIRED_PERMISSIONS[1])) {

             Toast.makeText(MainActivity.this, "퍼미션이 거부되었습니다. 앱을 다시 실행하여 퍼미션을 허용해주세요.", Toast.LENGTH_LONG).show();
             finish();


         }else {

             Toast.makeText(MainActivity.this, "퍼미션이 거부되었습니다. 설정(앱 정보)에서 퍼미션을 허용해야 합니다. ", Toast.LENGTH_LONG).show();

         }
     }

 }

```
 


# GpsTracker메소드

## description 

 - getLocation메소드 
            
## parameter

- context 
  - 액티비티 정보를 얻어오는 변수
  - https://developer.android.com/reference/android/content/Context
  - https://zxcv5500.tistory.com/258

## return value 

- type
  - void

- value
  - 없음
    
## Dependence function

- getlocation()
    
## Source code

```
public GpsTracker(Context context) {
    this.mContext = context;
    getLocation();
}
```

# getLocation메소드

## description 

- 위도랑 경도를 리턴하는 메소드
            
## parameter

- 없음

## return value

- type
  - Location

- value
  - 받은 제공자를 기준으로 위도랑 경도를 반환합니다.
    
## Dependence function

- getSystemService(): 주어진 파라미터에 대응되는 안드로이드가 제공하는 시스템 서비스를 요청한다. 
  - https://developer.android.com/reference/android/content/Context#getSystemService(java.lang.String)

- locationManager.isProviderEnabled():지정된 Provider의 Enabled/Disabled 상태를 Return합니다. 
  - https://developer.android.com/reference/android/location/LocationManager#isProviderEnabled(java.lang.String)
    
## Source code

```
public Location getLocation() {
 try {
     locationManager = (LocationManager) mContext.getSystemService(LOCATION_SERVICE);

     boolean isGPSEnabled = locationManager.isProviderEnabled(LocationManager.GPS_PROVIDER);
     boolean isNetworkEnabled = locationManager.isProviderEnabled(LocationManager.NETWORK_PROVIDER);

     if (!isGPSEnabled && !isNetworkEnabled) {

     } else {

         int hasFineLocationPermission = ContextCompat.checkSelfPermission(mContext,
                 Manifest.permission.ACCESS_FINE_LOCATION);
         int hasCoarseLocationPermission = ContextCompat.checkSelfPermission(mContext,
                 Manifest.permission.ACCESS_COARSE_LOCATION);


         if (hasFineLocationPermission == PackageManager.PERMISSION_GRANTED &&
                 hasCoarseLocationPermission == PackageManager.PERMISSION_GRANTED) {


         } else
             return null;


         if (isNetworkEnabled) {


             locationManager.requestLocationUpdates(LocationManager.NETWORK_PROVIDER, MIN_TIME_BW_UPDATES, MIN_DISTANCE_CHANGE_FOR_UPDATES, this);

             if (locationManager != null)
             {
                 location = locationManager.getLastKnownLocation(LocationManager.NETWORK_PROVIDER);
                 if (location != null)
                 {
                     latitude = location.getLatitude();
                     longitude = location.getLongitude();
                 }
             }
         }


         if (isGPSEnabled)
         {
             if (location == null)
             {
                 locationManager.requestLocationUpdates(LocationManager.GPS_PROVIDER, MIN_TIME_BW_UPDATES, MIN_DISTANCE_CHANGE_FOR_UPDATES, this);
                 if (locationManager != null)
                 {
                     location = locationManager.getLastKnownLocation(LocationManager.GPS_PROVIDER);
                     if (location != null)
                     {
                         latitude = location.getLatitude();
                         longitude = location.getLongitude();
                     }
                 }
             }
         }
     }
 }
```

# onCreate메소드의 추가 설명

## description 

- 버튼을 누르면 화면에 위치정보를 띄움
            
    
## Dependence function

- ShowLocationButton.setOnClickListener()
  - ShowLocationButton 버튼이 클릭되면 함수 실행

- gpsTracker.getLatitude()
  - 얻어온 위도를 변수에 초기화
- gpsTracker.getLongitude()
  - 얻어온 경도를 변수에 초기화

- getCurrentAddress()
  - 위도랑 경도를 파라미터로 받아 주소 리턴

    
## Source code
```
Button ShowLocationButton = (Button) findViewById(R.id.button);
        ShowLocationButton.setOnClickListener(new View.OnClickListener()
        {
            @Override
            public void onClick(View arg0)
            {

                gpsTracker = new GpsTracker(MainActivity.this);

                double latitude = gpsTracker.getLatitude();
                double longitude = gpsTracker.getLongitude();

                String address = getCurrentAddress(latitude, longitude);
                textview_address.setText(address);

                Toast.makeText(MainActivity.this, "현재위치 \n위도 " + latitude + "\n경도 " + longitude, Toast.LENGTH_LONG).show();
            }
        });
```


      
      
    
