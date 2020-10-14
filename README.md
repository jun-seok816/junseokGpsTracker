# checkLocationServicesStatus() 메소드


## description 

지정된 공급자의 현재 활성화/비활성화 상태를 반환합니다.
            
## parameter

없음
    
## return value   

isProviderEnabled()을 사용하여 지정된 공급자의 현재 활성화 / 비활성화 상태를 반환합니다. 
    
## Dependence function
getSystemService(): 주어진 파라미터에 대응되는 안드로이드가 제공하는 시스템 서비스를 요청한다. >https://promobile.tistory.com/169

isProviderEnabled():지정된 공급자의 활성화/비활성화 상태를 반환합니다.                                        
    
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

이 메소드는 사용자에게 대화박스를 이용하여 위치서비스를 요구합니다.
            
## parameter

없음
    
## return value   

사용자에게 대화박스를 띄워줍니다 
    
## Dependence function

setTitle: 대화상자의 제목

setMessage: 대화상자의 메세지

setCancelable: 사용자가 대화상자를 없앨 수 있는지에 대한 여부 설정

setPositiveButton: 대화상자의 허용버튼을 눌렀을때 일어나는 함수 onClick메소드를 오버라이드 한다
setPositiveButton: 대화상자의 취소버튼을 눌렀을때 일어나는 함수 onClick메소드를 오버라이드 한다

startActivityForResult: onActivityResult메소드와 콜백에서 액티비티의 결과를 별도의 Intent객체로 수신하는 함수
                        >https://developer.android.com/training/basics/intents/result?hl=ko
    
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

startActivityForResult메소드에서 수신한 객체랑 Gps코드를 확인하고 지정된 공급자가 활성화 되어있는지 확인되면
checkRunTimePermission메소드를 호출한다.
            
## parameter

requestCode: 대화상자 액티비티랑 MainActivity를 구별하기 위한 코드

resultCode: 어떠한 결과코드를 주었는지에 대한 변수

Intent data: 액티비티에서 보낸 결과 데이터가 들어가있는 부분

>https://medium.com/@henen/%EB%B9%A0%EB%A5%B4%EA%B2%8C-%EB%B0%B0%EC%9A%B0%EB%8A%94-%EC%95%88%EB%93%9C%EB%A1%9C%EC%9D%B4%EB%93%9C-intent-3-%EC%97%91%ED%8B%B0%EB%B9%84%ED%8B%B0%EA%B0%84%EC%9D%98-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EC%A0%84%EC%86%A1-2-50e7456226fc

    
## return value   

log.d

checkRunTimePermission();
    
## Dependence function

checkRunTimePermission메소드

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

위치 퍼미션을 가지고 있는지 체크합니다.
            
## parameter

없음
    
## return value   

퍼미션을 가지고 있다면 허용된 걸로 인식하고 종료,
퍼미션이 허용되지 않았다면 퍼미션 요청
    
## Dependence function

ContextCompat.CheckSelfPermission: 이 메서드를 통해 특정권한이 이미 획득됐는지 확인합니다.  
                                    권한이 없으면 shouldShowRequestPermissionRationale을 호출합니다

shouldShowRequestPermissionRationale: 이 메서드는 사용자가 권한 요청 팝업에서 수락을 누르지 않으면 자동으로 반환값이 true가 됩니다.

requestPermissions: 사용자에게 퍼미션을 요청합니다, 이 메소드의 요청 결과는 onRequestPermissionResult에서 수신됩니다.

>https://developer.android.com/training/permissions/requesting?hl=en
    
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

requestPermissions메소드의 권한 요청 결과에 대한 콜백메소드
            
## parameter

int permsRequestCode : 전달된 요청 코드

@NonNull String[] permissions: 요청 된 권한

@NonNull int[] grandResults: 해당 권한에 대한 부여 결과
>https://m.blog.naver.com/PostView.nhn?blogId=wnwogh88&logNo=220548983598&proxyReferer=https:%2F%2Fwww.google.com%2F

## return value   

퍼미션을 허용했는지 체크, 만약 거부한 퍼미션이 있으면 앱을 사용할 수 없는 이유를 설명해줌
    
## Dependence function

shouldShowRequestPermissionRationale: 권한 요청을 한 번 거절하면 메서드 반환 값이 true가 됩니다.
>https://academy.realm.io/kr/posts/android-marshmellow-permission/
    
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

context 변수를 파라미터로 받고 getLocation메소드 호출
            
## parameter

context: 액티비티 정보를 얻어오는 변수
>https://zxcv5500.tistory.com/258

## return value   

변수mContext 초기화
getLocation메소드 호출
    
## Dependence function

getLocation메소드 호출
    
## Source code

```
public GpsTracker(Context context) {
    this.mContext = context;
    getLocation();
}
```

# getLocation메소드

## description 

위도랑 경도를 리턴하는 메소드
            
## parameter

없음

## return value   

받은 제공자를 기준으로 위도랑 경도를 반환합니다.
    
## Dependence function

getSystemService(): 주어진 파라미터에 대응되는 안드로이드가 제공하는 시스템 서비스를 요청한다. >https://promobile.tistory.com/169

isProviderEnabled():공급자가 허용되어있는지 확인한다. 
    
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

# onCreat메소드의 추가 설명

## description 

버튼을 누르면
화면에 위치정보를 띄움
            
    
## Dependence function

setOnClickListener(): 변수ShowLocationButton 으로 지정한 버튼이 클릭되면 함수 실행

gpsTracker.getLatitude(): 얻어온 위도를 변수에 초기화
gpsTracker.getLongitude():얻어온 경도를 변수에 초기화

getCurrentAddress(): 위도랑 경도를 파라미터로 받아 주소 리턴

    
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


      
      
    
