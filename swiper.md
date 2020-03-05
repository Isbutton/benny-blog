## 使用swiper 开发中遇到的问题记录

### 1.数据异步更新或swiper样式变动后，swiper无变化或滚动异常

启动动态检查器，observer：当子元素变更时，或改变swiper样式（隐藏显示）时动初始化swiper
observeParents：父元素变化时自动更新
```
swiperOption: {
    slidesOffsetBefore: 0,
    spaceBetween: 16,
    slidesPerView: "auto",
    freeMode: true
    observer: true,
    observeParents: true,
    observeSlideChildren: true,
  }
```

### 2.loop模式下，复制出的slide绑定事件未生效
在初始化时绑定事件
```
swiperOption() {
  return {
    loop: true,
    spaceBetween: 16,
    autoplay: { delay: 3000 },
    pagination: {
      el: ".swiper-pagination"
    },
    on: {
      click: function() {
        if (vm) vm.jumpToBuyPage(this.realIndex)
      }
    },
    preventLinksPropagation: false
  }
}
```

### 3.loop模式下，第一个slide不随异步变化而变化
背景：在初始化swiper之后，会进行异步查询，将查询结果作为角标显示在slide上。但是当轮播一遍后，回到一个slide会缺少角标，在移动到第二个slide的时候可以看到角标又出现了。

原因是：初始化时复制了slide，异步查询后并不会自动更新复制的slide。所以导致复制slide缺少了角标。

解决方案：在异步请求后手动更新loop

```
fun().then(() => {
    this.$refs.serviceZoneAdsSwiper.swiper && this.$refs.serviceZoneAdsSwiper.swiper.loopDestroy()
    this.$refs.serviceZoneAdsSwiper.swiper && this$refs.serviceZoneAdsSwiper.swiper.loopCreate()
})
```

### 4.swiper update和observer都无效
背景：页面底部有个卡片，会展示商品swiper。用户点击关闭可收起卡片，打开后卡片恢复原长度。 此时swiper滚动异常，可以拖拉超出滚动范围。

在打开卡片时，this.$nextTick(）里swiper.update()无效，observer也无效。

后观察发现容纳swiper的div在卡片打开时宽度为390px,收起后div隐藏。
打开后由于有展开的动画，导致div会先恢复为10px（div显示且此时卡片还在收起状态），动画结束（.3s）后div宽度才恢复390px。

所以在update的时候div为10px，导致滚动异常。
应该在动画结束div恢复至正常时再update

```
this.$nextTick(() => {
  setTimeout(() => {
    this.$refs.newsSwiper && this.$refs.newsSwiper.swiper && this.$refs.newsSwiper.swiper.update()
  }, 400)
})
```