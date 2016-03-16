# android_ioc


1 定义注解

/**
 * Created by mazhou on 2016/3/16.
 */
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
public @interface ContentView {
    int value();
}

2 构建注解

@ContentView(R.id.fab)
public FloatingActionButton fab;

@ContentView(R.id.toolbar)
public Toolbar toolbar;

3 通过反射注入

public static void inject(Activity activity)
{
    injectContentView(activity);
    injectViews(activity);
}

private static final String METHOD_SET_CONTENTVIEW = "setContentView";
private static void injectContentView(Activity activity){
    Class<? extends  Activity> clazz = activity.getClass();
    ContentView contentView = clazz.getAnnotation(ContentView.class);
    if (contentView != null){
        int contentViewLayoutId = contentView.value();
        try {
            Method method = clazz.getMethod(METHOD_SET_CONTENTVIEW , int.class);
            method.setAccessible(true);
            method.invoke(activity, contentViewLayoutId);
        }catch (Exception e){
        }
    }
}

//通过反射进行注入
private static final String METHOD_FIND_VIEW_BY_ID = "findViewById";
private static void injectViews(Activity activity){

    Class<? extends Activity> clazz = activity.getClass();
    Field[] fields = clazz.getDeclaredFields();
    for (Field field : fields){
        ContentView contentView = field.getAnnotation(ContentView.class);
        if (contentView != null){
            int viewId = contentView.value();
            if (viewId != -1){
                Log.e("Tag", viewId + "");
                try {
                    Method method = clazz.getMethod(METHOD_FIND_VIEW_BY_ID, int.class);
                    Object resView = method.invoke(activity, viewId);
                    field.setAccessible(true);
                    field.set(activity, resView);
                }catch (Exception e){
                }
            }
        }
    }
}


