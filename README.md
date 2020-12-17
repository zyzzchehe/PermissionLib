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
	        implementation 'com.github.zyzzchehe:PermissionLib:1.0'
	}


https://www.jianshu.com/p/3ec8fd6c4754

Activity 中如何调用？
1、Activity 实现回调接口，并重写对应回调函数：
如：
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

2、private ZyzzcPermissionUtils zyzzcPermissionUtils;
 zyzzcPermissionUtils = new ZyzzcPermissionUtils(this);
        perList = new ArrayList<>();
        perList.add(Manifest.permission.INTERNET);
        perList.add(Manifest.permission.READ_EXTERNAL_STORAGE);
        perList.add(Manifest.permission.WRITE_EXTERNAL_STORAGE);
        zyzzcPermissionUtils.request(perList, reqCode, this);
	
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
