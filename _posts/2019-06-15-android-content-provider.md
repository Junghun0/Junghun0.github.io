---
layout: post
title: "[Android] Content Provider 사용해보기"
subtitle: 'Android Content Provider Sample'
author: "JungHoon-Park"
header-style: text
header-mask: 0.3
comments: true
catalog:  true
tags:
  - Android
  - Mobile
  - Study
---

Android Content Provider 사용해보기

---

# Content Provider 란

안드로이드는 기본적으로 SQLite를 사용할 수 있도록 다양한 API를 제공해주고 있다. 다시 말해 어플리케이션 내에서 사용하는 데이터를 공유하기 위한 컴포넌트이다. `Content Provider`는 여러 앱 간에 데이터를 공유할 필요가 있을 때 사용한다.<br />
`ContentProvider`에 접근하는 것은 `ContentResolver`를 통해서만 가능하다. 그리고 `ContentResolver`는 `Context`의 `getContentResolver()` 메서드로 구할 수 있다. `ContentResolver`는 추상 클래스이고 실제 구현체는 `ContextImpl`의 내부 클래스인 `ApplicationContentResolver`이다.<br /><br />
예제는 에뮬레이터의 이미지를 불러와 GridView에 보여주는 예제이다.<br />

### 소스코드
**MainActivity**
~~~java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_sample5);

    GridView gridView = findViewById(R.id.photo_list);

    Cursor cursor = getContentResolver().query(
            MediaStore.Images.Media.EXTERNAL_CONTENT_URI,
            null,
            null,
            null,
            MediaStore.Images.ImageColumns.DATE_TAKEN + "DESC"
    );

    MyCursorAdapter adapter = new MyCursorAdapter(this, cursor);
    gridView.setAdapter(adapter);
    
    gridView.setOnItemClickListener((parent, view, position, id) -> {
        Cursor cursor1 = (Cursor) parent.getAdapter().getItem(position);
        String uri = cursor1.getString(cursor1.getColumnIndexOrThrow(MediaStore.Images.Media.DATA));
        Toast.makeText(Sample5Activity.this, "사진 경로: "+uri, Toast.LENGTH_SHORT).show();
        
    });
}
~~~

**CursorAdapter**
~~~java
public class MyCursorAdapter extends CursorAdapter {
    public MyCursorAdapter(Context context, Cursor c) {
        super(context, c);
    }

    @Override
    public View newView(Context context, Cursor cursor, ViewGroup viewGroup) {
        return LayoutInflater.from(context).inflate(R.layout.item_photo, viewGroup, false);
    }

    @Override
    public void bindView(View view, Context context, Cursor cursor) {
        ImageView imageView = view.findViewById(R.id.photo_image);

        /*String uri = cursor.getString(cursor.getColumnIndexOrThrow(MediaStore.Images.Media.DATA));
        imageView.setImageURI(Uri.parse(uri));*/
        String uri = cursor.getString(cursor.getColumnIndexOrThrow(MediaStore.Images.Media.DATA));
        Glide.with(context).load(uri).into(imageView);
    }
}
~~~
이후에 권한 체크하는 부분이 필요하다. 추후에 추가예정



