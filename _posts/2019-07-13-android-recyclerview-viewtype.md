---
layout: post
title: "[Android] RecyclerView에 아이템이 두가지 타입"
subtitle: 'Android RecyclerView ViewType'
author: "JungHoon-Park"
header-style: text
header-mask: 0.3
comments: true
catalog:  true
tags:
  - Android
  - Study
---

Android RecyclerView ViewType

---

## 리사이클러뷰에서 아이템의 타입이 두가지 이상일 때
<h3>Item.class 생성</h3>
```java
public class Item<T> {
    private T item;

    public Item(T item) {
        this.item = item;
    }

    public T getItem() {
        return item;
    }
}
```

<h3>RecyclerView Adapter</h3>
```java
public class RecyclerViewAdapter extends RecyclerView.Adapter<RecyclerView.ViewHolder> {

    public static final int TYPE_PAGER = 0;
    public static final int TYPE_NORMAL = 1;

    private final FragmentManager mFragmentManager;
    private List<Item> mItems;

    public RecyclerViewAdapter(FragmentManager fm, List<Item> items) {
        mFragmentManager = fm;
        mItems = items;
    }

    static class PagerViewHolder extends RecyclerView.ViewHolder {
        ViewPager viewPager;

        public PagerViewHolder(View itemView) {
            super(itemView);
            viewPager = (ViewPager) itemView.findViewById(R.id.view_pager);
        }
    }

    static class ItemViewHolder extends RecyclerView.ViewHolder {
        TextView textView;

        public ItemViewHolder(View itemView) {
            super(itemView);
            textView = (TextView) itemView.findViewById(R.id.text_view);
        }
    }

    @Override
    public int getItemViewType(int position) {
        return position == 0 ? TYPE_PAGER : TYPE_NORMAL;
    }

    @Override
    public RecyclerView.ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        switch (viewType) {
            case TYPE_PAGER:
                return new PagerViewHolder(LayoutInflater.from(parent.getContext()).inflate(R.layout.item_pager, parent, false));
            case TYPE_NORMAL:
                return new ItemViewHolder(LayoutInflater.from(parent.getContext()).inflate(R.layout.item_recyler, parent, false));
        }
        return null;
    }

    @Override
    public void onBindViewHolder(RecyclerView.ViewHolder holder, int position) {
        switch (holder.getItemViewType()) {
            case TYPE_PAGER:
                List<Fragment> item = (List<Fragment>) mItems.get(position).getItem();
                PagerViewHolder holder1 = (PagerViewHolder) holder;
                ViewPagerAdapter adapter = new ViewPagerAdapter(mFragmentManager,item);
                holder1.viewPager.setAdapter(adapter);
                break;
            case TYPE_NORMAL:
                String item1 = (String) mItems.get(position).getItem();
                ItemViewHolder holder2 = (ItemViewHolder) holder;
                holder2.textView.setText(item1);
                break;
        }
    }

    @Override
    public int getItemCount() {
        return mItems.size();
    }
}
```
Item 타입으로 데이터를 생성자를 통해 받고, onBindViewHolder에서 viewType에 따라 데이터를 set 해주면 된다.