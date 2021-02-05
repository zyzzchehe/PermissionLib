# PermissionLib
android6.0以上动态权限申请

Step 1. Add the JitPack repository to your build file

Add it in your root build.gradle at the end of repositories:
	
	allprojects {
		repositories {
			...
			maven { url 'https://jitpack.io' }
		}
	}

Step 2. Add the dependency

	dependencies {
	        implementation 'com.github.zyzzchehe:PermissionLib:1.2'
	}


可以调用的方案有两种：

第一种：Activity 中如何调用？

1、Activity 实现回调接口，并重写对应回调函数，如：

    public class MainActivity extends AppCompatActivity implements ZyzzcPermissionUtils.CallBack {
        @Override
        public void grantAll() {
            Log.i(TAG, "grantAll: ");
        }

        @Override
        public void denied() {
            Log.i(TAG, "denied: ");
        }
    }

2、调用

    //申请动态权限
    private ZyzzcPermissionUtils zyzzcPermissionUtils;
    private ArrayList<String> permissionList;
    private int reqCode = 1;
    
onCreate 方法中：    

        zyzzcPermissionUtils = new ZyzzcPermissionUtils(this);
        permissionList = new ArrayList<>();
        permissionList.add(Manifest.permission.INTERNET);
        permissionList.add(Manifest.permission.READ_EXTERNAL_STORAGE);
        permissionList.add(Manifest.permission.WRITE_EXTERNAL_STORAGE);
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
            zyzzcPermissionUtils.request(permissionList, reqCode, this);
        }
	
3、重写onRequestPermissionsResult函数

    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);
        if (requestCode == reqCode) {
            AtomicBoolean grantAll = new AtomicBoolean(true);
            //遍历每一个授权结果
            for (int i = 0; i < grantResults.length; i++) {
                if (grantResults[i] != PackageManager.PERMISSION_GRANTED) {
                    grantAll.set(false);
                    Toast.makeText(this, permissions[i] + "未授权", Toast.LENGTH_SHORT).show();
                    break;
                }
            }
            if (grantAll.get()) {
                grantAll();
            } else {
                denied();
            }
        }
    }

第二种：Application中如何调用？

    public class MyApp extends Application {
        static List<String> permissionList = new ArrayList<>();

        static {
            permissionList.add(Manifest.permission.WRITE_EXTERNAL_STORAGE);
            permissionList.add(Manifest.permission.READ_EXTERNAL_STORAGE);
        }

        @Override
        public void onCreate() {
            super.onCreate();
            if(Build.VERSION.SDK_INT > 23) {
                PermissionsUtil.requestPermission(getApplicationContext(), new PermissionListener() {
                    @Override
                    public void permissionGranted(@NonNull String[] permissions) {
                        Log.d("yazhou", "permission allow");
                    }

                    @Override
                    public void permissionDenied(@NonNull String[] permissions) {
                        Log.d("yazhou", "permission deny");
                    }
                }, permissionList.toArray(new String[]{}));
            }
        }
    }
