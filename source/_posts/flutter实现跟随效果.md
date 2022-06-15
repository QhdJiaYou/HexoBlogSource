---
title: flutter跟随
date: 2022-06-06 17:50:43
---

### 一、背景

PC上ToolTip组件、Selector组件、Cascade组件、以及时间选择组件等等，在App上需要弹出展示的情况，大多数情况下，在PC上的交互是跟随弹出。

### 二、ToolTip难以胜任

但是目前flutter原生的ToolTip组件对外暴露参数有限，且很难扩展使用，举一个最难接受的就是，弹出内容无法直接限制宽度，如果不做限制，会用页面最大宽度展示。

此外，弹出内容只能传字符串类型，无法自定义Widget，这也就导致很难画出PC toolTip上的三角形效果，更不能定制Widget展示，难以实现Selector、Cascade等带有交互的复杂组件。

如果是只是简单实现ToolTip效果，不在乎太多样式，倒是有解决办法，手动在字符串中拼接"\n"字符， 外部通过传递参数**lineWordCount**设置一行容纳的字符个数，以此解决宽度问题，下面是封装好的ToolTip组件。

```dart
class ToolTipNew extends StatelessWidget {
  const ToolTipNew({
    this.key,
    @required this.target,
    @required this.tip,
    this.width,
    this.height,
    this.onClick,
    this.lineWordCount = 30,
  }) : super(key: key);

  final Key key;
  final Widget target;
  final String tip;
  final double width;
  final double height;
  final Function onClick;

  /// 每一行字符数量
  final int lineWordCount;

  String generateMessage(String str) {
    String newLabel = '';
    int totalLen = str.length;
    if (totalLen <= lineWordCount) {
      newLabel = str;
    } else {
      int lines = totalLen ~/ lineWordCount;
      for (int i = 0; i <= lines; i++) {
        newLabel += (str.substring(
                lineWordCount * i, min(lineWordCount * (i + 1), totalLen)) +
            ((i == lines) ? '' : '\n'));
      }
    }
    return newLabel;
  }

  @override
  Widget build(BuildContext context) {
    return UITools.isPc
        ? Material(
            child: Container(
              color: Colors.white,
              width: width ?? 100,
              height: height ?? 36,
              child: Center(
                child: Tooltip(
                  verticalOffset: 10,
                  message: generateMessage(tip),
                  padding: const EdgeInsets.all(12),
                  decoration: BoxDecoration(
                      color: Color(0xFF3f4156),
                      borderRadius: BorderRadius.circular(2)),
                  textStyle: TextStyle(
                    decoration: TextDecoration.none,
                    fontSize: 14,
                    color: Colors.white,
                  ),
                  child: target,
                ),
              ),
            ),
          )
        : GestureDetector(
            child: target,
            onTap: onClick ?? () {},
          );
  }
}
```

如果为了追求更好的交互样式效果，我们还是另寻别的出路，比较好。

### 三、Overlay配合伴随组件实现Follower组件

1. 使用overlay
1. CompositedTransformTarget和CompositedTransformFollower


完整封装follower.dart

```dart
import 'package:flutter/material.dart';

enum DirectionTypeVertical {
  top,
  bottom,
}
enum DirectionTypeHorizontal {
  left,
  right,
}
const double defaultModalHeight = 180;
const double shadowWidth = 16;
const Color borderDefaultColor = Color(0xFFE9EAF2);

class EolFollowerWidget {
  OverlayEntry _overlayEntry;
  GlobalKey _gkey = GlobalKey();
  LayerLink _layerLink = LayerLink();

  /// 取消
  void cancel() {
    if (_overlayEntry != null) {
      _overlayEntry.remove();
      _overlayEntry = null;
    }
  }

  /// target组件
  Widget target(
    BuildContext context, {
    @required Widget child,
  }) {
    return CompositedTransformTarget(
      link: _layerLink,
      child: Container(
        key: _gkey,
        child: child,
      ),
    );
  }

  /// follower组件
  follower(
    BuildContext context, {
    @required Widget child,

    /// 弹窗高度， 默认180
    double height,

    /// 弹窗宽度, 默认与目标点击对象宽度一致
    double width,

    /// 弹窗距离目标内容底部偏移offset
    double topOffset = 0,

    /// 点击其他区域时回调函刷
    Function onClickOtherSpace,

    // /// 弹窗弹出方向
    // DirectionTypeVertical directionVertical = DirectionTypeVertical.bottom,
  }) {
    RenderBox renderBox = _gkey.currentContext.findRenderObject();
    Size targetSize = renderBox.size;
    // 弹出内容是否加滚动，当两侧宽度都小的时候加
    // bool containerHasVerticalScroll = false;
    // 极端情况，后续支持，TODO
    // bool containerHasHorizontalScroll = false;
    // 全局坐标
    double globalPositionY = renderBox.localToGlobal(Offset.zero).dy;
    // 横坐标方向宽度
    double globalPositionX = renderBox.localToGlobal(Offset.zero).dx;
    double screenHeight = MediaQuery.of(context).size.height;
    double screenWidth = MediaQuery.of(context).size.width;
    // 距离窗口底部的高度
    double heightToBottom = screenHeight - globalPositionY;

    /// 计算弹窗垂直弹出方向
    DirectionTypeVertical directionVertical = DirectionTypeVertical.bottom;
    // 底部高度太小
    double modalHeight = height ?? defaultModalHeight;
    if (modalHeight > heightToBottom) {
      directionVertical = DirectionTypeVertical.top;
      // 顶部高度也小
      if (modalHeight > globalPositionY) {
        // 选两个里面最大的滚动展示
        // containerHasVerticalScroll = true;
        if (heightToBottom > globalPositionY) {
          directionVertical = DirectionTypeVertical.bottom;
          modalHeight = heightToBottom;
        } else {
          modalHeight = globalPositionY;
        }
      }
    }

    // 计算水平方向弹出方向，默认左对齐
    DirectionTypeHorizontal directionHorizontal = DirectionTypeHorizontal.left;
    // 距离右侧的宽度
    double widthToRight = screenWidth - globalPositionX;
    // 距离左侧的宽度
    double widthToLeft = globalPositionX + targetSize.width;
    // 弹窗的宽度
    double modalWidth = width ?? targetSize.width;
    // 选择框最左侧距离右边的宽度 是否 大于弹窗的宽度
    if (modalWidth > widthToRight) {
      directionHorizontal = DirectionTypeHorizontal.right;
      if (modalWidth > widthToLeft) {
        // containerHasHorizontalScroll = true;
        if (widthToRight > widthToLeft) {
          directionHorizontal = DirectionTypeHorizontal.left;
          modalWidth = widthToRight;
        } else {
          modalWidth = widthToLeft;
        }
      }
    }
    // 右侧对齐弹出时，modal与target的宽度差距
    double diffRight = modalWidth + shadowWidth - targetSize.width;
    // 右侧对齐弹出时，左侧区域宽度计算
    double leftCloseAreaWidth =
        directionHorizontal == DirectionTypeHorizontal.right
            ? globalPositionX - diffRight
            : globalPositionX;

    // 点击是关闭区域
    Widget closeAreaWidget = GestureDetector(
      behavior: HitTestBehavior.opaque,
      onTap: () {
        cancel();
        if (onClickOtherSpace != null) {
          onClickOtherSpace();
        }
      },
      child: Material(color: Colors.transparent, child: SizedBox.shrink()),
    );

    Widget showModalInner = Container(
      margin: EdgeInsets.only(left: shadowWidth),
      child: Container(
        alignment: Alignment.topCenter,
        width: modalWidth,
        height: modalHeight,
        child: Container(
          child: child,
          decoration: BoxDecoration(
            boxShadow: [
              BoxShadow(
                color: Color(0x26585A6E),
                offset: Offset(0, 10),
                blurRadius: shadowWidth,
              )
            ],
          ),
        ),
      ),
    );

    // 弹窗内容区域
    Widget showModalWidget = GestureDetector(
      behavior: HitTestBehavior.opaque,
      onTap: () {
        cancel();
        if (onClickOtherSpace != null) {
          onClickOtherSpace();
        }
      },
      child: Material(
        color: Colors.transparent,
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [showModalInner],
        ),
      ),
    );

    _overlayEntry = OverlayEntry(
        builder: (context) => Stack(
              children: [
                // 选择框下侧区域
                directionVertical == DirectionTypeVertical.bottom
                    ? Positioned(
                        top: 0,
                        left: 0,
                        right: 0,
                        bottom: 0,
                        child: CompositedTransformFollower(
                          link: _layerLink,
                          showWhenUnlinked: false,
                          offset: Offset(
                              directionHorizontal ==
                                      DirectionTypeHorizontal.right
                                  ? -diffRight
                                  : -shadowWidth,
                              targetSize.height + (topOffset ?? 0)),
                          child: showModalWidget,
                        ),
                      )
                    : Positioned(
                        top: 0,
                        left: 0,
                        right: 0,
                        bottom: 0,
                        child: CompositedTransformFollower(
                          link: _layerLink,
                          showWhenUnlinked: false,
                          offset: Offset(0, targetSize.height),
                          child: closeAreaWidget,
                        ),
                      ),
                // 选择框右侧区域
                Positioned(
                  top: 0,
                  left: 0,
                  right: 0,
                  height: targetSize.height,
                  child: CompositedTransformFollower(
                    link: _layerLink,
                    showWhenUnlinked: false,
                    offset: Offset(targetSize.width, 0),
                    child: closeAreaWidget,
                  ),
                ),
                // 选择框左侧区域
                Positioned(
                  top: 0,
                  left: 0,
                  width: leftCloseAreaWidth,
                  height: MediaQuery.of(context).size.height,
                  child: CompositedTransformFollower(
                    link: _layerLink,
                    showWhenUnlinked: false,
                    offset: Offset(-globalPositionX, -globalPositionY),
                    child: closeAreaWidget,
                  ),
                ),
                // 选择框左上侧区域(右下弹出)
                directionHorizontal == DirectionTypeHorizontal.right &&
                        directionVertical == DirectionTypeVertical.bottom
                    ? Positioned(
                        top: 0,
                        left: 0,
                        width: diffRight,
                        height: globalPositionY + targetSize.height,
                        child: CompositedTransformFollower(
                          link: _layerLink,
                          showWhenUnlinked: false,
                          offset: Offset(-diffRight, -globalPositionY),
                          child: closeAreaWidget,
                        ),
                      )
                    : SizedBox.shrink(),
                // 选择框左上侧区域(右上弹出)
                directionHorizontal == DirectionTypeHorizontal.right &&
                        directionVertical == DirectionTypeVertical.top
                    ? Positioned(
                        top: 0,
                        left: 0,
                        width: diffRight,
                        height: heightToBottom,
                        child: CompositedTransformFollower(
                          link: _layerLink,
                          showWhenUnlinked: false,
                          offset: Offset(-diffRight, 0),
                          child: closeAreaWidget,
                        ),
                      )
                    : SizedBox.shrink(),
                // 选择框上侧区域
                directionVertical == DirectionTypeVertical.top
                    ? Positioned(
                        top: 0,
                        left: 0,
                        right: 0,
                        height: globalPositionY - modalHeight,
                        child: CompositedTransformFollower(
                          link: _layerLink,
                          showWhenUnlinked: false,
                          offset: Offset(0, -globalPositionY),
                          child: closeAreaWidget,
                        ),
                      )
                    : SizedBox.shrink(),
                directionVertical == DirectionTypeVertical.top
                    ? Positioned(
                        top: 0,
                        left: 0,
                        right: 0,
                        height: modalHeight,
                        child: CompositedTransformFollower(
                          link: _layerLink,
                          showWhenUnlinked: false,
                          offset: Offset(
                              directionHorizontal ==
                                      DirectionTypeHorizontal.right
                                  ? -diffRight
                                  : -shadowWidth,
                              -modalHeight),
                          child: showModalWidget,
                        ),
                      )
                    : Positioned(
                        top: 0,
                        left: 0,
                        right: 0,
                        height: globalPositionY,
                        child: CompositedTransformFollower(
                          link: _layerLink,
                          showWhenUnlinked: false,
                          offset: Offset(0, -globalPositionY),
                          child: closeAreaWidget,
                        ),
                      ),
              ],
            ));
    var overlayState = Overlay.of(context);
    overlayState.insert(_overlayEntry);
  }
}
```

该组件已很好的支持实现了最开始说的PC上的组件，ToolTip组件、Selector组件、Cascade组件、以及时间选择组件。

