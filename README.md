checkLocationServicesStatus() 메소드
=======
1. 간단한 설명: locationManager 인스턴스에 getSystemService()메소드를 이용해 위치서비스를 집어 넣습니다.
            함수가 종료되기전에 return값으로 locationManager.isProviderEnabled()메소드를 사용해 
            (GPS : LocationManager.GPS_PROVIDER /전화기지국 : LocationManager.NETWORK_PROVIDER) 
            GPS 프로바이더 사용가능여부 true,네트워크 프로바이더 사용가능여부 true 을 리턴해줍니다
            
2 . 매개변수(의존함수 들의 매개변수 밖에 없음)
    getSystemService의 매개변수 LOCATION_SERVICE: 위치 업데이트를 제어하는 역할을 함
    isProviderEnabled의 매개변수 LocationManager.GPS_PROVIDER: 위성을 사용하여 위치를 결정하는 공급자
    isProviderEnabled의 매개변수 LocationManager.NETWORK_PROVIDER: 기지국 및 WiFi 액세스 포인트 근처를 기반으로 위치를 결정하는 공급자.
    
3 . 반환값    
    isProviderEnabled()을 사용하여 지정된 공급자의 현재 활성화 / 비활성화 상태를 반환합니다. 
    
4. 의존함수 
    getSystemService(): getSystemService()메서드를 사용하여 성공적으로 객체를 생성하게 된다면 대부분 Mannager라는
                                        접미어가 붙은 관리 매니저 객체를 반환한다 
    isProviderEnabled():지정된 공급자의 활성화/비활성화 상태를 반환합니다.                                        
    
5. 소스코드    
    
    ```
     public boolean checkLocationServicesStatus() {
        LocationManager locationManager = (LocationManager) getSystemService(LOCATION_SERVICE);

        return locationManager.isProviderEnabled(LocationManager.GPS_PROVIDER)
                || locationManager.isProviderEnabled(LocationManager.NETWORK_PROVIDER);
    }
    ```
    
 showDialogForLocationServiceSetting메소드
    =====
    
    1. 간단한 설명
    
        이 메소드는 사용자에게 대화박스를 이요하여 위치서비스를 요구합니다, 사용자가 허용을 누르면 
        startActivityForResult()메소드에 인텐트 객체랑 GPS코드를 파라미터로 넘깁니다.
                  
    2. 매개변수
    
       setPositiveButton의 매개변수:CharSequence text(버튼의 텍스트를 보여주기 위한 문자열),
                                   DialogInterface.OnClickListener listener(버튼의 클릭을 처리하는 리스너)
        
       setNegativeButton의 매개변수: setPositiveButton과 동일
       
       Intent의 매개변수: ACTION_LOCATION_SOURCE_SETTINGS(현재 위치 소스 구성을 허용하는 설정을 표시합니다)
       
       startActivityForResult의 매개변수: Intent 객체 ,변수 GPS_ENABLE_REQUEST_CODE

    
    3. 반환값
       이 메소드는 사용자에게 대화박스를 띄워주게되는 내장 메소드 입니다 포지티브버튼을 누르면 
       startActivityForResult()를 호출합니다. 액티비티는 해당 결과를 onActivityResult() 에게
       콜백에서 별도의 Intent 객체로 수신합니다
       네거티브버튼을 누르면 대화박스가 꺼집니다.
       
    4. 의존함수
        setTitle: 대화상자의 제목
        setMessage: 대화상자의 메세지
        setCancelable: 사용자가 대화상자를 없앨 수 있는지에 대한 여부 설정
        setPositiveButton: 대화상자의 허용버튼을 눌렀을때 일어나는 함수 onClick메소드를 오버라이드 한다
        setPositiveButton: 대화상자의 취소버튼을 눌렀을때 일어나는 함수 onClick메소드를 오버라이드 한다
        startActivityForResult:onActivityResult메소드와 콜백에서 액티비티의 결과를 별도의 Intent객체로 수신하는 함수
        
    5. 소스코드        
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
  OnActivityResult메소드
  =====
  
  1. 간단한 설명
     startActivityForResult메소드에서 수신한 객체랑 Gps코드를 switch문으로 확인하고 if문으로
     지정된 공급자가 활성화 되어있는지 확인되면 checkRunTimePermission메소드를 호출한다.
     
  2. 매개변수
     onActivityResult메소드의 매개변수 :requestCode는 대화상자 액티비티랑 MainActivity를 구별하기위해 사용
                                       resultCode는 어떠한 결과코드를 주었는지에 대한 변수
                                       Intent data는 액티비티에서 보낸 결과 데이터가 들어가있는 부분
  3. 반환값
     결과적으로 코드에 문제가 없다면 "GPS 활성화 되있음"이라는 로그를 남기고
     checkRunTimePermission메소드를 호출한다
     
  4. 의존함수
     checkRunTimePermission메소드
     
  5. 소스코드
     
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
    }
     ```
     
   checkRunTimePermission메소드
   ====
   1. 간단한 설명
      위치 퍼미션을 가지고 있는지 체크합니다 , 이미 퍼미션을 가지고 있다면 위치 값을 가져올 수 있지만
      퍼미션을 허용한적이 없다면 퍼미션 요청을 합니다.
   
   2. 매개변수
      ContextCompat.checkSelfPermission메소드의 매개변수: MainActivity.this, Manifest.permission.ACCESS_FINE_LOCATION
                                               액티비티와,위치 정보 엑세스권한을 파라미터로 받음
                                                     
      shouldShowRequestPermissionRationale메소드의 매개변수:                          
      MainActivity.this,
      shouldShowRequestPermissionRationale(MainActivity.this, REQUIRED_PERMISSIONS[0]) 위치정보 엑세스 권한을확인하고 
      거부를 한적이 있는 경우에는 true를 반환합니다.
      REQUIRED_PERMISSIONS[0]=Manifest.permission.ACCESS_FINE_LOCATION
      
      requestPermissions의 매개변수: MainActivity.this, 
      REQUIRED_PERMISSIONS=Manifest.permission.ACCESS_FINE_LOCATION, Manifest.permission.ACCESS_COARSE_LOCATION
      PERMISSIONS_REQUEST_CODE=2001;
      
   3. 반환값
      이미 퍼미션을 가지고 있다면 허용된 걸로 인식하고 종료,
      
      퍼미션 요청이 허용되지 않았으면 퍼미션 요청이 필요합니다
      1. 사용자가 퍼미션 거부를 한 적이 있는 경우에는 Toast문으로 퍼미션이 필요한 이유설명
         requestPermissions메소드로 퍼미션을 요청합니다.
      2. 사용자가 퍼미션 거부를 한 적이 없는 경우에는 퍼미션 요청을 바로 합니다.
         requestPermissions메소드로 퍼미션 요청
   
   4. 의존함수
      ContextCompat.CheckSelfPermission: 이 메서드를 통해 특정권한이 이미 획득됐는지 확인합니다.
                                         메서드에서 PERMISSION_DENIED를 반환하면 shouldShowRequestPermissionRationale()을 호출합니다.
      
      shouldShowRequestPermissionRationale: 이 메서드는 사용자가 권한 요청 팝업에서 수락을 누르지 않으면 자동으로 반환값이 true가 됩니다.
     
      requestPermissions메소드는: 요청코드를 포함하여 사용자에게 퍼미션을 요청합니다 권한 요청을 만날때 새 process팝업이 뜨며 
      사용자가 이를 수락하면 역시 권한 사용 기능을 수행할 수 있게 됩니다. 이 메소드의 요청 결과는 onRequestPermissionResult에서 
      수신됩니다.
      
   5. 수신코드
    
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
   onRequestPermissionResult
   ====
   
   1. 간단한 설명
      권한 요청 결과에 대한 콜백메소드
   
   2. 매개변수
     onRequestPermissionsResult메소드:
      int permsRequestCode : 전달된 요청 코드
      @NonNull String[] permissions: 요청 된 권한
      @NonNull int[] grandResults: 해당 권한에 대한 부여 결과
      
   3. 반환값
   
   
      
      
    
