---
layout: post
title: "[Android] ViewPager2 사용해보기"
subtitle: ' New ViewPager Version '
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

# ViewPager2

구글에서 May 7, 2019 에 새로운 viewpager2 알파버전을 공개했다.<br />
기존의 ViewPager 와 변경된 점은 `RTL 레이아웃 지원`, `세로 모드 지원`, `기존 ViewPager의 notifyDataSetChanged 버그 문제해결`, `offscreenPageLimit를 통한 뷰 계층에 저장된 뷰나 프레그먼트를 제어할 수 있다는 점`입니다.

## ViewPager2 사용해보기
**우선 ViewPager2는 AndroidX 용으로 나왔기때문에 프로젝트가 AndroidX 로 마이그레이션 되어야한다.**
### 1.build.gradle에 추가하기
~~~gradle
//viewpager2
implementation 'androidx.viewpager2:viewpager2:1.0.0-alpha04'
~~~
### 2.xml에 ViewPager2 추가
필자는 viewpager2 가 있는 xml에서 Databinding을 사용하였다.<br />
**[참고]** viewpager2의 `layout_width`, `layout_height` 는 `match_parent`로 해주어야 에러가 나지않는다. 크기를 지정해서 사용하고싶다면 viewpager2를 다른 뷰그룹으로 감싸주고 상위뷰의 크기를 변경시켜주어야한다.
~~~xml
<androidx.viewpager2.widget.ViewPager2
    android:id="@+id/view_pager"
    android:background="#BABABD"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal"/>
~~~

### 3.ViewPager2 에 들어갈 아이템 xml 정의하기
~~~xml
<layout xmlns:tools="http://schemas.android.com/tools"
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">
    <data>
        <variable
            name="imageUrl"
            type="String" />
    </data>
    <androidx.constraintlayout.widget.ConstraintLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">

        <ImageView
            android:id="@+id/imageView3"
            app:photoUrl="@{imageUrl}"
            android:layout_width="0dp"
            android:layout_height="150dp"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintHorizontal_bias="0.5"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toTopOf="parent"
            app:layout_constraintVertical_bias="0.5"
            app:layout_constraintWidth_percent="0.7"
            tools:ignore="ContentDescription" />

    </androidx.constraintlayout.widget.ConstraintLayout>
</layout>
~~~

### 4.ViewPager2 Adapter 구현하기
viewpager2의 adapter는 이전과 다르게 RecyclerView의 adapter를 구현하는것과 똑같다.
~~~java
private static class PhotoAdapter extends RecyclerView.Adapter<PhotoAdapter.PhotoViewHolder> {
    private List<String> mItems;

    public PhotoAdapter(List<String> mItems) {
        this.mItems = mItems;
    }

    @NonNull
    @Override
    public PhotoAdapter.PhotoViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        View view = LayoutInflater.from(parent.getContext()).inflate(R.layout.item_photo, parent, false);
        return new PhotoViewHolder(view);
    }

    @Override
    public void onBindViewHolder(@NonNull PhotoAdapter.PhotoViewHolder holder, int position) {
        holder.binding.setImageUrl(mItems.get(position));
    }

    @Override
    public int getItemCount() {
        return mItems.size();
    }

    public class PhotoViewHolder extends RecyclerView.ViewHolder {
        ItemPhotoBinding binding;

        public PhotoViewHolder(@NonNull View itemView) {
            super(itemView);
            binding = DataBindingUtil.bind(itemView);
        }
    }
}
~~~ 
### 5.ViewPager2에 adapter 설정해주기
~~~java
@Override
public void onViewCreated(@NonNull View view, @Nullable Bundle savedInstanceState) {
    super.onViewCreated(view, savedInstanceState);
    FragmentItemDetailBinding binding = DataBindingUtil.bind(view);

    PhotoAdapter adapter = new PhotoAdapter(mProduct.getPhotoUrl());
    binding.viewPager.setAdapter(adapter);
}
~~~

[ViewPager2 Goole Sample 링크](https://github.com/googlesamples/android-viewpager2)<br />
[안드로이드 공식 문서 링크](https://developer.android.com/jetpack/androidx/releases/viewpager2)