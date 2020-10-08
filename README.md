# junseokGpsTracker

GpsTracker 어플에 대한 설명
============

1. 어플에 퍼미션 추가
2. GpsTracker 클래스에 대하여
3. MainActivity 얻어온 위치값 toast메세지로 띄우기

*어플에 퍼미션 추가

먼저 manifest파일에 추가할 퍼미션을 먼저 적어놓습니다.
1. android.permission.ACCESS_COARSE_LOCATION - API에서 WiFi 또는 모바일 데이터(또는 둘 다)를 사용하여 기기의 위치를 결정할 수 있습니다. API에서는 정확도가 도시 블록 1개 정도의 오차 수준으로 위치를 반환합니다.
2. android.permission.ACCESS_FINE_LOCATION - API에서 위성 위치 확인 시스템(GPS), WiFi 및 모바일 데이터 등 이용 가능한 위치 제공자를 사용하여 최대한 정확하게 위치를 결정할 수 있습니다.

```
 <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>
```
다음은 MainaActivity클래스에서 사용자에게 어플이 켜지면 요구할 퍼미션에대한 함수를 작성합니다

*MainActivity 변수들

```
 private GpsTracker gpsTracker;

    private static final int GPS_ENABLE_REQUEST_CODE = 2001;
    private static final int PERMISSIONS_REQUEST_CODE = 100;
    String[] REQUIRED_PERMISSIONS  = {Manifest.permission.ACCESS_FINE_LOCATION, Manifest.permission.ACCESS_COARSE_LOCATION};
    
   
   
```
*checkRunTimePermission()함수에서  requsetPermissions()함수를 이용해 사용자에게 시스템 권한요청을 합니다.

```
 void checkRunTimePermission(){


        int hasFineLocationPermission = ContextCompat.checkSelfPermission(MainActivity.this,
                Manifest.permission.ACCESS_FINE_LOCATION);
        int hasCoarseLocationPermission = ContextCompat.checkSelfPermission(MainActivity.this,
                Manifest.permission.ACCESS_COARSE_LOCATION);


        if (hasFineLocationPermission == PackageManager.PERMISSION_GRANTED &&
                hasCoarseLocationPermission == PackageManager.PERMISSION_GRANTED) {




        } else {


            if (ActivityCompat.shouldShowRequestPermissionRationale(MainActivity.this, REQUIRED_PERMISSIONS[0])) {


                Toast.makeText(MainActivity.this, "이 앱을 실행하려면 위치 접근 권한이 필요합니다.", Toast.LENGTH_LONG).show();

                ActivityCompat.requestPermissions(MainActivity.this, REQUIRED_PERMISSIONS,
                        PERMISSIONS_REQUEST_CODE);


            } else {

                ActivityCompat.requestPermissions(MainActivity.this, REQUIRED_PERMISSIONS,
                        PERMISSIONS_REQUEST_CODE);
            }

        }

    }

```

*onRequestPermissionResult함수
사용자가 시스템 권한 대화상자에 응답하면 시스템은 앱의 onRequestPermissionResult()구현을 호출합니다.
onRequestPermissionResult함수에서 사용자의 권한 거부 처리를 합시다.
```
 @Override
    public void onRequestPermissionsResult(int permsRequestCode,
                                           @NonNull String[] permissions,
                                           @NonNull int[] grandResults) {

        if ( permsRequestCode == PERMISSIONS_REQUEST_CODE && grandResults.length == REQUIRED_PERMISSIONS.length) {


            boolean check_result = true;



            for (int result : grandResults) {
                if (result != PackageManager.PERMISSION_GRANTED) {
                    check_result = false;
                    break;
                }
            }


            if ( check_result ) {


            }
            else {

                if (ActivityCompat.shouldShowRequestPermissionRationale(this, REQUIRED_PERMISSIONS[0])
                        || ActivityCompat.shouldShowRequestPermissionRationale(this, REQUIRED_PERMISSIONS[1])) {

                    Toast.makeText(MainActivity.this, "퍼미션이 거부되었습니다. 앱을 다시 실행하여 퍼미션을 허용해주세요.", Toast.LENGTH_LONG).show();
                    finish();


                }else {

                    Toast.makeText(MainActivity.this, "퍼미션이 거부되었습니다. 설정(앱 정보)에서 퍼미션을 허용해야 합니다. ", Toast.LENGTH_LONG).show();

                }
            }

        }
    }
    
```
if문을 사용하여 사용자가 퍼미션을 허용했는지 거부했는지에 대한 결과값을 얻어옵니다.
*ContextCompat.checkSelfPermission()메서드에 권한을 전달하면 메서드가 앱이 권한이 있는지에 따라 PERMISSION_GRANTED 또는 PERMISSION_DENIED를 반환합니다.

```
 void checkRunTimePermission(){


        int hasFineLocationPermission = ContextCompat.checkSelfPermission(MainActivity.this,
                Manifest.permission.ACCESS_FINE_LOCATION);
        int hasCoarseLocationPermission = ContextCompat.checkSelfPermission(MainActivity.this,
                Manifest.permission.ACCESS_COARSE_LOCATION);


        if (hasFineLocationPermission == PackageManager.PERMISSION_GRANTED &&
                hasCoarseLocationPermission == PackageManager.PERMISSION_GRANTED) {




        } else {


            if (ActivityCompat.shouldShowRequestPermissionRationale(MainActivity.this, REQUIRED_PERMISSIONS[0])) {


                Toast.makeText(MainActivity.this, "이 앱을 실행하려면 위치 접근 권한이 필요합니다.", Toast.LENGTH_LONG).show();

                ActivityCompat.requestPermissions(MainActivity.this, REQUIRED_PERMISSIONS,
                        PERMISSIONS_REQUEST_CODE);


            } else {

                ActivityCompat.requestPermissions(MainActivity.this, REQUIRED_PERMISSIONS,
                        PERMISSIONS_REQUEST_CODE);
            }

        }

    }
```
