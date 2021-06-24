---
title: react-native滚动到顶部隐藏
date: 2020-10-27 13:28:10
tags:
---

### 实现功能

滚动至顶部隐藏当前内容

### 基本思路

- 记录当前位置与滚动后位置，判断是滚动方向
- 向下滚动则滚动内容
- 向上滚动，上一次为位置为 0 则隐藏内容

  ![scroll](temp.gif)

### 主要代码

```
 constructor(props) {
    super(props);
    this.isdrag = false;
    this.scrollY = new Animated.Value(0);
    this.topPosition = new Animated.Value(0);
  }
  render() {
    const { data, moreFlag, viewMoreFn, screenProps } = this.props;
    const { showActionAlert } = this.state;
    return (
      <>
        {moreFlag ? (
          <Animated.ScrollView
            style={{
              top: this.topPosition,
              position: "relative",
            }}
            onScrollBeginDrag={(e) => {
              const offsetY = e.nativeEvent.contentOffset.y;
              this.scrollViewStartOffsetY = offsetY;
            }}
            onScrollEndDrag={(e) => {
                //主要代码
              if (
                e.nativeEvent.contentOffset.y <= 0 &&
                e.nativeEvent.contentOffset.y <= this.scrollViewStartOffsetY
              ) {
                Animated.timing(this.topPosition, {
                  toValue: Dimensions.get("window").height,
                  duration: 300,
                }).start(() => {
                  this.props.changeMoreFlag(false);
                  this.topPosition = new Animated.Value(0);
                });
              }
            }}
            scrollEventThrottle={1}
          >

         <View>
            <Text>滚动内容</Text>
         </View>
          </Animated.ScrollView>
        </>)
        }
```
